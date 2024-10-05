================
补货规则
================

.. _inventory/management/reordering_rules:
.. |SO| replace:: :abbr:`SO (销售订单)`
.. |PO| replace:: :abbr:`PO (采购订单)`

*补货规则*用于确保预测的库存水平保持在某个阈值以上，同时不超过指定的上限。通过指定库存不应低于的最低数量和不应超过的最大数量来实现这一目标。

可以根据用于补充产品的路线为每个产品配置补货规则。如果产品使用 *采购* 路线，当触发补货规则时，将创建询价单 (RFQ)。如果产品使用 *生产* 路线，则会改为创建生产订单 (MO)。无论选择哪种补充路线，都是如此。

.. seealso::
   - `Odoo 教程: 自动补货规则 <https://www.youtube.com/watch?v=XEJZrCjoXaU>`_
   - `Odoo 教程: 手动补货规则 <https://www.youtube.com/watch?v=deIREJ1FFj4>`_

为补货规则配置产品
===================

要为产品使用补货规则，首先必须正确配置产品。首先，导航至 :menuselection:`库存应用 --> 产品 --> 产品`，然后选择一个现有产品，或通过点击 :guilabel:`新建` 来创建一个新产品。

在产品表单的 :guilabel:`基本信息` 标签下，确保 :guilabel:`产品类型` 设置为 :guilabel:`可存储产品`。这是必需的，因为 Odoo 仅跟踪可存储产品的库存数量，而此数量用于触发补货规则。

.. image:: reordering_rules/product-type.png
   :align: center
   :alt: 将产品类型设置为可存储产品。

接下来，点击 :guilabel:`库存` 标签，并从 :guilabel:`路线` 部分选择一个或多个路线。这样做告诉 Odoo 使用哪条路线来补充产品。

.. image:: reordering_rules/select-routes.png
   :align: center
   :alt: 在库存标签中选择一个或多个路线。

如果产品是通过 :guilabel:`采购` 路线补货的，请确认在产品名称下启用了 :guilabel:`可采购` 复选框。这样会显示 :guilabel:`采购` 标签。点击 :guilabel:`采购` 标签，并指定至少一个供应商以及他们销售该产品的价格，以便 Odoo 知道应从哪家公司采购产品。

.. image:: reordering_rules/specify-vendor.png
   :align: center
   :alt: 在采购标签中指定供应商和价格。

如果产品是通过 :guilabel:`生产` 路线补充的，则需要至少一个与之关联的物料清单 (BoM)。这是必需的，因为 Odoo 仅为有 :abbr:`BoM (物料清单)` 的产品创建生产订单。

如果产品还没有物料清单 (BoM)，请在产品表单顶部选择 :guilabel:`物料清单` 智能按钮，然后点击 :guilabel:`新建` 来配置新的 :abbr:`BoM (物料清单)`。

.. image:: reordering_rules/bom-smart-button.png
   :align: center
   :alt: 产品表单上的物料清单智能按钮。

创建新的补货规则
=================

要创建新的补货规则，导航至 :menuselection:`库存应用 --> 配置 --> 补货规则`，然后点击 :guilabel:`新建`，并按以下步骤填写新行：

- :guilabel:`产品`：由规则补充的产品。
- :guilabel:`库位`：存储产品的库位。
- :guilabel:`最小数量`：可以预测的最小数量，在不触发规则的情况下。当预测的库存低于此数量时，将创建产品的补货订单。
- :guilabel:`最大数量`：库存最多补充到的数量。
- :guilabel:`批量数量`：如果产品应以某个数量批量补充，请在此指定（例如，产品可以批量20个补充）。
- :guilabel:`计量单位`：用于补货产品的计量单位。此值可以是 `单位`，也可以是重量、长度等特定的计量单位。

.. image:: reordering_rules/reordering-rule-form.png
   :align: center
   :alt: 创建新补货规则的表单。

.. tip::
   补货规则也可以从每个产品表单中创建。为此，导航至 :menuselection:`库存应用 --> 产品 --> 产品`，选择一个产品。然后点击 :guilabel:`补货规则` 智能按钮，并点击 :guilabel:`新建`，按上述步骤填写新行。

有关补货规则高级用法，请了解以下补货规则字段：

- :ref:`触发器 <inventory/product_management/trigger>`
- :ref:`可见天数 <inventory/product_management/visibility-days>`
- :ref:`路线 <inventory/product_management/route>`

0/0/1 补货规则
----------------

*0/0/1* 补货规则是一种特殊规则，用于补充未保持现货的产品，每次确认该产品的销售订单 (SO) 时触发。

.. important::
   0/0/1 补货规则类似于 *按订单补充 (MTO)* 路线，因为两种工作流程都用于在确认 |SO| 后补充产品。

   两种方法的主要区别在于，*按订单补充* 路线会自动为导致补货的 |SO| 保留产品。这意味着产品 **不能** 用于其他 |SO|。

   0/0/1 补货规则没有这个限制。通过该规则补充的产品不会为任何特定 |SO| 保留，可以根据需要使用。

   另一个关键区别是，*按订单补充* 路线创建的补货订单通过智能按钮与原始 |SO| 关联。而使用 0/0/1 补货规则时，补货订单会创建，但不会与原始 |SO| 关联。

   查看 :doc:`按订单补充 (MTO) <mto>` 文档，了解 MTO 路线的完整概述。

要创建 0/0/1 补货规则，导航至 :menuselection:`库存应用 --> 产品 --> 产品`，选择一个产品。

在产品页面顶部，点击 :icon:`fa-refresh` :guilabel:`补货规则` 智能按钮，打开产品的 :guilabel:`补货规则` 页面。在结果页面中，点击 :guilabel:`新建`，开始配置新的补货规则。

在新补货规则的 :guilabel:`库位` 字段中，选择补充产品应存放的库位。默认情况下，此库位设置为 :guilabel:`WH/Stock`。

在 :guilabel:`路线` 字段中，选择规则应使用的补货路线。例如，如果产品应从供应商处采购，则选择 :guilabel:`采购` 路线。

在 :guilabel:`最小数量` 和 :guilabel:`最大数量` 字段中，将数值设置为 `0.00`。在 :guilabel:`订购数量` 字段中，输入 `1.00`。

.. image:: reordering_rules/001-rule.png
   :align: center
   :alt: 一个 0/0/1 补货规则。

使用这些数值配置补货规则后，每当 |SO| 导致产品的预测数量低于 `0.00` 的 :guilabel:`最小数量` 时，将使用选定的 :guilabel:`路线` 以单位为一的增量补充产品，直到达到 `0.00` 的 :guilabel:`最大数量`。

.. example::
   一个相框配置了使用 *采购* 路线的 0/0/1 补货规则。该相框在任何时间都没有现货。

   确认了一个相框的 |SO|，导致预测数量下降到 `-1.00`。这触发了补货规则，自动创建了一个相框的 |PO|。

   一旦收到供应商发来的产品，相框的预测数量恢复到 `0.00`。现在手头有一个相框，但它未保留给触发其采购的 |SO|。该相框可以用于履行该 |SO|，也可以保留给其他订单。

.. _inventory/product_management/trigger:

触发器
=======

当库存低于补货规则的最小值时，将补货规则的 *触发器* 设置为 *自动*，以自动创建采购或生产订单来补充库存。

或者，将补货规则的触发器设置为 *手动*，则产品和预测库存将显示在 *补货仪表板* 上，采购经理可以在此查看库存水平、交货时间和预计到货日期。

.. seealso::
   :doc:`../replenishment`

.. tip::
   补货仪表板可通过导航至 :menuselection:`库存应用 --> 运营 --> 补货` 进行访问。

要启用 :guilabel:`触发器` 字段，导航至 :menuselection:`库存应用 --> 配置 --> 补货规则`。然后，点击位于列标题最右侧的 :guilabel:`(滑块)` 图标，并在出现的附加选项下拉菜单中启用 :guilabel:`触发器` 选项。

.. image:: reordering_rules/enable-trigger.png
   :align: center
   :alt: 在附加选项菜单中启用触发器字段。

在 :guilabel:`触发器` 列中，选择 :guilabel:`自动` 或 :guilabel:`手动`。请参阅以下部分，了解不同类型的补货规则。

自动
----

通过将补货规则的 :guilabel:`触发器` 字段设置为 :guilabel:`自动` 来配置自动补货规则，当以下情况发生时，系统将生成采购或生产订单：

#. 调度程序运行时，*库存量* 低于最小值
#. 确认销售订单时，产品的 *预测数量* 低于最小值

.. tip::
   默认情况下，调度程序每天运行一次。

   要在调度程序运行之前手动触发补货规则，请确保启用了 :ref:`开发者模式 <developer-mode>`，并选择 :menuselection:`库存应用 --> 运营 --> 运行调度器`。然后，在弹出窗口中点击紫色的 :guilabel:`运行调度器` 按钮。

   请注意，这也会触发任何其他计划的操作。

.. example::
   产品 `办公台灯` 配置了自动补货规则，当预测数量低于 `5.00` 的 :guilabel:`最小数量` 时触发。由于当前的 :guilabel:`预测数量` 为 `55.00`，补货规则 **未** 被触发。

   .. image:: reordering_rules/auto.png
      :align: center
      :alt: 在补货规则页面显示自动补货规则。

如果选择了 *采购* 路线，则会生成 :abbr:`RFQ (询价单)`。要查看和管理 :abbr:`RFQ (询价单)`，导航至 :menuselection:`采购应用 --> 订单 --> 询价单`。

如果选择了 *生产* 路线，则会生成 :abbr:`MO (生产订单)`。要查看和管理 :abbr:`MO (生产订单)`，导航至 :menuselection:`生产应用 --> 运营 --> 生产订单`。

如果未选择任何路线，Odoo 将选择产品表单 :guilabel:`库存` 标签中指定的 :guilabel:`路线`。

.. _inventory/product_management/manual-rr:

手动
------

通过将补货规则的 :guilabel:`触发器` 字段设置为 :guilabel:`手动` 来配置手动补货规则，当预测数量低于指定的最小值时，产品将显示在补货仪表板上。这些产品称为 *需求*，因为它们是履行即将到来的销售订单所需的，但预测数量不足。

补货仪表板，可通过导航至 :menuselection:`库存应用 --> 运营 --> 补货` 访问，考虑销售订单的截止日期、预测库存水平和供应商的交货时间。只有在该补货项目需要补货时，它才会显示需求。

.. note::
   如果订购产品的一天窗口时间过短，请跳转到 :ref:`可见天数 <inventory/product_management/visibility-days>` 部分，使需求提前几天出现在补货仪表板上。

当产品出现在补货仪表板上时，点击 :guilabel:`一次性订购` 按钮生成带有指定数量的采购或生产订单 :guilabel:`订购数量`。

.. image:: reordering_rules/manual.png
   :align: center
   :alt: 在补货仪表板上点击一次性订购按钮补充库存。

.. _inventory/product_management/visibility-days:

可见天数
===============

.. important::
   在继续本节之前，确保理解了 :doc:`交货时间 <lead_times>`。

当产品分配了 :ref:`手动补货规则 <inventory/product_management/manual-rr>` 时，*可见天数* 可使产品在某个时间提前几天显示在补货仪表板 (:menuselection:`库存应用 --> 运营 --> 补货`) 上。

.. example::
   一个产品设置了手动补货规则，当库存水平低于四个单位时触发。当前的库存数量为十个单位。

   当前日期为2月20日，销售订单的 *交货日期*（在 :guilabel:`其他信息` 标签中）为3月3日——即距当前日期12天。

   供应商交货时间为4天，采购安全交货时间为1天。

   当补货规则的 :guilabel:`可见天数` 字段设置为0时，产品将在交货日期前5天出现在补货仪表板上，在本例中为2月27日。

   .. image:: reordering_rules/need-dates.png
      :align: center
      :alt: 显示需求出现在补货仪表板上的日期：2月27日。

   要使产品在当前日期（2月20日）出现在补货仪表板上，请将 :guilabel:`可见天数` 设置为 `7.00`。

要确定在补货仪表板上看到产品所需的可见天数，请用 *需求出现日期* 减去 *今天的日期*。

.. math::

   可见天数 = 需求出现日期 - 今天的日期

.. example::
   参照上述示例，今天的日期是2月20日，产品的需求将在2月27日出现。

   （2月27日 - 2月20日 = 7天）

   在这种情况下，错误地将 :guilabel:`可见天数` 设置为少于7天，将导致需求 **不会** 出现在补货仪表板上。

   .. image:: reordering_rules/visibility-days.png
      :align: center
      :alt: 显示补货仪表板上设置正确和错误可见天数的情况。

.. _inventory/product_management/route:

路线
=====

Odoo 允许在每个产品表单的 :guilabel:`库存` 标签下选择多条路线。例如，可以同时选择 :guilabel:`采购` 和 :guilabel:`生产`，从而启用两条路线的功能。

Odoo 还允许用户为产品的补货规则设置首选路线。这是规则默认使用的路线，如果选择了多条路线。要选择首选路线，首先导航至 :menuselection:`库存应用 --> 配置 --> 补货规则`。

默认情况下，:guilabel:`路线` 列在 :guilabel:`补货规则` 页面上是隐藏的。

通过选择列标题最右侧的 :guilabel:`(滑块)` 图标，显示 :guilabel:`路线` 列，并从出现的下拉菜单中勾选 :guilabel:`路线` 选项。

点击补货规则行中的列，显示该规则的所有可用路线的下拉菜单。选择一个作为首选路线。

.. image:: reordering_rules/select-preferred-route.png
   :align: center
   :alt: 从下拉菜单中选择首选路线。

.. important::
   如果为产品启用了多条路线，但未为其补货规则设置首选路线，则将使用产品表单 :guilabel:`库存` 标签中列出的第一条选定路线补货。
