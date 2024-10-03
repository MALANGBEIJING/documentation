=============
SMS 营销
=============

利用 :abbr:`SMS (短消息服务)` 作为沟通策略的一部分，可以帮助公司扩大市场覆盖，尤其是在一些电子邮件使用不广泛甚至根本不用的国家。

Odoo 的 *SMS 营销* 应用程序还可以帮助提高重要行为的转化率，例如活动注册、免费试用、购买等，因为基于短信和移动的营销渠道通常会产生更高的 :abbr:`CTOR (点击打开率)` 和 :abbr:`CTR (点击通过率)`。

.. seealso::
   `Odoo 教程: 营销 <https://www.odoo.com/slides/marketing-27>`_

.. cards::

   .. card:: 创建 SMS 消息
      :target: sms_marketing/create_sms

      探索如何创建、配置、发送和安排 SMS 消息。

   .. card:: SMS 分析
      :target: sms_marketing/sms_analysis

      检查与发送的每条 SMS 消息相关的详细报告指标。

   .. card:: SMS 营销活动
      :target: sms_marketing/marketing_campaigns

      了解如何使用 Odoo 为任何场合创建和自定义 SMS 营销活动。

   .. card:: 邮件列表和黑名单
      :target: sms_marketing/mailing_lists_blacklists

      设置自定义邮件列表和黑名单，确保所有 SMS 消息到达正确的收件人。

   .. card:: 定价和常见问题
      :target: sms_marketing/pricing_and_faq

      了解更多关于 Odoo SMS 定价的信息，并查看一些最常见的问题。

SMS 营销仪表板
=======================

当打开应用程序时，Odoo 显示主 :guilabel:`SMS 营销` 仪表板，展示已创建的各种 SMS 邮件，以及与该特定消息相关的重要信息和数据。

:icon:`oi-view-kanban` :guilabel:`看板` 视图是 Odoo 打开应用程序时的默认显示视图，提供了已创建 SMS 邮件的有序展示及其当前状态。

.. note::
   :abbr:`SMS (短消息服务)` 邮件可能具有以下状态之一：
   :guilabel:`草稿`, :guilabel:`队列中`, :guilabel:`发送中` 或 :guilabel:`已发送`。

在主 :guilabel:`SMS 营销` 仪表板的右上角，有几种不同的查看选项可供选择。每一个都以独特的方式展示相同的 SMS 信息。

:icon:`oi-view-list` :guilabel:`列表` 视图以更传统的列表布局提供与 SMS 邮件相关的相同有用数据。

:icon:`fa-calendar` :guilabel:`日历` 视图显示 SMS 邮件的计划发送时间或已发送时间。点击将来的日期会打开一个空白的 SMS 模板，以便在该日期安排发送。

最后，:icon:`fa-area-chart` :guilabel:`图表` 视图以一系列图表和曲线的形式可视化相同的 SMS 相关数据。Odoo 还提供了多种方式对数据进行排序和分组，以进行更详细的分析。

.. toctree::
   :titlesonly:

   sms_marketing/create_sms
   sms_marketing/sms_analysis
   sms_marketing/marketing_campaigns
   sms_marketing/mailing_lists_blacklists
   sms_marketing/pricing_and_faq
