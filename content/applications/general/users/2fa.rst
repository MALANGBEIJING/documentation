=========================
双因素认证
=========================

.. |2fa| replace:: :abbr:`2FA (双因素认证)`
.. |QR| replace:: :abbr:`QR (快速响应)` 码

*双因素认证 (2FA)* 是一种提高安全性的方法，可以防止未经授权的人员访问用户账户。

实际上，|2fa| 意味着将一个密钥存储在*认证器*中，通常是在手机上，并在尝试登录时从认证器中交换一个代码。

这意味着未经授权的用户需要同时猜测账户密码*并且*拥有认证器的访问权限，这将更难实现。

要求
=====

.. important::
   这些列表只是示例。它们并**不是**对任何特定软件的推荐。

基于手机的认证器是最简单和最常用的。示例包括：

- `Authy <https://authy.com/>`_
- `FreeOTP <https://freeotp.github.io/>`_
- `Google 身份验证器 <https://support.google.com/accounts/answer/1066447?hl=zh>`_
- `LastPass 身份验证器 <https://lastpass.com/auth/>`_
- `微软身份验证器
  <https://www.microsoft.com/zh-cn/account/authenticator?cmp=h66ftb_42hbak>`_

密码管理器是另一种选择。常见示例包括：

- `1Password <https://support.1password.com/one-time-passwords/>`_
- `Bitwarden <https://bitwarden.com/help/article/authenticator-keys/>`_,

.. note::
   本文档的其余部分使用Google身份验证器作为示例，因为它是最常用的之一。这**不是**对该产品的推荐。

双因素认证设置
===============

选择认证器后，登录Odoo，然后点击右上角的个人头像，从下拉菜单中选择 :guilabel:`我的个人资料`。

点击 :guilabel:`账户安全` 选项卡，然后将 :guilabel:`双因素认证` 开关滑动为*激活*。

.. figure:: 2fa/account-security.png
   :align: center

此时会生成一个 :guilabel:`安全控制` 弹出窗口，要求确认密码才能继续。输入相应的密码，然后点击 :guilabel:`确认密码`。接下来会出现一个
:guilabel:`双因素认证激活` 弹出窗口，显示一个 |QR| 码。

.. figure:: 2fa/qr-code.png
   :align: center

使用所需的认证器应用程序，在提示时扫描 |QR| 码。

.. tip::
   如果无法扫描屏幕（例如设置在*同一*设备上完成，该设备也是认证器的设备），点击提供的 :guilabel:`无法扫描？` 链接，或复制密钥手动设置认证器，都是替代方案。

   .. figure:: 2fa/secret-visible.png
      :align: center

   .. figure:: 2fa/input-secret.png
      :align: center

之后，认证器应显示一个*验证代码*。

.. figure:: 2fa/authenticator.png
   :align: center

在 :guilabel:`验证码` 字段中输入该代码，然后点击 :guilabel:`激活`。

.. figure:: 2fa/2fa-enabled.png
   :align: center

登录
====

要确认 |2fa| 设置已完成，登出Odoo。

在登录页面，输入用户名和密码，然后点击 :guilabel:`登录`。在 :guilabel:`双因素认证` 页面上，输入所选认证器提供的代码，在 :guilabel:`认证码` 字段中输入，然后点击 :guilabel:`登录`。

.. image:: 2fa/2fa-login.png
   :align: center
   :alt: 启用了2FA的登录页面。

.. danger::
   如果用户丢失了认证器的访问权限，管理员**必须**在用户能够登录之前停用其账户上的 |2fa|。

强制使用双因素认证
====================

要强制所有用户使用 |2fa|，首先导航到 :menuselection:`Odoo主面板 --> 应用程序`。从 :guilabel:`搜索...` 栏中移除 :guilabel:`应用程序` 过滤器，然后搜索 `通过邮件的2FA`。

点击 :guilabel:`安装` 在 :guilabel:`通过邮件的2FA` 模块的卡片上。

.. image:: 2fa/2FA-by-mail.png
   :align: center
   :alt: 应用程序目录中的通过邮件的2FA模块。

安装完成后，进入 :guilabel:`设置 应用程序: 权限`。勾选标有 :guilabel:`强制双因素认证` 的复选框。然后使用单选按钮选择是否将此设置应用于 :guilabel:`仅限员工`，还是 :guilabel:`所有用户`。

.. note::
   选择 :guilabel:`所有用户` 将设置应用于门户用户，以及员工。

.. image:: 2fa/enforce-settings.png
   :align: center
   :alt: 设置应用程序中的强制双因素设置。

点击 :guilabel:`保存` 以提交任何未保存的更改。
