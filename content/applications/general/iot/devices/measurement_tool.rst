==========================
连接测量工具
==========================

.. _iot/devices/measurement-tool:

使用 Odoo 的 :abbr:`IoT (物联网)` 盒子，可以将测量工具连接到 Odoo 数据库，并在 *质量应用* 中用于质量控制点/质量检查，或在制造过程中用于工作中心。

在此处查看支持的设备列表：`支持的设备 <https://www.odoo.com/page/iot-hardware>`_。

通过通用串行总线（USB）连接
=======================================

要添加通过 :abbr:`USB (通用串行总线)` 连接的设备，请将 :abbr:`USB (通用串行总线)` 电缆插入 :abbr:`IoT (物联网)` 盒子，设备将显示在 Odoo 数据库中。

.. image:: measurement_tool/device-dropdown.png
   :align: center
   :alt: 在 IoT 盒子上识别到的测量工具。

通过蓝牙连接
======================

激活设备的蓝牙功能（请参阅设备手册了解更多说明），:abbr:`IoT (物联网)` 盒子将自动连接到设备。

.. image:: measurement_tool/measurement-tool.jpeg
   :align: center
   :alt: 测量工具上的蓝牙指示灯。

将测量工具链接到制造过程中的质量控制点
===============================================================================

在 *质量应用* 中，可以在质量控制点上设置设备。为此，导航到 :menuselection:`质量应用 --> 质量控制 --> 控制点`，并打开要链接测量工具的控制点。

在这里，选择 :guilabel:`类型` 字段，并从下拉菜单中点击 :guilabel:`测量`。这样会显示一个名为 :guilabel:`设备` 的字段，可以在此处选择已连接的设备。

另外，您还可以配置 :guilabel:`标准` 和 :guilabel:`公差`。如有需要，点击 :guilabel:`保存` 进行更改。

此时，测量工具已链接到所选的质量控制点。通常需要手动更改的值会在使用工具时自动更新。

.. image:: measurement_tool/measurement-control-point.png
   :align: center
   :alt: Odoo 数据库中的测量工具输入。

.. tip::
   质量控制点也可以通过导航到 :menuselection:`IoT 应用 --> 设备` 进行访问，然后选择设备。在 :guilabel:`质量控制点` 选项卡中，可以添加与设备相关的控制点。

.. note::
   在质量检查详细表单中，检查的 :guilabel:`类型` 也可以指定为 :guilabel:`测量`。导航到 :menuselection:`质量应用 --> 质量控制 --> 质量检查 --> 新建` 以创建新的质量检查详细页面。

.. seealso::
   - :doc:`../../../inventory_and_mrp/quality/quality_management/quality_control_points`
   - :doc:`../../../inventory_and_mrp/quality/quality_management/quality_alerts`

在制造应用中将测量工具链接到工作中心
=================================================================

要将测量工具链接到操作，首先需要在工作中心进行配置。为此，导航到 :menuselection:`制造应用 --> 配置 --> 工作中心`，然后选择将使用测量工具的工作中心。

在工作中心页面中，通过点击 :guilabel:`添加行`，在 :guilabel:`IoT 触发器` 选项卡的 :guilabel:`设备` 列中添加设备。然后，可以将测量工具链接到 :guilabel:`操作` 下拉菜单中的 :guilabel:`进行测量` 选项。可以添加按键来触发该操作。

.. important::
   应注意的是，首先列出的触发器将优先选择。因此，顺序很重要，触发器可以拖动到任何顺序。

.. note::
   在 :guilabel:`工作订单` 屏幕上，状态图形指示数据库是否正确连接到测量工具。

.. seealso::
   :ref:`workcenter_iot`
