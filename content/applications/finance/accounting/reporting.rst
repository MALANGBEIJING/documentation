:show-content:

=========
报告
=========

Odoo 提供 **通用** 和 **动态** 报告，适用于所有国家，无论安装了何种 :doc:`本地化包 <../../finance/fiscal_localizations>`：

-  :ref:`资产负债表 <accounting/reporting/balance-sheet>`
-  :ref:`利润和损失表 <accounting/reporting/profit-and-loss>`
-  :ref:`管理摘要 <accounting/reporting/executive-summary>`
-  :ref:`总账 <accounting/reporting/general-ledger>`
-  :ref:`应收账款账龄分析 <accounting/reporting/aged-receivable>`
-  :ref:`应付账款账龄分析 <accounting/reporting/aged-payable>`
-  :ref:`现金流量表 <accounting/reporting/cash-flow-statement>`
-  :ref:`税务报告 <accounting/reporting/tax-report>`

要展开报告并查看其详细信息，请点击左侧的 :icon:`fa-caret-right` (:guilabel:`右箭头`)。然后点击帐户、日记账分录、付款、发票等右侧的 :icon:`fa-caret-down` (:guilabel:`下箭头`)，以 :guilabel:`注释` 并查看详细信息。

.. image:: reporting/reporting-annotate.png
   :alt: 注释报告。

要以 PDF 或 XLSX 格式导出报告，请点击顶部的 :guilabel:`PDF`，或点击 :guilabel:`PDF` 按钮旁边的 :icon:`fa-caret-down` (:guilabel:`下箭头`) 并选择 :guilabel:`XLSX`。

要比较不同期间的值，请点击 :guilabel:`比较` 菜单并选择要比较的期间。

.. image:: reporting/reporting-comparison.png
   :alt: 比较菜单以比较时间段。

.. _accounting/reporting/balance-sheet:

资产负债表
=============

:guilabel:`资产负债表` 显示了在特定日期您组织的资产、负债和权益的快照。

.. _accounting/reporting/profit-and-loss:

利润和损失表
===============

:guilabel:`利润和损失表`（或 **收入报表**）通过从收入中扣除费用，显示您公司在报告期间的净收入。

.. _accounting/reporting/executive-summary:

管理摘要
=================

:guilabel:`管理摘要` 提供了监督您公司业绩的所有重要数据的概述。

它包括以下项目：

- :guilabel:`绩效`:
    - :guilabel:`毛利率`:
        您业务所有销售的贡献 **减去** 实现这些销售所需的任何直接成本（人工、材料等）。
    - :guilabel:`净利率`:
        您业务所有销售的贡献 **减去** 实现这些销售所需的任何直接成本 *和* 您公司拥有的固定开销（电费、租金、销售产生的税款等）。
    - :guilabel:`投资回报率（每年）`:
        净利润与公司用来获得这些利润的资产金额的比率。
- :guilabel:`财务状况`:
    - :guilabel:`平均应收账款天数`:
        您的客户支付您的所有发票所需的平均天数。
    - :guilabel:`平均应付账款天数`:
        您支付所有账单所需的平均天数。
    - :guilabel:`短期现金预测`:
        您的业务在下个月预计有多少现金流入或流出，即 **销售账户** 的月余额 **减去** **采购账户** 的月余额。
    - :guilabel:`流动资产与负债比率`:
        也称为 **流动比率**，这是流动资产（:dfn:`在一年内可以变现的资产`）与流动负债（:dfn:`将在下一年到期的负债`）的比率。通常用于衡量公司偿还债务的能力。

.. _accounting/reporting/general-ledger:

总账
==============

:guilabel:`总账` 报告显示选定日期范围内所有帐户的所有交易。初始汇总报告显示每个帐户的总额。要展开帐户并查看其详细信息，请点击左侧的 :icon:`fa-caret-right` (:guilabel:`右箭头`)。此报告对于审查特定期间内发生的每笔交易非常有用。

.. _accounting/reporting/aged-receivable:

应收账款账龄分析
===============

:guilabel:`应收账款账龄分析` 报告显示在选定月份及之前几个月待付款的销售发票。

.. _accounting/reporting/aged-payable:

应付账款账龄分析
============

:guilabel:`应付账款账龄分析` 报告显示您所欠的个人账单、贷记单和多付款项以及它们未支付的时间。

.. _accounting/reporting/cash-flow-statement:

现金流量表
===================

:guilabel:`现金流量表` 显示资产负债表账户和收入变化如何影响现金及现金等价物，并将分析分解为经营活动、投资活动和融资活动。

.. _accounting/reporting/tax-report:

税务报告
==========

:guilabel:`税务报告` 显示按类型（:guilabel:`销售`/:guilabel:`采购`）分组的所有税收的 :guilabel:`净额` 和 :guilabel:`税额`。

.. toctree::
   :titlesonly:

   reporting/tax_returns
   reporting/tax_carryover
   reporting/analytic_accounting
   reporting/budget
   reporting/intrastat
   reporting/data_inalterability
   reporting/silverfin
   reporting/customize
   reporting/year_end
