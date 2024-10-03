=========
报告
=========

在大多数应用的 :guilabel:`报告` 菜单下，您可以找到多个报告，帮助您分析和可视化记录中的数据。

.. _reporting/views:

选择视图
================

根据报告的不同，Odoo 可以以多种方式显示数据。某些报告有特定的专属视图，而其他报告则提供了多个视图选择。不过，有两个通用视图专门用于报告：图表视图和数据透视表视图。

.. _reporting/views/graph:

图表视图
----------

:ref:`图表视图 <reporting/using-graph>` 用于可视化记录的数据，帮助您识别模式和趋势。该视图通常位于应用的 :guilabel:`报告` 菜单下，但也可以在其他地方找到。点击右上角的 **图表视图按钮** 进入此视图。

.. image:: reporting/graph-button.png
   :alt: 选择图表视图

.. _reporting/views/pivot:

数据透视表视图
----------

:ref:`数据透视表视图 <reporting/using-pivot>` 用于汇总记录的数据并进行分析。该视图通常位于应用的 :guilabel:`报告` 菜单下，但也可以在其他地方找到。点击右上角的 **数据透视表视图按钮** 进入此视图。

.. image:: reporting/pivot-button.png
   :alt: 选择数据透视表视图

.. _reporting/choosing-measures:

选择指标
=================

选择视图后，应确保只过滤相关的记录 :doc:`<search>`。接下来，您应选择要衡量的内容。默认情况下，总会选择一个衡量指标。如果您希望编辑它，请点击 :guilabel:`指标` 并选择一个，或者在数据透视表中选择多个指标。

.. note::
   当您选择一个指标时，Odoo 会汇总该字段中已过滤记录的值。只有数值字段 (:ref:`整数 <studio/fields/simple-fields/integer>`、:ref:`小数 <studio/fields/simple-fields/decimal>`、:ref:`货币 <studio/fields/simple-fields/monetary>`) 可以被衡量。此外，:guilabel:`计数` 选项用于统计已过滤记录的总数。

选择要衡量的内容后，您可以根据要分析的维度，定义数据应如何 :ref:`分组 <search/group>`。默认情况下，数据通常按 *日期 > 月份* 分组，用于分析某个指标在几个月内的变化。

.. tip::
   当您过滤一个时间段时，系统会出现与另一个时间段进行对比的选项。

   .. image:: reporting/comparison.png
      :alt: 使用比较选项

.. example::

   .. tabs::

      .. tab:: 选择指标

         在其他指标中，您可以在销售分析报告中添加 :guilabel:`利润率` 和 :guilabel:`计数` 指标。默认情况下，已选择 :guilabel:`未税金额` 指标。

         .. image:: reporting/measures.png
            :alt: 在销售分析报告中选择不同的指标

      .. tab:: 分组指标

         您可以在前面的销售分析报告示例中按 :guilabel:`产品类别` 对指标进行分组。

         .. image:: reporting/single-group.png
            :alt: 在销售分析报告中添加分组

.. _reporting/using-pivot:

使用数据透视表视图
====================

数据分组是数据透视表视图的核心功能，它使您能够深入挖掘数据以获取更深入的见解。您可以使用 :guilabel:`按组` 选项快速在行级别添加分组，如上面的示例所示，您还可以点击行级别和列级别的 :guilabel:`总计` 标题旁边的加号按钮 (:guilabel:`➕`)，然后选择一个 **预配置分组**。要移除一个分组，点击减号按钮 (:guilabel:`➖`)。

添加一个分组后，您可以在相反的轴或新创建的子组中添加新的分组。

.. example::
   您可以在前面的销售分析报告示例中按列级别的 :guilabel:`销售人员` 分组，并按 :guilabel:`订单日期 > 月份` 对 :guilabel:`全部 / 可销售 / 办公家具` 产品类别进行分组。

   .. image:: reporting/multiple-groups.png
      :alt: 在销售分析报告中添加多个分组

.. tip::
   - 点击翻转轴按钮 (:guilabel:`⇄`) 切换行和列的分组。
   - 点击某个指标的标签，可以按升序 (⏶) 或降序 (⏷) 排序。
   - 点击下载按钮 (:guilabel:`⭳`)，可以下载数据透视表的 `.xlsx` 版本。

.. _reporting/using-graph:

使用图表视图
====================

图表视图中提供三种图表：柱状图、折线图和饼图。

**柱状图** 用于展示多个类别的分布或对比，特别适用于处理较大的数据集。

**折线图** 适用于展示时间序列的变化和趋势。

**饼图** 用于展示较少类别的分布或对比，适合形成一个有意义整体的情况。

.. tabs::

   .. tab:: 柱状图

      .. image:: reporting/bar.png
         :alt: 以柱状图查看销售分析报告

   .. tab:: 折线图

      .. image:: reporting/line.png
         :alt: 以折线图查看销售分析报告

   .. tab:: 饼图

      .. image:: reporting/pie.png
         :alt: 以饼图查看销售分析报告

.. tip::
   对于 **柱状图** 和 **折线图**，当您至少有两个分组时，可以使用堆叠选项，这时分组会叠加在一起，而不是并列显示。

   .. tabs::

      .. tab:: 堆叠柱状图

         .. image:: reporting/stacked-bar.png
            :alt: 堆叠柱状图示例

      .. tab:: 常规柱状图

         .. image:: reporting/non-stacked-bar.png
            :alt: 常规柱状图示例

      .. tab:: 堆叠折线图

         .. image:: reporting/stacked-line.png
            :alt: 堆叠折线图示例

      .. tab:: 常规折线图

         .. image:: reporting/non-stacked-line.png
            :alt: 常规折线图示例

   对于 **折线图**，您可以使用累计选项来累加数值，这对于展示某个时间段内的增长变化尤其有用。

   .. tabs::

      .. tab:: 累计折线图

         .. image:: reporting/cumulative.png
            :alt: 累计折线图示例

      .. tab:: 常规折线图

         .. image:: reporting/non-cumulative.png
            :alt: 常规折线图示例
