==============
分包生产
==============

.. |BoM| replace:: :abbr:`BoM (物料清单)`

在制造业中，*分包*是指公司与第三方制造商（或分包商）合作，由分包商生产产品，而合同公司负责销售这些产品。

分包对合同公司和分包商都有多种好处。

对于合同公司而言，分包允许他们销售种类繁多的制造产品，而无需担心为设备和劳动力投入资金，自己进行制造。这样一来，合同公司在经济周期中能够保持灵活性，可以根据实际需求轻松增加或减少与分包商的合作。此外，这还意味着他们可以专注于自己擅长的任务，同时将更专业的工作委托给分包商。

对分包商而言，分包使其能够专注于生产中的细分领域，而这些领域在没有分包合作的情况下可能不会那么盈利。在某些安排中，分包还为分包商提供了选择接受或拒绝项目的灵活性，并可以根据自身需求决定工作量。

在 Odoo 中，公司可以根据多个因素配置分包工作流，包括如何采购组件以及成品生产后会发生什么。

.. cards::

   .. card:: 基本分包
      :target: subcontracting/subcontracting_basic

      不向分包商提供组件的分包产品。

   .. card:: 向分包商补货
      :target: subcontracting/subcontracting_resupply

      每当确认分包产品的采购订单时，向分包商发送组件。

   .. card:: 分包商直运
      :target: subcontracting/subcontracting_dropship

      每当确认分包产品的采购订单时，将组件直接发运给分包商。

配置
============

要在 Odoo 中启用分包，导航到 :menuselection:`制造应用 --> 配置 --> 设置`，在 :guilabel:`操作` 部分勾选 :guilabel:`分包` 选项，然后点击 :guilabel:`保存`。

.. image:: subcontracting/subcontracting-setting.png
   :align: center
   :alt: 制造应用中的分包设置。

启用分包后，Odoo 中会提供几个不同的功能：

- 在物料清单 (BoM) 上，*BoM 类型*字段现在包含一个 *分包* 选项。启用 *分包* |BoM| 类型将 |BoM| 的产品指定为分包产品，这意味着 Odoo 知道该产品是由分包商生产的，而不是由拥有 Odoo 数据库的公司生产的。
- 在 *库存* 应用中会提供两种分包路线，可以在产品页面的 *库存* 选项卡中为特定产品分配这些路线：

  - *按订单向分包商补货*
  - *按订单直运给分包商*

分包工作流
============

在 Odoo 中，有三种分包工作流，主要区别在于分包商如何获取必要的组件：

- 在 *基本* 分包工作流中，分包商完全负责获取组件。此工作流在 :doc:`subcontracting/subcontracting_basic` 文档中进行了概述。
- 在 *按订单向分包商补货* 的工作流中，合同公司从其仓库向分包商发送组件。此工作流在 :doc:`subcontracting/subcontracting_resupply` 文档中进行了概述。
- 在 *按订单直运给分包商* 的工作流中，合同公司从供应商处购买组件，并将其直接交付给分包商。此工作流在 :doc:`subcontracting/subcontracting_dropship` 文档中进行了概述。

除了分包商如何获得组件外，还需要考虑分包产品的制造目的以及成品制造后会发生什么。

关于分包产品的制造原因，主要有两个：履行客户订单或补充库存数量。

关于成品生产后的处理方式，它们可以运送回合同公司，也可以直接发运给最终客户。

上述三种分包工作流都可以配置以实现这些不同的可能性，具体方法在相应文档中进行了说明。

分包产品的估价
================

分包产品的估价取决于几个不同的变量：

- 如果由合同公司提供的必要组件的成本；此处简称为 `C`。
- 向分包商支付的制造分包产品的服务费用；此处简称为 `M`。
- 将组件运送给分包商并将成品运回合同公司的运输成本；此处简称为 `S`。
- 如果组件由分包商直接运送给最终客户，则需要考虑的直运成本；此处简称为 `D`。
- 其他相关费用，如进口税等；此处简称为 `x`。

因此，分包产品的总估值 (`P`) 可以用以下公式表示：

.. math::
   P = C + M + S + D + x

需要注意的是，并非所有分包产品的估价都包含所有这些变量。例如，如果产品没有直接发运给最终客户，则无需考虑直运成本。

.. toctree::

   subcontracting/subcontracting_basic
   subcontracting/basic_subcontracting_lead_times
   subcontracting/subcontracting_resupply
   subcontracting/resupply_subcontracting_lead_times
   subcontracting/subcontracting_dropship
   subcontracting/dropship_subcontracting_lead_times
