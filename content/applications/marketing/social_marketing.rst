================
社交营销
================

Odoo 的 *社交营销* 应用程序帮助内容营销人员创建和安排帖子，管理各种社交媒体账户，分析内容效果，并在一个集中位置直接与社交媒体关注者互动。

.. seealso::
   - `Odoo 教程: 营销 <https://www.odoo.com/slides/marketing-27>`_

.. cards::

   .. card:: 社交帖子
      :target: social_marketing/social_posts

      探索如何使用 Odoo 创建和自定义社交媒体帖子。

   .. card:: 社交活动
      :target: social_marketing/social_campaigns

      了解此应用程序提供的所有不同活动和营销工具。

社交媒体账户
=====================

为了使用 Odoo *社交营销* 创建社交帖子并分析内容，必须将社交媒体账户作为 *流* 添加到应用程序的主仪表板上。

.. note::
   请注意，个人资料 **不能** 作为流添加。Odoo *社交营销* 的主要用途是管理和分析社交媒体平台上的商业账户。

.. warning::
   Odoo *社交营销* 对社交媒体账户有一些限制。例如，Odoo **无法** 处理大量不同页面（如 ~40 页）在同一公司下的情况。由于 API 的构建方式，多公司环境中也存在同样的限制。

.. warning::
   在多公司环境中，如果每个公司没有同时激活页面，则会导致权限错误。

   例如，如果从主 Odoo 仪表板中只选择公司 1 并激活 *Facebook 页面 1* 和 *Facebook 页面 2*，那么这些页面将可以在 *社交营销* 仪表板上访问。

   但是，如果在同一个数据库中，用户从标题中的公司下拉菜单中添加公司 2 并尝试添加相同的流，则会导致权限错误。

   .. image:: social_marketing/permission-error.png
      :align: center
      :alt: 错误尝试添加流时显示的权限错误视图。

社交媒体流
====================

要将社交媒体商业账户添加为流，请导航到 :menuselection:`社交营销应用程序` 并选择位于左上角的 :guilabel:`添加流` 按钮。这样会弹出 :guilabel:`添加流` 窗口。

.. image:: social_marketing/add-stream-social-popup.png
   :align: center
   :alt: 当在 Odoo 中选择“添加流”时弹出的窗口视图。

在 :guilabel:`添加流` 弹出窗口中，选择为商业帐户添加新帐户的社交媒体平台：:guilabel:`Facebook`、:guilabel:`Instagram`、:guilabel:`LinkedIn`、:guilabel:`Twitter` 和 :guilabel:`YouTube`。

点击 :guilabel:`添加流` 弹出窗口中的所需社交媒体平台后，Odoo 直接导航到该特定社交媒体平台的授权页面，必须授予权限才能将该特定社交媒体帐户作为流添加到 *社交营销* 应用程序中。

.. image:: social_marketing/social-marketing-dashboard.png
   :align: center
   :alt: 填充了社交媒体流和内容的 Odoo 社交营销仪表板的示例。

授予权限后，Odoo 会返回到主 :guilabel:`社交营销` 仪表板上的 :guilabel:`Feed`，并添加一个带有该帐户帖子的列。可以随时添加帐户/流。

.. important::
   只要授予权限的 :guilabel:`Facebook` 帐户是页面的管理员，就可以将 :guilabel:`Facebook` 页面添加为流。还应注意，不同的页面可以为不同的流添加。

.. note::
   :guilabel:`Instagram` 帐户通过 :guilabel:`Facebook` 登录添加，因为它使用相同的 API。这意味着必须将 :guilabel:`Instagram` 帐户链接到 :guilabel:`Facebook` 帐户，才能在 Odoo 中显示为流。

社交媒体页面
=================

另一种快速将社交媒体帐户链接到 Odoo *社交营销* 的方法是在 :guilabel:`社交媒体` 页面上完成。要访问 :guilabel:`社交媒体` 页面，请导航到 :menuselection:`社交营销应用程序 --> 配置 --> 社交媒体`。

在 :guilabel:`社交媒体` 页面上，所有社交媒体选项都有一个 :guilabel:`链接帐户` 按钮：:guilabel:`Facebook`、:guilabel:`Instagram`、:guilabel:`LinkedIn`、:guilabel:`Twitter`、:guilabel:`YouTube` 和 :guilabel:`推送通知`。

.. image:: social_marketing/social-media-page.png
   :align: center
   :alt: Odoo 社交营销应用程序中的社交媒体页面视图。

社交账户页面
====================

要查看数据库中链接的所有社交账户和网站的列表，请转到 :menuselection:`社交营销应用程序 --> 配置 --> 社交账户`。此 :guilabel:`社交账户` 页面显示 :guilabel:`名称`、:guilabel:`用户名/简称`、:guilabel:`社交媒体` 平台、由谁 :guilabel:`创建` 以及与之关联的 :guilabel:`公司`。

.. image:: social_marketing/social-accounts-page.png
   :align: center
   :alt: Odoo 社交营销应用程序中的社交账户页面视图。

要编辑/修改此页面上的任何社交账户，只需从此页面上的列表中选择所需的账户，然后进行必要的调整。

社交流页面
====================

要查看一个单独的页面，其中包含已添加到主 *社交营销* 仪表板的所有社交媒体流，请导航到 :menuselection:`社交营销应用程序 --> 配置 --> 社交流`。

.. image:: social_marketing/social-streams-page.png
   :align: center
   :alt: Odoo 社交营销应用程序中的社交流页面视图。

在此处，社交流信息以列表形式显示，其中包含 :guilabel:`社交媒体`、流的 :guilabel:`标题`、流的 :guilabel:`类型`（例如 :guilabel:`帖子`、:guilabel:`关键字` 等）、由谁 :guilabel:`创建` 以及与之关联的 :guilabel:`公司`。

要修改任何流的信息，只需从列表中单击所需的流，然后进行必要的调整。

访客
========

要查看连接到数据库的所有网站访问者的完整概览，请导航到 :menuselection:`社交营销应用程序 --> 访客`。

.. image:: social_marketing/visitors.png
   :align: center
   :alt: Odoo 社交营销应用程序中的访客页面视图。

在此处，Odoo 提供了所有访客重要信息的详细布局，默认情况下为看板视图。如果访客的联系信息已经存在于数据库中，可以选择向他们发送 :guilabel:`电子邮件` 和/或 :guilabel:`短信`。

这些相同的访客数据也可以以列表或图表形式查看。这些查看选项位于 :guilabel:`访客` 页面右上角。

.. toctree::

   social_marketing/social_posts
   social_marketing/social_campaigns
