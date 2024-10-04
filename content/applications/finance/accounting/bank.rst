======================
银行和现金账户
======================

您可以在数据库中管理所需的任意多个银行或现金账户。正确配置它们可确保您的银行数据保持最新，并准备好与您的日记账条目进行 :doc:`对账 <bank/reconciliation>`。

在 Odoo 会计中，每个银行账户都有一个专用的日记账，用于在专用账户中发布所有条目。每当您添加银行账户时，日记账和账户都会自动创建和配置。

.. note::
   现金日记账和账户必须手动配置。

默认情况下，银行日记账会以卡片形式显示在 :guilabel:`会计仪表板` 上，并包含操作按钮。

.. image:: bank/card.png
   :alt: 银行日记账显示在会计仪表板上并包含操作按钮

.. _bank_accounts/manage:

管理您的银行和现金账户
===========================

连接您的银行以实现自动同步
------------------------------

要将您的银行账户连接到数据库，前往 :menuselection:`会计 --> 配置 --> 银行：添加银行账户`，在列表中选择您的银行，点击 :guilabel:`连接`，并按照指示操作。

.. seealso::
   :doc:`bank/bank_synchronization`

.. _bank_accounts/create:

创建银行账户
------------

如果您的银行机构在 Odoo 中不可用，或如果您不希望将银行账户连接到数据库，您可以手动配置您的银行账户。

要手动添加银行账户，请前往 :menuselection:`会计 --> 配置 --> 银行：添加银行账户`，点击右下角的 :guilabel:`创建`，并填写表格。

.. note::
   - Odoo 会自动检测银行账户类型（例如，IBAN）并相应启用某些功能。
   - 默认的银行日记账是可用的，您可以通过进入 :menuselection:`会计 --> 配置 --> 会计：日记账 --> 银行` 来使用它。打开它并编辑不同的字段以匹配您的银行账户信息。

创建现金日记账
--------------

要创建新的现金日记账，前往 :menuselection:`会计 --> 配置 --> 会计：日记账`，点击 :guilabel:`创建` 并在 :guilabel:`类型` 字段中选择 :guilabel:`现金`。

有关会计信息字段的更多信息，请阅读本页的 :ref:`bank_accounts/configuration` 部分。

.. note::
   一个默认的现金日记账是可用的，可以立即使用。您可以通过前往 :menuselection:`会计 --> 配置 --> 会计：日记账 --> 现金` 来查看它。

编辑现有的银行或现金日记账
------------------------------

要编辑现有的银行日记账，前往 :menuselection:`会计 --> 配置 --> 会计：日记账` 并选择您想要修改的日记账。

.. _bank_accounts/configuration:

配置
====

您可以根据需要编辑会计信息和银行账户号码。

.. image:: bank/bank-journal-config.png
   :alt: 手动配置您的银行信息

.. seealso::
   - :doc:`get_started/multi_currency`
   - :doc:`bank/transactions`

.. _bank_accounts/suspense:

暂记账户
--------

银行对账单交易会发布在 :guilabel:`暂记账户` 上，直到最终对账完成以找到正确的账户。

损益账户
--------

当现金寄存器的期末余额与系统计算的结果不一致时，:guilabel:`利润账户` 用于记录利润，而 :guilabel:`亏损账户` 用于记录亏损。

货币
----

您可以编辑用于输入对账单的货币。

.. seealso::
   :doc:`get_started/multi_currency`

账户号码
--------

如果您需要**编辑您的银行账户详细信息**，请点击您 :guilabel:`账户号码` 旁边的外部链接箭头。在新页面上，点击您 :guilabel:`银行` 旁边的外部链接箭头并相应更新您的银行信息。这些详细信息在注册付款时使用。

.. image:: bank/bank-account-number.png
   :alt: 编辑您的银行信息

银行数据
--------

:guilabel:`银行数据` 定义了银行对账单的注册方式。有三种可用选项：

- :guilabel:`尚未定义`，应选择此选项，当您尚未确定是否将银行账户与数据库同步。
- :guilabel:`导入 (CAMT, CODA, CSV, OFX, QIF)`，如果您想使用不同的格式导入银行对账单，请选择此选项。
- :guilabel:`自动银行同步`，如果您的银行已与数据库同步，请选择此选项。

.. seealso::
   - :doc:`bank/bank_synchronization`
   - :doc:`bank/transactions`

.. _bank/outstanding-accounts:

未结算账户
==========

默认情况下，付款通过名为**未结算账户**的临时账户进行登记，之后再记录到您的银行账户中。

- **未结支付账户** 是指发出付款后，在与银行对账单中的提款挂钩之前，这些付款被记录的账户。
- **未结收款账户** 是指收到付款后，在与银行对账单中的存款挂钩之前，这些付款被记录的账户。

这些账户应属于 :ref:`类型 <chart-of-account/type>` :guilabel:`流动资产`。

.. note::
   当您对银行账户与银行对账单进行对账时，未结算账户与银行账户之间的转账是自动完成的。

默认账户配置
-------------

未结算账户是默认定义的。如果有必要，您可以通过前往 :menuselection:`会计 --> 配置 --> 设置 --> 默认账户` 并更新您的 :guilabel:`未结收款账户` 和 :guilabel:`未结支付账户` 来进行更新。

银行和现金日记账配置
--------------------

您还可以为任何 :ref:`类型 <chart-of-account/type>` 为 :guilabel:`银行` 或 :guilabel:`现金` 的日记账设置特定的未结算账户。

在您的 :guilabel:`会计仪表板` 中，点击您想要配置的日记账的菜单选择 ⋮，并点击 :guilabel:`配置`，然后打开 :guilabel:`收/付款` 标签。要显示未结算账户列，点击切换按钮并勾选 :guilabel:`未结收款/支付账户`，然后更新该账户。

.. image:: bank/toggle-button.png
   :align: center
   :alt: 选择切换按钮并点击未结算账户

.. note::
   - 如果您没有为特定日记账指定未结支付账户或未结收款账户，Odoo 将使用默认的未结算账户。
   - 如果您的主要银行账户被添加为未结收款账户或未结支付账户，那么在登记付款时，发票或账单的状态会直接设置为 :guilabel:`已付款`。

.. toctree::
   :titlesonly:

   bank/bank_synchronization
   bank/transactions
   bank/reconciliation
   bank/reconciliation_models
   bank/foreign_currency
