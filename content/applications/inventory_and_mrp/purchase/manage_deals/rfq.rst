======================
询价单请求
======================

.. _purchase/manage_deals/rfq:

.. |PO| replace:: :abbr:`PO (采购订单)`
.. |POs| replace:: :abbr:`POs (采购订单)`
.. |RFQ| replace:: :abbr:`RFQ (询价单)`
.. |RFQs| replace:: :abbr:`RFQs (询价单)`

Odoo 的询价单请求 (RFQs) 功能位于**采购**应用中，标准化了从多个供应商处以不同价格和交货时间订购产品的流程。

|RFQs| 是公司发送给供应商的文件，用于请求产品的价格信息。在 Odoo 中，一旦供应商批准了 |RFQ|，采购订单 (PO) 将被确认，以对齐交货时间和价格。

配置
=============

产品
-------

要在 |RFQ| 上自动填充产品信息和价格，请通过导航到 :menuselection:`采购应用 --> 产品 --> 产品` 配置产品。选择一个现有产品，或通过点击 :guilabel:`新建` 创建一个新产品。这将打开产品表单，可以在其中配置各种销售和采购数据。

要配置可采购的产品，请勾选产品名称下的 :guilabel:`可采购` 复选框。然后，转到 :guilabel:`库存` 选项卡，并启用 :guilabel:`采购` 路线。

.. image:: rfq/product-vendor-pricelist-config.png
   :align: center
   :alt: 可采购产品所需的配置。

供应商价格表
----------------

在产品表单的 :guilabel:`采购` 选项卡中，输入供应商及其价格，以便每次列出该产品时此信息都能自动填充到 |RFQ| 中。

默认列包括 :guilabel:`数量`、:guilabel:`价格` 和 :guilabel:`交货时间`，但其他列如 :guilabel:`产品变体` 或 :guilabel:`折扣` 也可以启用。

要启用或禁用列，请点击标题行右侧的 :icon:`oi-settings-adjust` :guilabel:`(更多选项)` 图标，以显示可添加（或删除）列的下拉菜单。

.. note::
   另外，还可以通过导航到 :menuselection:`采购应用 --> 配置 --> 供应商价格表`，批量添加现有产品的价格和交货时间。点击左上角的 :guilabel:`新建`。在价格表表单的 :guilabel:`供应商` 部分，添加与供应商相关的产品信息。

订购产品
==============

配置好产品和价格后，按照以下步骤创建并发送 |RFQs| 以进行公司采购。

|RFQ| 仪表板
---------------

首先，导航到 :menuselection:`采购应用 --> 订单 --> 询价单请求`。

:guilabel:`询价单请求` 仪表板显示了公司的 |RFQs|、|POs| 及其状态的概览。屏幕顶部按状态总结了公司的所有 |RFQs| 以及个人（作为买家）的 |RFQs|。

屏幕右上角还提供了公司的近期采购总金额、交货时间和发送的 |RFQs| 数量的快速报告。

此外，仪表板还包括以下按钮：

- :guilabel:`待发送`: 处于 |RFQ| 阶段且尚未发送给供应商的订单。
- :guilabel:`等待中`: 已通过电子邮件发送的 |RFQs|，正在等待供应商确认。
- :guilabel:`逾期`: |RFQs| 或 |POs| 的 :guilabel:`订单截止日期` 已过。

.. image:: rfq/rfq-dashboard.png
   :align: center
   :alt: 包含订单和订单状态的询价单仪表板。

除了各种视图选项外，:guilabel:`询价单请求` 仪表板还提供了可通过搜索栏下拉菜单访问的 :guilabel:`过滤器` 和 :guilabel:`分组依据` 选项。

.. seealso::
   :doc:`../../../essentials/search`

创建新 |RFQ|
----------------

要创建新的 |RFQ|，请点击 :guilabel:`新建` 按钮，位于 :guilabel:`询价单请求` 仪表板的左上角，以显示新的 |PO| 表单。

首先，指定一个 :guilabel:`供应商`。

:guilabel:`供应商参考` 字段指向供应商发送的销售和交货订单编号。当收到产品并需要将采购订单与交货订单匹配时，这非常方便。

:guilabel:`长期订单` 字段是指以固定价格订购的长期采购协议。要查看和配置长期订单，请前往 :menuselection:`采购应用 --> 订单 --> 采购协议`。

如果从其他国家的供应商处采购产品，可以更改 :guilabel:`货币`。

接下来，配置 :guilabel:`订单截止日期`，即供应商必须确认其同意供应产品的日期。

.. note::
   在 :guilabel:`订单截止日期` 过期后，|RFQ| 会被标记为逾期，但产品仍然可以订购。

:guilabel:`预计到货` 是基于 :guilabel:`订单截止日期` 和供应商交货时间自动计算的。勾选 :guilabel:`请求确认` 复选框，以要求在交货时签字确认。

如果启用了 :doc:`使用存储位置功能
<../../inventory/warehouses_storage/inventory_management/use_locations>`，
则会显示 :guilabel:`交货到` 字段，提供订单发货选项。

在这里选择收货仓库地址，或选择 :guilabel:`直运`，表示此订单将直接送到最终客户处。选择 :guilabel:`直运` 后，将启用 :guilabel:`直运地址` 字段。联系人信息会从**联系人**应用自动填充到这里。

产品选项卡
~~~~~~~~~~~~

在 :guilabel:`产品` 选项卡中，添加要订购的产品。点击 :guilabel:`添加产品`，输入产品名称，或从下拉菜单中选择该产品。

要创建新产品并添加，输入新产品名称，然后从下拉菜单中选择 :guilabel:`创建 [产品名称]`，并手动添加单价。或者，选择 :guilabel:`创建并编辑...`，以便进入该新产品的产品表单。

还可以选择 :guilabel:`目录` 以导航到所选供应商的产品菜单。从这里可以将产品添加到购物车。

.. note::
   要对产品和价格进行调整，请通过点击悬停在 :guilabel:`产品` 名称上的 :icon:`oi-arrow-right` :guilabel:`(右箭头)` 图标访问产品表单。

发送 |RFQ|
----------

点击 :guilabel:`通过电子邮件发送` 会显示一个 :guilabel:`撰写邮件` 弹出窗口，加载了 :guilabel:`采购: 询价单请求` 模板，准备发送到供应商的电子邮件地址（在**联系人**应用中配置）。

撰写好所需的消息后，点击 :guilabel:`发送`。发送后，|RFQ| 进入 :guilabel:`询价单已发送` 阶段。

点击 :guilabel:`打印询价单` 会下载 |RFQ| 的 PDF 文件。

确认订单
-------------

点击 :guilabel:`确认订单` 可以将 |RFQ| 直接转换为有效的 |PO|。

.. tip::
   Odoo 通过采购订单 (PO) 表单的消息功能跟踪每个订单的通信。这显示了用户与联系人之间发送的电子邮件，以及任何内部备注和活动。也可以在消息功能中记录消息、备注和活动。

一旦确认了 |RFQ|，它将创建一个 |PO|。

在新的 |PO| 上，:guilabel:`订单截止日期` 字段更改为 :guilabel:`确认日期`，显示用户确认订单的日期和时间。

根据用户在**采购**应用设置中选择的配置，一旦产品已订购或收到，将创建*供应商账单*。有关详细信息，请参阅 :doc:`管理供应商账单 <manage>`。

.. note::
   下订单后，点击 :guilabel:`接收产品` 可记录新产品的接收情况，并将其录入数据库。

.. note::
   安装了**库存**应用后，确认 |PO| 将自动创建一份收货单，产品信息和预计到货日期将自动填充。

.. seealso::
   :doc:`manage`
