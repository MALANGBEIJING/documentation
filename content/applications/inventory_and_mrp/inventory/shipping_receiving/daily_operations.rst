:show-content:
:hide-page-toc:

==========================
入库与出库流程
==========================

仓库接收产品（收货）和发货（出货）的方式有多种。根据多个因素，例如存储和销售产品的类型、仓库规模以及每日确认的收货和发货订单数量，产品进出仓库的处理方式可能会有很大差异。收货和发货的设置可以不同步配置，它们不必具有相同的步骤数量。

.. seealso::
   - `使用路线 (eLearning 教程) <https://www.odoo.com/slides/slide/using-routes-1018>`_
   - `推送与拉取规则 (eLearning 教程)
     <https://www.odoo.com/slides/slide/push-pull-rules-1024>`_

选择合适的库存流来处理收货和发货
===================================================================

默认情况下，Odoo 以三种不同的方式处理收货和发货：一步、两步或三步。最简单的配置是一步，这是默认配置。每增加一个步骤，仓库的收货或发货流程就会增加一个额外的操作层，在产品被接收或发货之前需要执行。配置完全取决于存储产品的要求，例如对收到的产品进行质量检查，或在发货的产品上使用特殊包装。

一步流程
-------------

一步配置的收货和发货规则如下：

- **收货**：直接将产品接收到库存中。收货与库存之间没有中间步骤，例如转移到质量控制库位。
- **发货**：直接从库存中发货。库存与发货之间没有中间步骤，例如转移到包装库位。
- 仅在不使用 :abbr:`FIFO (先进先出)`、:abbr:`LIFO (后进先出)` 或 :abbr:`FEFO (先到期先出)` 移除策略时才能使用。
- 收货和/或发货处理速度快。
- 推荐用于库存水平较低的小型仓库以及非易腐物品。
- 产品直接从库存中接收或发货。

.. seealso::
   :doc:`daily_operations/receipts_delivery_one_step`

两步流程
-------------

两步配置的收货和发货规则如下：

- **输入库位 + 库存**：将产品先移至输入库位 *然后* 再移至库存。在入库之前，可以按不同的内部存储库位（如不同的货架、冷藏库和锁定区）对产品进行分类。
- **拣货 + 发货**：将产品先移至输出库位，然后再发货。在发货之前，可以按不同的承运人或装运码头对包裹进行分类。
- 最低要求使用批次号或序列号，按 :abbr:`FIFO (先进先出)`、:abbr:`LIFO (后进先出)` 或 :abbr:`FEFO (先到期先出)` 移除策略进行产品追踪。
- 推荐用于库存量大的大型仓库，或存储大件物品时（如床垫、大型家具、重型机械等）。
- 在产品转移至库存之前，接收的产品不可用于生产、发货等操作。

.. seealso::
   :doc:`daily_operations/receipts_delivery_two_steps`

三步流程
---------------

三步配置的收货和发货规则如下：

- **输入库位 + 质量检查 + 库存**：先将产品接收到输入库位，转移至质量控制区，检查合格后再移至库存。
- **拣货 + 包装 + 发货**：根据移除策略拣货，将产品在专用的包装区进行包装，然后移至输出库位发货。
- 可用于按批次号或序列号追踪产品，使用 :abbr:`FIFO (先进先出)`、:abbr:`LIFO (后进先出)` 或 :abbr:`FEFO (先到期先出)` 移除策略。
- 推荐用于库存水平极高的超大型仓库。
- 需要对任何仓库在接收产品入库之前执行质量控制检查。
- 在产品转移至库存之前，接收的产品不可用于生产、发货等操作。

.. seealso::
   - :doc:`daily_operations/receipts_three_steps`
   - :doc:`daily_operations/delivery_three_steps`

.. toctree::
   :titlesonly:

   daily_operations/use_routes
   daily_operations/receipts_delivery_one_step
   daily_operations/receipts_delivery_two_steps
   daily_operations/receipts_three_steps
   daily_operations/delivery_three_steps
   daily_operations/putaway
   daily_operations/storage_category
   daily_operations/cross_dock
   daily_operations/stock_warehouses
   daily_operations/owned_stock
   daily_operations/dropshipping
