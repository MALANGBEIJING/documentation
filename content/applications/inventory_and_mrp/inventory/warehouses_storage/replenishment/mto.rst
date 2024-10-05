========================
按订单补货 (MTO)
========================

.. |SO| replace:: :abbr:`SO (销售订单)`
.. |SOs| replace:: :abbr:`SOs (销售订单)`
.. |MO| replace:: :abbr:`MO (生产订单)`
.. |PO| replace:: :abbr:`PO (采购订单)`
.. |MTO| replace:: :abbr:`MTO (按订单生产)`
.. |RFQ| replace:: :abbr:`RFQ (询价单)`
.. |BOM| replace:: :abbr:`BOM (物料清单)`

*按订单补货*，也称为 *MTO* (按订单生产)，是一种补货策略，每当需要满足销售订单 (SO) 或在生产订单 (MO) 中作为组件时，都会为产品创建一个草稿订单。

对于从供应商采购的产品，将创建询价单 (RFQ) 以补充产品，而对于制造的产品，则会创建 |MO|。无论所订购产品的当前库存水平如何，只要确认了需要该产品的 |SO| 或 |MO|，就会创建 |RFQ| 或 |MO|。

.. important::
   为了使用 |MTO| 路线，必须启用 :guilabel:`多步骤路线` 功能。为此，导航至 :menuselection:`库存应用 --> 配置 --> 设置`，并在 :guilabel:`仓库` 标题下勾选 :guilabel:`多步骤路线` 旁的复选框。

   最后，点击 :guilabel:`保存` 保存更改。

取消归档 MTO 路线
===================

默认情况下，Odoo 将 |MTO| 路线设置为 *归档*。这是因为 |MTO| 是一种较为小众的工作流程，只有某些公司会使用。然而，只需几个简单步骤即可轻松取消归档此路线。

为此，首先导航至 :menuselection:`库存应用 --> 配置 --> 路线`。在 :guilabel:`路线` 页面，点击搜索栏右侧的 :icon:`fa-caret-down` :guilabel:`(下箭头)` 图标，然后点击 :guilabel:`归档` 筛选器以启用它。

.. image:: mto/archived-filter.png
   :align: center
   :alt: 路线页面上的归档筛选器。

启用 :guilabel:`归档` 筛选器后，:guilabel:`路线` 页面将显示所有当前归档的路线。勾选 :guilabel:`按订单补货 (MTO)` 旁的复选框，然后点击 :icon:`fa-cog` :guilabel:`操作` 按钮以显示下拉菜单。从下拉菜单中选择 :guilabel:`取消归档`。

.. image:: mto/unarchive-button.png
   :align: center
   :alt: 路线页面上的取消归档操作。

最后，从搜索栏中移除 :guilabel:`归档` 筛选器。现在，:guilabel:`路线` 页面将显示所有未归档的路线，包括 :guilabel:`按订单补货 (MTO)`，可以在每个产品页面的 *库存* 标签中选择。

为 MTO 配置产品
=========================

取消归档 |MTO| 路线后，现在可以正确配置产品以使用按订单补货。为此，首先进入 :menuselection:`库存应用 --> 产品 --> 产品`，然后选择一个现有产品，或点击 :guilabel:`新建` 以配置一个新产品。

在产品页面，选择 :guilabel:`库存` 标签，并在 :guilabel:`路线` 部分启用 :guilabel:`按订单补货 (MTO)` 路线，以及 :guilabel:`采购` 或 :guilabel:`生产` 路线。

.. important::
   :guilabel:`按订单补货 (MTO)` 路线 **不会** 独立工作，必须选择另一条路线。这是因为 Odoo 需要知道在下订单时如何补充产品（是采购还是生产）。

.. image:: mto/select-routes.png
   :align: center
   :alt: 在库存标签中选择 MTO 路线和另一条路线。

如果该产品是从供应商采购以满足 |SOs|，请在产品名称下启用 :guilabel:`可采购` 复选框。这样会使 :guilabel:`采购` 标签显示在其他标签旁边。

点击 :guilabel:`采购` 标签，并指定一个 :guilabel:`供应商` 以及他们销售该产品的 :guilabel:`价格`。

.. important::
   指定供应商是此工作流程的关键，因为如果不知道产品从谁处采购，Odoo 无法生成 |RFQ|。

如果产品是制造的，请确保为其配置了物料清单 (BOM)。为此，点击屏幕顶部的 :guilabel:`物料清单` 智能按钮，然后在 :guilabel:`物料清单` 页面上点击 :guilabel:`新建` 以为该产品配置新的 |BOM|。

.. seealso::
   有关 |BOM| 创建的完整概述，请参阅 :doc:`物料清单 <../../../manufacturing/basic_setup/bill_configuration>` 文档。

使用 MTO 补货
===================

在将产品配置为使用 |MTO| 路线后，每当确认包含该产品的 |SO| 或 |MO| 时，都会为其创建补货订单。创建的订单类型取决于除 |MTO| 之外选择的第二条路线。

例如，如果选择了 *采购* 作为第二条路线，那么在确认 |SO| 时将创建 |PO|。

.. important::
   当为产品启用 |MTO| 路线时，每当确认 |SO| 或 |MO| 时，都会创建补货订单。即使有足够的现有库存来满足 |SO|，也会这样做，而无需采购或生产额外的产品。

虽然可以将 |MTO| 路线与 *采购* 或 *生产* 路线一起使用，但本文将 *采购* 路线作为此工作流程的示例。首先，导航至 :menuselection:`销售` 应用，然后点击 :guilabel:`新建`，打开一个空白的报价单表单。

在空白报价单表单上，添加一个 :guilabel:`客户`。然后，在 :guilabel:`订单行` 标签下点击 :guilabel:`添加产品`，并输入已配置为使用 *MTO* 和 *采购* 路线的产品。点击 :guilabel:`确认`，报价单将变成 |SO|。

现在，页面顶部会出现一个 :guilabel:`采购` 智能按钮。点击它可以打开与该 |SO| 相关联的 |RFQ|。

点击 :guilabel:`确认订单` 以确认 |RFQ|，并将其转为 |PO|。页面顶部现在会出现一个紫色的 :guilabel:`接收产品` 按钮。收到产品后，点击 :guilabel:`接收产品` 打开收货单，然后点击 :guilabel:`验证` 以将产品录入库存。

通过点击 :guilabel:`SO` 面包屑返回 |SO|，或导航至 :menuselection:`销售应用 --> 订单 --> 订单`，并选择 |SO|。

最后，点击订单顶部的 :guilabel:`发货` 智能按钮以打开发货单。当产品已发给客户后，点击 :guilabel:`验证` 以确认发货。

.. seealso::
   有关包含 |MTO| 路线的工作流程的信息，请参阅以下文档：

   - :doc:`resupply_warehouses`
   - :doc:`../../../manufacturing/subcontracting/subcontracting_basic`
   - :doc:`../../../manufacturing/advanced_configuration/sub_assemblies`
