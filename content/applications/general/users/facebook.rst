===============================
Facebook 登录认证
===============================

*Facebook* OAuth 登录功能允许 Odoo 用户使用其 Facebook 账户登录数据库。

.. danger::
   在 Odoo.com 上托管的数据库**不应**为数据库的所有者或管理员使用 OAuth 登录，因为这会将数据库与他们的 Odoo.com 账户解除关联。如果为该用户设置了 OAuth，数据库将无法再通过 Odoo.com 门户进行复制、重命名或管理。

Meta for Developers 设置
=========================

前往 `Meta for Developers <https://developers.facebook.com/>`_ 并登录。点击 :guilabel:`我的应用程序`。在 :guilabel:`应用程序` 页面，点击 :guilabel:`创建应用程序`。

在 :guilabel:`使用案例` 页面中，选择 :guilabel:`通过 Facebook 登录进行用户认证和请求数据`，然后点击 :guilabel:`下一步`。

在 :guilabel:`添加应用名称` 字段中输入 `Odoo 登录 OAuth` 或类似的标题。

.. note::
   :guilabel:`应用程序联系电子邮件` 会自动默认使用与 Meta 账户关联的电子邮件地址。如果该电子邮件地址未被定期监控，建议使用其他电子邮件地址。

点击 :guilabel:`下一步`。查看 :guilabel:`发布要求`、:guilabel:`Meta 平台条款` 和 :guilabel:`开发者政策`。然后点击 :guilabel:`创建应用程序`。

.. important::
   点击 :guilabel:`创建应用程序` 可能需要重新输入密码。

自定义应用程序
-------------

创建新应用程序后，会显示一个 :guilabel:`仪表盘` 页面，其中列出了发布应用程序前需要完成的步骤。从这里，点击 :guilabel:`自定义添加 Facebook 登录按钮`。

.. image:: facebook/app-requirements.png
   :align: center
   :alt: Meta 开发者平台中的应用程序仪表盘。

在 :guilabel:`自定义` 页面中，点击 :guilabel:`设置`。

在 :guilabel:`有效的 OAuth 重定向 URI` 字段中，输入 `https://<odoo 基础 URL>/auth_oauth/signin`，将 `<odoo 基础 URL>` 替换为适用数据库的 URL。

.. example::
   如果数据库的 URL 是 `https://example.odoo.com`，则在 :guilabel:`有效的 OAuth 重定向 URI` 字段中输入 `https://example.odoo.com/auth_oauth/signin`。

完成后点击 :guilabel:`保存更改`。

配置设置
------------------

在页面的最左侧，点击 :menuselection:`应用程序设置 --> 基本`。此页面包含应用程序提交审批前所需的其他设置。

在 :guilabel:`隐私政策 URL` 字段中输入 `https://www.odoo.com/privacy`。

.. note::
   `<https://www.odoo.com/privacy>`_ 是在 Odoo.com 上托管的数据库的默认隐私政策。

点击 :guilabel:`应用程序图标` 字段以打开文件上传窗口。从这里选择并上传应用程序图标。

在 :guilabel:`用户数据删除` 字段中输入
`https://www.odoo.com/documentation/17.0/administration/odoo_accounts.html`。

.. note::
   此文档提供了用户如何删除其 Odoo 账户的说明。

最后，点击 :guilabel:`类别` 字段，并从下拉菜单中选择 :guilabel:`商业和页面`。

点击 :guilabel:`保存更改`。

.. image:: facebook/app-id.png
   :align: center
   :alt: Meta 开发者平台中基本设置页面的示例。

.. _users/app-id:

获取应用程序 ID
--------------

应用程序创建并批准后，选择并复制 :guilabel:`应用程序 ID`。将此信息粘贴到剪贴板或记事本文件中，因为它在后续步骤中需要用于完成设置。

发布
-------

在页面的左侧，点击 :guilabel:`发布`。根据连接的 Facebook 账户的状态，可能需要额外的验证和测试步骤，并在此页面上列出。

审查完信息后，点击 :guilabel:`发布`。

.. seealso::
   有关 Meta 应用程序开发的更多信息，包括构建、测试和使用案例的详细信息，请参阅 `Meta 开发者文档 <https://developers.facebook.com/docs/development>`_。

Odoo 设置
==========

首先，激活 :ref:`开发者模式 <developer-mode/activation>`。

导航到 :menuselection:`设置应用程序`，向下滚动到 :guilabel:`集成` 部分。在那里，勾选标有 :guilabel:`OAuth 身份验证` 的复选框。点击 :guilabel:`保存`。

.. image:: facebook/enable-oauth.png
   :align: center
   :alt: 设置应用程序中启用 OAuth 的设置。

然后，当登录屏幕加载后，登录数据库。

成功登录后，导航到 :menuselection:`设置应用程序 --> 用户和公司 --> OAuth 提供者`。点击 :guilabel:`Facebook Graph`。

在 :guilabel:`客户端 ID` 字段中输入前一部分中的 :ref:`应用程序 ID <users/app-id>`，然后勾选 :guilabel:`允许` 复选框。

.. image:: facebook/facebook-graph.png
   :align: center
   :alt: Odoo 中的 Facebook Graph 记录。
