:show-content:

==============================
Odoo中的邮件沟通
==============================

在与记录（如CRM机会、销售订单、发票等）相关的Odoo沟通中，有一个名为 **chatter** 的讨论线程，通常显示在记录的右侧。

在chatter中，您可以直接发送电子邮件或Odoo通知给文档的关注者（取决于他们的通知偏好），记录内部备注，发送WhatsApp消息或SMS，安排活动。

如果关注者回复消息，回复会更新chatter，Odoo将其作为通知传递给关注者。所有的电子邮件 - 包括发出的和收到的 - 都会出现在同一个chatter中。

.. _email-online-sh:

Odoo Online和Odoo.sh用户
=============================

在Odoo Online和Odoo.sh上，发出的和收到的电子邮件开箱即用，**不需要做任何操作**。一切都已经在您的子域上配置好了。

默认情况下，发出的电子邮件使用以下 :ref:`通知电子邮件地址 <email-outbound-notifications>` `notifications@company-name.odoo.com`。

.. _email-online-sh-domain:

使用其他域
--------------------

如果您不希望从Odoo的子域 `@company-name.odoo.com` 发送电子邮件，而是想 :ref:`使用自己的域 <email-outbound-custom-domain>`，**在域和Odoo中将需要额外配置**。这引入了额外的复杂性，并需要技术知识（主要是关于DNS和邮件协议的）。

通过添加域并配置管理访问权限，您还可以访问 :ref:`新域别名 <email-outbound-alias-domain>` 页面以配置公司别名。如果只配置了一个域，那么该域将由数据库中的所有公司共享。

如果您希望继续使用Odoo的邮件服务器，您将需要 :ref:`配置SPF和DKIM <email-domain-spf>`。

如果 :ref:`您希望使用自己的邮件服务器 <email-outbound-custom-domain-smtp-server>`，您将需要遵循邮件服务器提供商的具体文档。

对于收到的电子邮件，在添加您的域后， :ref:`客户的回复将会回到您的域 <email-inbound-custom-domain>`，并且您需要使用三种可能的方式之一将电子邮件返回到Odoo（使用 :ref:`收件邮件服务器 <email-inbound-custom-domain-incoming-server>`、 :ref:`重定向/转发 <email-inbound-custom-domain-redirections>` 或 :ref:`DNS MX记录 <email-inbound-custom-domain-mx>`）。一切都在 :doc:`管理收件消息文档 <email_communication/email_servers_inbound>` 中有介绍。

.. _email-on-premise:

本地部署用户
================

如果您使用本地部署，您需要完全配置您的发出和收件电子邮件：

- 对于发出电子邮件，您将需要 :ref:`使用SMTP服务器和自定义域 <email-outbound-custom-domain-odoo-server>`。
- 对于收件电子邮件，请设置足够低的频率以响应，但不要过高以避免对系统或提供商造成压力。由于此原因以及配置的简单性，我们通常建议使用收件邮件服务器。要使用SMTP服务器，请查看 :ref:`"为收件消息使用自定义域" 文档 <email-inbound-custom-domain>`。

.. _email-third-party-server:

使用第三方邮件服务器
==========================

Odoo文档还涵盖了几种流行的邮件服务器。由于它们需要特定的授权和配置，它们增加了复杂性。因此，建议使用Odoo的发件邮件服务器。

- :doc:`Outlook文档 <email_communication/azure_oauth>`
- :doc:`Gmail文档 <email_communication/google_oauth>`
- :doc:`Mailjet文档 <email_communication/mailjet_api>`

.. note::
   每个提供商都有其自己的限制。在配置之前请先研究所需的提供商。例如，Outlook和Gmail可能不适合大型营销活动。

.. seealso::
   - :doc:`活动 <../essentials/activities>`
   - :doc:`讨论应用 <../productivity/discuss>`
   - :doc:`摘要邮件 <companies/digest_emails>`
   - :doc:`邮件营销应用 <../marketing/email_marketing>`
   - :doc:`邮件模板 <companies/email_template>`
   - :ref:`通过邮件别名创建费用 <expenses/email_expense>`
   - :ref:`通过邮件别名创建帮助工单 <helpdesk/receiving_tickets/email-alias>`
   - :ref:`通过邮件别名创建潜在客户 <crm/configure_email_alias>`
   - :ref:`通过邮件别名创建项目任务 <task_creation/email_alias>`
   - :doc:`本地部署用户的技术邮件网关 <../../administration/on_premise/email_gateway>`
   - :ref:`通过命令行接口配置带有发件邮件服务器的Odoo数据库的技术启动 <reference/cmdline/server/emails>`

.. toctree::
   :titlesonly:

   email_communication/email_servers_inbound
   email_communication/email_servers_outbound
   email_communication/email_domain
   email_communication/azure_oauth
   email_communication/google_oauth
   email_communication/mailjet_api
   email_communication/faq
