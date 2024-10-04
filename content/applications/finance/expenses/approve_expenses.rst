================
审批费用报销单
================

在 Odoo 中，并不是所有人都可以审批费用报销单，只有具有相应权限的用户才能进行审批。这意味着用户 **必须** 至少拥有 *费用* 应用的 *团队审批人* 权限。拥有相应权限的员工可以查看费用报销单、批准或拒绝报销单，并通过集成的通信工具提供反馈。

请参考 :doc:`访问权限文档 </applications/general/users>` 以了解更多有关管理用户及其访问权限的信息。

查看费用报销单
================

通常具有审批权限的用户（如经理）可以轻松查看他们有权限访问的所有费用报销单。前往 :menuselection:`费用应用 --> 费用报销单`，即可查看 :guilabel:`所有报销单` 仪表盘。

您将看到所有状态为 :guilabel:`待提交`、:guilabel:`已提交`、:guilabel:`已批准`、:guilabel:`已过账` 或 :guilabel:`已完成` 的费用报销单列表。状态为 :guilabel:`已拒绝` 的报销单默认情况下被隐藏。

.. image:: approve_expenses/expense-reports-list.png
   :align: center
   :alt: 需要验证的报销单可在“待批准的报销单”页面找到。

审批费用报销单
=======================

费用报销单可以通过两种方式批准：:ref:`单个批准 <expenses/individual>` 或 :ref:`批量批准 <expenses/multiple>`。

.. important::
   只有状态为 :guilabel:`已提交` 的报销单才能被批准。

   建议通过在左侧栏的 :guilabel:`状态` 部分勾选 :guilabel:`已提交` 筛选器旁的复选框，仅显示 :guilabel:`已提交` 的报销单。

   如果某个报销单 **无法** 批准，则 :guilabel:`批准报销单` 按钮 **不会** 出现在 :guilabel:`所有报销单` 页面上。

.. _expenses/individual:

审批单个报销单
--------------------

要审批单个报销单，请前往 :menuselection:`费用应用 --> 费用报销单`，点击某个报销单以查看报销单表单。

此时会显示几个选项：:guilabel:`批准`、:guilabel:`拒绝` 和 :guilabel:`重置为草稿`。

点击 :guilabel:`批准` 以批准该报销单。

.. _expenses/multiple:

批量审批报销单
----------------------

要一次批准多个费用报销单，首先前往 :menuselection:`费用应用 --> 费用报销单` 查看报销单列表。接下来，通过勾选每个报销单旁边的复选框来选择要批准的报销单，或者勾选 :guilabel:`员工` 列标题旁的复选框以选择列表中的所有报销单。

然后，点击 :guilabel:`批准报销单` 按钮。

.. image:: approve_expenses/approve-report.png
   :align: center
   :alt: 通过勾选每个报销单旁的复选框来批准多个报销单。

.. tip::
   团队经理可以查看其团队成员的所有费用报销单。

   要执行此操作，请在 :guilabel:`所有报销单` 页面上，点击搜索栏右侧的 :icon:`fa-caret-down` :guilabel:`（下箭头）`，然后在 :guilabel:`筛选器` 部分点击 :guilabel:`我的团队`。

   这将显示仅属于经理团队的所有报销单。

   .. image:: approve_expenses/my-team-filter.png
      :align: center
      :alt: 选择“我的团队”筛选器。

拒绝费用报销单
================

费用报销单只能在单个报销单页面上 **拒绝**，而不能在 :guilabel:`所有报销单` 仪表盘上拒绝。要打开单个费用报销单，前往 :menuselection:`费用应用 --> 费用报销单`，然后点击某个报销单以查看报销单表单。

如果需要更多信息（例如缺少的收据），可以在报销单表单的 *沟通区* 中请求必要的信息。在单个费用报销单上，点击 :guilabel:`发送消息` 以打开消息文本框。

输入消息，标记相关人员，然后点击 :guilabel:`发送` 将其发布到沟通区中。消息会发布到沟通区中，相关人员会通过电子邮件收到通知。

.. note::
   只有特定报销单的 *关注者* 才能在消息中被标记。要查看谁是关注者，请点击 :icon:`fa-user-o` :guilabel:`（用户）` 图标以显示该费用报销单的关注者。

   .. image:: approve_expenses/chatter.png
      :align: center
      :alt: 在沟通区中发送消息。

要拒绝费用报销单，点击 :guilabel:`拒绝`，此时会弹出 :guilabel:`拒绝报销单` 窗口。在 :guilabel:`拒绝报销单的原因` 字段下输入拒绝的简要说明，然后点击 :guilabel:`拒绝`。

.. image:: approve_expenses/refuse-expense.png
   :align: center
   :alt: 在沟通区中发送消息。

一旦费用报销单被拒绝，其状态将更改为 :guilabel:`已拒绝`，并且左上角唯一显示的按钮是 :guilabel:`重置为草稿`。
