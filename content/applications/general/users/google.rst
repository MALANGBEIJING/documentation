=============================
Google 登录认证
=============================

*Google 登录认证* 是一项有用的功能，允许 Odoo 用户使用其 Google 账户登录数据库。

这对于使用 Google Workspace 的组织特别有帮助，组织希望其员工通过 Google 账户连接到 Odoo。

.. warning::
   在 Odoo.com 上托管的数据库不应为数据库的所有者或管理员使用 OAuth 登录，因为这会将数据库与他们的 Odoo.com 账户解除关联。如果为该用户设置了 OAuth，数据库将无法再通过 Odoo.com 门户进行复制、重命名或管理。

.. seealso::
   - :doc:`/applications/productivity/calendar/google`
   - :doc:`../email_communication/google_oauth`

.. _google-sign-in/configuration:

配置
=============

Google 登录功能的集成需要在 Google *和* Odoo 上进行配置。

.. _google-sign-in/api:

Google API 仪表盘
--------------------

#. 前往 `Google API 仪表盘 <https://console.developers.google.com/>`_。
#. 确保打开了正确的项目。如果还没有项目，点击 :guilabel:`创建项目`，填写项目名称和公司的其他详细信息，然后点击 :guilabel:`创建`。

   .. image:: google/new-project-details.png
      :align: center
      :alt: 填写新项目的详细信息。

   .. tip::
      从下拉菜单中选择公司的名称。

.. _google-sign-in/oauth:

OAuth 同意屏幕
~~~~~~~~~~~~~~~~~~~~

#. 在左侧菜单中，点击 :menuselection:`OAuth 同意屏幕`。

   .. image:: google/consent-selection.png
      :align: center
      :alt: Google OAuth 同意选择菜单。

#. 选择其中一个选项（:guilabel:`内部` / :guilabel:`外部`），然后点击 :guilabel:`创建`。

   .. image:: google/consent.png
      :align: center
      :alt: 在 OAuth 同意屏幕中选择用户类型。

   .. warning::
      *个人* Gmail 账户只允许为 **外部** 用户类型，这意味着 Google 可能需要批准，或添加 *范围*。然而，使用 *Google Workspace* 账户可以使用 **内部** 用户类型。

      请注意，当 API 连接处于 *外部* 测试模式时，无需 Google 批准。此测试模式下的用户限制为 100 名用户。

#. 填写所需的详细信息和域信息，然后点击 :guilabel:`保存并继续`。
#. 在 :menuselection:`范围` 页面上，保持所有字段不变，然后点击 :guilabel:`保存并继续`。
#. 接下来，如果继续处于测试模式（*外部*），在 :guilabel:`测试用户` 步骤中通过点击 :guilabel:`添加用户` 来添加正在配置的电子邮件地址，然后点击 :guilabel:`保存并继续`。应用程序注册摘要将显示出来。
#. 最后，向下滚动到底部，点击 :guilabel:`返回仪表盘`。

.. _google-sign-in/credentials:

凭据
~~~~~~~~~~~

#. 在左侧菜单中，点击 :menuselection:`凭据`。

   .. image:: google/credentials-button.png
      :align: center
      :alt: 凭据按钮菜单。

#. 点击 :guilabel:`创建凭据`，并选择 :guilabel:`OAuth 客户端 ID`。

   .. image:: google/client-id.png
      :align: center
      :alt: 选择 OAuth 客户端 ID。

#. 选择 :guilabel:`Web 应用程序` 作为 :guilabel:`应用程序类型`。现在，配置 Odoo 将被重定向的允许页面。

   在 :guilabel:`授权重定向 URI` 字段中，输入数据库的域名，紧跟 `/auth_oauth/signin`。例如：
   `https://mydomain.odoo.com/auth_oauth/signin`，然后点击 :guilabel:`创建`。

#. 现在，*OAuth 客户端* 已创建，屏幕上会显示 :guilabel:`客户端 ID` 和 :guilabel:`客户端密钥`。复制 :guilabel:`客户端 ID` 以备后用，因为它将在后续 Odoo 配置步骤中使用。

.. _google-sign-in/auth-odoo:

Odoo 上的 Google 身份验证
-----------------------------

.. _google-sign-in/client-id:

检索客户端 ID
~~~~~~~~~~~~~~~~~~~~~~

完成前面的步骤后，会在 Google API 仪表盘上生成两个密钥：:guilabel:`客户端 ID` 和 :guilabel:`客户端密钥`。复制 :guilabel:`客户端 ID`。

.. image:: google/secret-ids.png
   :align: center
   :alt: Google OAuth 客户端 ID 已生成。

.. _google-sign-in/odoo-activation:

Odoo 激活
~~~~~~~~~~~~~~~

#. 前往 :menuselection:`Odoo 常规设置 --> 集成` 并启用 :guilabel:`OAuth 身份验证`。

   .. note::
      Odoo 可能会在此步骤后提示用户重新登录。

#. 返回 :menuselection:`常规设置 --> 集成 --> OAuth 身份验证`，启用该选项并点击 :guilabel:`保存`。接着，返回 :menuselection:`常规设置 --> 集成 --> Google 身份验证` 并启用该选项。然后使用来自 Google API 仪表盘的密钥填写 :guilabel:`客户端 ID`，并点击 :guilabel:`保存`。

   .. image:: google/odoo-client-id.png
      :align: center
      :alt: 在 Odoo 设置中填写客户端 ID。

   .. note::
      还可以通过点击 :guilabel:`OAuth 身份验证` 部分下的 :guilabel:`OAuth 提供者` 来访问 Google OAuth2 配置。

.. _google-sign-in/log-in:

使用 Google 登录 Odoo
==========================

要将 Google 账户链接到 Odoo 配置文件，首次登录 Odoo 时，点击 :guilabel:`使用 Google 登录`。

   .. image:: google/first-login.png
      :align: center
      :alt: 重置密码屏幕上的“使用 Google 登录”按钮。

现有用户必须 :ref:`重置密码 <users/reset-password>` 以访问 :menuselection:`重置密码` 页面，而新用户可以直接点击 :guilabel:`使用 Google 登录`，而不是选择新密码。

.. seealso::
   - `Google Cloud 平台控制台帮助 - 设置 OAuth 2.0 <https://support.google.com/cloud/answer/6158849>`_
