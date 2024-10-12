=======================
工单依赖管理
=======================

.. |BOM| replace:: :abbr:`BoM (物料清单)`

在制造某些产品时，某些操作需要在其他操作开始之前完成。为了确保操作按正确顺序进行，Odoo *制造*模块提供了*工单依赖*设置。启用该设置后，物料清单（BoM）上的某些操作可以被其他必须首先完成的操作“阻止”。

配置
=============

*工单依赖*设置默认情况下未启用。要启用此设置，首先导航到 :menuselection:`制造 --> 配置 --> 设置`。然后，启用 :guilabel:`工单` 设置（如果尚未激活）。

启用 :guilabel:`工单` 设置后，:guilabel:`工单依赖` 设置将出现在其下方。启用 :guilabel:`工单依赖`，然后点击 :guilabel:`保存` 以确认更改。

将依赖项添加到BoM
=======================

工单依赖是在产品的 |BOM| 上配置的。为此，导航到 :menuselection:`制造 --> 产品 --> 物料清单`，然后选择一个 |BOM|，或通过点击 :guilabel:`新建` 创建一个新的BoM。

.. admonition:: 了解更多

   有关如何正确配置新 |BOM| 的完整指南，请参阅 :doc:`创建物料清单 <../basic_setup/bill_configuration>` 文档。

在 |BOM| 上，点击 :guilabel:`杂项` 标签，然后启用 :guilabel:`操作依赖` 复选框。这会使 :guilabel:`操作` 标签的设置中出现新的 :guilabel:`被阻止` 选项。

.. image:: work_order_dependencies/operation-dependencies.png
   :align: center
   :alt: 物料清单杂项标签中的操作依赖复选框。

接下来，点击 :guilabel:`操作` 标签。在标签的右上角，点击 :guilabel:`设置` 按钮，然后启用 :guilabel:`被阻止` 复选框。这会使每个操作的行中显示 :guilabel:`被阻止` 字段。

.. image:: work_order_dependencies/operations-settings.png
   :align: center
   :alt: 物料清单操作标签的设置。

在应被其他操作阻止的操作行中，点击 :guilabel:`被阻止` 字段，弹出一个 :guilabel:`打开: 操作` 窗口。在弹出的窗口中的 :guilabel:`被阻止` 下拉菜单中，选择必须在该操作之前完成的阻止操作。

.. image:: work_order_dependencies/blocked-by.png
   :align: center
   :alt: 物料清单中某操作的被阻止下拉字段。

最后，点击 :guilabel:`保存` 来保存 |BOM|。

使用依赖项计划工单
===================================

一旦在 |BOM| 上配置了工单依赖，Odoo *制造*模块就可以根据依赖关系来计划工单的执行时间。要为制造订单计划工单，首先导航到 :menuselection:`制造 --> 操作 --> 制造订单`。

接着，选择一个具有工单依赖的产品的制造订单，或点击 :guilabel:`新建` 来创建一个新的制造订单。如果创建了新制造订单，从 :guilabel:`物料清单` 下拉菜单中选择一个配置了工单依赖的 |BOM|，然后点击 :guilabel:`确认`。

确认制造订单后，选择 :guilabel:`工单` 标签以查看完成该订单所需的工单。任何没有被其他工单阻止的工单将在 :guilabel:`状态` 栏显示 `准备好` 标签。

被一个或多个工单阻止的工单则会显示 `等待其他工单` 标签。一旦阻止的工单完成，标签将更新为 `准备好`。

.. image:: work_order_dependencies/work-order-status.png
   :align: center
   :alt: 制造订单中工单的状态标签。

要计划制造订单的工单，点击页面顶部的 :guilabel:`计划` 按钮。这样做之后，:guilabel:`预定开始日期` 字段将自动填充每个工单的计划开始日期和时间。被阻止的工单将在其前置工单的 :guilabel:`预计持续时间` 指定的时间段结束后安排。

.. image:: work_order_dependencies/scheduled-start-date.png
   :align: center
   :alt: 制造订单中工单的预定开始日期字段。

.. example::
   为产品A创建了一个制造订单。该制造订单有两个操作：切割和组装。每个操作的预计持续时间为60分钟，而组装操作被切割操作阻止。

   制造订单的 :guilabel:`计划` 按钮在下午1:30点击，切割操作立即计划开始。由于切割操作的预计持续时间为60分钟，因此组装操作计划在下午2:30开始。

按工作中心计划
----------------------

要查看工单如何计划的可视化表示，请通过导航到 :menuselection:`制造 --> 计划 --> 按工作中心计划` 页面。这一页面显示了为每个操作安排的工单时间线。

如果一个工单被另一个工单阻止，那么被阻止的工单将在阻止它的工单完成后计划开始。此外，一条箭头将两个工单连接起来，从阻止操作指向被阻止的操作。

.. image:: work_order_dependencies/planning-arrow.png
   :align: center
   :alt: 连接被阻止工单和阻止它的工单的箭头。
