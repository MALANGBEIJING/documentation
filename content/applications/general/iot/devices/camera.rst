================
连接摄像头
================

可以通过几步简单操作将摄像头连接到 Odoo 数据库中的 :abbr:`IoT (物联网)` 盒子。一旦摄像头连接到 :abbr:`IoT (物联网)` 盒子，它可以在制造过程中使用，或者可以链接到质量控制点/质量检查。这样可以在到达选定的质量控制点/检查时，或者在制造过程中按下特定按键时拍摄照片。

连接
==========

要将摄像头连接到 :abbr:`IoT (物联网)` 盒子，只需通过电缆连接二者。通常使用某种类型的 :abbr:`USB (通用串行总线)` 电缆来完成连接。

如果摄像头是 `支持的设备 <https://www.odoo.com/page/iot-hardware>`_，则无需进行任何设置，连接后将立即被检测到。

.. image:: camera/camera-dropdown.png
   :align: center
   :alt: IoT 盒子上识别到的摄像头。

在制造过程中将摄像头链接到质量控制点
=============================================================

在 :menuselection:`质量` 应用中，可以在 :guilabel:`质量控制点` 上设置设备。为此，导航到 :menuselection:`质量应用 --> 质量控制 --> 控制点` 并打开要与摄像头连接的 :guilabel:`控制点`。

在控制点表单中，选择 :guilabel:`类型` 字段并从下拉菜单中点击 :guilabel:`拍照`。这样会显示一个名为 :guilabel:`设备` 的字段，在此处可以选择已连接的 *设备*。如有需要，点击 :guilabel:`保存` 进行更改。

.. image:: camera/control-point-device.png
   :align: center
   :alt: 在质量控制点设置设备。

摄像头现在可以与选定的质量控制点一起使用。当在制造过程中到达质量控制点时，数据库会提示操作员拍照。

.. image:: camera/serial-number-picture.png
   :align: center
   :alt: 质量控制点上设备的图形用户界面。

.. note::
   质量控制点也可以通过导航到 :menuselection:`IoT 应用 --> 设备` 进行访问。在这里，选择设备。可以在 :guilabel:`质量控制点` 选项卡中添加与设备相关联的控制点。

.. tip::
   在质量检查表单中，检查的 :guilabel:`类型` 也可以指定为 :guilabel:`拍照`。导航到 :menuselection:`质量应用 --> 质量控制 --> 质量检查 --> 新建` 以从 :guilabel:`质量检查` 页面创建新的质量检查。

.. seealso::
   - :doc:`/applications/inventory_and_mrp/quality/quality_management/quality_control_points`
   - :doc:`/applications/inventory_and_mrp/quality/quality_management/quality_alerts`

在制造应用中将摄像头链接到工作中心
=====================================================

要将摄像头链接到操作，首先需要在工作中心进行配置。导航到 :menuselection:`制造应用 --> 配置 --> 工作中心`。接下来，进入将使用摄像头的 :guilabel:`工作中心`，以查看该特定工作中心的详细信息表单。在这里，通过点击 :guilabel:`添加行`，在 :guilabel:`IoT 触发器` 选项卡的 :guilabel:`设备` 列中添加设备。

现在，可以将摄像头设备链接到 :guilabel:`操作` 列下拉选项中标记为 :guilabel:`拍照` 的操作。还可以添加一个按键来触发该操作。

.. important::
   首先列出的触发器将首先选择。触发器的顺序很重要，可以根据需要拖动到任何顺序。

.. note::
   在 :guilabel:`工作订单` 屏幕上，状态图形指示数据库是否正确连接到摄像头。

.. seealso::
   :ref:`workcenter_iot`
