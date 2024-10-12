==============================
制造产品配置
==============================

.. _manufacturing/management/configure-manufacturing-product:
.. |BOM| 替换:: :abbr:`BOM (材料清单)`

要在Odoo *制造* 模块中制造产品，必须正确配置产品。这包括启用*制造*路线并为产品配置材料清单（BOM）。一旦这些步骤完成，产品就可以在创建新的制造订单时进行选择。

激活制造路线
=================

制造路线需要在每个产品的产品页面上激活。要执行此操作，首先导航到：菜单选择 `制造 --> 产品 --> 产品`。然后，选择一个现有产品，或通过点击 :guilabel:`新建` 来创建一个新产品。

在产品页面上，选择 :guilabel:`库存` 选项卡，然后在 :guilabel:`路线` 部分中启用 :guilabel:`制造` 复选框。这告知Odoo该产品可以被制造。

.. image:: configure_manufacturing_product/manufacturing-route.png
   :align: center
   :alt: 产品页面库存选项卡中的制造路线。

.. _manufacturing/basic_setup/lot-serial-tracking:

批次/序列号跟踪
----------------

为新制造的产品分配批次号或序列号是可选的。要选择性地 :doc:`为新制造的产品分配批次号或序列号 <../../inventory/product_management/product_tracking/create_sn>`，请转到 :guilabel:`库存` 选项卡中的 :guilabel:`追溯` 部分。在 :guilabel:`追踪` 字段中，选择 :guilabel:`按唯一序列号` 或 :guilabel:`按批次`。

这样做会在制造订单中启用*批次/序列号*字段，或者在*车间管理*应用的工作订单卡片上启用*登记生产*指令。

.. figure:: configure_manufacturing_product/lot-number-field.png
   :align: center
   :alt: 制造订单中的“批次/序列号”字段。

   **制造订单**中的**批次/序列号**字段。

.. figure:: configure_manufacturing_product/register-production.png
   :align: center
   :alt: 工作订单卡片上的**登记生产**选项，用于生成批次/序列号。

   **工作订单卡片**上的**登记生产**选项，用于生成批次/序列号。

配置材料清单（BOM）
==================

接下来，必须为产品配置一个 |BOM|，以便Odoo知道如何制造该产品。|BOM| 是生产产品所需的组件和操作的列表。

要为特定产品创建一个 |BOM|，请导航到：菜单选择 `制造 --> 产品 --> 产品`，然后选择产品。在产品页面上，点击页面顶部的 :guilabel:`材料清单` 智能按钮，然后选择 :guilabel:`新建` 来配置新的 |BOM|。

.. image:: configure_manufacturing_product/bom-smart-button.png
   :align: center
   :alt: 产品页面上的材料清单智能按钮。

在 |BOM| 中，:guilabel:`产品` 字段会自动填充产品名称。在 :guilabel:`数量` 字段中，指定该材料清单可以生产的单位数量。

通过选择 :guilabel:`组件` 选项卡并点击 :guilabel:`添加一行` 来添加组件。从 :guilabel:`组件` 下拉菜单中选择一个组件，然后在 :guilabel:`数量` 字段中输入数量。继续在新行上添加组件，直到所有组件都已添加。

.. image:: configure_manufacturing_product/components-tab.png
   :align: center
   :alt: 材料清单的组件选项卡。

接下来，选择 :guilabel:`操作` 选项卡。点击 :guilabel:`添加一行`，将弹出一个 :guilabel:`创建操作` 弹出窗口。在 :guilabel:`操作` 字段中，指定要添加的操作名称（例如，装配、切割等）。从 :guilabel:`工作中心` 下拉菜单中选择执行操作的工作中心。最后，点击 :guilabel:`保存并关闭` 完成操作添加，或者点击 :guilabel:`保存并新建` 继续添加更多操作。

.. 重要::
   只有在启用了 :guilabel:`工作订单` 设置的情况下，:guilabel:`操作` 选项卡才会显示。要启用此功能，请导航到：菜单选择 `制造 --> 配置 --> 设置`，然后勾选 :guilabel:`工作订单` 复选框。

.. image:: configure_manufacturing_product/operations-tab.png
   :align: center
   :alt: 材料清单的操作选项卡。

.. admonition:: 了解更多

   上述部分提供了如何创建一个基本的 |BOM| 以在Odoo中制造产品的指示。然而，这并不是配置 |BOM| 时可用所有选项的详尽摘要。有关材料清单的更多信息，请参阅如何 :doc:`创建材料清单 <bill_configuration>` 的文档。
