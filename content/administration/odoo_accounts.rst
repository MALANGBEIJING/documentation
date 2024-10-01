=================
Odoo.com 账户
=================

本文件专门说明对 Odoo.com 账户进行的编辑操作。以下过程描述了如何删除 Odoo.com 账户以及如何更改 Odoo.com 账户的密码。

删除 Odoo.com 账户
=======================

要删除 Odoo.com 账户，首先点击右上角的个人资料图标（由用户名和图标表示）以显示下拉菜单。在下拉菜单中，选择 :guilabel:`我的 Odoo.com 账户`，以进入用户门户。

从用户门户中，可以通过导航到 :menuselection:`我的账户 --> 编辑安全设置 --> 删除账户` 来访问删除选项。也可以通过访问 `https://www.odoo.com/my/home <https://www.odoo.com/my/home>`_ 进行访问。

.. danger::
   删除 Odoo 账户是不可逆的。执行此操作时请小心，因为 Odoo.com 账户一旦删除，将**无法**恢复。

点击 :guilabel:`删除账户` 按钮后，会出现一个弹出窗口，要求确认删除账户。

.. image:: odoo_account/delete-account.png
   :align: center
   :alt: 点击删除账户按钮后，将显示一个确认更改的窗口。

要确认删除，请输入要删除账户的 :guilabel:`密码` 和 :guilabel:`登录名`。然后点击 :guilabel:`删除账户` 按钮以确认删除。

.. _odoocom/change_password:

更改 Odoo.com 账户密码
================================

要更改 Odoo.com 账户密码，请首先从 Odoo.com 登录页面登录到 Odoo.com 用户账户。登录后，进入屏幕右上角，点击个人资料图标旁边的 :guilabel:`▼（向下箭头）` 图标。然后选择 :guilabel:`我的账户`，会显示门户仪表板。

要更改 Odoo.com 密码，请点击 :guilabel:`编辑安全设置` 链接，该链接位于 :menuselection:`账户安全` 部分下方。接下来，通过输入当前的 :guilabel:`密码`、:guilabel:`新密码` 并确认新密码来进行必要的更改。最后，点击 :guilabel:`更改密码` 完成密码更改。

.. note::
   如果客户想更改登录名，请`联系 Odoo 支持 <https://www.odoo.com/help>`_。

.. note::
   即使使用相同的电子邮件地址，Odoo.com 用户和门户用户的密码仍然是分开的。

添加双因素认证
=============================

要添加双因素认证，请从 Odoo.com 登录页面登录到 Odoo.com 用户账户。登录后，进入屏幕右上角，点击个人资料图标旁边的 :guilabel:`▼（向下箭头）` 图标。然后选择 :guilabel:`我的账户`，会显示门户仪表板。

如果用户希望为 Odoo.com 访问启用双因素认证 (2FA)，请点击 :menuselection:`账户安全` 部分下方的 :guilabel:`编辑安全设置` 链接。

点击 :guilabel:`启用双因素认证` 以启用 :abbr:`2FA (双因素认证)`。然后，在 :guilabel:`密码` 字段中确认当前密码。接下来，点击 :guilabel:`确认密码`。随后，在 :abbr:`2FA (双因素认证)` 应用（如 Google Authenticator、Authy 等）中，通过扫描 :guilabel:`二维码` 或输入 :guilabel:`验证码` 来激活 :abbr:`2FA (双因素认证)`。

最后，点击 :guilabel:`启用双因素认证` 以完成设置。

.. note::
   在 :guilabel:`我的账户` 下，Odoo.com 用户还可以访问以下内容：

   - :guilabel:`我的合作伙伴仪表板`
   - :guilabel:`我的应用内服务`
   - :guilabel:`我的应用仪表板`
