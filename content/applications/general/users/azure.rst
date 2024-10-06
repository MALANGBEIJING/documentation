======================================
Microsoft Azure 登录认证
======================================

Microsoft Azure OAuth 登录认证是一项有用的功能，允许 Odoo 用户使用其 Microsoft Azure 账户登录数据库。

这对于使用 Azure Workspace 的组织特别有帮助，组织希望其员工通过 Microsoft 账户连接到 Odoo。

.. warning::
   在 Odoo.com 上托管的数据库不应为数据库的所有者或管理员使用 OAuth 登录，因为这会将数据库与他们的 Odoo.com 账户解除关联。如果为该用户设置了 OAuth，数据库将无法再通过 Odoo.com 门户进行复制、重命名或管理。

.. seealso::
   - :doc:`../../productivity/calendar/outlook`
   - :doc:`../email_communication/azure_oauth`

配置
=============

集成 Microsoft 登录功能需要在 Microsoft 和 Odoo 上进行配置。

Odoo 系统参数
---------------------

首先激活 :ref:`开发者模式 <developer-mode>`，然后进入 :menuselection:`设置 --> 技术 --> 系统参数`。

点击 :guilabel:`创建`，在出现的新表单中，将系统参数 `auth_oauth.authorization_header` 添加到 :guilabel:`键` 字段，并将 :guilabel:`值` 设置为 `1`。然后点击 :guilabel:`保存` 完成。

Microsoft Azure 仪表盘
-------------------------

创建新应用程序
~~~~~~~~~~~~~~~~~~~~~~~~

现在，Odoo 中的系统参数已设置，接下来是在 Microsoft Azure 中创建相应的应用程序。要开始创建新应用程序，请前往 `Microsoft 的 Azure 门户 <https://portal.azure.com/>`_。如果有 `Microsoft Outlook Office 365` 账户，请使用该账户登录，否则，请使用个人 `Microsoft 账户` 登录。

.. important::
   拥有 *Azure 设置* 管理权限的用户必须连接并执行以下配置步骤。

接下来，导航到标有 :guilabel:`管理 Microsoft Entra ID`（原 *Azure Active Directory*）的部分。该链接通常位于页面中央。

现在，点击顶部菜单中的 :guilabel:`添加 (+)` 图标，然后从下拉菜单中选择 :guilabel:`应用注册`。在 :guilabel:`注册应用程序` 页面中，将 :guilabel:`名称` 字段重命名为 `Odoo 登录 OAuth` 或其他易于识别的标题。在 :guilabel:`支持的账户类型` 部分，选择 :guilabel:`仅限于此组织目录中的账户（仅默认目录 - 单租户）`。

.. warning::
   :guilabel:`支持的账户类型` 可能会因 Microsoft 账户类型和 OAuth 的最终用途而有所不同。例如：登录是为组织内的内部用户，还是为客户门户访问？上述配置用于组织内的内部用户。

   如果目标受众是门户用户，请选择 :guilabel:`仅限个人 Microsoft 账户`。如果目标受众是公司用户，请选择 :guilabel:`仅限于此组织目录中的账户（仅默认目录 - 单租户）`。

在 :guilabel:`重定向 URL` 部分，选择 :guilabel:`Web` 作为平台，然后在 :guilabel:`URL` 字段中输入 `https://<odoo 基础 URL>/auth_oauth/signin`。Odoo 基础 :abbr:`URL (统一资源定位符)` 是可以访问您的 Odoo 实例的规范域名（例如，如果您托管在 Odoo.com 上，则为 *mydatabase.odoo.com*）。然后点击 :guilabel:`注册`，应用程序即创建成功。

身份验证
~~~~~~~~~~~~~~

在被重定向到应用程序设置后，点击左侧菜单中的 :guilabel:`身份验证` 菜单项来编辑新应用的身份验证。

接下来，选择 OAuth 身份验证所需的 *令牌*。这些不是货币令牌，而是 Microsoft 和 Odoo 之间传递的身份验证令牌。因此，这些令牌是免费的，仅用于 Microsoft 和 Odoo 之间的身份验证。向下滚动屏幕，勾选标有 :guilabel:`访问令牌（用于隐式流）` 和 :guilabel:`ID 令牌（用于隐式和混合流）` 的复选框。

.. image:: azure/authentication-tokens.png
   :align: center
   :alt: 身份验证设置和端点令牌。

点击 :guilabel:`保存` 确保这些设置已保存。

收集凭据
~~~~~~~~~~~~~~~~~~

在 Microsoft Azure 控制台中创建并验证应用程序后，接下来是收集凭据。为此，点击左侧列中的 :guilabel:`概览` 菜单项。选择并复制 :guilabel:`应用程序（客户端）ID`，然后将其粘贴到剪贴板/记事本中，此凭据稍后将用于 Odoo 配置。

完成此步骤后，点击顶部菜单中的 :guilabel:`端点`，并点击 :guilabel:`OAuth 2.0 授权端点 (v2)` 字段旁的 *复制图标*。将此值粘贴到剪贴板/记事本中。

.. image:: azure/overview-azure-app.png
   :align: center
   :alt: 应用程序 ID 和 OAuth 2.0 授权端点 (v2) 凭据。

Odoo 设置
----------

最后一步是在 Odoo 中配置一些设置以完成 Microsoft Azure OAuth 配置。导航到 :menuselection:`设置 --> 集成 --> OAuth 身份验证` 并勾选启用 OAuth 登录功能的复选框。点击 :guilabel:`保存` 确保进度已保存。然后，当登录屏幕加载后，登录数据库。

再次导航到 :menuselection:`设置 --> 集成 --> OAuth 身份验证` 并点击 :guilabel:`OAuth 提供者`。现在，点击左上角的 :guilabel:`新建`，并将提供者命名为 `Azure`。

将前一部分中的 :guilabel:`应用程序（客户端）ID` 粘贴到 :guilabel:`客户端 ID` 字段中。完成后，将新的 :guilabel:`OAuth 2.0 授权端点 (v2)` 值粘贴到 :guilabel:`授权 URL` 字段中。

在 :guilabel:`用户信息 URL` 字段中，粘贴以下 :abbr:`URL (统一资源定位符)`：`https://graph.microsoft.com/oidc/userinfo`

在 :guilabel:`范围` 字段中，粘贴以下值：`openid profile email`。接下来，可以通过在 :guilabel:`CSS 类` 字段中输入以下值，将 Windows 徽标用作登录屏幕上的 CSS 类：`fa fa-fw fa-windows`。

勾选 :guilabel:`允许` 字段旁边的复选框以启用 OAuth 提供者。最后，将 `Microsoft Azure` 添加到 :guilabel:`登录按钮标签` 字段中。此文本将显示在登录页面的 Windows 徽标旁。

.. image:: azure/odoo-provider-settings.png
   :align: center
   :alt: Odoo 设置应用程序中的提供者设置。

点击 :guilabel:`保存` 完成 Odoo 中的 OAuth 身份验证设置。

用户体验流程
---------------------

要使用 Microsoft Azure 登录 Odoo，用户必须位于 :menuselection:`Odoo 密码重置页面`。这是 Odoo 能够链接 Microsoft Azure 账户并允许用户登录的唯一方法。

.. note::
   现有用户必须 :ref:`重置密码 <users/reset-password>` 以访问 :menuselection:`Odoo 密码重置页面`。新 Odoo 用户必须点击通过电子邮件发送的新用户邀请链接，然后点击 :guilabel:`Microsoft Azure`。用户不应设置新密码。

要首次使用 Microsoft Azure OAuth 提供者登录 Odoo，请导航到 :menuselection:`Odoo 密码重置页面`（使用新用户邀请链接）。应出现一个密码重置页面。然后，点击标有 :guilabel:`Microsoft Azure` 的选项。页面将重定向到 Microsoft 登录页面。

.. image:: azure/odoo-login.png
   :align: center
   :alt: Microsoft Outlook 登录页面。

输入 :guilabel:`Microsoft 电子邮件地址`，然后点击 :guilabel:`下一步`。按照流程登录账户。如果启用了 :abbr:`2FA (双因素认证)`，则可能需要执行额外步骤。

.. image:: azure/login-next.png
   :align: center
   :alt: 输入 Microsoft 登录凭据。

最后，登录账户后，页面将重定向到权限页面，提示用户 :guilabel:`接受` Odoo 应用程序访问其 Microsoft 信息的条件。

.. image:: azure/accept-access.png
   :align: center
   :alt: 接受 Microsoft 权限访问您的账户信息的条件。
