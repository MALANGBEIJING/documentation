:show-content:

===============
在线支付
===============

.. toctree::
   :titlesonly:

   payment_providers/wire_transfer
   payment_providers/adyen
   payment_providers/alipay
   payment_providers/amazon_payment_services
   payment_providers/asiapay
   payment_providers/authorize
   payment_providers/buckaroo
   payment_providers/demo
   payment_providers/flutterwave
   payment_providers/mercado_pago
   payment_providers/mollie
   payment_providers/ogone
   payment_providers/paypal
   payment_providers/razorpay
   payment_providers/sips
   payment_providers/stripe
   payment_providers/xendit

Odoo 集成了多个 **支付提供商**，允许您的客户通过其 *客户门户* 或您的 *电子商务网站* 在线支付。他们可以通过其喜欢的支付方式（如 **信用卡**）支付销售订单、发票或订阅，支持循环付款。

每个支付提供商都与支持的 :ref:`支付方式 <payment_providers/payment_methods>` 相关联，您可以根据需要启用或停用这些支付方式。

.. image:: payment_providers/online-payment.png
   :alt: 在线支付表单

.. note::
   Odoo 应用程序将敏感信息的处理委托给经过认证的支付提供商，因此您不必担心 PCI 合规性。没有敏感信息（如信用卡号）会存储在 Odoo 服务器或托管在其他地方的 Odoo 数据库中。相反，Odoo 应用程序使用在支付提供商系统中安全存储的数据的唯一参考编号。

.. _payment_providers/supported_providers:

支持的支付提供商
===========================

要访问支持的支付提供商，请转到 :menuselection:`会计 --> 配置 --> 支付提供商`，:menuselection:`网站 --> 配置 --> 支付提供商`，或 :menuselection:`销售 --> 配置 --> 支付提供商`。

.. _payment_providers/online_providers:

在线支付提供商
------------------------

.. list-table::
   :header-rows: 1
   :stub-columns: 1
   :widths: auto

   * -
     - 支付流程来自
     - :ref:`令牌化 <payment_providers/tokenization>`
     - :ref:`手动捕获 <payment_providers/manual_capture>`
     - :ref:`退款 <payment_providers/refunds>`
     - :ref:`快速结账 <payment_providers/express_checkout>`
   * - :doc:`Adyen <payment_providers/adyen>`
     - Odoo
     - |V|
     - 全额和部分
     - 全额和部分
     -
   * - :doc:`Amazon Payment Services <payment_providers/amazon_payment_services>`
     - 提供商网站
     -
     -
     -
     -
   * - :doc:`AsiaPay <payment_providers/asiapay>`
     - 提供商网站
     -
     -
     -
     -
   * - :doc:`Authorize.Net <payment_providers/authorize>`
     - Odoo
     - |V|
     - 仅全额
     - 仅全额
     -
   * - :doc:`Buckaroo <payment_providers/buckaroo>`
     - 提供商网站
     -
     -
     -
     -
   * - :doc:`Flutterwave <payment_providers/flutterwave>`
     - 提供商网站
     - |V|
     -
     -
     -
   * - :doc:`Mercado Pago <payment_providers/mercado_pago>`
     - 提供商网站
     -
     -
     -
     -
   * - :doc:`Mollie <payment_providers/mollie>`
     - 提供商网站
     -
     -
     -
     -
   * - :doc:`PayPal <payment_providers/paypal>`
     - 提供商网站
     -
     -
     -
     -
   * - :doc:`Razorpay <payment_providers/razorpay>`
     - Odoo
     - |V|
     - 仅全额
     - 全额和部分
     -
   * - :doc:`SIPS <payment_providers/sips>`
     - 提供商网站
     -
     -
     -
     -
   * - :doc:`Stripe <payment_providers/stripe>`
     - Odoo
     - |V|
     - 仅全额
     - 全额和部分
     - |V|
   * - :doc:`Xendit <payment_providers/xendit>`
     - 提供商网站
     -
     -
     -
     -

.. |V| replace:: ✔

.. note::
   - 每个提供商都有其特定的配置流程，具体取决于所提供的功能。
   - 一些在线支付提供商也可以添加为 :doc:`银行账户 <../finance/accounting/bank>`，但这与将其添加为支付提供商的过程不同。支付提供商允许客户在线支付，而银行账户则添加并配置在会计应用中进行 :doc:`银行对账 <accounting/bank/reconciliation>`。

.. tip::
   除了集成了 API 的常规支付提供商（如 Stripe、PayPal 或 Adyen）外，Odoo 还提供了 :doc:`演示支付提供商 <payment_providers/demo>`。该支付提供商允许您测试涉及在线支付的业务流程，无需凭证，因为演示支付为虚拟支付。

.. _payment_providers/bank_payments:

银行支付
-------------

- | :doc:`电汇 <payment_providers/wire_transfer>`
  | 选择后，Odoo 会显示您的支付信息和付款参考。收到银行账户中的付款后，您需要手动批准支付。
- | :doc:`SEPA 直接借记 <../finance/accounting/payments/batch_sdd>`
  | 您的客户可以进行银行转账以注册 SEPA 直接借记授权并直接扣除他们的银行账户。

.. _payment_providers/add_new:

启用支付提供商
===========================

要添加新的支付提供商并使其相关的支付方式对您的客户可用，请按以下步骤操作：

#. 转到支付提供商的网站，创建账户，并确保您获得了请求的第三方使用 API 凭据。Odoo 需要这些凭据才能与支付提供商进行通信。
#. 在 Odoo 中，导航到 :guilabel:`支付提供商`，通过 :menuselection:`会计 --> 配置 --> 支付提供商`，:menuselection:`网站 --> 配置 --> 支付提供商`，或 :menuselection:`销售 --> 配置 --> 支付提供商`。
#. 选择提供商并配置 :guilabel:`凭据` 标签。
#. 将 :guilabel:`状态` 字段设置为 :guilabel:`已启用`。

.. note::
   - :guilabel:`凭据` 标签中的字段取决于支付提供商。请参阅 :ref:`相关文档 <payment_providers/supported_providers>` 以获取更多信息。
   - 启用支付提供商后，它会自动发布在您的网站上。如果您希望取消发布，请点击 :guilabel:`已发布` 按钮。客户无法通过未发布的提供商进行支付，但仍可以管理 :dfn:`（删除并分配给订阅）` 与该提供商相关的现有令牌。


.. _payment_providers/test-mode:
测试模式
---------

如果您希望尝试支付提供商的测试，请在支付提供商表单中的 :guilabel:`状态` 字段设置为 :guilabel:`测试模式`，然后在 :guilabel:`凭据` 选项卡中输入您的提供商测试/沙箱凭据。

.. note::
   默认情况下，支付提供商在测试模式下 **未发布**，因此对访问者不可见。

.. warning::
   我们建议在副本或测试数据库上使用测试模式，以避免发票编号的潜在问题。

.. _payment_providers/payment_methods:

支付方式
===============

每个支付提供商都与一组支持的支付方式相关联；在支付提供商表单的 :guilabel:`配置` 选项卡中的 :guilabel:`支付方式` 字段列出了已激活的支付方式。要启用或停用提供商的支付方式，请点击 :guilabel:`启用支付方式`，然后点击相关方法旁边的切换按钮。

.. tip::
   支付方式根据其顺序显示在您的网站上。要重新排序，请在支付提供商表单中点击 :guilabel:`启用支付方式`，然后在 :guilabel:`支付方式` 列表中拖放支付方式以调整顺序。

图标和品牌
----------------

网站上支付方式旁边显示的图标是已激活品牌的图标，或者，如果没有品牌图标，则显示支付方式本身的图标。要修改这些图标，请前往 :menuselection:`会计 --> 配置 --> 支付方式`，:menuselection:`网站 --> 配置 --> 支付方式` 或 :menuselection:`销售 --> 配置 --> 支付方式`，然后点击支付方式。

要修改支付方式的图标，将鼠标悬停在支付方式表单右上角的图像上，然后点击铅笔图标 (:guilabel:`✎`)。

选择 :guilabel:`品牌` 选项卡以查看为支付方式激活的品牌。品牌及其相关图标根据顺序显示；要重新排序，拖放它们以调整顺序。要修改品牌图标，请选择该品牌，然后在弹出的窗口中，将鼠标悬停在右上角的图像上并点击铅笔图标 (:guilabel:`✎`)。

高级配置
----------------------

要进一步配置支付方式，请转到 :menuselection:`会计 --> 配置 --> 支付方式`，:menuselection:`网站 --> 配置 --> 支付方式` 或 :menuselection:`销售 --> 配置 --> 支付方式`。点击支付方式，然后激活 :ref:`开发者模式 <developer-mode>`。点击 :guilabel:`配置` 选项卡以适应功能。

.. danger::
   - 每种支付方式都是根据支付提供商的行为及其与 Odoo 的集成预先配置的。对配置的任何更改可能会导致错误，应该首先在副本或测试数据库中进行测试。
   - 支付方式的配置修改仅在方法和提供商的能力范围内起作用。例如，添加仅在一个国家支持的支付方式的 :ref:`国家 <payment_providers/currencies_countries>` 或为与不支持的提供商相关联的支付方式启用 :ref:`令牌化 <payment_providers/tokenization>` 将无法产生预期效果。

.. _payment_providers/tokenization:

令牌化
============

:ref:`如果支付提供商支持此功能 <payment_providers/online_providers>`，客户可以保存他们的支付方式详细信息以备后用。要启用此功能，请转到所选支付提供商的 :guilabel:`配置` 选项卡并启用 :guilabel:`允许保存支付方式`。

在这种情况下，会在 Odoo 中创建一个 **支付令牌**，可用作后续支付的支付方式，而客户无需再次输入其支付方式详细信息。这对于电子商务转化率和使用定期付款的订阅特别有用。

.. tip::
   为了添加或删除他们保存的支付方式详细信息，客户可以点击 :guilabel:`管理支付方式` 在 :ref:`客户门户 <users-portal-payment-methods>` 中进行管理。

.. admonition:: PCI DSS 和合规证明

   Odoo 并未获得 `PCI <https://www.pcisecuritystandards.org>`_ DSS 认证，因为它不存储持卡人数据或处理支付。相反，它将令牌化和支付外包给 :ref:`外部支付提供商 <payment_providers/online_providers>`，这意味着作为 Odoo 客户，您只需与提供商完成最低限度的自我评估问卷 (SAQ) 即可获得合规证明 (AoC) 并实现 PCI 合规性。在 :abbr:`SAQ (自我评估问卷)` 中，不应将 Odoo 作为支付处理器或第三方服务提供商提及。


.. _payment_providers/manual_capture:
手动捕获
==============

:ref:`如果支付提供商支持此功能 <payment_providers/online_providers>`，您可以通过两步而不是一步来授权和捕获支付。要启用此功能，请转到所选支付提供商的 :guilabel:`配置` 选项卡并启用 :guilabel:`手动捕获金额`。

当您授权支付时，资金会在客户的支付方式上被保留，但不会立即收取。稍后您可以手动捕获支付时才会实际扣款。您还可以作废授权以取消支付并释放已保留的资金。在许多情况下，手动捕获支付非常有用：

- 收到支付确认并等到订单发货后再捕获支付。
- 在支付完成之前审核并验证订单的合法性，并启动履行流程。
- 避免因退款支付高额退款费用：支付提供商不会对作废授权收费。
- 保留押金，并在稍后退还时扣除任何费用（例如，损坏费用）。

在授权后捕获支付，请转到相关的销售订单或发票并点击 :guilabel:`捕获交易` 按钮。要释放资金，请点击 :guilabel:`作废交易` 按钮。

.. note::
   - 某些支付提供商仅支持捕获部分授权金额。剩余的金额可以选择捕获或作废。这些提供商在 :ref:`上表 <payment_providers/online_providers>` 中的值为 **全额和部分**。仅支持捕获或作废总金额的提供商，其值为 **仅全额**。
   - 资金可能不会永久保留。在一定时间后，它们可能会自动释放回客户的支付方式。请参考您的支付提供商文档以了解确切的保留期限。
   - Odoo 并不支持所有支付提供商的此功能，但某些提供商允许从其网站界面手动捕获支付。

.. _payment_providers/refunds:

退款
=======

如果您的支付提供商支持此功能，您可以直接从 Odoo 退款支付。无需提前启用此功能。要退款客户支付，请导航到该支付并点击 :guilabel:`退款` 按钮。

.. note::
   - 某些支付提供商仅支持退款部分金额。剩余金额可以选择继续退款或不退款。这些提供商在 :ref:`上表 <payment_providers/online_providers>` 中的值为 **全额和部分**。仅支持全额退款的提供商，其值为 **仅全额**。
   - Odoo 并不支持所有支付提供商的此功能，但某些提供商允许从其网站界面退款支付。

.. _payment_providers/express_checkout:

快捷结账
================

:ref:`如果支付提供商支持此功能 <payment_providers/online_providers>`，您可以允许客户使用 :guilabel:`Google Pay` 和 :guilabel:`Apple Pay` 按钮并一键支付他们的电子商务订单。当他们使用这些按钮时，客户可以直接从购物车跳到确认页面，无需填写联系表格。客户只需在 Google 或 Apple 的支付表单上验证支付即可。

要启用此功能，请转到所选支付提供商的 :guilabel:`配置` 选项卡并启用 :guilabel:`允许快捷结账`。

.. note::
   快捷结账支付表单上显示的所有价格始终包含税费。

可用性
============

您可以通过在 :guilabel:`配置` 选项卡中指定允许的 :guilabel:`最大金额` 和修改 :guilabel:`货币` 和 :guilabel:`国家` 来调整支付提供商的可用性。

.. _payment_providers/currencies_countries:

货币和国家
------------------------

所有支付提供商都有不同的可用货币和国家列表。它们作为支付操作中的第一个筛选器，即如果客户的货币或国家不在支持列表中，则与支付提供商相关联的支付方式将不可供选择。由于可能存在错误、更新和未知的情况，添加或删除支付提供商的支持货币或国家是可能的。

.. note::

   - :ref:`支付方式 <payment_providers/payment_methods>` 也有自己的可用货币和国家列表，这作为支付操作中的另一个筛选器。
   - 如果支持货币或国家列表为空，这意味着列表太长无法显示，或者 Odoo 没有有关该支付提供商的信息。支付提供商仍然可用，尽管如果稍后阶段发现不支持该国家或货币，则支付可能会被拒绝。

最大金额
--------------

您可以限制所选提供商允许的 :guilabel:`最大金额`。将字段设置为 `0.00`，以便在不管支付金额的情况下提供支付服务。

.. important::
   此功能不适用于允许客户更新支付金额的页面，例如启用了 **捐赠** 片段和 **结账** 页面时使用付费 :doc:`运输方式 <../websites/ecommerce/checkout_payment_shipping/shipping>` 的情况。

.. _payment_providers/journal:

支付日志
===============

必须为支付提供商定义一个 :doc:`支付日志 <accounting/bank>`，以记录支付在 **未结账账户** 中。默认情况下，:guilabel:`银行` 日志被添加为所有支付提供商的支付日志。要修改它，请转到所选支付提供商的 :guilabel:`配置` 选项卡并选择另一个 :guilabel:`支付日志`。

.. note::
   - 支付日志必须是 :guilabel:`银行` 日志。
   - 同一日志可以用于多个支付提供商。
   - 仅在安装了 :doc:`发票或会计应用 <accounting>` 时才需要配置支付日志。

会计视角
----------------------

从会计角度来看，有两种在线支付工作流：直接存入您银行账户的支付，遵循通常的 :doc:`对账 <accounting/bank/reconciliation>` 工作流程，和来自第三方 :ref:`在线支付提供商 <payment_providers/online_providers>` 的支付，您需要遵循另一种会计工作流程。对于这些支付，您需要考虑如何记录支付的会计分录。我们建议您咨询您的会计师以获取建议。

默认情况下，使用为 :ref:`支付日志 <payment_providers/journal>` 定义的 :guilabel:`银行账户`，但您也可以为每个支付提供商指定一个 :ref:`未结账账户 <bank/outstanding-accounts>`，以将其支付与其他支付分开。

.. image:: payment_providers/bank_journal.png
   :alt: 为支付提供商定义未结账账户。

.. seealso::
   - :doc:`payment_providers/wire_transfer`
   - :doc:`payment_providers/adyen`
   - :doc:`payment_providers/alipay`
   - :doc:`payment_providers/authorize`
   - :doc:`payment_providers/asiapay`
   - :doc:`payment_providers/buckaroo`
   - :doc:`payment_providers/demo`
   - :doc:`payment_providers/mercado_pago`
   - :doc:`payment_providers/mollie`
   - :doc:`payment_providers/ogone`
   - :doc:`payment_providers/paypal`
   - :doc:`payment_providers/razorpay`
   - :doc:`payment_providers/sips`
   - :doc:`payment_providers/stripe`
   - :doc:`payment_providers/xendit`
   - :doc:`../websites/ecommerce/checkout_payment_shipping/payments`
   - :doc:`accounting/bank`
