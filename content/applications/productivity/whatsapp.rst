========
WhatsApp
========

**WhatsApp** 是一款即时消息和网络电话应用程序，允许用户发送消息、进行通话并分享内容。企业可以使用 `WhatsApp Business <https://developers.facebook.com/products/whatsapp/>`_ 与客户通过文字交流、发送文件和提供支持。

.. warning::
   WhatsApp 是 Odoo 企业版专属的应用程序，无法在 Odoo 社区版中使用。要注册 Odoo 企业版，请点击此处：`Odoo 免费试用 <https://www.odoo.com/trial>`_。

.. seealso::
   有关从 Odoo 社区版迁移到 Odoo 企业版的更多信息，请参见此文档：:doc:`/administration/on_premise/community_to_enterprise`。

通过 **Odoo WhatsApp** 应用程序，公司可以将 WhatsApp Business 账户（WABA）连接到 Odoo 数据库，实现以下功能：

- 直接从 Odoo 数据库接收和回复 WhatsApp 消息
- 创建带有动态占位符/变量的新模板
- 发送使用动态变量的预先批准模板，例如：

  - 来自销售应用程序的报价单
  - 来自销售点（POS）应用程序的收据和发票
  - 来自事件应用程序的票据

.. seealso::
   - `Meta Business：为 WhatsApp Business 账户创建消息模板 <https://www.facebook.com/business/help/2055875911147364>`_。
   - `Meta Business：将电话号码连接到 WhatsApp Business 账户 <https://www.facebook.com/business/help/456220311516626>`_。
   - `Meta Business：更改 WhatsApp Business 显示名称 <https://www.facebook.com/business/help/378834799515077>`_。

WhatsApp 是由 Meta 运营的消息服务，Meta 也是 Facebook 的母公司。WhatsApp 常被许多国家和企业用作沟通工具。本文件将介绍如何将 WhatsApp Business 账户与 Odoo 集成。公司的 Meta 账户通过应用编程接口（API）与 Odoo 进行配置。

WhatsApp 连接器支持两种消息流：公司发起和客户发起。公司可以通过发送模板向一个或多个客户发起对话。一旦发送了模板，接收方可以回复，从而触发发送方和接收方之间的对话（如果客户在 15 天内回复，将会弹出 *Discuss* 聊天窗口）。

如果对话是由客户发起的（例如，发送消息至公司的公共 WhatsApp 号码），Odoo 将打开一个包含所有负责此 WhatsApp 频道操作员的群组聊天窗口。

.. tip::
   建议为不同部门设置多个 WhatsApp 账户。例如，帮助台团队和销售团队可以在不同的频道上进行聊天。

在 Meta 中配置 WhatsApp
=======================

通过标准的应用编程接口（API）连接，WhatsApp 可以与 Odoo 集成，配置过程包括以下步骤：

#. 创建一个 Meta 企业账户
#. 创建一个 Meta 开发者账户
#. 在 Meta 开发者控制台中设置一个*应用程序*和 WhatsApp *产品*
#. 测试 API 连接

一旦连接成功，消息将通过 Odoo 的 *Discuss* 应用程序通过 WhatsApp API 发送和接收。

在 Meta 中创建企业账户
----------------------

要在 Meta（Facebook 的所有者）上创建企业账户，请前往 `Facebook Business Manager <https://business.facebook.com/overview>`_。首先点击 :guilabel:`创建账户`，然后输入企业名称、管理员名称和工作邮箱地址。接着点击 :guilabel:`下一步`，系统会弹出窗口提示确认邮箱地址。确认后点击 :guilabel:`完成` 关闭窗口。

接下来，按照 Facebook 发送的电子邮件中的指示，确认企业账户的创建并完成设置过程。

.. seealso::
   `设置 Meta 企业账户 <https://www.facebook.com/business/help/1710077379203657?id=180505742745347>`_。

.. important::
   如果企业账户与个人 Facebook 账户相关联，则管理员必须在配置的剩余部分中在个人账户和企业账户之间切换。

   要切换到企业账户，请前往 `Facebook 开发者控制台 <https://developers.facebook.com>`_ 并点击右上角的 *账户名称*。在 :guilabel:`企业账户` 部分下，点击需要进行 WhatsApp 配置的企业账户。这将是 Odoo 用于发送和接收 WhatsApp 消息的账户。

   .. image:: whatsapp/toggle.png
      :align: center
      :alt: 在 Meta 个人账户和企业账户之间切换。

.. important::
   创建 Meta 企业账户的前提是用户已经有一个存在至少一小时以上的个人 Facebook 账户。如果在此时间之前尝试创建企业账户，将会导致错误。

应用程序创建
------------

在 `Meta for Developers <https://developers.facebook.com>`_ 仪表板中，使用 Meta 开发者账户登录。如果尚未配置账户，链接一个 Facebook 账户以创建 Meta 开发者账户。

.. note::
   Facebook *开发者* 账户与 Facebook *企业* 账户不同。开发者账户由个人 Facebook 账户组成，而企业账户则**不是**，它们代表企业并管理 Meta 中的所有企业资产，如应用程序。

.. seealso::
   `设置 WhatsApp Business 平台 <https://www.facebookblueprint.com/student/collection/409587/path/360218>`_。

登录成功后，点击右上角的 :guilabel:`我的应用程序`，这将重定向到该开发者账户中配置的所有应用程序。点击 :guilabel:`创建应用程序` 开始配置新的 Meta 应用程序。

应用程序类型
--------

在 :menuselection:`创建应用程序` 页面中，在 :guilabel:`寻找其他内容？` 部分下选择 :guilabel:`其他`，然后点击 :guilabel:`下一步`，以跳转到选择应用程序类型的页面。接下来，点击 :guilabel:`选择应用程序类型` 标签下的第一个选项，标题为 :guilabel:`企业`。此选择允许创建和管理 WhatsApp 应用编程接口（API）。

现在，点击 :guilabel:`下一步` 以根据需要配置应用程序。当应用程序类型配置完成后，管理员将转到应用程序*详细信息*部分。

应用程序详细信息
-----------

在 :guilabel:`创建应用程序` 过程的 :guilabel:`详细信息` 部分中，在 :guilabel:`添加应用程序名称` 标签下输入 `Odoo`。

.. note::
   如果有必要，应用程序名称可以在设置中更改。

.. warning::
   此文本部分不能使用商标和品牌元素，包括 Meta 集团公司。不要包含 “WhatsApp” 这个词，否则系统会报错。

接下来，在 :guilabel:`应用程序联系电子邮件` 标签下输入开发者的电子邮件地址。

最后，通过下拉菜单将 :guilabel:`企业账户 - 可选` 字段设置为 Meta 企业账户配置文件。点击 :guilabel:`创建应用程序` 完成。此操作将创建应用程序，并提示同意 *Meta 平台条款* 和 *开发者政策*。

要接受这些协议，请输入 Facebook 密码以确保安全，然后点击 :guilabel:`提交` 完成应用程序创建。浏览器将重定向到 :guilabel:`Meta for Developers` 仪表板。

.. note::
   如果 Meta 企业账户被禁止进行广告宣传，则无法申请应用程序。要解决此问题，请前往 `<https://business.facebook.com/business>`_ 寻求帮助。

   有关更多信息，请参阅 `Meta 关于广告限制的文档 <https://www.facebook.com/business/help/975570072950669>`_。
向应用程序添加 WhatsApp 产品
---------------------------------

现在应用程序的基本结构已经创建，需要向应用程序中添加产品。首先，通过访问 Meta 应用程序仪表板，导航至 `<https://developers.facebook.com/apps>`_，并点击正在配置的应用程序。

在下一页上，因为要使用 WhatsApp，点击页面底部 WhatsApp 旁边的 :guilabel:`设置`。

.. seealso::
   `Meta 的 WhatsApp 开发者文档 <https://developers.facebook.com/docs/whatsapp/>`_。

页面将跳转到 :guilabel:`WhatsApp Business 平台 API` 的配置页面。在 :guilabel:`选择 Meta 企业账户` 选项中使用下拉菜单选择要配置的 Meta 企业，然后点击 :guilabel:`继续` 确认选择。

.. note::
   当点击 :guilabel:`继续` 时，管理员同意 Meta 仪表板上链接的 Meta 条款和条件。

.. note::
   一旦 WhatsApp 产品添加到应用程序中，Meta 将提供一个 WhatsApp 测试电话号码和 5 条测试消息。

开始使用 WhatsApp API
----------------------------

完成 WhatsApp 产品向导后，点击 :guilabel:`继续`，浏览器将跳转到 WhatsApp 的 :guilabel:`快速入门` 页面；这是配置 WhatsApp API 的开始步骤，添加电话号码并发送初始测试消息。

.. image:: whatsapp/quickstart.png
   :align: center
   :alt: 在 Meta 开发者仪表板中导航到 WhatsApp 快速入门向导。

.. note::
   如果浏览器没有跳转到 WhatsApp 的 :guilabel:`快速入门` 页面，导航至 `<https://developers.facebook.com/apps>`_ 并点击正在配置的应用程序（如果按照上述说明操作，应用名称为 `Odoo`）。

   然后，在页面左侧的菜单中，点击 :guilabel:`v（菜单切换）` 图标，位于 :guilabel:`WhatsApp` 部分标题旁。会弹出一个小菜单，包含以下选项：

   - :guilabel:`快速入门`
   - :guilabel:`API 设置`
   - :guilabel:`配置`

   点击 :guilabel:`快速入门` 选项，然后点击 :guilabel:`开始使用 API`。

API 设置
~~~~~~~~~

点击 :guilabel:`开始使用 API` 后，页面跳转到 :guilabel:`API 设置`。现在，测试号码已创建，可以发送测试消息确认 WhatsApp 是否正常工作。首先，导航到页面上标为 :guilabel:`发送和接收消息` 的部分，点击 :guilabel:`步骤 1 选择电话号码` 下的 :guilabel:`收件人` 旁边的下拉菜单。

现在，选择唯一可用的选项：:guilabel:`管理电话号码列表`。按照步骤操作，最多添加五个电话号码以发送免费的测试消息。在输入正确的国家代码和电话号码后，点击 :guilabel:`下一步`。

.. important::
   此步骤中添加的电话号码将允许通过终端发送成功的测试消息。这对于确保 WhatsApp :abbr:`API（应用程序编程接口）` 正常工作至关重要。

接下来，WhatsApp Business 将向电话号码发送一个验证码，需要在下一屏幕上输入验证码以验证号码的所有权。输入验证码后，点击 :guilabel:`下一步` 进行验证。

通过终端发送测试消息
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

接下来，通过终端发送测试消息。在标为 :guilabel:`步骤 2 通过 API 发送消息` 的部分，点击 :guilabel:`发送消息`。测试消息将发送到先前设置的电话号码。

成功收到消息后，继续下一部分，生成并配置 Webhooks。

.. _productivity/whatsapp/webhooks:
在 Odoo 中配置 WhatsApp
==============================

本节中的接下来的配置步骤都在 Odoo 数据库中完成。需要在 Odoo 中配置一些不同的值，包括令牌、电话号码和账户 ID。这些值是创建 :guilabel:`回调 URL` 和 :guilabel:`Webhook 验证令牌` 所必需的，这些用于配置 Webhooks（以便将消息接收到数据库中）。

在 Odoo 中，导航到 :menuselection:`WhatsApp 应用 --> 配置 --> WhatsApp 商业账户`，然后点击 :guilabel:`新建` 来配置 Odoo 中的 WhatsApp 商业账户。

在另一个浏览器选项卡中，导航到 :menuselection:`https://developers.facebook.com --> 我的应用 --> WhatsApp --> API 配置`，然后将 Meta 开发者控制台中的以下值复制到 Odoo 中的相应字段：

.. list-table::
   :header-rows: 1
   :stub-columns: 1

   * - 名称
     - Meta 控制台
     - Odoo 界面
   * - 电话
     - :guilabel:`电话号码 ID`
     - :guilabel:`电话号码 ID`
   * - 令牌
     - :guilabel:`临时访问令牌`
     - :guilabel:`访问令牌`
   * - 应用 ID
     - :guilabel:`应用 ID`
     - :guilabel:`应用 ID`
   * - 账户 ID
     - :guilabel:`WhatsApp 商业账户 ID`
     - :guilabel:`账户 ID`

要检索 :guilabel:`应用密钥`，请导航至 Meta 开发者控制台，`<https://developers.facebook.com/apps>`_，并选择正在 Odoo 中配置的应用程序。然后在左侧菜单中，点击 :guilabel:`应用设置`，选择 :guilabel:`基本设置`。

接下来，点击 :guilabel:`显示`，位于 :guilabel:`应用密钥` 字段旁边，输入账户密码以验证所有权。复制 :guilabel:`应用密钥`，然后将该值粘贴到 Odoo :guilabel:`WhatsApp 商业账户` 配置仪表板中的 :guilabel:`应用密钥` 字段中。

完成 WhatsApp 商业账户在 Odoo 中的设置后，点击 :guilabel:`测试连接`。如果配置正确，仪表板右上角将会显示成功的绿色提示信息。

配置 Webhooks
--------------------

要在 Odoo 中配置 WhatsApp 的 Webhooks，请导航到 `<https://developers.facebook.com/apps>`_ 并选择正在 Odoo 中配置的应用程序。接下来，在屏幕左侧的 :guilabel:`WhatsApp` 菜单标题下，点击 :guilabel:`API 设置` 菜单项。最后，转到标记为 :guilabel:`步骤 3：配置 Webhooks 以接收消息` 的部分，点击 :guilabel:`配置 Webhooks`。

.. tip::
   配置 *Webhooks* 的另一种方法是导航至 `<https://developers.facebook.com/apps>`_ 并选择正在 Odoo 中配置的应用程序。然后选择左侧菜单中的 :guilabel:`Webhooks`。

   .. image:: whatsapp/webhooks.png
      :align: center
      :alt: 手动导航到 WhatsApp Webhooks 配置。

在 :menuselection:`Webhook 配置` 页面，点击 :guilabel:`编辑`，然后将 Odoo 中的 :guilabel:`回调 URL` 和 :guilabel:`Webhook 验证令牌` 值添加到相应字段中。

..  note::
    在上一步点击 :guilabel:`测试连接` 后，:guilabel:`回调 URL` 和 :guilabel:`Webhook 验证令牌` 的值会自动生成。

在另一个浏览器窗口中，导航到 :menuselection:`WhatsApp 应用 --> 配置 --> WhatsApp 商业账户`，并选择正在配置的账户。在标记为 :guilabel:`接收消息` 的部分中找到这些值。

将 Odoo 中的 :guilabel:`回调 URL` 复制并粘贴到 Meta 中的 :guilabel:`回调 URL` 字段。同样，将 :guilabel:`Webhook 验证令牌` 复制并粘贴到 Meta 开发者控制台中的 :guilabel:`验证令牌` 字段中。

最后，点击 :guilabel:`验证并保存` 以在 Meta 开发者控制台中记录这些值。
Webhook 字段
~~~~~~~~~~~~~~

现在，在 Meta 的开发者控制台的 :guilabel:`Webhook 字段` 部分中输入各个字段。点击 :guilabel:`管理`，当弹出窗口出现时，在 :guilabel:`订阅` 列中勾选以下 *字段名称*：

- account_update
- message_template_quality_update
- message_template_status_update
- messages
- template_category_update

完成选择后，点击 :guilabel:`完成`。

在 Meta 开发者控制台中完成的 :guilabel:`Webhooks` 配置将如下所示：

.. image:: whatsapp/webhooks-done.png
   :align: center
   :alt: 在 Meta 开发者控制台中设置 WhatsApp webhooks。

.. important::
   只有在使用 :guilabel:`回调 URL` 和 :guilabel:`Webhook 验证令牌` 确认订阅后，:guilabel:`Webhook 字段` 才会出现。

.. seealso::
   `Meta 的 WhatsApp 设置 Webhooks 文档 <https://developers.facebook.com/docs/whatsapp/cloud-api/guides/set-up-webhooks>`_。

添加电话号码
~~~~~~~~~~~~~~~~

要配置 WhatsApp 中使用的电话号码，请导航回 Meta 开发者控制台 (`<https://developers.facebook.com/apps>`_)，然后再次选择正在 Odoo 中配置的应用程序。在屏幕左侧的 :guilabel:`WhatsApp` 菜单标题下，点击 :guilabel:`API 设置` 菜单项。然后，转到标记为 :guilabel:`步骤 5：添加电话号码` 的部分，并点击 :guilabel:`添加电话号码`。

在字段中输入 :guilabel:`企业名称` 以及 :guilabel:`企业网站或个人主页`。

.. tip::
   :guilabel:`企业网站或个人主页` 字段可以是社交媒体页面的 :abbr:`URL (统一资源定位符)`。

填写完企业信息后，接下来从 :guilabel:`国家/地区` 下拉菜单中选择公司业务所在的国家/地区。如果需要，可以添加地址，不过这不是必须的。添加完位置后，点击 :guilabel:`下一步` 继续。

下一页面包含 :guilabel:`WhatsApp 企业资料` 的信息。按以下要求填写这些部分：

- :guilabel:`WhatsApp 企业资料显示名称`
- :guilabel:`时区`
- :guilabel:`类别`
- :guilabel:`企业描述`（可选）

完成这些部分后，点击 :guilabel:`下一步`。页面刷新后，会提示管理员在相应字段中输入要在 WhatsApp 中配置的电话号码。在这里输入电话号码。

.. seealso::
   `将现有 WhatsApp 号码迁移到企业账户
   <https://developers.facebook.com/docs/whatsapp/cloud-api/get-started/migrate-existing-whatsapp-
   number-to-a-business-account>`_。

接下来，选择电话号码的验证方式。选择 :guilabel:`短信` 或 :guilabel:`电话`，然后点击 :guilabel:`下一步` 继续。

输入的电话号码将通过 WhatsApp 接收一条短信或电话，其中包含代码，具体取决于选择的验证方式。将该验证代码输入 :guilabel:`验证代码` 字段，然后点击 :guilabel:`下一步` 以完成操作。

.. warning::
   如果尚未添加付款方式，则必须执行此步骤。 `访问 Meta 文档，了解如何在 Meta Business Manager 中添加付款方式
   <https://www.facebook.com/business/help/915454841921082?id=180505742745347>`_。这是 Meta 的欺诈检测系统的一部分，以确保帐户/公司真实存在，需要添加付款方式才能继续。

.. seealso::
   `Meta 开发者文档：添加电话号码
   <https://developers.facebook.com/docs/whatsapp/cloud-api/get-started/add-a-phone-number>`_。

.. _productivity/whatsapp/token:

永久令牌
~~~~~~~~~~~~~~~

配置和测试完成后，应创建永久令牌以替换 :guilabel:`临时令牌`。

.. seealso::
   `Meta 开发者文档：系统用户访问令牌
   <https://developers.facebook.com/docs/whatsapp/business-management-api/get-started#system-user-
   access-tokens>`_。

首先，导航至 `<https://business.facebook.com/>`_，然后前往 :menuselection:`企业设置 --> 用户 --> 系统用户`。选择一个现有的系统用户或点击 :guilabel:`添加` 创建一个新系统用户。

现在必须为系统用户添加资产，然后可以生成永久令牌。

点击 :guilabel:`添加资产`，弹出窗口出现时，选择 :guilabel:`应用`，然后在 :guilabel:`选择资产类型` 下选择 Odoo 应用程序，并在 :guilabel:`完全控制` 选项下将权限切换为“开启”。点击 :guilabel:`保存更改` 来设置这个新权限设置，确认窗口将会出现，确认资产已添加到系统用户。最后点击 :guilabel:`完成`。

接下来，将生成永久令牌。点击 :guilabel:`生成新令牌`，弹出窗口将询问该令牌应为哪个应用程序生成。选择该令牌对应的 :guilabel:`应用程序`。然后选择到期日期，设置为 :guilabel:`60 天` 或 :guilabel:`永不过期`。

最后，当 Meta 询问系统用户应该允许哪些权限时，添加以下所有权限：

- WhatsApp_business_messaging
- WhatsApp_business_management

设置好权限后，点击 :guilabel:`生成令牌`。复制随后屏幕上显示的令牌值。

使用该令牌值，通过导航到 :menuselection:`WhatsApp 应用 --> 配置 --> WhatsApp 商业账户` 更新 Odoo 中 WhatsApp 商业账户的 :guilabel:`访问令牌` 字段。
将 Meta 应用程序设置为上线
=========================

最后，为了上线应用程序，必须在 Meta 开发者控制台中将应用程序设置为 :guilabel:`上线`。导航至 `<https://developers.facebook.com/apps>`_，然后点击正在配置的应用程序。在顶部菜单中，将 :guilabel:`应用程序模式` 字段从 :guilabel:`开发` 切换到 :guilabel:`上线`。

.. important::
   如果应用程序状态未设置为 *上线*，数据库将只能联系开发者控制台中指定的测试号码。

.. warning::
   必须设置隐私政策 URL，应用程序才能上线。前往 Meta 开发者控制台，`<https://developers.facebook.com/apps>`_，选择正在配置的 Odoo 应用程序。然后，使用屏幕左侧的菜单，前往 :menuselection:`应用设置 --> 基本信息`。接着，在 :guilabel:`隐私政策 URL` 字段中输入隐私政策超链接地址。点击 :guilabel:`保存更改` 以将隐私政策应用于应用程序。

应用程序上线后，确认电子邮件将发送给管理员。

.. _productivity/whatsapp/templates:

WhatsApp 模板
==================

WhatsApp 模板是保存的消息，可反复使用来从数据库发送消息。它们允许用户发送高质量的通信，而无需反复撰写相同的文本。

创建针对特定情况定制的不同模板，允许用户为合适的受众选择合适的信息。这提高了信息质量和客户的整体参与率。

WhatsApp 模板可以在 Odoo 和 Meta 控制台上创建。以下流程概述了在 Odoo 中创建模板的流程，然后在 Meta 中进行。

.. important::
   WhatsApp 有一个批准流程，必须完成此流程后模板才能使用。
   :ref:`productivity/whatsapp/approval`.

.. _WhatsApp/templates:

在 Odoo 中创建模板
--------------------------

要访问和创建 WhatsApp 模板，首先导航到 :menuselection:`WhatsApp 应用 --> 模板` 仪表板。

在单个模板表单的底部，有三个选项卡：:guilabel:`正文`、:guilabel:`按钮` 和 :guilabel:`变量`；这三个选项卡共同创建了 WhatsApp 模板。

文本输入到 :guilabel:`正文` 选项卡中，正文中调用的动态内容在 :guilabel:`变量` 选项卡中指定。消息（正文）中的每个动态内容（例如占位符）都在 :guilabel:`变量` 选项卡中具体调用并指定。

模板是预制的布局，允许用户向客户发送专业外观的消息。这些模板能够包含动态数据，模板配置中设置的变量将填充到最终消息中。例如，消息可以包含最终用户的姓名、调用特定产品或引用销售订单等便捷且有影响力的变量。

要创建 WhatsApp 模板，前往 :menuselection:`WhatsApp 应用 --> 模板` 仪表板并点击 :guilabel:`新建`。在表单中，为模板输入一个 :guilabel:`名称`，并选择一个 :guilabel:`语言`。

.. important::
   为了完成接下来的任务，需要管理员访问权限来编辑 :guilabel:`适用于` 字段。有关详细信息，请参阅此 :doc:`访问权限文档 <../general/users/access_rights>`。

在 :guilabel:`账户` 下拉菜单中，选择 Odoo 中应链接到此模板的 *WhatsApp 商业账户*。接下来，在 :guilabel:`适用于` 字段下选择服务器操作将应用于此模板的 *模型*。

.. tip::
   这些模型也可以在 :ref:`开发者模式 <developer-mode>` 中访问。在联系人表单（或 Odoo 中其他类似相关表单）上，导航到要引用的模型，并将鼠标悬停在任何字段名称上。一个后台信息框将显示出来，其中包含 Odoo 后台的特定 :guilabel:`模型` 名称。使用前端名称在 WhatsApp 模板的 :guilabel:`适用于` 下拉菜单中搜索此模型。

.. warning::
   通常在更改模型或 :guilabel:`适用于` 字段时，:guilabel:`电话号码字段` 可能会产生错误。:guilabel:`电话号码字段` 应始终设置为 `Phone` 或 `Mobile` 模型。

要搜索可用字段，请在 :guilabel:`搜索...` 框中输入前端名称。这将在模板为其创建的模型 (:guilabel:`适用于`) 的所有可用字段中找到结果。

.. note::
   为了找到特定字段，可能需要在搜索结果框中导航多个级别。使用 :guilabel:`> (右箭头)` 和 :guilabel:`⬅️ (左箭头)` 图标在菜单级别之间导航。

.. image:: whatsapp/phone-field.png
   :align: center
   :alt: 在搜索栏中搜索电话号码字段。
将 :guilabel:`类别` 更改为适合 :guilabel:`市场营销`、:guilabel:`公用事业` 或 :guilabel:`身份验证` 类别。通常情况下，将使用前两个选项，除非用户希望发送密码重置或其他与安全相关的内容。如果发送任何促销内容，则应设置为 :guilabel:`市场营销`，如果发送一般交易消息（即销售订单、活动票等），则应设置为 :guilabel:`公用事业`。

.. important::
   指定不正确的类别可能会导致在批准过程中被 Meta 标记为拒绝状态。

添加任何被允许使用此模板的 :guilabel:`用户`。在右侧列中，可以配置 :guilabel:`标题类型` 以及 :guilabel:`标题消息`。

可用的 :guilabel:`标题类型` 如下：

- 文本
- 图片
- 视频
- 文档
- 位置（需要设置变量）

导航到 :guilabel:`正文` 选项卡以配置模板的主要消息。

当对模板进行所有必要更改后，点击左上角的 :guilabel:`提交审批` 按钮。这将导致模板的状态更改为 :guilabel:`待定`。

该状态将保持在 :guilabel:`待定`，直到 Meta 做出决定，随后会发送确认电子邮件，指示模板已被批准（或拒绝）。然后需要从 Odoo 数据库同步模板。

有关同步模板的更多信息，请参阅 :ref:`同步模板 <productivity/whatsapp/sync>` 这一部分。

.. tip::
   Odoo 中有可用的预配置演示数据模板，可以使用或修改。这些模板可以按原样使用，也可以修改以满足特定的业务需求。

   要使用这些模板，导航到 :menuselection:`WhatsApp 应用 --> 模板` 并选择一个预配置的模板。点击 :guilabel:`提交审批` 开始审批流程。当模板被批准时，Meta 账户的管理员将收到电子邮件。

按钮
~~~~~~~

可以从 :guilabel:`按钮` 选项卡中将按钮添加到消息中。输入 :guilabel:`类型`（可以是 :guilabel:`访问网站`、:guilabel:`拨打号码` 或 :guilabel:`快速回复`），然后根据按钮的 :guilabel:`类型` 指定 :guilabel:`按钮文本`、:guilabel:`拨打号码` 或 :guilabel:`网站 URL`（包括 :guilabel:`URL 类型`）。

.. note::
   按钮也可以在 Meta 商业控制台中添加。通过导航到 `<https://business.facebook.com/wa/manage/home>`_ 查看 Meta 的 WhatsApp 模板仪表板。然后转到 :menuselection:`账户工具 --> 消息模板`。

使用占位符和变量
~~~~~~~~~~~~~~~~~~~~~~~~

动态变量引用 Odoo 数据库中的某些字段，以便在使用模板时生成 WhatsApp 消息中的唯一数据。动态变量被编码以显示数据库中的字段，引用模型中的字段。

.. example::
   许多公司喜欢通过个性化的客户信息来定制他们的 WhatsApp 消息以吸引注意。可以通过在 Odoo 中设置动态变量来引用模型中的字段来实现。例如，可以在 :guilabel:`销售订单` 模型的 :guilabel:`客户` 字段中引用客户的姓名。

.. image:: whatsapp/message.png
   :align: center
   :alt: 突出显示动态变量的 WhatsApp 消息。

动态变量可以通过在 *文本* 中添加 :guilabel:`占位符` 添加到 :guilabel:`正文` 中。要在 *消息正文* 中添加占位符，请输入以下文本 `{{1}}`。对于第二个占位符输入 `{{2}}`，并在添加更多占位符时逐渐增加。

.. example::
   *以下是付款收据模板正文的文本：*

   亲爱的 {{1}},

   | 这是您从 *{{3}}* 收到的发票 *{{2}}*，总额为 *{{4}}{{5}}*。
   | 要查看您的发票或在线支付：{{6}}

   谢谢
.. seealso::
   :ref:`productivity/whatsapp/templates`.

这些占位符必须在提交 Meta 审批之前，在模板的 :guilabel:`变量` 选项卡中进行配置。要编辑模板中的动态变量，首先将 :guilabel:`类型` 更改为 :guilabel:`模型字段`。这使得 Odoo 能够引用模型内的字段，以在发送的消息中生成唯一的数据。

接下来，编辑动态变量的 :guilabel:`字段`。在模板中，应该先编辑 :guilabel:`适用对象` 字段，以确保引用正确的模型和字段。

要搜索可用字段，请在搜索框中输入字段的前端名称。这将查找模板所创建模型 (:guilabel:`适用对象`) 的所有可用字段结果。可能需要配置多个级别。

.. example::
   以下是上述付款收据中为占位符设置的变量示例：

   .. list-table::
      :header-rows: 1
      :stub-columns: 1

      * - 名称
        - 示例值
        - 类型
        - 字段
      * - 正文 - {{1}}
        - Azure Interior
        - 模型字段
        - `Partner`
      * - 正文 - {{2}}
        - INV/2022/00001
        - 模型字段
        - `Number`
      * - 正文 - {{3}}
        - My Company
        - 模型字段
        - `Company`
      * - 正文 - {{4}}
        - $
        - 模型字段
        - `Currency > Symbol`
      * - 正文 - {{5}}
        - 4000
        - 模型字段
        - `Amount`
      * - 正文 - {{6}}
        - \https://..
        - Portal link
        -

.. example::
   例如，在 :guilabel:`正文` 选项卡中，如果输入以下内容，“Hello {{1}},”，则必须在 :guilabel:`变量` 选项卡中设置 `{{1}}`。在此特定情况下，消息应通过姓名问候客户，因此 `{{1}}` 应配置为填充 :guilabel:`Customer` 名称的 `{{1}}` :guilabel:`字段`。

.. warning::
   自定义 WhatsApp 模板不在 Odoo 支持的范围内。

.. _productivity/whatsapp/approval:

Meta 模板审批
~~~~~~~~~~~~~~~~~~~~~~

在更新模板上的动态变量后，模板需要再次提交给 Meta 进行审批。点击 :guilabel:`提交审批` 以启动审批流程。当模板被批准时，将向 Meta 账户的管理员发送电子邮件。

获得 Meta 批准后，再次在 Odoo 数据库中同步模板。有关更多信息，请参见 :ref:`productivity/whatsapp/sync`。

.. tip::
   要查看 Meta 的 WhatsApp 模板状态，请导航到 `<https://business.facebook.com/wa/manage/home>`_。然后转到 :menuselection:`账户工具 --> 消息模板`。

.. _productivity/whatsapp/sync:

同步模板
~~~~~~~~~~~~~~~~~

模板在 Meta 团队批准后，必须在 Odoo 数据库中同步。要做到这一点，请首先导航到 :menuselection:`WhatsApp 应用 --> 配置 --> WhatsApp 商务账户`，并选择要同步的配置。在标记为 :menuselection:`发送消息` 的部分下，向下滚动并点击 :guilabel:`同步模板`。Meta 将更新已批准的模板，以便它们可以与数据库中的各种应用一起使用。

.. image:: whatsapp/sync-template.png
   :align: center
   :alt: 将 Meta WhatsApp 模板同步到 Odoo 数据库，突出显示“同步模板”。

右上角会出现绿色的成功消息，显示更新的模板数量。

.. tip::
   模板也可以从模板本身单独同步。导航到 :menuselection:`WhatsApp 应用 --> 模板` 仪表板，选择要同步的模板。然后，点击模板表单顶部菜单中的 :guilabel:`同步模板` 按钮。
创建 Meta 中的模板
--------------------------

首先，导航到 `Meta 的 WhatsApp 模板仪表板 <https://business.facebook.com/wa/manage/home>`_，然后转到 :menuselection:`账户工具 --> 消息模板`。

.. image:: whatsapp/account-tools.png
   :align: center
   :alt: 在商业管理器中突出显示的账户工具和管理模板链接。

要创建 WhatsApp 模板，点击蓝色的 :guilabel:`创建模板` 按钮，然后选择 :guilabel:`类别`。可选项包括： :guilabel:`营销`、:guilabel:`实用` 和 :guilabel:`身份验证`。在大多数情况下，将使用前两个选项，除非用户想要发送密码重置或与安全相关的内容。

输入模板的 :guilabel:`名称`，然后选择模板的 :guilabel:`语言`。

.. note::
   可以通过输入语言名称并根据需要选择其他语言来选择多种语言。

.. image:: whatsapp/template-config.png
   :align: center
   :alt: 列出模板配置选项，包括营销、实用、名称和语言。

在做出适当选择后，点击右上角的 :guilabel:`继续`。页面将重定向到 :guilabel:`编辑模板` 页面。在这里配置 :guilabel:`头部`、:guilabel:`正文`、:guilabel:`底部` 和 :guilabel:`按钮`。在模板的右侧是模板在生产中的预览。

.. image:: whatsapp/edit-template.png
   :align: center
   :alt: 使用头部、正文、底部和按钮编辑模板。

当对模板进行所有必要更改后，点击左上角的 :guilabel:`提交` 按钮。将出现一个确认窗口以确认语言——点击 :guilabel:`确认` 以批准，然后会出现另一个窗口，指出模板将提交给 Meta 进行审核和批准。

模板的 :guilabel:`状态` 将保持为 :guilabel:`审核中`，直到 Meta 做出决定。一旦收到确认电子邮件，确认模板已获得批准，则需要在 Odoo 数据库中同步模板。

.. seealso::
   有关在 Meta 开发者控制台上配置模板的更多信息，请访问 `Meta 的 WhatsApp 模板文档 <https://developers.facebook.com/docs/whatsapp/business-management-api/message-templates/>`_。

通知
=============

WhatsApp 中的通知类似于 Odoo 中的消息对话处理。当客户收到对话时，会弹出一个窗口。默认情况下，通知在 Odoo 中的 WhatsApp 商务账户配置中设置。

可以通过导航到 :menuselection:`WhatsApp 应用 --> 配置 --> WhatsApp 商务账户` 来调整通知设置。从那里，选择账户并向下滚动到 :menuselection:`控制` 部分，在那里处理通知。在 :guilabel:`通知用户` 标题下，输入应该为该 WhatsApp 渠道通知的用户。

.. note::
   一旦用户与客户之间发起了对话，则在 WhatsApp 商务账户配置中指定的所有用户将不会收到通知。只有对话中的用户会收到通知。如果用户在 15 天内未响应，客户在 15 天后回复的消息将再次显示给 WhatsApp 配置中指定的所有用户。

向聊天添加用户
====================

可以通过展开 WhatsApp 弹出窗口将用户添加到 WhatsApp 聊天中。WhatsApp 对话位于 *讨论* 应用中。点击 :guilabel:`👤+ (添加用户)` 图标，窗口将弹出以邀请用户加入对话。

.. image:: whatsapp/add-users.png
   :align: center
   :alt: 在 WhatsApp 对话中添加用户，突出显示添加用户图标。

WhatsApp API 常见问题解答
=====================

验证
------------

截至 2023 年 2 月 1 日，如果 Meta 应用程序需要高级权限访问，则可能需要完成完整的业务验证。这包括向 Meta 提交办公商业文件。 `查看此文档 <https://developers.facebook.com/docs/development/release/business-verification>`_。

.. seealso::
   `Meta 的 WhatsApp 访问验证文档 <https://developers.facebook.com/docs/development/release/access-verification/>`_。

模板错误
---------------

编辑模板可能会导致追溯和错误，除非严格按照上述流程进行： (:ref:`productivity/whatsapp/templates`)。
重复验证错误
~~~~~~~~~~~~~~~~~~~~~~~~~~

在同步模板时，可能会出现多个模板在 Meta 的商业管理器和 Odoo 中具有相同名称的情况。这会导致重复验证错误。要解决此问题，请在 Odoo 中重命名重复的模板名称，然后按照此处的步骤重新同步模板： :ref:`productivity/whatsapp/sync`。

.. image:: whatsapp/validation-error-2.png
   :align: center
   :alt: 当存在重复模板时，Odoo 中出现的用户错误。

令牌错误
------------

用户错误
~~~~~~~~~~

如果临时令牌未被永久令牌替换，当发送失败后测试连接时，Odoo 中将出现用户错误。要解决此问题，请参见 :ref:`productivity/whatsapp/token`。

.. image:: whatsapp/user-error.png
   :align: center
   :alt: 当令牌过期时，Odoo 中出现的用户错误。

系统用户错误 100
~~~~~~~~~~~~~~~~~~~~~

如果在设置永久令牌时，系统用户是 :guilabel:`员工`，则会出现用户错误 100。

要解决此错误，请创建一个 :guilabel:`管理员` 系统用户，按照此处的流程操作： :ref:`productivity/whatsapp/token`。

.. image:: whatsapp/user-error-2.png
   :align: center
   :alt: 当生成员工令牌而不是管理员用户时，Odoo 中出现的用户错误。
