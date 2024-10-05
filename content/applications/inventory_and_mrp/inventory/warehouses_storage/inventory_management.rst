:show-content:
:hide-page-toc:

====================
库存管理
====================

在 Odoo 的 *库存* 应用中，:doc:`仓库 <inventory_management/warehouses>` 处理跨不同物理地点的库存组织和分配，而 :doc:`库位 <inventory_management/use_locations>` 则为每个仓库内的物品管理提供了更详细的分解，以提高管理效率。

本文档作为掌握 *库存* 所需术语和概念的介绍。有关如何操作的具体说明和示例，请参阅各个文档页面。

.. seealso::
   `Odoo 教程: 仓库与库位 <https://www.youtube.com/watch?v=zMvudZVLuUo>`_

仓库
==========

:doc:`仓库 <inventory_management/warehouses>` 表示一个物理地点，具有实际地址，用于存储公司的物品。

在仓库中配置 :doc:`路线 <../shipping_receiving/daily_operations/use_routes>`，以控制产品如何向客户发货、从供应商处接收、在仓库内移动，或 :doc:`在仓库之间移动 <replenishment/resupply_warehouses>`。

库位
=========

:doc:`库位 <inventory_management/use_locations>` 是指仓库中的特定区域，例如货架、地板或走道。这些是仓库内的细分，且是该仓库独有的。用户可以在一个仓库内创建和管理多个库位，以更精确地组织库存。

.. seealso::
   - :doc:`inventory_management/use_locations`
   - :doc:`inventory_management/count_products`
   - :doc:`inventory_management/cycle_counts`
   - :doc:`inventory_management/scrap_inventory`

.. _inventory/warehouses_storage/location-type:

库位类型
--------------

Odoo 中的 *库位类型* 帮助分类和管理产品所在的位置，以及需要对其采取的操作。默认情况下，在 :menuselection:`库存应用 --> 配置 --> 库位` 页面上，只显示内部库位。

要查看 Odoo 中的七种库位类型，请选择任意库位，并在 :guilabel:`库位类型` 字段中显示以下选项：

- :guilabel:`供应商库位`：定义从供应商处购买的产品来源区域。此处的物品 **不** 计入库存。

- :guilabel:`视图`：用于组织和构建仓库层次结构。例如，视图库位 `WH`（仓库缩写）将所有内部库位分组，如 `库存`、接收码头、质量检查点和包装区域，表明它们都属于同一仓库。

  .. important::
     视图库位不应包含产品，但可以将产品移入该库位。

- :guilabel:`内部库位`：仓库内的存储库位。存放在这些库位中的物品在 :doc:`库存估值 <../product_management/inventory_valuation/using_inventory_valuation>` 中记账。

- :guilabel:`客户库位`：用于追踪已售出的产品；此处的物品不再计入库存。

- :guilabel:`库存损失`：用于消耗丢失物品或创建库存，以记录差异。

  在 Odoo 中，库存损失库位的示例包括 *库存调整*，用于在库存盘点期间记录差异，以及 *报废*，用于将损坏的物品送至该位置，以记录库存损失。

   .. example::
      `虚拟库位/库存调整` 是一种库位类型为 :guilabel:`库存损失` 的库位。数据库显示 `WH/库存` 中有 `65` 单位，但库存检查显示为 `60`。为了更正数量，将五个单位从 `WH/库存` 移动到 `虚拟库位/库存调整`。

      .. image:: inventory_management/inventory_management/inventory-loss.png
         :align: center
         :alt: 产品最终移至虚拟库位/库存调整。

- :guilabel:`生产`：原材料被消耗且 :doc:`成品 <../../manufacturing>` 被制造的地方。

- :guilabel:`运输库位`：用于跨公司或跨仓库操作，追踪在不同地址之间运输的产品，例如 :ref:`物理库位/仓库间运输 <inventory/warehouses_storage/interwarehouse-transit>`。

.. image:: inventory_management/inventory_management/locations.png
   :align: center
   :alt: Odoo 中的库位列表。

.. note::
   在 Odoo 中，库位类型按颜色编码：
     - **红色**：内部库位
     - **蓝色**：视图库位
     - **黑色**：外部库位（包括库存损失、供应商和客户库位）。

Odoo 中的视图库位
----------------------

Odoo 数据库包括预先配置的视图库位，以组织库位层次结构。这些提供了有用的上下文，并区分内部和外部库位。

- *物理库位* 作为外部库位的上层结构，而不会改变产品的库存价值。（当产品从内部库位移至外部库位时，库存估值发生变化）。

.. _inventory/warehouses_storage/interwarehouse-transit:

  .. example::
     在仓库 `WH` 和 `WH2` 中移动产品时，物品不在任一仓库中，但仍属于公司。当产品处于运输过程中时，它们被放置在 `仓库间运输` 库位中，这是一个 :guilabel:`运输库位` 类型。

     此库位位于视图库位 `物理库位` 之下，表示 `仓库间运输` 位于仓库之外，但仍属于公司。这样不会影响产品的库存估值。

- *合作伙伴库位* 将客户和供应商库位（外部库位）分组。转移到这些库位会影响库存估值。
- *虚拟库位* 是指物理上不存在的库位，但可以将不在库存中的物品放置在那里。这些物品可能由于丢失或其他原因不再计入库存。

.. toctree::
   :titlesonly:

   inventory_management/warehouses
   inventory_management/use_locations
   inventory_management/count_products
   inventory_management/cycle_counts
   inventory_management/scrap_inventory
