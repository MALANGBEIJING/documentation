=================
连接打印机
=================

.. |iot| replace:: :abbr:`IoT (物联网)`
.. |usb| replace:: :abbr:`USB (通用串行总线)`

打印机的安装可以通过几个简单的步骤完成。打印机可以用于打印收据、标签、订单，甚至 Odoo 应用中的报告。此外，打印操作还可以在制造过程中作为 *触发器上的操作* 进行分配，或者添加到质量控制点或质量检查中。

.. warning::
   将打印机直接连接到 Odoo 数据库的**唯一**方法是通过使用 |iot| 盒子。

   如果没有 |iot| 盒子，打印仍然可以进行，但它由打印机本身管理，这不是推荐的流程。

连接
==========

|iot| 盒子支持通过 |usb|、网络连接或蓝牙连接的打印机。`支持的打印机 <https://www.odoo.com/page/iot-hardware>`__ 会自动检测到，并显示在 *IoT* 应用的 :guilabel:`设备` 列表中。

.. image:: printer/printer-detected.png
   :align: center
   :alt: 打印机在 IoT 应用的设备列表中显示。

.. note::
   打印机可能需要最多两分钟的时间才能显示在 *IoT* 应用的 :guilabel:`设备` 列表中。

链接打印机
============

将打印机链接到工作订单
---------------------------

*工作订单* 可以通过质量控制点链接到打印机，以打印制造产品的标签。

在 *质量应用* 中，可以在质量控制点上设置设备。为此，导航到 :menuselection:`质量应用 --> 质量控制 --> 控制点`，并打开要链接打印机的控制点。

.. important::
   必须将 *制造操作* 和 *工作订单操作* 附加到质量控制点，才能在 :guilabel:`类型` 字段中选择 :guilabel:`打印标签` 选项。

在这里，选择 :guilabel:`类型` 字段，并从下拉菜单中选择 :guilabel:`打印标签`。这样会显示一个名为 :guilabel:`设备` 的字段，在此处可以选择已连接的设备。如有需要，点击 :guilabel:`保存` 进行更改。

.. image:: printer/printer-controlpoint.png
   :align: center
   :alt: 这是质量控制点的设置。

现在可以在选定的质量控制点上使用打印机。当制造过程到达质量控制点时，数据库会提供为特定产品打印标签的选项。

.. image:: printer/printer-prompt.png
   :align: center

.. tip::
   质量控制点也可以通过导航到 :menuselection:`IoT 应用 --> 设备` 进行访问，然后选择设备。在 :guilabel:`质量控制点` 选项卡中，可以为设备添加质量控制点。

.. note::
   在质量检查详细表单中，检查的 :guilabel:`类型` 也可以指定为 :guilabel:`打印标签`。要创建 :doc:`新质量检查 <../../../inventory_and_mrp/quality/quality_management/quality_checks>`，导航到 :menuselection:`质量应用 --> 质量控制 --> 质量检查 --> 新建`。

.. seealso::
   - :doc:`../../../inventory_and_mrp/quality/quality_management/quality_control_points`
   - :doc:`../../../inventory_and_mrp/quality/quality_management/quality_alerts`

.. _iot/link-printer:

将打印机链接到报告
-----------------------

可以将报告类型链接到特定的打印机。在 *IoT* 应用中，进入 :guilabel:`设备` 菜单，并选择要配置的打印机。

在这里，进入 :guilabel:`打印机报告` 选项卡，点击 :guilabel:`添加行`。在出现的窗口中，勾选所有应链接到此打印机的 :guilabel:`报告` 类型。

.. image:: printer/printer-reports.png
   :align: center
   :alt: 在 IoT 应用中分配给打印机的报告列表。

.. tip::
   报告还可以在 *设置* 应用的 *技术* 菜单中进行配置，前提是处于 :ref:`调试模式 <developer-mode>`。为此，导航到 :menuselection:`设置应用 --> 技术 --> 操作：报告`。从列表中选择所需的报告。然后可以在报告上设置 :guilabel:`IoT 设备`。

首次选择要打印的链接报告时，会出现一个 :guilabel:`选择打印机` 弹出窗口。勾选正确的报告打印机旁边的复选框，然后点击 :guilabel:`打印`。此时，报告已链接到打印机。

清除设备打印机缓存
~~~~~~~~~~~~~~~~~~~~~~~~~~

将打印机链接到报告后，该设置会保存在浏览器的缓存中。这意味着用户可以为不同报告在不同设备上保存不同的设备设置，具体取决于他们使用的设备访问 Odoo 的方式。这也意味着，不同用户可以根据自己的偏好，自动从不同的打印机打印报告。

要将报告与打印机取消链接，请导航到 :menuselection:`IoT 应用 --> 清除已选设备`。这会生成一个与当前设备上的打印机链接的报告列表。点击每个报告旁的 :guilabel:`取消链接` 按钮以删除链接。

.. important::
   此步骤**仅**阻止报告从当前浏览器自动打印到列出的打印机。报告仍然在设备上 :ref:`链接 <iot/link-printer>`，位于 :guilabel:`打印机报告` 选项卡中。

.. image:: printer/clear-reports.png
   :align: center
   :alt: 在 IoT 应用中当前链接到打印机的报告列表。

.. seealso::
   :doc:`POS 订单打印 <../../../sales/point_of_sale/restaurant/kitchen_printing>`
