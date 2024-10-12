===========================
采购费用报告
===========================

.. |RFQ| replace:: :abbr:`RFQ (报价请求)`
.. |RFQs| replace:: :abbr:`RFQs (报价请求)`
.. |POs| replace:: :abbr:`POs (采购订单)`
.. |PO| replace:: :abbr:`PO (采购订单)`
.. |caret| replace:: :icon:`fa-caret-down` :guilabel:`(向下)` 图标
.. |per| replace:: 采购费用报告

通过 *采购* 应用，用户可以监控一段时间内的采购费用。此报告帮助公司跟踪和分析支出，识别节约成本的机会，并确保高效的预算管理。

创建采购费用报告
===================

要创建 |per|，首先导航到 :menuselection:`采购应用 --> 报告 --> 采购` 以打开 :guilabel:`采购分析` 仪表板。

默认情况下，仪表板显示带有 :guilabel:`确认日期` 的 :guilabel:`采购订单` (POs) 的 :guilabel:`不含税总计` 的折线图概览，或者显示状态为 *草稿*、*已发送* 或 *已取消* 的报价请求 (RFQs)。

添加筛选器和分组
------------------

在右上角，点击 :icon:`oi-view-pivot` :guilabel:`(数据透视表)` 图标切换到数据透视表视图。

.. tip::
   虽然 |per| 也可以以 :ref:`图表视图 <purchase/view-results>` 查看，如 :icon:`fa-bar-chart` :guilabel:`(柱状图)`、:icon:`fa-line-chart` :guilabel:`(折线图)` 或 :icon:`fa-pie-chart` :guilabel:`(饼图)`，但数据透视表视图提供了最详细的数据视图，是推荐的起点。

删除 :guilabel:`搜索...` 栏中的任何默认筛选器。然后，点击 |caret| 打开包含 :guilabel:`筛选器`、:guilabel:`分组依据` 和 :guilabel:`收藏` 列的下拉菜单。

.. note::
   除非另有说明，报告显示的数据包括 |RFQs| 和 |POs|。可以通过在 :guilabel:`筛选器` 列中选择 :guilabel:`报价请求` 或 :guilabel:`采购订单` 来更改此设置。

在 :guilabel:`筛选器` 列下，选择用于比较的时间范围。报告可以按 :guilabel:`订单日期` 或 :guilabel:`确认日期` 进行筛选。从列表中选择一个，然后点击 |caret| 以按月份、季度或年份指定日期范围。

接下来，在 :guilabel:`分组依据` 列下，选择 :guilabel:`供应商`。然后，选择 :guilabel:`产品类别`，该选项也位于 :guilabel:`分组依据` 列中。

.. note::
   :guilabel:`分组依据` 标题下的选择可以根据公司的需求进行更改。例如，选择 :guilabel:`产品` 而不是 :guilabel:`产品类别`，可以更深入地查看特定产品的表现，而不是整个类别。

接下来，在出现的 :guilabel:`比较` 标题下进行选择。只有在 :guilabel:`筛选器` 列中选择日期范围后，这些选项才可用，并且根据所选的时间范围而有所不同。:guilabel:`上一期间` 将添加与上一期间（例如上个月或上一季度）的比较。:guilabel:`上一年` 将与前一年的同一时间段进行比较。

.. note::
   虽然可以一次添加多个基于时间的筛选器，但一次只能选择一个比较选项。

.. figure:: procurement_expenses_report/filters-groups.png
   :align: center
   :alt: 采购费用报告的筛选器、分组依据和比较选项的下拉菜单。

   选择了 Q2 的筛选器、**上一期间** 的比较和 **供应商** 与 **产品类别** 的分组依据。

添加度量标准
------------

选择了 :guilabel:`筛选器`、:guilabel:`分组依据` 和 :guilabel:`比较` 设置后，点击菜单外关闭下拉菜单。

默认情况下，报告显示包含以下度量标准的数据：:guilabel:`订单`、:guilabel:`总计`、:guilabel:`不含税总计` 和 :guilabel:`数量`。点击左上角的 :guilabel:`度量标准` 以打开可用度量标准的下拉列表。

点击以下特定度量标准以为采购费用报告添加额外的列：

- :guilabel:`总计` 和 :guilabel:`不含税总计`：可以包含一个或两个度量标准。这些用于总体支出分析。
- :guilabel:`平均成本`：用于评估成本效率。
- :guilabel:`确认天数` 和 :guilabel:`接收天数`：用于评估供应商的表现。
- :guilabel:`订购数量` 和 :guilabel:`接收数量`：用于了解订单效率。
- :guilabel:`已开票数量` 和 :guilabel:`待开票数量`：用于跟踪订单的准确性。

.. tip::
   如果需要，可以在报告中包含其他度量标准，以提供更多的洞察。例如，可以包含 :guilabel:`总重量` 和 :guilabel:`体积` 以进一步进行物流和管理分析。

选择所有必要的度量标准后，点击菜单外关闭下拉菜单。

.. _purchase/view-results:

查看结果
========

选择了所有筛选器和度量标准后，报告会以所选视图生成。

.. image:: procurement_expenses_report/sample-per-report.png
   :align: center
   :alt: 采购费用报告的示例版本。

点击 :guilabel:`插入到电子表格`，将数据透视表视图添加到 *文件* 应用中的可编辑电子表格格式。

.. important::
   只有在安装了 *Documents Spreadsheet* 模块时，才会显示 :guilabel:`插入到电子表格` 选项。

.. note::
   |per| 也可以以图表视图显示。点击 :icon:`fa-area-chart` :guilabel:`(面积图)` 图标切换到图表视图。点击报告顶部的相应图标可以切换到 :icon:`fa-bar-chart` :guilabel:`(柱状图)`、:icon:`fa-line-chart` :guilabel:`(折线图)` 或 :icon:`fa-pie-chart` :guilabel:`(饼图)`。

.. seealso::
   要将此报告保存为 *收藏*，请参阅 :ref:`搜索/收藏`。
