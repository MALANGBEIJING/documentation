===============================
从另一个仓库补货
===============================

多个仓库的常见使用场景是拥有一个中央仓库，为多个商店补货。在这种情况下，每个商店被视为一个本地仓库。当商店想要补充产品时，产品会订购到中央仓库。Odoo 允许用户轻松设置哪些仓库可以为另一个仓库补货。

配置
=============

要从另一个仓库补货，首先进入 :menuselection:`库存 --> 配置 --> 设置 --> 仓库` 并启用 :guilabel:`多步骤路线`。然后点击 :guilabel:`保存` 应用设置。

.. image:: resupply_warehouses/virtual-warehouses-settings.png
   :align: center
   :alt: 在库存设置中启用多步骤路线。

通过导航至 :menuselection:`库存 --> 配置 --> 仓库` 查看所有已配置的仓库。

点击 :guilabel:`创建` 创建一个新仓库。然后给仓库起一个名称和一个 :guilabel:`简称`。最后点击 :guilabel:`保存` 完成仓库的创建。

之后，返回 :guilabel:`仓库` 页面并打开将由第二个仓库补货的仓库。接着点击 :guilabel:`编辑`。在 :guilabel:`仓库配置` 标签中，找到 :guilabel:`从...补货` 字段，并勾选第二个仓库的名称旁边的复选框。如果该仓库可以从多个仓库补货，请确保勾选这些仓库的复选框。最后点击 :guilabel:`保存` 应用设置。现在，Odoo 知道哪些仓库可以为这个仓库补货。

.. image:: resupply_warehouses/resupply-from-second-warehouse.png
   :align: center
   :alt: 在仓库配置标签中设置一个仓库通过另一个仓库补货。

为产品设置路线
======================

在配置了可以从哪些仓库补货后，现在所有产品表单上都提供了一个新的路线。在产品表单的 :guilabel:`库存` 标签下，新的路线显示为 :guilabel:`从[仓库名称]补充产品`。使用 :guilabel:`从[仓库名称]补充产品` 路线与补货规则或按订单生产 (MTO) 路线一起补充库存，将产品从一个仓库转移到另一个仓库。

.. image:: resupply_warehouses/product-resupply-route-settings.png
   :align: center
   :alt: 启用从第二个仓库补货产品的路线设置。

当产品的补货规则被触发并且产品设置了 :guilabel:`从[仓库名称]补充产品` 路线时，Odoo 会自动创建两个拣货任务。一个拣货任务是来自第二个仓库的 *发货单*，包含所有必需的产品，另一个拣货任务是为主仓库接收相同产品的 *收货单*。产品从第二个仓库到主仓库的移动在 Odoo 中被完全追踪。

在 Odoo 创建的拣货/转移记录中，:guilabel:`来源单据` 是产品的补货规则。发货单和收货单之间的位置是一个中转库位。

.. image:: resupply_warehouses/resupply-receipts-from-reordering-rule.png
   :align: center
   :alt: 补货规则自动创建两份仓库间的库存收货单。

.. image:: resupply_warehouses/second-warehouse-delivery-order.png
   :align: center
   :alt: 用于通过另一个仓库补充一个仓库库存的发货单。

.. image:: resupply_warehouses/second-warehouse-stock-receipt.png
   :align: center
   :alt: 一个仓库接收另一个仓库库存的收货单。
