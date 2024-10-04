===================
重新开票费用
===================

如果费用是在客户项目上进行跟踪的，它们可以自动计入客户费用。这可以通过 :ref:`创建费用 <expenses/reinvoice-create>`，将费用添加到销售订单中，并然后 :ref:`创建费用报告 <expenses/reinvoice-report>` 完成。

接着，经理们会 :ref:`批准费用报告 <expenses/reinvoice-approve>`，在此之后会计部门会 :ref:`发布日记账条目 <expenses/reinvoice-approve>`。

最后，一旦费用报告发布到日记账，费用将出现在指定的 :abbr:`SO (销售订单)` 中。然后该 :abbr:`SO (销售订单)` 会被 :ref:`开票 <expenses/reinvoice>`，从而向客户收取费用。

.. important::
   批准费用、将费用发布到会计中、并在 :abbr:`SO (销售订单)` 上重新开票，**仅**对具有适当 :doc:`访问权限 <../../general/users/access_rights>` 的用户可用。

.. seealso::
   本文档提供了创建、提交、批准和发布费用的低级别说明。有关这些步骤的详细说明，请参阅以下文档：

   - :doc:`记录费用 <../expenses/log_expenses>`
   - :doc:`费用报告 <../expenses/expense_reports>`
   - :doc:`批准费用 <../expenses/approve_expenses>`
   - :doc:`在会计中发布费用 <../expenses/post_expenses>`

设置
=====

首先，针对每个费用类别指定开票策略。导航至 :menuselection:`费用应用 --> 配置 --> 费用类别`。点击一个费用类别以查看费用类别表单。在 :guilabel:`开票` 部分，点击 :guilabel:`重新开票费用` 旁的单选按钮选择所需的选项：

- :guilabel:`否`：费用类别不能重新开票。
- :guilabel:`按成本`：费用类别按费用类别表单中设置的成本开票。
- :guilabel:`销售价格`：费用类别按费用表单中设置的销售价格开票。

.. _expenses/reinvoice-create:

创建费用
========

首先，在 :doc:`创建新费用 <../expenses/log_expenses>` 时，需要输入正确的信息才能将费用重新开票给客户。使用下拉菜单，选择要将费用添加到的 :abbr:`SO (销售订单)`，该字段为 :guilabel:`客户重新开票`。

接着，选择费用要发布到的 :guilabel:`分析分配`。如果需要，可以选择多个账户。

要添加另一个 :guilabel:`分析分配`，点击该行以显示 :guilabel:`分析` 弹窗。点击 :guilabel:`添加一行`，然后从下拉字段中选择所需的 :guilabel:`分析分配`。如果选择了多个 :guilabel:`分析分配`，则必须修改 :guilabel:`百分比` 字段。默认情况下，所有字段的百分比为 `100%`。调整所有字段的百分比，使所有选定账户的总和等于 `100%`。

.. example::
   一家油漆公司同意为一个办公楼刷漆，而这座办公楼里有两家公司。会议在现场举行，讨论项目。

   两家公司都同意支付油漆公司员工的差旅费用。在为差旅和酒店创建费用时，**两家公司** 都在 :guilabel:`分析分配` 行中各占 50%。

.. _expenses/reinvoice-report:

创建费用报告
============

在创建费用后，必须 :ref:`创建费用报告 <expenses/create_report>` 并以与其他费用相同的方式 :ref:`提交 <expenses/submit>`。

一旦费用报告提交，:icon:`fa-money` :guilabel:`销售订单` 智能按钮将出现在费用报告和每个重新开票的单个费用记录的顶部。

.. image:: reinvoice_expenses/reinvoice-expense.png
   :align: center
   :alt: 确保费用在重新开票时明确指明了要开票的客户。

.. important::
   选择正确的 :abbr:`SO (销售订单)` 在 :guilabel:`客户重新开票` 字段中是**至关重要的**，因为这是费用报告批准后费用自动开票的方式。

   在费用报告 **批准** 之前，:guilabel:`客户重新开票` 字段可以进行修改。费用报告批准后，该字段将无法再修改。

.. _expenses/reinvoice-approve:

批准并发布费用
===============

在 :doc:`批准费用报告 <../expenses/approve_expenses>` 之前，确保每个费用行的 :guilabel:`分析分配` 部分都已填写。

如果缺少 :guilabel:`分析分配` 条目，请从下拉菜单中分配正确的账户，然后点击 :guilabel:`批准`。

.. image:: reinvoice_expenses/analytic-dist.png
   :align: center
   :alt: 分析分配行已填写的费用报告。

.. note::
   只有在费用报告 :ref:`提交 <expenses/submit>` 后，:guilabel:`批准` 按钮才会出现。

会计部门通常负责 :doc:`发布日记账条目 <../expenses/post_expenses>`。要将费用发布到会计日记账中，点击 :guilabel:`发布日记账条目`。费用报告一经批准，即可发布。

费用发布到日记账后，:abbr:`SO (销售订单)` 将**仅**在此之后更新。一旦日记账条目发布，费用将显示在参考的 :abbr:`SO (销售订单)` 上。

.. _expenses/reinvoice:

开票费用
========

费用报告批准后，日记账条目已发布，:abbr:`SO (销售订单)` 更新，客户可以被开票。

选择费用报告，点击 :icon:`fa-money` :guilabel:`销售订单` 智能按钮以打开 :abbr:`SO (销售订单)`。重新开票的费用现在显示在 :abbr:`SO (销售订单)` 中。

.. note::
   一个费用报告中可以引用多个 :abbr:`SO (销售订单)`。如果引用了多个 :abbr:`SO (销售订单)`，点击 :guilabel:`销售订单` 智能按钮将打开一个列表，显示与该费用报告关联的所有 :abbr:`SO (销售订单)`。点击某个 :abbr:`SO (销售订单)` 以打开单个 :abbr:`SO (销售订单)` 详细信息。

费用列在 :abbr:`SO (销售订单)` 的 :guilabel:`订单行` 选项卡中。

.. image:: reinvoice_expenses/so-details.png
   :align: center
   :alt: 在进入后看到列在销售订单中的费用。

接着，点击 :guilabel:`创建发票`，将出现一个 :guilabel:`创建发票` 弹窗。选择发票类型为 :guilabel:`常规发票`，:guilabel:`预付款 (百分比)`，或 :guilabel:`预付款 (固定金额)`。然后点击 :guilabel:`创建草稿发票`。这样可以为客户创建一张草稿发票。点击 :guilabel:`确认` 以确认发票，客户将收到这些费用的发票。
