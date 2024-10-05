:show-content:
:hide-page-toc:

==================
移除策略
==================

对于拥有仓库的公司，*移除策略* 决定 **从仓库中提取哪些产品**，以及 **何时提取**。例如，对于易腐产品，优先选择最接近保质期的商品有助于减少食品浪费。

下表列出了 Odoo 中可用的移除策略，并详细说明了如何确定拣货顺序。利用这些移除策略，让 Odoo 自动选择订单中的产品：

.. list-table::
   :header-rows: 1
   :stub-columns: 1

   * -
     - :doc:`先进先出 (FIFO) <removal_strategies/fifo>`
     - :doc:`后进先出 (LIFO) <removal_strategies/lifo>`
     - :doc:`先到期先出 (FEFO) <removal_strategies/fefo>`
     - :doc:`最近库位 <removal_strategies/closest_location>`
     - :doc:`最少包裹数 <removal_strategies/least_packages>`
   * - 基于
     - :ref:`到货日期 <inventory/warehouses_storage/arrival_date>`
     - :ref:`到货日期 <inventory/warehouses_storage/arrival_date>`
     - :ref:`移除日期 <inventory/warehouses_storage/removal-date>`
     - :ref:`库位顺序 <inventory/warehouses_storage/sequence>`
     - :ref:`包裹数量 <inventory/warehouses_storage/pkg-qty>`
   * - 选择顺序
     - 先进先出
     - 后进先出
     - :ref:`先到期先出 <inventory/warehouses_storage/exp-date>`
     - :ref:`按库位字母顺序 <inventory/warehouses_storage/location-name>`
     - 按最接近需求的数量

有关如何使用每种移除策略的详细示例，请参阅各个文档页面。

.. _inventory/warehouses_storage/removal-config:

配置
=============

移除策略可以在产品类别或存储库位上进行设置。

.. image:: removal_strategies/removal/navigate-location-category.png
   :align: center
   :alt: 更改产品类别或库位的强制移除策略。

要在库位上配置移除策略，请前往 :menuselection:`库存 --> 配置 --> 库位`，并选择所需的库位。在库位表单中，从 :guilabel:`移除策略` 字段的下拉菜单中选择一种移除策略。

.. important::
   要在库位上设置移除策略，必须在 :menuselection:`库存 --> 配置 --> 设置` 中启用 :guilabel:`存储库位` 和 :guilabel:`多步骤路线` 设置。

   这些功能 **仅在** 设置库位的移除策略时才需要。

要在产品类别上配置移除策略，请前往 :menuselection:`库存 --> 配置 --> 产品类别` 并选择预期的产品类别。接着，从 :guilabel:`强制移除策略` 下拉菜单中选择一种移除策略。

.. important::
   当某个产品同时在库位和产品类别上应用了不同的移除策略时，:guilabel:`产品类别` 表单中的 :guilabel:`强制移除策略` 字段中设置的值优先应用。

必需功能
=================

虽然某些移除策略是默认可用的，但要使移除策略选项出现在 :guilabel:`强制移除策略` 或 :guilabel:`移除策略` 字段的下拉菜单中，必须在 :menuselection:`库存 --> 配置 --> 设置` 中启用某些附加功能。

请参阅下表，了解所需功能的摘要。有关详细要求和使用的更多信息，请参阅每个移除策略的专用章节。

.. list-table::
   :header-rows: 1
   :stub-columns: 1

   * -
     - 先进先出 (FIFO)
     - 后进先出 (LIFO)
     - 先到期先出 (FEFO)
     - 最近库位
     - 最少包裹数
   * - 所需功能
     - 批次号和序列号
     - 批次号和序列号
     - 批次号和序列号、到期日期
     - 存储库位、多步骤路线
     - 包裹

.. _inventory/warehouses_storage/lots-setup:

批次号和序列号
-----------------------

批次号和序列号用于区分相同产品，并追踪到货日期或到期日期等信息。要启用此功能，请导航到 :menuselection:`库存 --> 配置 --> 设置`。在 :guilabel:`可追溯性` 标题下，勾选 :guilabel:`批次号和序列号` 旁的框以启用该功能。

.. image:: removal_strategies/removal/enable-lots.png
   :align: center
   :alt: 启用批次号和序列号。

接着，确保预期产品通过批次号或序列号进行追踪。为此，请通过 :menuselection:`库存 --> 产品 --> 产品` 导航到产品表单，并选择所需产品。在产品表单中，切换到 :guilabel:`库存` 标签，在 :guilabel:`追踪` 字段下，选择 :guilabel:`按唯一序列号` 或 :guilabel:`按批次号` 选项。

启用功能后，可以通过 :doc:`库存调整 <../warehouses_storage/inventory_management/count_products>` 或在 :ref:`产品接收时 <inventory/product_management/assign-lots>` 为产品分配批次号或序列号。

库位与路线
--------------------

**存储库位** 和 **多步骤路线** 是在库位上设置 **所有** 类型的移除策略所必需的功能。然而，最近库位移除策略特别需要这些功能，因为它仅适用于库位级别。

要启用这些功能，请导航到 :menuselection:`库存 --> 配置 --> 设置`。在 :guilabel:`仓库` 标题下，启用 :guilabel:`存储库位` 和 :guilabel:`多步骤路线` 功能。

.. image:: removal_strategies/removal/enable-location.png
   :align: center
   :alt: 启用库位和路线功能。

.. _inventory/warehouses_storage/exp-setup:

到期日期
---------------

启用 **到期日期** 功能以追踪批次号或序列号的到期日期、最佳食用日期、移除日期和警报日期，方法是导航到 :menuselection:`库存 --> 配置 --> 设置`。

在 :guilabel:`可追溯性` 标题下，确保选择了 :guilabel:`批次号和序列号` 功能，然后勾选 :guilabel:`到期日期` 旁的复选框以启用该功能。

.. image:: removal_strategies/removal/enable-expiration.png
   :align: center
   :alt: 为 FEFO 启用到期日期功能。

.. _inventory/warehouses_storage/pack-setup:

包裹
--------

*包裹* 功能用于将产品分组，是最少包裹数移除策略的必需功能。

导航到 :menuselection:`库存 --> 配置 --> 设置` 并勾选 :guilabel:`包裹` 功能旁的复选框。

.. image:: removal_strategies/removal/enable-pack.png
   :align: center
   :alt: 启用包裹功能。

.. seealso::
   - :doc:`包裹 <../product_management/configure/package>`
   - :doc:`两步发货 <daily_operations/receipts_delivery_two_steps>`
   - :doc:`三步发货 <daily_operations/delivery_three_steps>`

.. toctree::
   :titlesonly:

   removal_strategies/fifo
   removal_strategies/lifo
   removal_strategies/fefo
   removal_strategies/closest_location
   removal_strategies/least_packages
