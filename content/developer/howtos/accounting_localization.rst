=======================
会计本地化
=======================

.. warning::

    本教程需要了解如何在 Odoo 中构建模块（参见 :doc:`../tutorials/server_framework_101`）。


安装步骤
======================

安装 `account <{GITHUB_PATH}/addons/account>`_ 模块时，会根据公司的国家代码自动安装相应的本地化模块。
如果未设置国家代码或未找到本地化模块，则默认安装 `l10n_generic_coa <{GITHUB_PATH}/addons/l10n_generic_coa>`_（美国）本地化模块。
有关详细信息，请查看 `post init hook <{GITHUB_PATH}/addons/account/__init__.py>`_。

例如，如果公司国家是“瑞士”，则会安装 `l10n_ch <{GITHUB_PATH}/addons/l10n_ch>`_ 模块。

构建本地化模块
==============================

一个基础的 ``l10n_XX`` 模块的结构可以通过以下 :file:`__manifest__.py` 文件描述：

.. code-block:: python

    {
        "name": "国家 - 会计",
        "version": "1.0.0",
        "category": "会计/本地化/账户图表",
        "license": "LGPL-3",
        "depends": [
            "account",
        ],
        "data": [
            "data/other_data.xml",
            "views/xxxmodel_views.xml",
        ],
        "demo": [
            "demo/demo_company.xml",
        ]
    }

您的工作树应如下所示

.. code-block:: bash

  l10n_xx
  ├── data
  │   ├── template
  │   │   ├── account.account-xx.csv
  │   │   ├── account.group-xx.csv
  │   │   └── account.tax.group-xx.csv
  │   └── other_data.xml
  ├── views
  │   └── xxxmodel_views.xml
  ├── demo
  │   └── demo_company.xml
  ├── models
  │   ├── template_xx.py
  │   └── __init__.py
  ├── __init__.py
  └── __manifest__.py


在第一个文件 :file:`models/template_xx.py` 中，我们为账户图表设置了名称以及一些基本字段。

.. seealso::
   :doc:`账户图表模板参考 </developer/reference/standard_modules/account>`

.. example::
  `addons/l10n_be/models/template_be.py <{GITHUB_PATH}/addons/l10n_be/models/template_be.py>`_

  .. literalinclude:: {ODOO_RELPATH}/addons/l10n_be/models/template_be.py
    :condition: odoo_dir_in_path
    :language: python
    :start-at: _get_be_template_data
    :end-before: _get_be_reconcile_model


账户图表
=================

账户标签
------------

.. seealso::
   :ref:`账户标签参考 <reference/account_account_tag>`

标签是一种分类账户的方式。
例如，假设您想创建一个包含多行的财务报表，但找不到按 ``代码`` 分配账户的规则。
解决方案是使用标签，每个报表行都有一个标签，用于按您希望的方式过滤账户。

将标签放在 :file:`data/account_account_tag_data.xml` 文件中。

.. example::
  `addons/l10n_lt/data/template/account.account-lt.csv <{GITHUB_PATH}/addons/l10n_lt/data/template/account.account-lt.csv>`_

  .. literalinclude:: {ODOO_RELPATH}/addons/l10n_lt/data/template/account.account-lt.csv
    :condition: odoo_dir_in_path
    :language: csv
    :end-at: account_account_template_1201

账户
--------

.. seealso::
   - :ref:`账户参考 <reference/account_account>`
   - :doc:`/applications/finance/accounting/get_started/chart_of_accounts`

显然，:guilabel:`账户图表` 不能没有 :guilabel:`账户` 存在。您需要在 :file:`data/account.account.template.csv` 中指定它们。

.. example::
  `addons/l10n_ch/data/template/account.account-ch.csv <{GITHUB_PATH}/addons/l10n_ch/data/template/account.account-ch.csv>`_

  .. literalinclude:: {ODOO_RELPATH}/addons/l10n_ch/data/template/account.account-ch.csv
    :condition: odoo_dir_in_path
    :language: csv
    :end-at: ch_coa_1171

.. warning::

    - 避免使用 `asset_cash` ``account_type``！
      事实上，银行和现金账户会在安装本地化模块时直接创建，然后链接到 ``account.journal``。
    - 对于一般情况，一个应付/应收账户就足够了。但是，我们还需要定义一个 PoS 应收账户（在 CoA 中链接）。
    - 不要创建过多账户：200-300 个足够了。我们试图找到一个平衡点，使 CoA 在大多数公司中只需进行最少的调整。


账户组
--------------

.. seealso::
   :ref:`账户组参考 <reference/account_group>`

账户组允许描述账户图表的层次结构。过滤器需要在报表中激活，然后当您展开到会计分录时，它将显示账户的父组。

它使用前缀 *start*/*end*，因此每个代码以 *start* 到 *end* 之间的内容开头的账户将具有该 ``account.group`` 作为父组。此外，账户组还可以有父账户组，以形成层次结构。


.. example::
  `addons/l10n_il/data/template/account.group-il.csv <{GITHUB_PATH}/addons/l10n_il/data/template/account.group-il.csv>`_

  .. csv-table::
     :condition: odoo_dir_in_path
     :file: {ODOO_RELPATH}/addons/l10n_il/data/template/account.group-il.csv
     :widths: 20,20,20,20,20
     :header-rows: 1

税收
-----

.. seealso::
   - :ref:`税务参考 <reference/account_tax>`
   - :doc:`/applications/finance/accounting/taxes/`

要添加税收，首先需要指定税组。每种税率通常只需要一个税组，除了 0% 税率，因为您需要区分免税、0%、不适用等税种。
该模型只有两个必填字段：`name` 和 `country`。创建文件 :file:`data/template/account.tax.group-xx.csv` 并列出这些组。

.. example::
  `addons/l10n_uk/data/template/account.tax.group-uk.csv <{GITHUB_PATH}/addons/l10n_uk/data/template/account.tax.group-uk.csv>`_

  .. literalinclude:: {ODOO_RELPATH}/addons/l10n_uk/data/template/account.tax.group-uk.csv
    :condition: odoo_dir_in_path
    :language: csv


现在，您可以通过 :file:`data/template/account.tax-xx.csv` 文件添加税收。第一个定义的采购/销售税也将成为产品的默认采购/销售税。


.. example::
  `addons/l10n_ae/data/template/account.tax-ae.csv <{GITHUB_PATH}/addons/l10n_ae/data/template/account.tax-ae.csv>`_

  .. literalinclude:: {ODOO_RELPATH}/addons/l10n_ae/data/template/account.tax-ae.csv
    :condition: odoo_dir_in_path
    :language: xml
    :end-at: uae_sale_tax_5_ras_al_khaima


税务报告
----------

.. raw:: html

   <div><span class="badge" style="background-color:#AD5E99">企业功能</span><div>

税务报告在 :guilabel:`开票`（`account`）应用中声明，但仅在安装了 :guilabel:`会计`（`account_accountant`）时才能访问。

.. seealso::
   - :doc:`/developer/reference/standard_modules/account/account_report_line`
   - :doc:`/applications/finance/accounting/reporting/tax_returns`

在上一部分中，您可能注意到了字段 `invoice_repartition_line_ids` 或 `refund_repartition_line_ids`，并可能不太理解它们。好消息是：您并不是唯一不理解的人。坏消息是：您需要自己理解一部分。这个话题确实比较复杂。

.. graphviz:: accounting_localization/tax_report.dot
    :class: overflow-auto

简单来说，在税务模板中，您可以在发票/退款分配行中指示是否需要在哪个报表行中报告基数或税率的百分比（通过 *minus/plus_report_line_ids* 字段）。
您可以在 Odoo 界面中检查税务配置，或者查看文档 :ref:`税务参考 <reference/account_tax>`、:ref:`税务分配参考 <reference/account_tax_repartition>`。

因此，一旦您正确配置了税务，您只需添加包含 `account.report` 记录的 :file:`data/account_tax_report_data.xml` 文件。要将其视为税务报告，您需要为其提供正确的 `root_report_id`。

.. code-block:: xml

    <odoo>
        <record id="tax_report" model="account.report">
            <field name="name">税务报告</field>
            <field name="root_report_id" ref="account.generic_tax_report"/>
            <field name="country_id" ref="base.XX"/>
        </record>

        ...
    </odoo>

... 之后跟着 `account.report.line` 记录的行声明。

.. example::
  `addons/l10n_au/data/account_tax_report_data.xml <{GITHUB_PATH}/addons/l10n_au/data/account_tax_report_data.xml>`_

  .. literalinclude:: {ODOO_RELPATH}/addons/l10n_au/data/account_tax_report_data.xml
    :condition: odoo_dir_in_path
    :language: xml
    :start-at: tax_report
    :end-before: account_tax_report_gstrpt_g3


税务位置
----------------

.. seealso::
   - :ref:`税务位置参考 <reference/account_fiscal_position>`
   - :doc:`/applications/finance/accounting/taxes/fiscal_positions`

在 :file:`data/template/account.fiscal.position-xx.csv` 文件中指定税务位置。

.. example::
  `addons/l10n_es/data/template/account.fiscal.position-es_common.csv <{GITHUB_PATH}/addons/l10n_es/data/template/account.fiscal.position-es_common.csv>`_

  .. literalinclude:: {ODOO_RELPATH}/addons/l10n_es/data/template/account.fiscal.position-es_common.csv
    :condition: odoo_dir_in_path
    :language: csv
    :end-at: account_tax_template_p_iva10_sp_ex

最后步骤
===========

最后，您可以添加一个演示公司，以便在演示模式下轻松测试本地化模块。

.. example::
  `addons/l10n_ch/demo/demo_company.xml <{GITHUB_PATH}/addons/l10n_ch/demo/demo_company.xml>`_

  .. literalinclude:: {ODOO_RELPATH}/addons/l10n_ch/demo/demo_company.xml
    :condition: odoo_dir_in_path
    :language: xml
    :start-after: <odoo>
    :end-before: </odoo>

会计报表
==================

.. raw:: html

   <div><span class="badge" style="background-color:#AD5E99">企业功能</span><div>

.. seealso::
  :doc:`/applications/finance/accounting/reporting`

会计报表应通过一个独立模块 `l10n_XX_reports` 添加，该模块应放入 `enterprise repository <{GITHUB_ENT_PATH}>`_。

此类模块的基础 :file:`__manifest__.py` 文件如下所示：


.. code-block:: python

    {
        "name": "国家 - 会计报表",
        "category": "会计/本地化/报表",
        "version": "1.0.0",
        "license": "OEEL-1",
        "depends": [
            "l10n_XX", "account_reports"
        ],
        "data": [
            "data/balance_sheet.xml",
            "data/profit_and_loss.xml",
        ],
        "auto_install": True,
    }


财务报表的功能概述在此：:doc:`/applications/finance/accounting/reporting`。

一些好的示例：

* `l10n_ch_reports/data/account_financial_html_report_data.xml <{GITHUB_ENT_PATH}/l10n_ch_reports/data/account_financial_html_report_data.xml>`_
* `l10n_be_reports/data/account_financial_html_report_data.xml <{GITHUB_ENT_PATH}/l10n_be_reports/data/account_financial_html_report_data.xml>`_

您可以在这里查看字段的含义：

* :doc:`/developer/reference/standard_modules/account/account_report`
* :doc:`/developer/reference/standard_modules/account/account_report_line`

如果您为报表提供了 `root_report_id`，它现在可以在其变体选择器中使用。如果没有，您仍然需要为其添加一个菜单项。您可以通过单击 :menuselection:`操作 --> 创建菜单项` 从报表的表单视图中创建默认菜单项。然后，您需要刷新页面以查看它。或者，要为完全新报表在 :guilabel:`报表` 菜单中创建一个专门的部分，您需要在主 `l10n_XX` 模块中创建一个新的 `ir.ui.menu` 记录，并在新的报表 XML 文件中创建一个新的 `ir.actions.client`，该文件调用 `account.report` 并带有新的 **report id**。然后，在操作模型中将新的菜单设置为 `parent_id` 字段。

.. example::
   * `ir.ui.menu 创建 <{GITHUB_PATH}/addons/l10n_be/data/menuitem_data.xml>`_
   * `ir.actions.client 和菜单项创建 <{GITHUB_ENT_PATH}/l10n_be_reports/data/partner_vat_listing.xml>`_
