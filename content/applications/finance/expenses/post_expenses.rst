=============
发布费用
=============

一旦费用报告被 :doc:`批准 <../expenses/approve_expenses>`，下一步是将费用报告发布到相应的会计日记账中。

.. important::
   要将费用报告发布到会计日记账中，用户 **必须** 拥有以下 :doc:`访问权限 <../../general/users/access_rights>`：

   - 会计：*会计师* 或 *顾问*
   - 费用：*经理*

只有状态为 *已批准* 的费用报告才能将费用发布到日记账中。要查看所有费用报告，请导航到 :menuselection:`费用应用 --> 费用报告`。接着，为了只查看需要发布的**已批准**的费用报告，调整左侧的过滤器，只勾选 :guilabel:`已批准` 复选框。

.. image:: post_expenses/post-reports.png
   :align: center
   :alt: 通过点击费用报告查看要发布的报告，然后报告待发布。

.. note::
   默认的 :guilabel:`所有报告` 仪表盘会显示所有费用报告，除了状态为 :guilabel:`已拒绝` 的报告。

费用报告可以通过两种方式发布到会计日记账：:ref:`单独发布 <expenses/individual-reports>` 或 :ref:`批量发布 <expenses/multiple-reports>`。

.. _expenses/individual-reports:

单独发布报告
-----------------------

要单独发布报告，导航到 :menuselection:`费用应用 --> 费用报告`，然后点击状态为 :guilabel:`已批准` 的单个报告，以查看报告表单。在此视图中，有几个选项可供选择：:guilabel:`发布日记账分录`，:guilabel:`在下一个工资单中报告`，:guilabel:`拒绝`，或 :guilabel:`重置为草稿`。

点击 :guilabel:`发布日记账分录` 以发布报告。

费用发布的会计日记账会显示在费用报告的 :guilabel:`日记账` 字段中。

将费用发布到会计日记账后，屏幕顶部会出现一个 :guilabel:`日记账分录` 智能按钮。点击 :guilabel:`日记账分录` 智能按钮，日记账分录的详细信息将显示，状态为 :guilabel:`已发布`。

.. _expenses/multiple-reports:

批量发布报告
---------------------

要一次发布多个费用报告，导航到 :menuselection:`费用应用 --> 费用报告`，以查看费用报告列表。接着，勾选每个要批准的报告旁的复选框。

.. note::
   只有状态为 :guilabel:`已批准` 的费用报告才能将费用发布到会计日记账中。如果选择了**无法**发布的费用报告，例如未批准的报告，或者该报告已经发布到日记账中，:guilabel:`发布分录` 按钮将**不可见**。

.. tip::
   要只选择**已批准**的费用报告，调整左侧的过滤器，只勾选 :guilabel:`已批准` 复选框。接着，勾选 :guilabel:`员工` 列标题旁的复选框，以一次选择列表中**所有** :guilabel:`已批准` 的报告。

接着，点击 :guilabel:`发布分录` 按钮。

.. image:: post_expenses/post-entries.png
   :align: center
   :alt: 从费用报告视图中一次发布多个报告，使用已批准过滤器。
