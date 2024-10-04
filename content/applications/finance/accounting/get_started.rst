:show-content:

===========
快速入门
===========

当您第一次打开 Odoo 会计应用程序时，:guilabel:`会计仪表板` 会欢迎您，显示分步入门横幅，这是一个帮助您入门的向导。此入门横幅会一直显示，直到您选择将其关闭为止。

横幅中显示的设置稍后仍然可以通过导航到 :menuselection:`会计 --> 配置 --> 设置` 进行修改。

.. note::
   Odoo 会计根据数据库创建时选择的国家，自动安装适合您公司的 **财政本地化包**。这样，相关的账户、报告和税项都可以直接使用。:ref:`点击此处了解更多关于财政本地化包的信息 <fiscal_localizations/packages>`。

会计入门横幅
================

分步会计入门横幅由四个步骤组成：

.. image:: get_started/accounting-onboarding-banner.png
   :alt: Odoo 会计中的分步入门横幅

#. :ref:`accounting-setup-periods`
#. :ref:`accounting-setup-bank`
#. :ref:`accounting-setup-taxes`
#. :ref:`accounting-setup-chart`

.. _accounting-setup-periods:

会计期间
---------

定义 **财务年度** 的起止日期，用于自动生成报告，并设置您的 **税务申报周期**，同时设置一个提醒，以免错过任何税务申报截止日期。

默认情况下，起始日期设置为 1 月 1 日，结束日期为 12 月 31 日，这是最常见的使用方式。

.. note::
   您还可以通过导航到 :menuselection:`会计 --> 配置 --> 设置 --> 财务期间` 并更新这些值来更改这些设置。

.. _accounting-setup-bank:

银行账户
---------

将您的银行账户连接到数据库中，并自动同步银行对账单。为此，请在列表中找到您的银行，点击 :guilabel:`连接`，并按照屏幕上的指示操作。

.. note::
   :doc:`点击此处 <bank/bank_synchronization>` 了解有关此功能的更多信息。

如果您的银行无法自动同步，或者您不希望将其与数据库同步，您也可以通过输入其名称，点击 :guilabel:`创建您的银行账户`，并填写表格来手动配置银行账户。

- :guilabel:`名称`: 在 Odoo 中显示的银行账户名称。
- :guilabel:`账号`: 您的银行账号（欧洲为 IBAN）。
- :guilabel:`银行`: 点击 :guilabel:`创建并编辑` 来配置银行的详细信息。添加银行机构的 :guilabel:`名称` 和其 :guilabel:`标识代码`（BIC 或 SWIFT）。
- :guilabel:`代码`: 此代码是您的日记账的 :guilabel:`短代码`，在 Odoo 中显示。默认情况下，Odoo 会创建一个带有此短代码的新日记账。
- :guilabel:`日记账`: 如果您已有一个尚未关联到银行账户的现有银行日记账，则此字段会显示。如果是这样，请选择 :guilabel:`日记账` 来记录与此银行账户关联的财务交易，或者通过点击 :guilabel:`创建并编辑` 来创建一个新日记账。

.. note::
   - 您可以使用此工具添加所需的任意多个银行账户，路径为：:menuselection:`会计 --> 配置 --> 添加银行账户`。
   - :doc:`点击此处 <bank>` 了解有关银行账户的更多信息。

.. _accounting-setup-taxes:

税项
------

此菜单允许您创建新的税项、（停）启用或修改现有税项。根据您数据库中安装的 :doc:`本地化包 <../fiscal_localizations>`，所需的税项已为您的国家配置好。

.. note::
   :doc:`点击此处 <taxes>` 了解有关税项的更多信息。

.. _accounting-setup-chart:

会计科目表
------------

通过此菜单，您可以向 **会计科目表** 添加账户并指示其初始余额。

此页面显示了基本设置，以帮助您审查您的会计科目表。要访问账户的所有设置，请点击行末的 :guilabel:`设置` 按钮。

.. image:: get_started/setup_chart_of_accounts.png
   :alt: Odoo 会计中的会计科目表及其初始余额的设置

.. note::
   :doc:`点击此处 <get_started/chart_of_accounts>` 了解如何配置您的会计科目表。

发票入门横幅
===============

另一个分步入门横幅帮助您利用 Odoo 的发票和会计应用程序。如果您使用发票应用程序而不是会计应用程序，发票入门横幅将是欢迎您的内容。

如果您的数据库中安装了 Odoo 会计，您可以通过导航到 :menuselection:`会计 --> 客户 --> 发票` 来访问它。

发票入门横幅由四个主要步骤组成：

.. image:: get_started/invoicing-onboarding-banner.png
   :alt: Odoo 发票中的分步入门横幅

#. :ref:`invoicing-setup-company`
#. :ref:`invoicing-setup-layout`
#. :ref:`invoicing-setup-invoice`
#. :ref:`invoicing-setup-payments`

.. _invoicing-setup-company:

公司信息
---------

添加您公司的详细信息，例如名称、地址、徽标、网站、电话号码、电子邮件地址和税号或增值税号。这些详细信息将显示在您的文件中，如发票。

.. note::
   您还可以通过导航到 :menuselection:`设置 --> 常规设置`，向下滚动到 :guilabel:`公司` 部分并 :guilabel:`更新信息` 来更改公司详细信息。

.. _invoicing-setup-layout:

文档布局
---------

自定义 :ref:`默认发票布局 <studio/pdf-reports/default-layout>`。

.. note::
   您还可以通过导航到 :menuselection:`设置 --> 常规设置`，向下滚动到 :guilabel:`公司` 部分并点击 :guilabel:`配置文档布局` 来更改发票布局。

.. _invoicing-setup-invoice:

创建发票
----------

创建您的第一张发票。

.. tip::
   在页脚中添加您的 **银行账号** 和指向您 **通用条款和条件** 的链接。这样，您的联系人可以在线查看 GT&C 的完整内容，而无需在您签发的发票上打印它们。

.. _invoicing-setup-payments:

在线支付
---------

使用 Stripe 开始操作，并在 Odoo 中启用安全的集成信用卡和借记卡支付。

.. tip::
   要使用其他支付提供商，请前往 :guilabel:`发票 --> 配置 --> 支付提供商` 并 :doc:`启用所需的提供商 <../payment_providers>`。

.. seealso::
   * :doc:`银行账户 <bank>`
   * :doc:`会计科目表 <get_started/chart_of_accounts>`
   * :doc:`银行同步 <bank/bank_synchronization>`
   * :doc:`本地化包 <../fiscal_localizations>`
   * `Odoo 教程：会计与发票 - 快速入门 [视频] <https://www.odoo.com/slides/slide/getting-started-1692>`_

.. toctree::
   :titlesonly:

   get_started/cheat_sheet
   get_started/chart_of_accounts
   get_started/multi_currency
   get_started/avg_price_valuation
   get_started/tax_units
