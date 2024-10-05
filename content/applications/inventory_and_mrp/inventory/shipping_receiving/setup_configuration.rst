:show-content:
:hide-page-toc:

================
配送方法
================

在 Odoo 中激活 *配送方法* 设置后，可以在销售订单和电子商务购物车中添加计算运费的选项。

当与 :ref:`第三方承运商 <inventory/shipping/third_party>` 集成时，运费根据承运商的定价和包装信息进行计算。

.. seealso::
   - :ref:`第三方承运商设置 <inventory/shipping/third_party>`
   - `Odoo 教程：配送价格
     <https://www.odoo.com/slides/slide/delivery-prices-613?fullscreen=1>`_

配置
=============

要在销售订单和电子商务中计算运费，必须安装 *配送成本* 模块。为此，请从 Odoo 主仪表盘导航到 :menuselection:`应用` 应用程序。

然后，移除 :guilabel:`应用` 筛选器，并在 :guilabel:`搜索...` 栏中输入 `配送成本`。找到 :guilabel:`配送成本` 模块后，点击 :guilabel:`激活` 进行安装。

.. image:: setup_configuration/setup_configuration/install-module.png
   :align: center
   :alt: 安装配送成本模块。

添加配送方式
===================

要配置配送方式，请前往 :menuselection:`库存应用 --> 配置 --> 配送方式`。

.. note::
   如果 :guilabel:`配送方式` 选项未出现在 :guilabel:`配置` 下拉菜单中，请按照以下步骤验证该功能是否启用：

   #. 前往 :menuselection:`库存应用 --> 配置 --> 设置`。
   #. 向下滚动到 :guilabel:`配送` 部分，勾选 :guilabel:`配送方法` 旁的复选框以启用此功能。

   .. image:: setup_configuration/setup_configuration/enable-delivery.png
      :align: center
      :alt: 在配置 > 设置中启用 *配送方法* 功能。

在 :guilabel:`配送方式` 页面上，点击 :guilabel:`新建` 添加新配送方式。这样将打开一个表单，用于提供关于配送提供商的详细信息，包括：

- :guilabel:`配送方式` (*必填字段*): 配送方式的名称（例如 `固定运费`、`当天送达` 等）。
- :guilabel:`提供商` (*必填字段*): 选择配送服务，例如 Fedex（如果使用 :ref:`第三方承运商 <inventory/shipping/third_party>`）。确保已正确安装与承运商的集成，并从下拉菜单中选择提供商。

  有关配置自定义配送方式的更多详细信息，例如 :ref:`固定价格 <inventory/shipping/fixed>`、:ref:`基于规则 <inventory/shipping/rules>` 或 :ref:`店内取货 <inventory/shipping/pickup>` 选项，请参阅下方的相关章节。
- :guilabel:`网站`: 为电子商务页面配置配送方式。从下拉菜单中选择适用的网站，或将其留空以将方法应用于所有网页。
- :guilabel:`公司`: 如果配送方式应适用于特定公司，请从下拉菜单中选择该公司。将此字段留空以将方法应用于所有公司。
- :guilabel:`路线`: 选择适用的路线，以定义不同的配送方式，例如根据不同的交货时间进行标准或快递配送。有关更多信息，请跳转至 :ref:`设置配送方式的路线 <inventory/shipping_receiving/shipping-route>` 部分。

.. _inventory/shipping_receiving/delivery-product:

- :guilabel:`配送产品` (*必填字段*): 在 :ref:`销售订单行 <inventory/shipping/sales-order>` 中列为配送费用的产品。
- :guilabel:`如果订单金额超过则免费`: 勾选此框可在客户消费超过指定金额时启用免运费。

有关如何配置特定配送方式的示例，请参阅下方章节。

.. _inventory/shipping/fixed:

固定价格
-----------

要为所有订单配置相同的运费，请前往 :menuselection:`库存应用 --> 配置 --> 配送方式`。然后，点击 :guilabel:`新建`，在配送方式表单中，将 :guilabel:`提供商` 设置为 :guilabel:`固定价格` 选项。选择此选项后，将可用 :guilabel:`固定价格` 字段，在该字段中定义固定运费金额。

如果订单金额超过指定金额时启用免运费，请勾选 :guilabel:`如果订单金额超过则免费` 并填写金额。

.. example::
   要设置 `$20` 的固定运费，并在客户消费超过 `$100` 时免运费，请填写以下字段：

   - :guilabel:`配送方式`: `固定运费`
   - :guilabel:`提供商`: :guilabel:`固定价格`
   - :guilabel:`固定价格`: `$20.00`
   - :guilabel:`如果订单金额超过则免费`: `$100.00`
   - :guilabel:`配送产品`: `[SHIP] Flat`

   .. image:: setup_configuration/setup_configuration/new-shipping-method.png
      :align: center
      :alt: 填写配送方式示例。

.. _inventory/shipping/rules:

基于规则
--------------

要根据定价规则计算运费，将 :guilabel:`提供商` 字段设置为 :guilabel:`基于规则` 选项。可选择调整 :guilabel:`费率加价` 和 :guilabel:`附加费用` 以包括额外的运费。

创建定价规则
~~~~~~~~~~~~~~~~~~~~

导航到 :guilabel:`定价` 选项卡，点击 :guilabel:`添加一行`。此操作将打开 :guilabel:`创建定价规则` 窗口，其中根据产品的重量、体积、价格或数量与定义的金额进行比较，以计算 :guilabel:`配送费用`。

完成后，点击 :guilabel:`保存并新建` 以添加另一条规则，或点击 :guilabel:`保存并关闭`。

.. example::
   如果要为五个或更少产品的订单收取 $20 的运费，请将 :guilabel:`条件` 设置为 `数量 <= 5.00`，并将 :guilabel:`配送费用` 设置为 `$20`。

   .. image:: setup_configuration/setup_configuration/pricing-rule.png
      :align: center
      :alt: 显示添加定价规则的窗口。设置条件和配送费用。

要在电子商务网站上限制配送到特定目的地，请在配送方式表单中导航到 :guilabel:`目的地可用性` 选项卡，并定义 :guilabel:`国家`、:guilabel:`州` 和 :guilabel:`邮编前缀`。如果适用于所有位置，请将这些字段留空。

计算配送费用
~~~~~~~~~~~~~~~~~~~~~~~

运费是满足 :guilabel:`条件` 的规则中指定的 :guilabel:`配送费用`，再加上 :guilabel:`费率加价` 和 :guilabel:`附加费用` 的任何额外费用。

.. math::
   总计 = 规则的~配送费用 + (费率加价 \times 规则的~配送费用) + 附加费用

.. example::
   设置以下两条规则：

   #. 如果订单包含五个或更少的产品，运费为 $20
   #. 如果订单包含超过五个产品，运费为 $50。

   :guilabel:`费率加价` 为 `10%`，且 :guilabel:`附加费用` 为 `$9.00`。

   .. image:: setup_configuration/setup_configuration/delivery-cost-example.png
      :align: center
      :alt: 显示“基于规则”的配送方式及其设置的加价示例。

   当应用第一条规则时，运费为 $31 (20 + (0.1 * 20) + 9)。当应用第二条规则时，运费为 $64 (50 + (0.1 * 50) + 9)。

.. _inventory/shipping/pickup:

店内取货
---------------

要配置店内取货，请在 :guilabel:`提供商` 字段中选择 :guilabel:`店内取货`，并在 :guilabel:`仓库` 字段中指定取货地点。

要为客户开具运送至取货地点的运费发票，请在 :guilabel:`集成级别` 字段中选择 :guilabel:`获取费率并创建发货` 选项。然后，在 :guilabel:`开票政策` 字段中选择 :guilabel:`预计成本` 或 :guilabel:`实际成本` 单选选项，以决定在销售订单上添加的运费是承运商的确切费用。

.. seealso::
   :doc:`运费发票 <setup_configuration/invoicing>`

.. _inventory/shipping_receiving/shipping-route:

配送方式的路线
------------------------

可选择为配送方式配置不同的仓库配送流程，通过为其配置不同的 :doc:`路线 <daily_operations/use_routes>`。

.. example::
   配置每个配送方式的多个路线有助于根据以下条件调整仓库配送流程：

   - 速度（例如，使用 :doc:`一步交货 <daily_operations/receipts_delivery_one_step>` 进行快递配送，或使用 :doc:`两步交货 <daily_operations/receipts_delivery_two_steps>` 进行标准配送）。
   - 国际配送（例如，使用 :doc:`三步交货 <daily_operations/delivery_three_steps>` 来准备海关文件）
   - 店内取货或送货上门：根据客户选择，从中央仓库发货，或从商店库存中提货。

要设置路线，请前往 :menuselection:`库存应用 --> 配置 --> 路线`。点击 :guilabel:`新建`，或选择所需路线。

在路线表单中，找到 :guilabel:`适用于` 部分，勾选 :guilabel:`配送方式` 复选框。

.. figure:: setup_configuration/setup_configuration/shipping-route.png
   :align: center
   :alt: 路线表单中勾选“配送方式”复选框。

   路线表单中勾选“配送方式”复选框。

然后，前往 :menuselection:`库存应用 --> 配置 --> 配送方式`，并选择所需的配送方式。

在配送方式表单中，在 :guilabel:`路线` 字段中，从下拉菜单中选择可用的履行路线。

.. note::
   如果所需路线不可选择，请检查该路线的 *适用于* 部分是否启用了 *配送方式* 选项。

.. figure:: setup_configuration/setup_configuration/set-routes.png
   :align: center
   :alt: 显示配送方式表单上的路线设置。

   默认情况下，大多数配送方式会创建两个可用于标准或快递配送的路线。

.. _inventory/shipping/sales-order:

添加配送
============

配送方式可以作为配送产品添加到销售订单中，并显示为单独的订单行项。首先，通过前往 :menuselection:`销售应用 --> 订单 --> 订单` 导航到所需的销售订单。

在销售订单上，点击 :guilabel:`添加配送` 按钮，这将打开 :guilabel:`添加配送方式` 弹出窗口。然后，从列表中选择 :guilabel:`配送方式`。

根据产品重量（在每个产品表单的 :guilabel:`库存` 标签中定义），:guilabel:`订单总重量` 会自动填写。编辑该字段以指定确切重量，然后点击 :guilabel:`添加` 以添加配送方式。

.. note::
   定义在 :guilabel:`订单总重量` 中的金额将覆盖在产品表单中定义的产品总重量。

运费作为 *销售订单行* 中的 :guilabel:`配送产品` 添加，详细信息在配送方式表单中。

.. example::
   `家具配送`，一项固定费用为 `$200` 的配送产品，已添加到销售订单 `S00088`。

     .. image:: setup_configuration/setup_configuration/delivery-product.png
        :align: center
        :alt: 显示销售订单行中的配送订单。

配送订单
--------------

添加到销售订单的配送方式与配送订单上的承运商详细信息相关联。要在配送单本身上添加或更改配送方式，请前往 :guilabel:`附加信息` 标签并修改 :guilabel:`承运商` 字段。

.. image:: setup_configuration/setup_configuration/delivery-order.png
   :align: center
   :alt: 配送单表单上的承运商信息。

.. toctree::
   :titlesonly:

   setup_configuration/third_party_shipper
   setup_configuration/labels
   setup_configuration/bpost
   setup_configuration/dhl_credentials
   setup_configuration/fedex
   setup_configuration/sendcloud_shipping
   setup_configuration/starshipit_shipping
   setup_configuration/ups_credentials
   setup_configuration/zebra
   setup_configuration/cancel
   setup_configuration/invoicing
   setup_configuration/label_type
   setup_configuration/multipack
   setup_configuration/print_on_validation
