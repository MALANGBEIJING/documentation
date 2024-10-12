================
招标邀请
================

.. _purchase/manage_deals/alternative-rfqs:

.. |PO| replace:: :abbr:`PO (采购订单)`
.. |POs| replace:: :abbr:`POs (采购订单)`
.. |RfQ| replace:: :abbr:`RfQ (报价请求)`
.. |RfQs| replace:: :abbr:`RfQs (报价请求)`

有时，公司可能希望邀请供应商同时提交类似商品或服务的报价。这有助于公司选择最便宜、最快速的供应商，以满足其特定的业务需求。

在Odoo中，可以为不同供应商创建替代报价请求 (RfQs)。一旦收到每个供应商的回复，就可以比较每个报价请求中的产品行，并决定从哪些供应商购买哪些产品。

.. note::
   这种过程有时被称为*招标邀请*，主要由公共部门组织使用，因其在进行采购时受到法律约束。然而，私营公司也可以使用替代 |RfQs| 来有效地花费资金。

配置
=============

要创建替代 |RfQs|，必须在 *采购* 应用设置中启用 *采购协议* 功能。要启用此功能，请导航到 :menuselection:`采购应用 --> 配置 --> 设置`。在 :guilabel:`订单` 部分下，勾选 :guilabel:`采购协议`。

然后，点击 :guilabel:`保存` 以应用更改。

.. image:: calls_for_tenders/calls-for-tenders-enabled-setting.png
   :align: center
   :alt: 在采购应用设置中启用采购协议。

.. _purchase/manage_deals/create-rfq:

创建一个 |RfQ|
===============

要创建一个新的 |RfQ|，请按照 :doc:`rfq` 文档中的说明操作。

.. seealso::
   `Odoo 教程：采购基础和您的第一个报价请求 <https://www.youtube.com/watch?v=o_uI718P1Dc>`_

.. _purchase/manage_deals/create-alternatives:

创建替代 |RfQs|
=========================

一旦创建并发送了 |PO| 给供应商，可以为其他供应商创建替代的 |RfQs|，以比较价格、交货时间和其他因素，从而做出订单决策。

要从原始 |PO| 创建替代 |RfQs|，点击 :guilabel:`替代` 标签。然后，点击 :guilabel:`创建替代`。点击后，会出现一个 :guilabel:`创建替代` 弹出窗口。

.. image:: calls_for_tenders/calls-for-tenders-create-alternative.png
   :align: center
   :alt: 创建替代报价请求的弹出窗口。

在此窗口中，从 :guilabel:`供应商` 字段旁的下拉菜单中选择一个替代供应商，将该替代报价请求分配给该供应商。

在此旁边，有一个默认选中的 :guilabel:`复制产品` 复选框。选中时，原始 |RfQ| 的产品数量将复制到替代请求中。对于第一个替代报价请求，保持复选框选中。完成后，点击 :guilabel:`创建替代`。这将打开一个新的 |RfQ| 表单。

由于保留了 :guilabel:`复制产品` 复选框，新的表单已经预填充了与之前的原始 |RfQ| 相同的产品、数量和其他详细信息。

.. note::
   当创建替代报价请求时，选中 :guilabel:`复制产品` 复选框时，不需要添加额外的产品，除非有需要。

   但是，如果选择的供应商在订单中某个特定产品的表单下列出，则产品表单上设置的值将会传递到 |RfQ| 中，并且如果需要，必须手动更改。

准备就绪后，通过点击 :guilabel:`替代` 标签，创建第二个替代报价请求，然后点击 :guilabel:`创建替代`。

这会打开 :guilabel:`创建替代` 弹出窗口。再次从 :guilabel:`供应商` 旁边的下拉菜单中选择一个不同的供应商。然而，对于这个特定的 |RfQ|，*取消选中* :guilabel:`复制产品` 复选框。这样会移除新替代 |RfQ| 中的所有产品，使其为空白。可以根据需要添加特定供应商应订购的产品。

准备好后，点击 :guilabel:`创建替代`。

.. tip::
   如果需要删除 :guilabel:`替代` 标签中的某个替代报价请求，可以通过点击行末的 :guilabel:`X (删除)` 图标单独删除。

这会创建第三个新的 |RfQ|。但由于没有复制原始 |RfQ| 的产品数量，因此产品行为空白，可以通过点击 :guilabel:`添加产品` 添加新产品，并从下拉菜单中选择所需的产品。

一旦添加了所需的特定产品，点击 :guilabel:`通过电子邮件发送`。

.. image:: calls_for_tenders/calls-for-tenders-blank-quotation.png
   :align: center
   :alt: 空白的替代报价请求，面包屑中显示替代项。

这会打开一个 :guilabel:`撰写邮件` 弹出窗口，可以在其中自定义发给供应商的消息，并根据需要添加附件。准备好后，点击 :guilabel:`发送`。

从这个最新的表单中，点击 :guilabel:`替代` 标签。在此标签下，所有三个 |RfQs| 都可以在 :guilabel:`引用` 列中看到。此外，供应商列在 :guilabel:`供应商` 列中，订单的 :guilabel:`总计`（以及 :guilabel:`状态`）也在各行中。

根据供应商和产品表单中预先配置的交货时间，:guilabel:`预计到货` 列中的日期会为每个供应商进行计算。

.. _purchase/manage_deals/link-rfq:

将新 |RfQ| 链接到现有报价请求
=====================================

即使报价请求不是直接从另一个 |RfQ| 的 :guilabel:`替代` 标签中创建的，也可以将其链接到现有的 |RfQs|。

要执行此操作，首先创建一个新的 |RfQ|。导航到 :menuselection:`采购应用 --> 新建`。根据 :ref:`以前的说明 <purchase/manage_deals/create-rfq>` 填写 |RfQ|。

准备就绪后，点击 :guilabel:`替代` 标签。由于这是单独创建的新 |RfQ|，因此尚未链接其他订单。

但是，要将此 |RfQ| 与现有替代项链接，点击 :guilabel:`供应商` 列第一行的 :guilabel:`链接到现有报价请求`。

.. image:: calls_for_tenders/calls-for-tenders-link-rfq-popup.png
   :align: center
   :alt: 将新报价请求链接到现有报价请求的弹出窗口。

这会打开一个 :guilabel:`添加：替代采购订单` 弹出窗口。选择所需的之前创建的 |RfQs|，然后点击 :guilabel:`选择`。所有这些订单现在都被复制到此 |RfQ| 中，可以在 :guilabel:`替代` 标签下找到。

.. tip::
   如果处理的大量 |POs| 无法找到之前的 |POs|，可以点击弹出窗口顶部搜索栏右侧的 :icon:`fa-chevron-down` :guilabel:`(下拉)` 图标。

   然后，在 :guilabel:`按组` 部分下，点击 :guilabel:`供应商`。供应商会显示在自己的嵌套下拉列表中，每个供应商的列表可以展开以查看该供应商的开放 |POs|。

.. _purchase/manage_deals/compare-product-lines:

比较产品行
=====================

可以并排比较替代的 |RfQs|，以确定哪些供应商在订单中提供了最佳的产品交易。

要比较替代的 |RfQs|，导航到 :menuselection:`采购` 应用，并选择之前创建的 |RfQs| 之一。

然后，点击 :guilabel:`替代` 标签以查看所有链接的 |RfQs|。接着，在 :guilabel:`创建替代` 选项下，点击 :guilabel:`比较产品行`。这会导航到 :guilabel:`比较订单行` 页面。

.. image:: calls_for_tenders/calls-for-tenders-compare-products.png
   :align: center
   :alt: 比较替代报价请求的产品行页面。

默认情况下，:guilabel:`比较订单行` 页面按 :guilabel:`产品` 分组。任何 |RfQs| 中包含的每个产品都会显示在其自己的嵌套下拉列表中，并在 :guilabel:`引用` 列中显示所有 |PO| 号。

.. note::
   要从 :guilabel:`比较订单行` 页面中移除产品行，请点击该产品行行末的 :guilabel:`清除`。

   这样会将该特定产品从页面中移除为可选择的选项，并将页面上该产品的 :guilabel:`总计` 价格更改为 `0`。

   此外，在包含该产品的 |RfQ| 表单上，其订购数量也更改为 `0`。

一旦确定了最佳报价，单击每行末的 :guilabel:`选择` 按钮，选择个别产品。

一旦选择了所有所需的产品，点击页面顶部面包屑中的 :guilabel:`报价请求`，以导航回所有 |RfQs| 的概览。

.. _purchase/manage_deals/cancel-keep-alternatives:

取消（或保留）替代品
=============================

一旦从 :guilabel:`比较订单行` 页面选择了所需的产品，没有选择产品的剩余 |RfQs| 可以取消。

没有选择的产品的 :guilabel:`总计` 列中的成本会自动设置为 `0`，显示在每行末尾。

尽管尚未取消，但这表明一旦这些订单得到确认，就可以取消其中没有选择产品的订单，而不会对其他有效订单产生影响。

.. image:: calls_for_tenders/calls-for-tenders-zero-total.png
   :align: center
   :alt: 在采购应用概览中取消的报价请求。

要确认选择了产品的 |RfQ|，点击进入一个 |RfQ|，然后点击 :guilabel:`确认订单`。

这会弹出一个 :guilabel:`替代报价请求如何处理？` 弹出窗口。

要查看列出的某个 |RfQ| 的详细表单，请点击该报价请求的行项。这样会打开一个 :guilabel:`打开：替代采购订单` 弹出窗口，从中可以查看该特定 |RfQ| 的所有详细信息。

准备就绪后，点击 :guilabel:`关闭` 关闭弹出窗口。

在 :guilabel:`替代报价请求如何处理？` 弹出窗口中，提供了两个选项：:guilabel:`取消替代` 和 :guilabel:`保留替代`。

如果此 |PO| 不应确认，请点击 :guilabel:`放弃`。

选择 :guilabel:`取消替代` 会自动取消替代的 |RfQs|。选择 :guilabel:`保留替代` 会保留替代的 |RfQs| 以便以后需要订购额外的产品数量时仍然可以访问它们。

一旦所有产品都已订购，选择 :guilabel:`取消替代` 以取消当时打开的任何 |PO|。

.. image:: calls_for_tenders/calls-for-tenders-keep-or-cancel.png
   :align: center
   :alt: 替代报价请求的保留或取消弹出窗口。

最后，使用页面顶部的面包屑，点击 :guilabel:`报价请求` 以导航回所有 |RfQs| 的概览。

已取消的订单可以看到，灰显并列在其各自行的最右侧 :guilabel:`状态` 列中的 :guilabel:`已取消` 状态下。

现在所有产品数量都已订购，采购过程可以完成，产品可以收到仓库中。

.. seealso::
   :doc:`blanket_orders`
