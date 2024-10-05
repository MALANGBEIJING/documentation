:show-content:
:hide-page-toc:

================
产品追踪
================

*批次* 和 *序列号* 是 Odoo 中识别和追踪产品的两种方式。虽然这两种可追溯性方法有相似之处，但它们在收货、发货和库存报告方面存在显著差异。

*批次* 通常指收到的、当前存储的或从仓库发货的特定批次物品。然而，它也可以指内部制造的一批产品。

*序列号* 是分配给物品或产品的唯一标识符，用于将其与其他物品或产品区分开来，通常按顺序或递增分配。

.. seealso::
   - :doc:`product_tracking/lots`
   - :doc:`product_tracking/serial_numbers`

启用批次与序列号
============================

要使用批次和序列号追踪产品，必须启用 *批次与序列号* 功能。

为此，请转到 :menuselection:`库存应用 --> 配置 --> 设置`，滚动到 :guilabel:`可追溯性` 部分，并勾选 :guilabel:`批次与序列号` 旁的框。然后，点击 :guilabel:`保存` 按钮保存更改。

.. image:: product_tracking/product_tracking/differences-enabled-setting.png
   :align: center
   :alt: 在库存设置中启用批次与序列号功能。

何时使用批次
================

批次适用于大量制造或接收的产品，如衣物或食品。批次可用于追溯产品至一组物品，这在管理产品召回或保质期时尤为有用。

.. example::
   .. image:: product_tracking/product_tracking/differences-lot.png
      :align: center
      :alt: 创建的批次及其内的产品数量。

制造商为具有共同属性的产品组分配批次号；这可能导致多个商品共享相同的批次号。这有助于识别同一组中的产品，并允许这些产品在生命周期的各个阶段进行端到端的追踪。

何时使用序列号
==========================

为单个产品分配序列号的目的是确保每个物品在供应链中移动时其历史是可识别的。这对提供与其销售和交付的产品相关的售后服务的制造商尤其有用。

.. example::
   .. image:: product_tracking/product_tracking/differences-serial-numbers.png
      :align: center
      :alt: 产品的序列号列表。

序列号可以包含多种字符类型：数字、字母、符号，或三者混合使用。

可追溯性
============

制造商和公司可以参考可追溯性报告，查看产品的整个生命周期。这些报告包括关键信息，例如产品的来源、存储地点和发送给谁的详细信息。

要查看产品的完整可追溯性，或按批次和/或序列号对产品进行分组，请前往 :menuselection:`库存应用 --> 产品 --> 批次/序列号`。这样将显示 :guilabel:`批次/序列号` 仪表板。

从这里开始，默认情况下会列出分配了批次或序列号的产品。还可以展开以显示具体分配给它们的批次或序列号。

要按批次或序列号分组，请首先删除右上角搜索栏中的任何默认筛选器。然后，点击 :guilabel:`分组依据`，选择 :guilabel:`添加自定义分组`，这将显示一个小型下拉菜单。从此小型下拉菜单中选择 :guilabel:`批次/序列号`，然后点击 :guilabel:`应用`。

这样会显示所有现有的批次和序列号，并且每个批次或序列号都可以展开，显示具有该分配号的所有产品数量。对于不重复使用的唯一序列号，每个序列号下应该只有一个产品。

.. image:: product_tracking/product_tracking/differences-tracking.png
   :align: center
   :alt: 报告页面，带有批次和序列号的下拉列表。

.. tip::
   若要了解某个特定批次号或序列号的更多信息，点击批次号或序列号的行项目，显示该特定号的 :guilabel:`批次` 或 :guilabel:`序列号` 表单。在此表单中，点击 :guilabel:`库位` 和 :guilabel:`可追溯性` 智能按钮，可以查看使用该序列号的所有现有库存。使用该批次或序列号进行的任何操作都可以在这里找到。

.. toctree::
   :titlesonly:

   product_tracking/serial_numbers
   product_tracking/create_sn
   product_tracking/lots
   product_tracking/reassign
   product_tracking/expiration_dates
