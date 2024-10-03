======================
应用内购买 (IAP)
======================

.. |IAP| replace:: :abbr:`IAP (应用内购买)`

应用内购买 (IAP) 是可选服务，可增强 Odoo 数据库的功能。每项服务都提供其特定的功能和特色。完整的服务列表可以在 `Odoo IAP 目录 <https://iap.odoo.com/iap/all-in-app-services>`_ 上查看。

.. image:: in_app_purchase/iap.png
   :align: center
   :alt: IAP 目录，展示了 IAP.Odoo.com 上提供的各种服务。

.. example::
   :guilabel:`SMS` 服务直接从数据库向联系人发送短信，而 :guilabel:`文档数字化` 服务利用光学字符识别 (OCR) 和人工智能 (AI) 数字化扫描或 PDF 供应商账单、费用和简历。

|IAP| 服务在使用之前**不需要**进行配置或设置。Odoo 用户只需在应用中点击服务即可激活它。然而，每项服务都需要预付的积分，当积分用完时，用户**必须** :ref:`购买更多 <iap/buying_credits>` 才能继续使用该服务。

.. note::
   拥有有效订阅的 Odoo 企业用户可以免费获得积分，用于测试 IAP 功能，然后再决定是否为数据库购买更多积分。这包括演示/培训数据库、教育数据库和免费单应用数据库。

.. _in_app_purchase/portal:

IAP 服务
============

|IAP| 服务由 Odoo 及第三方提供，涵盖广泛的用途。

Odoo 提供的 |IAP| 服务如下：

- :guilabel:`文档数字化`：使用 OCR 和 AI 数字化扫描或 PDF 供应商账单、费用和简历。
- :guilabel:`合作伙伴自动完成`：自动填充联系人记录中的公司数据。
- :guilabel:`SMS`：直接从数据库向联系人发送 SMS 短信。
- :guilabel:`线索生成`：根据一组标准生成线索，并将网站访问者转换为优质线索和商机。
- :guilabel:`邮寄`：通过邮政向全球客户发送发票和跟进报告。
- :guilabel:`使用 itsme®️ 进行签名识别`：在 Odoo *签名* 中要求文件签署方使用其身份平台 *itsme®* 提供身份验证（该平台在比利时和荷兰提供）。

有关所有当前提供的服务（由 Odoo 以外的开发者提供）的更多信息，请访问 `Odoo IAP 目录 <https://iap.odoo.com/iap/all-in-app-services>`_。

使用 IAP 服务
----------------

|IAP| 服务与 Odoo 自动集成，用户**不需要**配置任何设置。要使用服务，只需在数据库中与其交互即可。

.. example::
   以下流程展示了如何从联系人的记录中使用 *SMS* |IAP| 服务。

   可以通过点击数据库中的 :guilabel:`📱 SMS` 图标来完成此操作。

   .. image:: in_app_purchase/sms-icon.png
      :align: center
      :alt: Odoo 数据库中联系人信息表单上的 SMS 图标。

   以下步骤展示了在 Odoo 中使用 *SMS* |IAP| 服务的方式：

   首先，导航到 :menuselection:`联系人应用`，然后点击在联系人表单的 :guilabel:`电话` 或 :guilabel:`手机` 字段中输入了手机号的联系人。

   接着，找到出现在 :guilabel:`电话` 或 :guilabel:`手机` 字段右侧的 :guilabel:`📱 SMS` 图标。点击 :guilabel:`📱 SMS` 图标，会弹出一个 :guilabel:`发送 SMS 短信` 窗口。

   在弹出窗口的 :guilabel:`消息` 字段中输入消息内容。然后点击 :guilabel:`发送 SMS` 按钮。Odoo 会通过 SMS 将消息发送给联系人，并在联系人的 *沟通记录* 中记录发送的内容。

   发送 SMS 消息后，*SMS* |IAP| 服务的预付积分会自动从现有积分中扣除。如果没有足够的积分发送消息，Odoo 会提示用户购买更多积分。

.. seealso::
   有关如何使用各种 |IAP| 服务的更多信息，以及与 Odoo 中 SMS 功能相关的详细说明，请参阅以下文档：

   - :doc:`线索挖掘 <../sales/crm/acquire_leads/lead_mining>`
   - :doc:`通过合作伙伴自动完成丰富您的联系人库 <../sales/crm/optimize/partner_autocomplete>`
   - :doc:`SMS 营销 <../marketing/sms_marketing>`

.. _in_app_purchase/credits:

IAP 积分
===========

每次使用 |IAP| 服务时，都会消耗该服务的预付积分。当积分不足以继续使用某项服务时，Odoo 会提示购买更多积分。还可以设置电子邮件提醒，当 :ref:`积分不足 <in_app_purchase/low-credits>` 时收到提醒。

积分可以从 `Odoo IAP 目录 <https://iap.odoo.com/iap/all-in-app-services>`_ 中以 *套餐* 形式购买，每项服务的定价不同。

.. example::
   `SMS 服务 <https://iap.odoo.com/iap/in-app-services/1>`_ 提供四种套餐，面额如下：

   - :guilabel:`入门套餐`：10 积分
   - :guilabel:`标准套餐`：100 积分
   - :guilabel:`高级套餐`：500 积分
   - :guilabel:`专家套餐`：1,000 积分

   .. image:: in_app_purchase/packs.png
      :align: center
      :alt: 四种不同的 SMS IAP 服务积分套餐。

   消耗的积分数量取决于 SMS 的长度和目的地国家。

   有关更多信息，请参阅 :doc:`SMS 定价和常见问题 <../marketing/sms_marketing/pricing_and_faq>` 文档。

.. _iap/buying_credits:

购买积分
-----------

如果积分不足以执行任务，数据库会自动提示购买更多积分。

用户可以通过导航到 :menuselection:`设置应用 --> 联系人部分`，并在 :guilabel:`Odoo IAP` 设置下点击 :guilabel:`查看我的服务`，检查每项服务的当前积分余额，并手动购买更多积分。

这样会显示一个 :guilabel:`IAP 服务` 页面，列出了数据库中的各种 |IAP| 服务。在此页面中，点击某项 |IAP| 服务以打开其 :guilabel:`账户信息` 页面，可以在该页面购买更多积分。

手动购买积分
~~~~~~~~~~~~~~~~~~~~

要在 Odoo 中手动购买积分，请按照以下步骤操作：

首先，转到 :menuselection:`设置应用`，并在 :guilabel:`搜索...` 栏中输入 `IAP`。或者，用户可以向下滚动到 :guilabel:`联系人` 部分。在 :guilabel:`联系人` 部分，点击 :guilabel:`Odoo IAP` 下的 :guilabel:`查看我的服务`。

.. image:: in_app_purchase/view-services.png
   :align: center
   :alt: 设置应用显示了 Odoo IAP 标题和查看我的服务按钮。

这样会显示一个 :guilabel:`IAP 账户` 页面，列出了数据库中的各种 |IAP| 服务。在此页面中，点击某项 |IAP| 服务以打开其 :guilabel:`账户信息` 页面，可以在该页面购买更多积分。

在 :guilabel:`账户信息` 页面上，点击 :guilabel:`购买积分` 按钮。此操作会在新选项卡中加载一个 :guilabel:`为 (IAP 账户) 购买积分` 页面。在此页面上，点击所需积分套餐旁的 :guilabel:`购买` 按钮。然后，按照提示输入付款详细信息并确认订单。

.. image:: in_app_purchase/buy-pack.png
   :align: center
   :alt: IAP.Odoo.com 上的 SMS 服务页面，显示了可购买的四个积分套餐。

交易完成后，积分即可在数据库中使用。

.. _in_app_purchase/low-credits:

低积分通知
~~~~~~~~~~~~~~~~~~~~~~~

可以在积分不足时收到通知，以避免在使用 |IAP| 服务时用完积分。要做到这一点，请按照以下步骤操作：

转到 :menuselection:`设置应用`，并在 :guilabel:`搜索...` 栏中输入 `IAP`。在 :guilabel:`联系人` 部分，点击 :guilabel:`Odoo IAP` 下的 :guilabel:`查看我的服务`。

在 :guilabel:`IAP 账户` 页面中会以列表视图显示可用的 |IAP| 账户。在此页面中，点击所需的 |IAP| 账户查看该服务的 :guilabel:`账户信息` 页面。

在 :guilabel:`账户信息` 页面上，勾选 :guilabel:`警告我` 复选框。此操作会显示表单中的两个字段：:guilabel:`阈值` 和 :guilabel:`警告邮箱`。

在 :guilabel:`阈值` 字段中，输入 Odoo 应用于该服务的最低积分阈值。在 :guilabel:`警告邮箱` 字段中，输入接收通知的邮箱地址。

当积分余额低于设定的 :guilabel:`阈值` 时，Odoo 会向 :guilabel:`警告邮箱` 发送低积分警告。
