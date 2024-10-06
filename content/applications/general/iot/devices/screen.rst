================
连接屏幕
================

在 Odoo 中，可以将 :abbr:`IoT (物联网)` 盒子连接到显示屏。配置完成后，屏幕可以用于向客户显示销售点 (PoS) 订单。

.. figure:: screen/screen-pos-client-display.png

   销售点 (PoS) 订单在屏幕显示上的示例。

通过访问 :abbr:`IoT (物联网)` 盒子主页并点击 :guilabel:`PoS 显示` 按钮进入客户显示屏。要进入 :abbr:`IoT (物联网)` 盒子主页，请导航到 :menuselection:`IoT 应用 --> IoT 盒子` 并点击 :abbr:`IoT (物联网)` 盒子主页链接。

连接
==========

将显示屏连接到 :abbr:`IoT (物联网)` 盒子的方式取决于型号。

.. tabs::

   .. tab:: IoT 盒子型号 4

      使用侧面的微型 HDMI 电缆连接最多两个屏幕。如果连接了两个屏幕，它们可以显示不同的内容（请参阅 :ref:`屏幕使用 <iot/usage_screen>`）。

   .. tab:: IoT 盒子型号 3

      使用侧面的 HDMI 电缆连接屏幕。

.. seealso::
   :ref:`参见 Raspberry Pi 连接图 <iot/connect_schema>`。

.. important::
   屏幕应在 :abbr:`IoT (物联网)` 盒子开启之前连接。如果盒子已经开启，连接屏幕后需要重新启动 :abbr:`IoT (物联网)` 盒子，方法是断开电源十秒钟，然后重新连接电源。

.. warning::
   使用 HDMI/微型 HDMI 适配器可能会导致问题，导致屏幕显示为空白或黑屏。建议使用特定的连接电缆。

如果连接成功，屏幕将显示 :guilabel:`PoS 客户显示` 屏幕。

.. image:: screen/screen-pos-client-display-no-order.png
   :align: center
   :alt: 成功连接到 IoT 盒子时显示的默认 "PoS 客户显示" 屏幕。

屏幕还会出现在 :abbr:`IoT (物联网)` 盒子主页的 :guilabel:`显示器` 列表中。或者，可以通过访问 :menuselection:`IoT 应用 --> 设备` 查看显示器。

.. image:: screen/screen-screen-name-example.png
   :align: center
   :alt: IoT 盒子主页上显示的屏幕显示名称示例。

.. note::
   如果未检测到屏幕，则会显示一个名为 :guilabel:`远程显示` 的默认显示，这表明没有连接任何硬件屏幕。

    .. image:: screen/screen-no-screen.png
       :align: center
       :alt: 如果未检测到屏幕，则使用 "远程显示" 屏幕名称。

.. _iot/usage_screen:

使用
=====

向客户展示销售点订单
--------------------------------------

要在 *销售点应用* 中使用屏幕，请导航到 :menuselection:`销售点 --> 配置 --> 销售点`，选择一个 :abbr:`PoS (销售点)`，如有需要，点击 :guilabel:`编辑` 并启用 :guilabel:`IoT 盒子` 功能。

接下来，从 :guilabel:`客户显示` 下拉菜单中选择屏幕。然后点击 :guilabel:`保存`，如有需要。

.. image:: screen/screen-pos-screen-config.png
   :align: center
   :alt: 将屏幕显示连接到销售点应用。

现在，该屏幕可以在所有 :abbr:`PoS (销售点)` 会话中使用。屏幕顶部菜单上将出现一个图标，显示屏幕的连接状态。

.. image:: screen/screen-pos-icon.png
   :align: center
   :alt: 销售点显示上的 "屏幕" 图标显示与屏幕的连接状态。

屏幕将自动显示 :abbr:`PoS (销售点)` 订单，并在订单更改时更新。

.. image:: screen/screen-pos-client-display.png
   :align: center
   :alt: 销售点订单在屏幕显示上的示例。

在屏幕上显示网站
-------------------------------

通过访问 :menuselection:`IoT 应用 --> 设备 --> 客户显示` 打开屏幕表单视图。此功能允许用户选择特定的网址并在屏幕上显示，使用 :guilabel:`显示 URL` 字段进行设置。
