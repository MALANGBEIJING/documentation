==========================
临时补货规则
==========================

某些企业要求特定产品在任何时候都保持最低的库存数量。为了避免库存低于某个阈值，企业可以在Odoo中创建*补货规则*，以自动为特定产品生成采购订单。

补货规则可保持预测的库存水平在某个阈值之上，同时不会超过指定的上限或最大数量。当带有补货规则的产品低于指定数量时，Odoo会使用指定的*路径*（例如*购买*或*制造*）生成订单以补充库存。

在某些情况下，企业可能会选择*临时补货规则*，当他们不希望特定产品自动补货时使用。

在Odoo中，当产品满足以下条件时，会在补货仪表板中创建“临时”补货规则：

#. 产品配置了*购买*路径
#. 未配置补货规则
#. 库存数量为`0`
#. 包含在销售订单 (SO) 中

该规则将在为该产品生成的采购订单（PO）确认后被删除。

.. seealso::
   - :doc:`../../inventory/warehouses_storage/replenishment/reordering_rules`
   - :doc:`../../purchase/products/reordering`

配置
=============

要配置在库存达到`0`时触发临时补货规则的产品，首先前往 :menuselection:`库存应用 --> 产品 --> 产品`，然后点击 :guilabel:`新建`。

.. note::
   也可以在现有产品上进行相同的配置，方法是前往 :menuselection:`库存应用 --> 产品 --> 产品`，并选择现有产品。

在产品表单上，输入产品名称，并确保启用了 :guilabel:`可销售` 和 :guilabel:`可采购` 选项，位于 :guilabel:`产品名称` 字段下方。

然后，在 :guilabel:`常规信息` 选项卡中将 :guilabel:`产品类型` 设置为 `可存储产品`。

接下来，点击 :guilabel:`采购` 选项卡，在 :guilabel:`供应商` 下，点击 :guilabel:`添加一行`，从下拉菜单中选择供应商。然后，在 :guilabel:`价格` 下设置采购价格。

.. important::
   必须为产品设置供应商才能使临时补货规则生效。虽然可以自动生成采购订单，但尝试从 *库存* 应用的 :guilabel:`补货` 仪表板中补充产品库存时，若未在产品表单中设置供应商，会触发警告。

   .. image:: temporary_reordering/temporary-reordering-warning-popup.png
      :align: center
      :alt: 点击补充未设置供应商的产品时弹出的警告提示。

在为产品创建 :abbr:`SO (销售订单)` 之前，请确保产品表单上的 :guilabel:`现有库存` 智能按钮显示为 `0.00 单位`。然后，确保 :guilabel:`补货规则` 智能按钮显示为 `0`，表示没有应用于该产品的规则。

.. image:: temporary_reordering/temporary-reordering-smart-buttons.png
   :align: center
   :alt: 显示补货规则和现有库存按钮的产品表单智能按钮行。

触发临时补货规则
=================================

要触发临时补货规则，首先通过导航到 :menuselection:`销售应用 --> 新建` 创建一个新销售订单。

然后，在 :guilabel:`客户` 字段中添加一个客户，并在 :guilabel:`订单行` 选项卡中的 :guilabel:`产品` 列下点击 :guilabel:`添加产品`。接下来，从下拉菜单中选择所需产品。最后，:guilabel:`确认` 该 :abbr:`SO (销售订单)`。

.. image:: temporary_reordering/temporary-reordering-sales-order.png
   :align: center
   :alt: 没有设置补货规则的产品的销售订单。

查看补货报告
==========================

要查看为销售订单中缺货产品创建的临时补货规则，请导航到 :menuselection:`库存应用 --> 操作 --> 补货`。这样将打开 :guilabel:`补货` 仪表板。

在该仪表板中，找到为其创建了临时补货规则的产品。在其产品行中，可以看到其 :guilabel:`现有库存` 数量、负的 :guilabel:`预测` 数量、*购买* :guilabel:`路径` 以及 :guilabel:`订购数量` 用于补充库存。

此外，在行的最右侧还可以看到两个补货选项：:guilabel:`一次性订购` 和 :guilabel:`自动化`。

.. image:: temporary_reordering/temporary-reordering-replenishment-dashboard.png
   :align: center
   :alt: 显示临时补货规则和选项的补货报告。

要使用一次性的临时补货规则，点击 :guilabel:`一次性订购`。此操作将在右上角触发一个确认弹出窗口，显示 :guilabel:`已生成以下补货订单`，并附有一个新的采购订单编号。

.. tip::
   点击 :guilabel:`一次性订购` 后生成采购订单后，刷新页面。该产品的临时补货规则将不再出现在 :guilabel:`补货` 仪表板中。

完成采购订单
=======================

要查看从 :guilabel:`补货` 仪表板生成的采购订单，请导航到 :menuselection:`采购应用`，然后从 :guilabel:`请求报价` 概览中选择生成的 :abbr:`PO (采购订单)`。

从这里，点击 :guilabel:`确认订单`，然后点击 :guilabel:`接收产品`。最后，点击 :guilabel:`验证` 以完成采购订单。

.. image:: temporary_reordering/temporary-reordering-purchase-order.png
   :align: center
   :alt: 使用临时补货规则订购的产品的采购订单。

现在，原始销售订单可以交付并开具发票。

.. note::
   一旦 :abbr:`SO (销售订单)` 交付并开具发票，请确保产品表单上没有补货规则。

   前往 :menuselection:`库存应用 --> 产品 --> 产品`，选择该产品，并确认 :guilabel:`补货规则` 智能按钮显示为 `0`。
