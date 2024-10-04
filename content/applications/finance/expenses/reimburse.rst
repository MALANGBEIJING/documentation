===================
报销员工费用
===================

在费用报告 :doc:`发布到会计日记账 <../expenses/post_expenses>` 之后，下一步是报销员工的费用。与批准和发布费用一样，员工的报销也可以通过现金、支票或直接存款的方式进行 (:ref:`单独报销 <expenses/reimburse-single>` 或 :ref:`批量报销 <expenses/reimburse-bulk>`)，或者 :ref:`在工资单中报销 <expenses/reimburse-payslip>`。

设置
====

费用报销可以通过工资单、支票、现金或银行转账进行支付。首先，通过导航到 :menuselection:`费用应用 --> 配置 --> 设置` 来配置各种设置。

要通过 :ref:`工资单报销员工费用 <expenses/reimburse-payslip>`，在 :guilabel:`费用` 部分中勾选 :guilabel:`工资单报销` 选项旁的复选框。

接着，在 :guilabel:`会计` 部分中设置支付方式。点击 :guilabel:`支付方式` 下拉菜单，选择所需的支付选项。默认选项包括 :guilabel:`手动 (现金)`，:guilabel:`支票 (银行)`，:guilabel:`NACHA (银行)`，以及其他选项。将此字段留空则允许使用**所有**可用的支付选项。

完成所有设置后，点击 :guilabel:`保存` 以激活设置。

.. _expenses/reimburse-single:

单独报销
========

要单独报销费用报告，首先导航到 :menuselection:`费用应用 --> 费用报告`。所有费用报告都以默认列表视图显示。点击要报销的费用报告以查看报告详细信息。

.. important::
   只有状态为 :guilabel:`已发布` 的费用报告可以进行报销。

点击费用报告左上角的 :guilabel:`登记付款` 按钮，弹出一个 :guilabel:`登记付款` 弹窗。在弹窗中输入以下信息：

- :guilabel:`日记账`：使用下拉菜单选择要将付款发布到的会计日记账。默认选项为 :guilabel:`银行` 或 :guilabel:`现金`。
- :guilabel:`支付方式`：使用下拉菜单选择支付方式。如果 :guilabel:`现金` 被选择为 :guilabel:`日记账`，唯一可用的选项是 :guilabel:`手动`。如果 :guilabel:`银行` 被选择为 :guilabel:`日记账`，默认选项为 :guilabel:`手动` 或 :guilabel:`支票`。
- :guilabel:`收款人银行账户`：选择员工的银行账户以发送付款。如果员工在 :ref:`员工表单中的私密信息标签 <employees/private-info>` 中登记了银行账户，系统会自动填充该银行账户。
- :guilabel:`金额`：此字段会自动填入总报销金额。货币字段位于右侧，可以使用下拉菜单修改。
- :guilabel:`付款日期`：输入付款日期。默认情况下，该字段会填入当天的日期。
- :guilabel:`备忘录`：费用报告中的 :doc:`费用报告摘要 <../expenses/expense_reports>` 字段中的文本会自动填入此字段。

.. image:: reimburse/payment.png
   :align: center
   :alt: 为单个费用报告报销填写的登记付款弹窗。

完成弹窗中的所有字段后，点击 :guilabel:`创建付款` 按钮以登记付款并报销员工费用。

.. _expenses/reimburse-bulk:

批量报销
========

要一次报销多个费用报告，导航到 :menuselection:`费用应用 --> 费用报告` 以查看所有费用报告的列表视图。接着，调整左侧的 :guilabel:`状态` 过滤器，使其只显示状态为 :guilabel:`已发布` 的费用报告。

.. tip::
   调整 :guilabel:`状态` 过滤器以仅显示 :guilabel:`已发布` 的费用报告不是必须的，但这可以省去选择每个单独报告的步骤。

勾选 :guilabel:`员工` 列标题旁的复选框以选择列表中的所有报告。勾选后，页面顶部会显示选中的费用报告数量 (:guilabel:`(#) 已选`)。同时，左上角还会出现 :guilabel:`登记付款` 按钮。

.. image:: reimburse/multiple-reports.png
   :align: center
   :alt: 已过滤为已发布状态的费用报告，登记付款按钮已显示。

点击 :guilabel:`登记付款` 按钮，弹出一个 :guilabel:`登记付款` 弹窗。在弹窗中输入以下信息：

- :guilabel:`日记账`：使用下拉菜单选择付款应发布到的会计日记账。默认选项为 :guilabel:`银行` 或 :guilabel:`现金`。
- :guilabel:`支付方式`：使用下拉菜单选择支付方式。如果 :guilabel:`现金` 被选择为 :guilabel:`日记账`，唯一可用的选项是 :guilabel:`手动`。如果 :guilabel:`银行` 被选择为 :guilabel:`日记账`，默认选项为 :guilabel:`手动` 或 :guilabel:`支票`。
- :guilabel:`合并付款`：如果为同一员工选择了多个费用报告，此选项会出现。勾选该复选框以只进行一次付款，而不是向同一员工发出多次付款。
- :guilabel:`付款日期`：输入付款日期。默认情况下，该字段会填入当天的日期。

.. image:: reimburse/register.png
   :align: center
   :alt: 填写的登记付款弹窗。

完成弹窗中的所有字段后，点击 :guilabel:`创建付款` 按钮以登记付款并报销员工费用。

.. _expenses/reimburse-payslip:

在下一个工资单中报告
=======================

如果在 *设置* 页面中激活了 *工资单报销* 选项，付款可以添加到员工的下一个工资单中，而不是手动发放。

.. important::
   通过工资单报销费用**只能**单独进行，且只能在费用报告状态为 *已批准* 时执行。一旦费用报告状态为 *已发布*，则不再显示通过下一工资单报销的选项。

导航到 :menuselection:`费用应用 --> 费用报告`，点击要在下一工资单中报销的个人费用报告。点击 :guilabel:`在下一个工资单中报告` 智能按钮，费用会被添加到该员工下一次发放的工资单中。同时，聊天记录中会记录一条消息，表明该费用已添加到下一份工资单中。

.. image:: reimburse/pay-via-payslip.png
   :align: center
   :alt: 状态为已批准的费用报告中显示的在下一工资单中报告按钮。

费用报告的状态仍然为 :guilabel:`已批准`。只有在工资单处理后，状态才会变为 :guilabel:`已发布`（随后变为 :guilabel:`已完成`）。

.. seealso::
   有关处理工资单的更多信息，请参阅 :doc:`工资单 <../../hr/payroll/payslips>` 文档。
