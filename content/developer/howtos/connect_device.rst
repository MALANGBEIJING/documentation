=====================
连接设备
=====================

IoT 驱动程序允许任何 Odoo 模块与连接到 IoT Box 的任何设备进行实时通信。与 IoT Box 的通信是双向的，因此 Odoo 客户端可以向受支持的任何设备发送命令并接收信息。

要为设备添加支持，我们只需要：

- 一个 `Interface`，用于检测特定类型的已连接设备
- 一个 `Driver`，用于与单个设备通信

每次启动时，IoT Box 都会加载可以在连接的 Odoo 实例上找到的所有接口和驱动程序。每个模块都可以包含一个 `iot_handlers` 目录，该目录将被复制到 IoT Box。该目录的结构如下：

.. code-block:: text

    your_module
    ├── ...
    └── iot_handlers
        ├── drivers
        │   ├── DriverName.py
        │   └── ...
        │
        └── interfaces
            ├── InterfaceName.py
            └── ...

检测设备
==============

通过 `Interfaces` 检测连接到 IoT Box 的设备。每种支持的连接类型（USB、蓝牙、视频、打印机、串口等）都有一个接口。接口维护检测到的设备列表，并将它们与正确的驱动程序关联。

受支持的设备将出现在您通过其 IP 地址访问的 IoT Box 首页和连接的 Odoo 实例的 IoT 模块中。

接口
---------

接口的作用是维护通过确定的连接类型连接的设备列表。创建一个新接口需要：

- 扩展 `Interface` 类
- 设置 `connection_type` 类属性
- 实现 `get_devices` 方法，该方法应返回一个包含每个检测到设备数据的字典。该数据将作为参数传递给驱动程序的构造函数和 `supported` 方法。

.. note::
    设置 `_loop_delay` 属性将修改调用 `get_devices` 的间隔。默认情况下，该间隔设置为 3 秒。

.. code-block:: python

    from odoo.addons.hw_drivers.interface import Interface

    class InterfaceName(Interface):
        connection_type = 'ConnectionType'

        def get_devices(self):
            return {
                'device_identifier_1': {...},
                ...
            }

驱动程序
------

接口检索到检测到的设备列表后，它将遍历所有具有相同 `connection_type` 属性的驱动程序，并在所有检测到的设备上测试其各自的 `supported` 方法。如果某个驱动程序的 `supported` 方法返回 `True`，则将为相应的设备创建该驱动程序的实例。

.. note::
    驱动程序的 `supported` 方法具有优先顺序。子类的 `supported` 方法始终在其父类之前进行测试。可以通过修改驱动程序的 `priority` 属性来调整此优先级。

创建新驱动程序需要：

- 扩展 `Driver`
- 设置 `connection_type` 类属性。
- 设置 `device_type`、`device_connection` 和 `device_name` 属性。
- 定义 `supported` 方法

.. code-block:: python

    from odoo.addons.hw_drivers.driver import Driver

    class DriverName(Driver):
        connection_type = 'ConnectionType'

        def __init__(self, identifier, device):
            super(NewDriver, self).__init__(identifier, device)
            self.device_type = 'DeviceType'
            self.device_connection = 'DeviceConnection'
            self.device_name = 'DeviceName'

        @classmethod
        def supported(cls, device):
            ...

与设备通信
========================

当您的新设备被检测到并出现在 IoT 模块中后，下一步就是与之通信。由于 Box 只有本地 IP 地址，因此只能从同一局域网中访问它。因此，通信需要在浏览器端使用 JavaScript 进行。

该过程取决于通信的方向：
- 从浏览器到 Box，通过 `Actions`_
- 从 Box 到浏览器，通过 `Longpolling`_

两种通道都可以通过同一个 JS 对象 `DeviceProxy` 访问，该对象通过 IoT Box 的 IP 和设备标识符实例化。

.. code-block:: javascript

    var DeviceProxy = require('iot.DeviceProxy');

    var iot_device = new DeviceProxy({
        iot_ip: iot_ip,
        identifier: device_identifier
    });

操作
-------

操作用于指示选定的设备执行特定操作，例如拍照、打印收据等。

.. note::
    请注意，Box 在此路径上不会发送任何“答案”，只会返回请求状态。操作的答案（如果有）必须通过长轮询（longpolling）获取。

可以在 DeviceProxy 对象上执行操作。

.. code-block:: javascript

    iot_device.action(data);

在您的驱动程序中，定义一个 `action` 方法，当从 Odoo 模块调用时执行。它将调用时传递的数据作为参数。

.. code-block:: python

    def action(self, data):
        ...

长轮询
-----------

当 Odoo 中的任何模块想要从特定设备读取数据时，它会创建一个由 Box 的 IP/域名和设备标识符标识的监听器，并传递一个回调函数，每当设备状态发生变化时调用该回调。回调将以新数据作为参数调用。

.. code-block:: javascript

    iot_device.add_listener(this._onValueChange.bind(this));

    _onValueChange: function (result) {
        ...
    }

在驱动程序中，通过调用 `event_manager` 的 `device_changed` 函数释放事件。然后，设置在监听器上的所有回调将以 `self.data` 作为参数被调用。

.. code-block:: python

    from odoo.addons.hw_drivers.event_manager import event_manager

    class DriverName(Driver):
        connection_type = 'ConnectionType'

        def methodName(self):
            self.data = {
                'value': 0.5,
                ...
            }
            event_manager.device_changed(self)
