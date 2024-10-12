====================
账单控制政策
====================

.. _purchase/manage_deals/control-bills:

.. |PO| replace:: :abbr:`PO (采购订单)`
.. |POs| replace:: :abbr:`POs (采购订单)`

在Odoo的*采购*应用中，*账单控制*政策决定了供应商在每个采购订单 (PO) 上计费的数量，基于订购数量或接收数量。

在*采购*应用设置中选择的政策将作为默认值应用，并适用于创建的任何新产品。

配置
=============

要配置*账单控制*政策，导航到 :menuselection:`采购应用 --> 配置 --> 设置`，然后向下滚动到 :guilabel:`发票` 部分。在 :guilabel:`账单控制` 下，选择 :guilabel:`订购数量` 或 :guilabel:`接收数量`。然后，点击 :guilabel:`保存`。

.. image:: control_bills/control-bills-selected-policy.png
   :align: center
   :alt: 采购应用设置中选定的账单控制政策。

- :guilabel:`订购数量`: 一旦确认采购订单 (PO)，便创建供应商账单。PO中的产品和数量用于生成草稿账单。
- :guilabel:`接收数量`: 仅在收到部分或全部订单后才生成账单。接收的产品和数量用于生成草稿账单。如果在未接收到任何物品时尝试创建供应商账单，则会出现错误消息。

  .. image:: control_bills/control-bills-error-message-popup.png
     :align: center
     :alt: 账单控制政策草稿账单错误消息。

.. note::
   如果某个特定产品的账单控制政策应与*采购*应用设置中的政策不同，可以从其产品表单中更改此产品的 :guilabel:`账单控制` 政策。

   要执行此操作，请导航到 :menuselection:`采购应用 --> 产品 --> 产品`，然后选择一个产品。从产品表单中，点击 :guilabel:`采购` 标签。在 :guilabel:`供应商账单` 部分下，修改 :guilabel:`控制政策` 字段中的选择。

三方匹配
==============

*三方匹配*功能确保只有在收到部分（或全部）采购订单 (PO) 中包含的产品后，供应商账单才会支付。

要激活*三方匹配*，请导航到 :menuselection:`采购应用 --> 配置 --> 设置`，然后向下滚动到 :guilabel:`发票` 部分。然后，勾选 :guilabel:`三方匹配` 复选框以启用该功能，并点击 :guilabel:`保存`。

.. image:: control_bills/control-bills-three-way-matching.png
   :align: center
   :alt: 在采购应用设置中启用三方匹配功能。

.. important::
   *三方匹配*功能**仅**在账单控制政策设置为 :guilabel:`接收数量` 时有效。

使用三方匹配支付供应商账单
------------------------------------

启用*三方匹配*后，供应商账单将在 :guilabel:`其他信息` 标签下显示一个 :guilabel:`应支付` 字段。创建新的供应商账单时，该字段默认设置为 :guilabel:`是`，因为在收到PO中包含的至少一些产品之前**不能**创建账单。

要从PO创建供应商账单，导航到 :menuselection:`采购应用 --> 订单 --> 采购订单`。在 :guilabel:`采购订单` 页面中，从列表中选择所需的PO。然后，点击 :guilabel:`创建账单`。这样会打开一个新的草稿 :guilabel:`供应商账单` 表单，处于 :guilabel:`草稿` 阶段。点击 :guilabel:`其他信息` 标签，找到 :guilabel:`应支付` 字段。

.. important::
   从列表中选择的PO**必须尚未**开具账单，否则会出现 :guilabel:`无效操作` 弹出窗口。这种情况发生在PO的 :guilabel:`计费状态` 为 :guilabel:`完全开账单` 时，并且账单控制政策设置为 :guilabel:`接收数量`。

   .. image:: control_bills/control-bills-invalid-operation.png
      :align: center
      :alt: 对已开具账单的采购订单操作无效的弹出窗口。

点击 :guilabel:`应支付` 字段旁边的下拉菜单查看可用选项：:guilabel:`是`，:guilabel:`否` 和 :guilabel:`例外`。

.. image:: control_bills/control-bills-should-be-paid.png
   :align: center
   :alt: 草稿供应商账单上的应支付字段状态。

.. note::
   如果未收到PO的全部产品，Odoo只会在草稿账单中包含已收到的产品。

草稿供应商账单可以编辑以增加账单数量、更改账单中产品的价格或添加其他产品。

如果更改了草稿账单的信息，:guilabel:`应支付` 字段状态将设置为 :guilabel:`例外`。这意味着Odoo注意到不一致，但不会阻止更改或显示错误消息，因为更改草稿账单可能有合理的理由。

要处理供应商账单，请在 :guilabel:`账单日期` 字段中选择一个日期，然后点击 :guilabel:`确认`，接着点击 :guilabel:`登记付款`。

这会打开一个 :guilabel:`登记付款` 弹出窗口。在此窗口中，基于数据库的会计设置预填充会计信息。点击 :guilabel:`创建付款` 处理供应商账单。

一旦供应商账单的付款已登记，账单显示绿色 :guilabel:`已支付` 标志，:guilabel:`应支付` 字段状态会被设置为 :guilabel:`否`。

.. tip::
   Odoo会自动设置账单上的 :guilabel:`应支付` 状态。但是，可以通过点击 :guilabel:`其他信息` 标签内的字段下拉菜单手动更改状态。

查看采购订单的计费状态
======================================

一旦确认了PO，可以在PO表单的 :guilabel:`其他信息` 标签下查看其 :guilabel:`计费状态`。

要查看PO的 :guilabel:`计费状态`，请导航到 :menuselection:`采购应用 --> 订单 --> 采购订单`，然后选择要查看的PO。

点击 :guilabel:`其他信息` 标签，并找到 :guilabel:`计费状态` 字段。

.. image:: control_bills/control-bills-billing-status.png
   :align: center
   :alt: 采购订单表单上的计费状态字段。

下表详细说明了 :guilabel:`计费状态` 字段的不同值，以及根据所使用的账单控制政策何时显示这些值。

.. list-table::
   :header-rows: 1
   :stub-columns: 1

   * - 计费状态
     - 基于接收数量
     - 基于订购数量
   * - 无需开账单
     - PO确认；未收到任何产品
     - *不适用*
   * - 等待账单
     - 全部/部分产品已收到；未创建账单
     - PO确认
   * - 完全开账单
     - 全部/部分产品已收到；草稿账单已创建
     - 草稿账单已创建

.. seealso::
   :doc:`manage`
