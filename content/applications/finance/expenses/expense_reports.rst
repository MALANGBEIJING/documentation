===============
费用报销单
===============

当费用准备好提交时（例如，在商务旅行结束时或每月一次），需要创建一个 *费用报销单*。打开主界面 :menuselection:`费用应用` 仪表盘，默认显示 :guilabel:`我的费用` 仪表盘。或者，导航到 :menuselection:`费用应用 --> 我的费用 --> 我的费用`。

费用根据状态进行颜色编码。任何状态为 :guilabel:`待报告`（需要添加到费用报销单中的费用）的费用以蓝色文本显示。其他状态 (:guilabel:`待提交`, :guilabel:`已提交`, 和 :guilabel:`已批准`) 的费用文本显示为黑色。

.. _expenses/create_report:

创建费用报销单
================

首先，在 :guilabel:`我的费用` 仪表盘中选择要添加到报销单中的每项费用，通过勾选每项条目旁的复选框，或快速选择列表中的所有费用（如有需要），通过勾选 :guilabel:`费用日期` 列标题旁的复选框。

另一种快速添加尚未在费用报销单上的所有费用的方法是，在未选择任何费用的情况下点击 :guilabel:`创建报销单` 按钮，Odoo 将自动选择所有状态为 :guilabel:`待提交` 且尚未在其他报销单上的费用。

.. image:: expense_reports/create-report.png
   :align: center
   :alt: 选择要提交的费用，然后创建报销单。

.. note::
   可以从 :guilabel:`我的费用` 列表中选择任何费用，除了状态为 :guilabel:`已批准` 的费用。

   只要列表中至少有一项状态为 :guilabel:`待报告` 或 :guilabel:`待提交` 的费用，:guilabel:`创建报销单` 按钮就会显示。

   当点击 :guilabel:`创建报销单` 按钮时，所有状态为 :guilabel:`待提交` 且未在其他报销单上的费用都会出现在新创建的费用报销单中。

   如果 :guilabel:`我的费用` 报销单上的所有费用都已经关联到另一个报销单，将会弹出一个 :guilabel:`无效操作` 窗口，显示 :guilabel:`您没有费用可报告`。

选择费用后，点击 :guilabel:`创建报销单` 按钮。新报销单将显示所有列出的费用在 :guilabel:`费用` 标签下。如果某个费用附有收据，:icon:`fa-paperclip` :guilabel:`(回形针)` 图标会显示在 :guilabel:`客户可重计费` 和 :guilabel:`分析分配` 列之间。

创建报销单后，费用的日期范围默认显示在 :guilabel:`费用报销单摘要` 字段中。建议编辑此字段，为每个报销单添加简短摘要，以帮助整理费用。在 :guilabel:`费用报销单摘要` 字段中输入报销单的描述，如 `客户行程 NYC` 或 `演示文稿办公用品`。

:guilabel:`员工`, :guilabel:`支付方` 和 :guilabel:`公司` 字段会根据个人费用中的信息自动填写。

接下来，从下拉菜单中选择一个 :guilabel:`经理` 来审核报销单。如有需要，使用下拉菜单更新 :guilabel:`日记账` 字段。

.. image:: expense_reports/expense-report-summary.png
   :align: center
   :alt: 输入简短描述并为报销单选择经理。

如果报销单中缺少某些费用，仍然可以从此报销单表单中添加。为此，请点击 :guilabel:`添加一行` 按钮，位于 :guilabel:`费用` 标签的底部。

弹出窗口 :guilabel:`添加：费用行` 会显示所有可以添加到报销单的费用（状态为 :guilabel:`待提交`）。

如果需要添加一个不在列表中的新费用，点击 :guilabel:`新建` 以 :doc:`创建新费用 <../expenses/log_expenses>` 并将其添加到报销单中。

勾选每项要添加的费用旁的复选框，然后点击 :guilabel:`选择`。

这样会关闭弹出窗口，所选项目现在显示在报销单中。

.. image:: expense_reports/add-an-expense-line.png
   :align: center
   :alt: 在提交前添加更多费用到报销单。

.. note::
   可以在三个地方创建费用报销单：

   #. 导航到主界面 :menuselection:`费用应用` 仪表盘（也可以通过 :menuselection:`费用应用 --> 我的费用 --> 我的费用` 访问）
   #. 导航到 :menuselection:`费用应用 --> 我的费用 --> 我的报销单`
   #. 导航到 :menuselection:`费用应用 --> 费用报销单`

   在这些视图中的任意一个，点击 :guilabel:`新建` 以创建新的费用报销单。

.. _expenses/submit:

提交费用报销单
=================

当费用报销单完成后，下一步是将报销单提交给经理审批。要查看所有费用报销单，导航到 :menuselection:`费用应用 --> 我的费用 --> 我的报销单`。从报销单列表中打开特定报销单。

.. note::
   报销单必须单独提交，**不能** 批量提交。

如果列表很大，将结果按状态分组可能会有帮助，因为只有状态为 :guilabel:`待提交` 的报销单需要提交；状态为 :guilabel:`已批准` 或 :guilabel:`已提交` 的报销单不需要提交。

状态为 :guilabel:`待提交` 的报销单可以通过 :guilabel:`待提交` 状态和蓝色文本识别，而所有其他报销单的文本显示为黑色。

.. image:: expense_reports/expense-status.png
   :align: center
   :alt: 将报销单提交给经理。

.. note::
   每个报销单的状态显示在 :guilabel:`状态` 列中。如果 :guilabel:`状态` 列不可见，请点击行末的 :icon:`oi-settings-adjust` :guilabel:`(附加选项)` 图标，然后在下拉菜单中勾选 :guilabel:`状态` 复选框。

点击报销单打开它，然后点击 :guilabel:`提交给经理`。提交报销单后，下一步是等待经理批准。

.. important::
   :doc:`审批 <../expenses/approve_expenses>` 费用、:doc:`过账 <../expenses/post_expenses>` 费用和 :doc:`报销 <../expenses/reimburse>` 费用 **仅限** 具有相应权限的用户执行。请参阅 :doc:`访问权限文档 </applications/general/users>`。
