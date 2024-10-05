:show-content:
:hide-page-toc:

=============
补货管理
=============

.. |MTO| replace:: :abbr:`MTO (按订单生产)`
.. |PO| replace:: :abbr:`PO (采购订单)`
.. |MO| replace:: :abbr:`MO (生产订单)`
.. |SO| replace:: :abbr:`SO (销售订单)`

在 Odoo 中，有两种自动补货策略：*补货规则* 和 *按订单生产 (MTO)* 路线。尽管这些策略略有不同，但它们具有类似的后果：触发自动创建 |PO| 或 |MO|。选择哪种策略取决于企业的生产和交货流程。

术语
===========

补货报告与补货规则
-----------------------------------------

补货报告是一份包含所有库存预测数量为负数的产品的清单。

*补货规则* 用于确保始终有最少量的产品库存，以便生产产品和/或满足销售订单需求。当产品的库存量达到最小值时，Odoo 会自动生成一个采购订单，数量足以达到最大库存量。

补货规则可以在补货报告中或通过产品表单创建和管理。

.. _inventory/management/products/strategies:

按订单生产
-------------

*按订单生产 (MTO)* 是一种采购路线，每当销售订单确认时，都会创建一个草稿采购订单（或生产订单），**无论当前库存水平如何**。

与使用补货规则补货的产品不同，Odoo 会自动将销售订单与 |MTO| 路线生成的 |PO| 或 |MO| 关联起来。

补货规则和 |MTO| 之间的另一个区别是，使用 |MTO| 时，Odoo 会在确认 |SO| 后立即生成草稿 |PO| 或 |MO|。而使用补货规则时，Odoo 会在产品的预测库存低于设定的最小数量时生成草稿 |PO| 或 |MO|。

此外，只要 |PO| 或 |MO| 尚未确认，Odoo 会根据预测的变化自动将数量添加到 |PO| 或 |MO| 中。

|MTO| 路线是定制化产品和/或不保留现货产品的最佳补货策略。

配置
=============

补货报告与补货规则
-----------------------------------------

要访问补货报告，请前往 :menuselection:`库存应用 --> 运营 --> 补货`。

默认情况下，补货报告仪表板显示需要手动重新订购的所有产品。如果产品没有特定的规则，Odoo 假定 :guilabel:`最小数量` 和 :guilabel:`最大数量` 都为 `0.00`。

.. note::
   对于没有设定补货规则的产品，Odoo 根据已确认的销售订单、发货和收货来计算预测。对于设定了补货规则的产品，Odoo 按常规计算预测，但也考虑采购/生产交货时间和安全交货时间。

.. important::
   在创建新的补货规则之前，请确保产品在产品表单中配置了 *供应商* 或 *物料清单*。要检查这一点，请转到 :menuselection:`库存应用 --> 产品 --> 产品`，选择产品以打开其产品表单。如果已配置供应商，则在 :guilabel:`采购` 标签中列出；如果已配置物料清单，则在表单顶部的 :guilabel:`物料清单` 智能按钮中找到。

   产品表单中的 :guilabel:`产品类型`（位于 :guilabel:`常规信息` 标签下）**必须** 设置为 :guilabel:`可存储产品`。按定义，消耗品的库存水平不被跟踪，因此 Odoo 无法在补货报告中计算消耗品。

.. image:: replenishment/replenishment/replenishment-report-dashboard.png
   :align: center
   :alt: 补货报告列出所有需要采购以满足当前需求的项目。

要从补货报告中创建新的补货规则，请转到 :menuselection:`库存应用 --> 运营 --> 补货`，点击 :guilabel:`创建`，然后从 :guilabel:`产品` 列中的下拉菜单中选择所需的产品。如果需要，还可以在 :guilabel:`补货` 报告页面的相应列中配置 :guilabel:`最小数量` 和 :guilabel:`最大数量`。

要从产品表单中创建新的补货规则，请前往 :menuselection:`库存应用 --> 产品 --> 产品`，选择产品以打开其产品表单。点击 :guilabel:`补货规则` 智能按钮，点击 :guilabel:`创建` 并填写字段。

补货报告字段
~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下字段显示在 :guilabel:`补货` 报告中。如果任何字段不可见，点击报告右侧的 :guilabel:`⋮（附加选项）` 图标，然后点击字段旁的复选框以显示该字段。

- :guilabel:`产品`：需要补货的产品。
- :guilabel:`库位`：存放产品的特定库位。
- :guilabel:`仓库`：存放产品的仓库。
- :guilabel:`现有数量`：当前可用的产品数量。
- :guilabel:`预测`：在考虑所有当前订单（销售、生产、采购等）后可用的产品数量。
- :guilabel:`首选路线`：产品的采购方式，可能为 :guilabel:`采购`、:guilabel:`生产`、:guilabel:`直运` 等。
- :guilabel:`供应商`：产品的供应公司。
- :guilabel:`物料清单`：产品的物料清单（如果已配置）。
- :guilabel:`触发器`：补货的创建方式，可能为 :guilabel:`自动`（当 :guilabel:`现有数量` 低于 :guilabel:`最小数量` 时自动创建）或 :guilabel:`手动`（仅在请求补货时创建）。
- :guilabel:`采购组`：产品的采购参考编号，如销售订单、采购订单或生产订单。
- :guilabel:`最小数量`：应可用的最小产品数量。当库存低于此数量时，触发补货。
- :guilabel:`最大数量`：补货后应可用的产品数量。
- :guilabel:`倍数数量`：如果产品应按特定数量订购，请输入应订购的数量。例如，如果 :guilabel:`倍数数量` 设置为 `5`，而只需要 3 个，则补充 5 个产品。
- :guilabel:`订购数量`：当前所需的产品数量，如果点击 :guilabel:`一次性订购` 或 :guilabel:`自动订单` 按钮，则会订购此数量。
- :guilabel:`计量单位`：采购产品时使用的计量单位。
- :guilabel:`公司`：为公司采购产品。

默认情况下，:guilabel:`订购数量` 字段中的数量是达到设定的 :guilabel:`最大数量` 所需的数量。然而，可以通过点击字段并更改值来调整 :guilabel:`订购数量`。要手动补货，请点击 :guilabel:`一次性订购`。

要在 :guilabel:`补货` 页面上自动补货，点击行右侧的 :guilabel:`自动订单` 按钮，表示为 :guilabel:`🔄（圆形箭头）` 图标。

点击此按钮后，Odoo 会在预测库存水平低于补货规则设定的 :guilabel:`最小数量` 时自动生成草稿 |PO|/|MO|。

在 :guilabel:`补货` 页面上，可以通过点击行最右侧的 :guilabel:`🔕（暂停）` 图标，在给定时间段内暂时停用补货规则或手动补货。

.. image:: replenishment/replenishment/reordering-rule-snooze-settings.png
   :align: center
   :alt: 暂停选项可在一段时间内关闭补货通知。

通过手动补货创建的 |PO| 或 |MO| 的来源文档为 :guilabel:`补货报告`。通过自动补货规则创建的 |PO| 或 |MO| 的来源文档为触发规则的 |SO| 参考编号。

.. image:: replenishment/replenishment/rfq-source-document.png
   :align: center
   :alt: 报价请求列表显示哪些报价直接来自补货报告。

按订单生产 (MTO) 路线
=========================

由于 |MTO| 路线推荐用于定制化产品，因此默认情况下该路线被隐藏。

要在 Odoo 中激活 |MTO| 路线：
 #. 前往 :menuselection:`库存应用 --> 配置 --> 设置`。
 #. 在 :guilabel:`仓库` 部分启用 :guilabel:`多步骤路线` 设置，然后点击 :guilabel:`保存`。
 #. 然后，前往 :menuselection:`库存应用 --> 配置 --> 路线`。
 #. 点击 :menuselection:`筛选器 --> 归档` 以显示归档路线。
 #. 勾选 :guilabel:`按订单补货 (MTO)` 旁的复选框，然后点击 :menuselection:`操作 --> 取消归档`。

.. note::
   激活 :guilabel:`多步骤路线` 设置还会激活 :guilabel:`存储库位`。如果这些功能不适用于仓库，在取消归档 |MTO| 路线后可以禁用这些设置。

要将产品的采购路线设置为 |MTO|，请前往 :menuselection:`库存应用 --> 产品 --> 产品`，然后点击所需产品以打开其产品表单。

接着，点击 :guilabel:`库存` 标签，在 :guilabel:`路线` 部分中选择 :guilabel:`按订单补货 (MTO)`。

对于直接从供应商处采购的产品，请确保选择了 :guilabel:`采购` 路线，以及 :guilabel:`按订单补货 (MTO)` 路线。此外，请确保在产品表单的 :guilabel:`采购` 标签中配置了供应商。

对于在内部生产的产品，请确保选择了 :guilabel:`生产` 路线，以及 :guilabel:`按订单补货 (MTO)` 路线。此外，请确保已为产品配置了物料清单，可以通过产品表单上的 :guilabel:`物料清单` 智能按钮访问。

.. note::
   |MTO| 路线不能单独选择。|MTO| **只能** 在选择了 :guilabel:`生产` 或 :guilabel:`采购` 路线时有效。

.. image:: replenishment/replenishment/acoustic-block-screen-replenish.png
   :align: center
   :alt: 产品表单上选择按订单补货。

.. toctree::
   :titlesonly:

   replenishment/mto
   replenishment/reordering_rules
   replenishment/lead_times
   replenishment/resupply_warehouses
   replenishment/warehouse_replenishment_transfer
