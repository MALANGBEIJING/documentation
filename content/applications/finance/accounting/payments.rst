:show-content:

========
付款
========

在 Odoo 中，付款可以自动关联到发票或账单，或者作为独立记录用于以后使用：

- 如果付款是 **关联到发票或账单** 的，它将减少/结清发票的应付款项。您可以有多个付款与同一张发票相关联。
  
- 如果付款 **没有关联到发票或账单**，客户对您的公司有未结清的信用，或者您的公司对供应商有未结清的借记。您可以使用这些未结金额来减少/结清未付款的发票/账单。

.. seealso::
   - :doc:`内部转账 <payments/internal_transfers>`
   - :doc:`银行对账 <bank/reconciliation>`
   - `Odoo 教程：银行配置 <https://www.odoo.com/slides/slide/bank-configuration-1880>`_

.. _payments/register:

从发票或账单中登记付款
===========================

在客户发票或供应商账单中点击 :guilabel:`登记付款`，它将生成一条新的日记账分录，并根据付款金额更改应付款项。对应的金额反映在 :ref:`未清款项 <bank/outstanding-accounts>` **收款** 或 **付款** 账户中。此时，客户发票或供应商账单的状态为 :guilabel:`付款中`。然后，当未清账户与银行对账单行对账时，发票或供应商账单的状态会变为 :guilabel:`已支付`。

付款行旁边的信息图标显示了有关付款的更多信息。您可以通过点击 :guilabel:`查看` 访问相关日记账等其他信息。

.. image:: payments/information-icon.png
   :alt: 查看付款的详细信息。

.. note::
   - 客户发票或供应商账单必须处于 :guilabel:`已过账` 状态才能登记付款。
   - 如果您取消对账付款，付款仍然会在您的账簿中显示，但不再与发票关联。
   - 如果您在不同货币中（取消）对账付款，系统将自动生成一条日记账分录，以记录货币兑换的损益（反向）金额。
   - 如果您在（取消）对账付款和带有现金基础税的发票，系统将自动生成一条日记账分录，以记录现金基础税（反向）金额。

.. tip::
   - 如果您的主银行账户设置为 :ref:`未清账户 <bank/outstanding-accounts>`，并且付款在 Odoo 中登记（未通过相关银行对账单），发票和账单将直接被登记为 :guilabel:`已支付`。

未关联发票或账单的付款登记
=================================

通过 :menuselection:`客户 / 供应商 --> 付款` 菜单登记的新付款不会直接关联到发票或账单。相反，账户应收款或账户应付款会与 **未清账户** 匹配，直到手动将其与相关发票或账单匹配。

匹配发票和账单与付款
--------------------------

当您验证新发票/账单并且存在该特定客户或供应商的 **未清付款** 时，将显示一个蓝色横幅。您可以通过点击 :guilabel:`添加` 在 :guilabel:`未清信用` 或 :guilabel:`未清借方` 下轻松匹配发票或账单。

.. image:: payments/add-option.png
   :alt: 显示添加选项以对发票或账单与付款进行对账。

发票或账单现在标记为 :guilabel:`付款中`，直到与相应的银行对账单对账为止。

.. _payments/batch-payments:

批量付款
---------

批量付款允许您将不同的付款分组，以便于 :doc:`对账 <bank/reconciliation>`。它们在您将 :doc:`支票 <payments/checks>` 存入银行或用于 :doc:`SEPA 付款 <payments/pay_sepa>` 时也很有用。为此，请转到 :menuselection:`会计 --> 客户 --> 批量付款` 或 :menuselection:`会计 --> 供应商 --> 批量付款`。在付款的列表视图中，您可以选择并通过点击 :menuselection:`操作 --> 创建批量付款` 将多个付款分组成一个批量。

.. seealso::
  - :doc:`批量付款 <payments/batch>`
  - :doc:`批量 SDD 付款 <payments/batch_sdd>`

.. _payments/matching:

付款对账
----------

:guilabel:`付款对账` 工具打开所有未对账的客户发票或供应商账单，允许您单独处理它们，在一个地方匹配所有付款和发票。您可以从 :menuselection:`会计仪表板 --> 客户发票 / 供应商账单`，点击下拉菜单按钮 (:guilabel:`⋮`)，并选择 :guilabel:`付款对账`，或转到 :menuselection:`会计 --> 会计 --> 对账`。

.. image:: payments/payments-journal.png
   :alt: 下拉菜单中的付款对账选项。

.. note::
   在 :doc:`对账 <bank/reconciliation>` 过程中，如果借方和贷方的总和不匹配，则会有一个剩余余额。这个余额要么需要在以后对账，要么直接核销。

批量付款对账
--------------

您可以使用 **批量对账功能** 同时对特定客户或供应商的多个未清付款或发票进行对账。进入您的 **会计仪表板** 并打开您的 **银行日记账**。在 **银行对账** 视图中，选择一个 **交易**，然后点击 :guilabel:`批量付款` 选项卡。从这里，您可以将您的 :doc:`批量付款 <payments/batch>` 与您的未清付款或发票对账。

.. image:: payments/reconcile-option.png
   :alt: 批量付款对账选项。

登记部分付款
===============

要登记 **部分付款**，请从相关发票或账单中点击 :guilabel:`登记付款`，并输入收到或支付的金额。输入金额后，系统会提示您决定是 :guilabel:`保持未结` 发票或账单，还是 :guilabel:`标记为已全额支付`。选择 :guilabel:`保持未结` 并点击 :guilabel:`创建付款`。发票或账单现在标记为 :guilabel:`部分`。如果您希望以不同金额结清发票或账单，请选择 :guilabel:`标记为已全额支付`。

.. image:: payments/payment-difference.png
   :alt: 发票或账单的部分付款。

与银行对账单对账付款
=========================

一旦付款登记完毕，发票或账单的状态为 :guilabel:`付款中`。下一步是将其与相关银行对账单行 :doc:`对账 <bank/reconciliation>`，以完成交易并将发票或账单标记为 :guilabel:`已支付`。

.. toctree::
   :titlesonly:

   payments/online
   payments/checks
   payments/batch
   payments/batch_sdd
   payments/follow_up
   payments/internal_transfers
   payments/pay_sepa
   payments/pay_checks
   payments/forecast
   payments/trusted_accounts
