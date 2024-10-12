===================
供应商成本报告
===================

.. |RFQ| replace:: :abbr:`RFQ (报价请求)`
.. |RFQs| replace:: :abbr:`RFQs (报价请求)`
.. |POs| replace:: :abbr:`POs (采购订单)`
.. |caret| replace:: :icon:`fa-caret-down` :guilabel:`(向下)` 图标

通过 *采购* 应用，用户可以跟踪供应商成本随时间的波动。这使用户能够识别最昂贵的供应商，并追踪季节性变化。

创建供应商成本报告
=====================

要创建供应商成本报告，首先导航到 :menuselection:`采购应用 --> 报告 --> 采购` 以打开 :guilabel:`采购分析` 仪表板。默认情况下，仪表板显示带有 :guilabel:`确认日期` 的 POs (采购订单) 的 :guilabel:`不含税总计` 的折线图概览，或者显示状态为 *草稿*、*已发送* 或 *已取消* 的报价请求 (RFQs)。

.. _purchase/vender-cost-report-filters:

添加筛选器和分组
------------------

在右上角，点击 :icon:`oi-view-pivot` :guilabel:`(数据透视表)` 图标切换到数据透视表视图。

删除 :guilabel:`搜索...` 栏中的任何默认筛选器。然后，点击 |caret| 打开包含 :guilabel:`筛选器`、:guilabel:`分组依据` 和 :guilabel:`收藏` 列的下拉菜单。

.. note::
   除非另有说明，报告显示的数据包括 |RFQs| 和 |POs|。可以通过在 :guilabel:`筛选器` 列中选择 :guilabel:`报价请求` 或 :guilabel:`采购订单` 来更改此设置。

在 :guilabel:`筛选器` 列下，选择用于比较的日期范围。报告可以按 :guilabel:`订单日期` 或 :guilabel:`确认日期` 进行筛选。从列表中选择一个，然后点击 |caret| 以按月份、季度或年份指定日期范围。

接下来，在 :guilabel:`分组依据` 列下，选择 :guilabel:`供应商`。然后，选择 :guilabel:`产品`，该选项也位于 :guilabel:`分组依据` 列中。

.. note::
   选择 :guilabel:`产品` 并非此报告的必要选项，但建议选择，因为它可以为各个供应商的表现提供额外的见解。还可以在 :guilabel:`分组依据` 标题下进行其他选择，包括 :guilabel:`产品类别`、:guilabel:`状态` 和 :guilabel:`采购代表`。

   为确保报告正确生成，请确保 :guilabel:`供应商` 是在 :guilabel:`分组依据` 列下的 **第一个** 选择。

接下来，在出现的 :guilabel:`比较` 标题下进行选择。只有在 :guilabel:`筛选器` 列中选择日期范围后，这些选项才可用，并且根据所选的时间范围而有所不同。:guilabel:`上一期间` 将添加与上一期间（例如上个月或上一季度）的比较。:guilabel:`上一年` 将与前一年的同一时间段进行比较。

.. note::
   虽然可以一次添加多个基于时间的筛选器，但一次只能选择一个比较选项。

.. image:: vendor_costs_report/filters-groups.png
   :align: center
   :alt: 供应商成本报告的筛选器、分组依据和比较选项的下拉菜单。

添加度量标准
------------

选择了 :guilabel:`筛选器`、:guilabel:`分组依据` 和 :guilabel:`比较` 设置后，点击菜单外关闭下拉菜单。

默认情况下，报告显示以下度量标准的数据：:guilabel:`订单`、:guilabel:`总计`、:guilabel:`不含税总计` 和 :guilabel:`数量`。点击左上角的 :guilabel:`度量标准` 以打开可用度量标准的下拉列表。点击 :guilabel:`平均成本` 将其添加到报告中。选择任何其他要添加到报告中的度量标准，或点击任何已选中的度量标准以将其删除（如果需要）。

.. tip::
   建议在运行报告时至少选择 :guilabel:`平均成本`、:guilabel:`总计` 或 :guilabel:`不含税总计`。还可以添加其他度量标准，例如 :guilabel:`接收天数`，以提供更多的见解。

查看结果
========

选择了所有 :ref:`筛选器和度量标准 <purchase/vender-cost-report-filters>` 后，报告将在数据透视表视图中生成。点击 :guilabel:`插入到电子表格`，将数据透视表视图添加到 *文件* 应用中的可编辑电子表格格式。

.. important::
   只有在安装了 *Documents Spreadsheet* 模块时，才会显示 :guilabel:`插入到电子表格` 选项。

.. image:: vendor_costs_report/sample-vendor-report.png
   :align: center
   :alt: 一个设置了总成本和平均成本度量标准的供应商成本报告示例。

.. note::
   供应商成本报告也可以以 *图表* 视图显示。点击 :icon:`fa-area-chart` :guilabel:`(面积图)` 图标切换到图表视图。点击报告顶部的相应图标可以切换到 :icon:`fa-bar-chart` :guilabel:`(柱状图)`、:icon:`fa-line-chart` :guilabel:`(折线图)` 或 :icon:`fa-pie-chart` :guilabel:`(饼图)`。

.. seealso::
   要将此报告保存为 *收藏*，请参阅 :ref:`搜索/收藏`。
