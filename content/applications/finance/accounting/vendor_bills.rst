:show-content:

============
供应商账单
============

在 Odoo 中，我们可以 **手动** 或 **自动** 登记供应商账单，同时 **应付账款报告** 提供所有未结账单的概览，帮助我们及时支付正确的金额。

.. seealso::
   - 教程 `登记供应商账单 <https://www.odoo.com/slides/slide/registering-a-vendor-bill-1683?fullscreen=1>`_
   - :doc:`/applications/inventory_and_mrp/purchase/manage_deals/manage`

账单创建
=============

手动创建
--------

通过前往 :menuselection:`会计 --> 供应商 --> 账单` 并点击 :guilabel:`创建` 手动创建供应商账单。

自动创建
-------------

供应商账单可以通过 **发送电子邮件** 到与采购日记账关联的 :ref:`电子邮件别名 <invoice-digitization/email-alias>` 自动创建，或通过在 :menuselection:`会计 --> 供应商 --> 账单` 中上传 PDF 文件并点击 :guilabel:`上传` 来自动创建。

账单填写
===============

无论账单是手动创建还是自动创建，确保以下字段填写正确：

- :guilabel:`供应商`：Odoo 根据供应商的注册信息、以前的采购订单或账单自动填写部分信息。
- :guilabel:`账单参考`：添加供应商提供的销售订单参考，并用于在收到产品时进行 :ref:`匹配 <payments/matching>`。
- :guilabel:`自动完成`：选择过去的账单/采购订单来自动填写文档。应先填写 :guilabel:`供应商` 字段再完成此字段。
- :guilabel:`账单日期`：该字段是文件的发出日期。
- :guilabel:`会计日期`：该字段是文件在会计系统中注册的日期。
- :guilabel:`付款参考`：在登记付款时，它会自动显示在 :guilabel:`备注` 字段中。
- :guilabel:`收款银行`：指定付款应支付到哪个账户号码。
- :guilabel:`到期日` 或 :guilabel:`条款` 用于支付账单。
- :guilabel:`日记账`：选择该账单应记录在哪个日记账中，以及 :doc:`货币 <get_started/multi_currency>`。

.. image:: vendor_bills/bill-completion.png
   :alt: 填写供应商账单

.. note::
   - 可以通过点击 :guilabel:`发送进行数字化` 来 :doc:`数字化账单 <vendor_bills/invoice_digitization>` 以自动完成。
   - 如果您上传了账单，PDF 文件将显示在屏幕右侧，方便您轻松填写账单信息。

账单确认
=================

在完成文档后，点击 :guilabel:`确认`。文档状态变为 :guilabel:`已过账`，并根据发票配置生成日记账分录。

.. note::
   一旦确认，无法再更新文档。如果需要进行更改，请点击 :guilabel:`重置为草稿`。

账单付款
============

在支付供应商账单后，点击 :guilabel:`登记付款` 以打开新的付款窗口。

选择 :guilabel:`日记账`、:guilabel:`付款方式`、您希望支付的 :guilabel:`金额`（全额或部分付款），并选择 :guilabel:`货币`。如果是部分付款（支付金额少于供应商账单的剩余总金额），则 :guilabel:`付款差异` 字段将显示未结余额。
您有两个选项：

- :guilabel:`保持未结`：保持账单未结状态，并标记为 :guilabel:`部分付款`；
- :guilabel:`标记为全额支付`：在这种情况下，请在 :guilabel:`将差额过账到` 字段中选择一个账户，并根据需要更改 :guilabel:`标签`。将创建一笔日记账分录，以将应收账款与所选账户结清。

.. image:: vendor_bills/partial-payment.png
   :alt: 登记部分付款

如果在供应商账单中正确设置了 :guilabel:`付款参考`，则 :guilabel:`备注` 字段会自动填写。如果该字段为空，请选择供应商发票号作为参考。

一旦确认，账单上会显示 :guilabel:`付款中` 横幅，直到它被 :doc:`对账 <bank/reconciliation>`。

应付账款报告
===================

要查看未结供应商账单及其相关的到期日，您可以使用 **应付账款报告**。前往 :menuselection:`会计 --> 报告 --> 合作伙伴报告：应付账款`。

点击供应商名称，查看所有未结账单的详细信息、应付金额、到期日等。

.. Note::
   - 点击 :guilabel:`保存` 按钮，您可以将屏幕上可用的信息导出为 PDF 或 XLSX 文件，并将其保存到您选择的文件夹中。
   - 如果您的供应商处于未完全交货的状态并根据产品的发货情况向您发送发票，或者您的供应商向您发送部分账单或要求支付定金，您可能会为同一采购订单收到多张账单。

.. toctree::
   :titlesonly:

   vendor_bills/invoice_digitization
   vendor_bills/assets
   vendor_bills/deferred_expenses
