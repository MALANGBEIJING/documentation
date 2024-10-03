:show-content:
=====
用户
=====

Odoo 将*用户*定义为有权访问数据库的人。管理员可以根据公司需要添加任意数量的用户，并且为了限制每个用户可以访问的信息类型，可以为每个用户应用规则。可以随时添加和更改用户及其访问权限。

.. seealso::
   - :doc:`users/language`
   - :doc:`users/access_rights`
   - :ref:`access-rights/superuser`
   - :ref:`access-rights/groups`

.. _users/add-individual:

添加单个用户
====================

要添加新用户，请导航到 :menuselection:`设置应用 --> 用户部分 --> 管理用户`，然后点击 :guilabel:`新建`。

.. image:: users/manage-users.png
   :align: center
   :alt: Odoo 中设置页面中的管理用户字段视图。

填写表单的所有必填信息。在 :doc:`访问权限 <users/access_rights>` 标签下，选择用户可以访问的每个应用程序的组。

显示的应用程序列表基于数据库中安装的应用程序。

.. image:: users/new-user.png
   :align: center
   :alt: Odoo 中用户表单的访问权限标签视图。

填写页面上所有必需字段后，手动点击 :guilabel:`保存`。系统会自动使用 :guilabel:`电子邮件地址` 字段中的电子邮件向用户发送邀请电子邮件。用户必须点击电子邮件中的链接接受邀请，并创建数据库登录信息。

.. image:: users/invitation-email.png
   :align: center
   :alt: Odoo 中用户表单显示邀请电子邮件已发送的通知。

.. warning::
   如果公司使用的是按月订阅计划，数据库会自动更新以反映添加的用户。如果公司使用的是按年或多年订阅计划，数据库中会出现到期通知横幅。可以点击横幅创建升级报价以更新订阅。或者，`发送支持工单 <https://www.odoo.com/help>`_ 以解决问题。

用户类型
---------

可以通过用户表单的 :guilabel:`访问权限` 标签选择 :guilabel:`用户类型`，可通过 :menuselection:`设置应用 --> 用户部分 --> 管理用户` 访问。

用户有三种类型：:guilabel:`内部用户`、:guilabel:`门户`和 :guilabel:`公共`。

.. image:: users/user-type.png
   :align: center
   :alt: 在开发者模式下 Odoo 中用户表单强调用户类型字段的视图。

.. tip::
   用户被认为是内部数据库用户。门户用户是外部用户，他们只能访问数据库门户以查看记录。有关更多信息，请参阅 :doc:`users/portal`。

   公共用户是那些通过网站前端访问网站的用户。

:guilabel:`门户` 和 :guilabel:`公共` 用户选项**不**允许管理员选择访问权限。这些用户具有预设的特定访问权限（例如记录规则和限制菜单），通常不属于 Odoo 的常规用户组。

.. _users/deactivate:

停用用户
================

要停用（即归档）用户，请导航到 :menuselection:`设置应用 --> 用户部分 --> 管理用户`。然后，勾选要停用的用户左侧的复选框。

选择要归档的用户后，点击 :guilabel:`⚙️ 操作` 图标，然后从下拉菜单中选择 :guilabel:`归档`。接着，点击弹出窗口中的 :guilabel:`确定`。

.. danger::
   **切勿**停用主要/管理员用户（admin）。更改管理员用户可能会对数据库产生不利影响。这包括*无权管理的管理员*，即数据库中没有用户可以更改访问权限。因此，Odoo 建议在进行更改之前联系 Odoo 商业分析师或我们的支持团队。

错误：用户数量过多
---------------------

如果 Odoo 数据库中的用户数量超过 Odoo 企业版订阅中规定的数量，则会显示以下消息。

.. image:: users/add-more-users.png
   :align: center
   :alt: 数据库用户过多错误消息。

当出现该消息时，数据库管理员有 30 天的时间采取行动，否则数据库将过期。每天都会更新倒计时。

要解决此问题，可以选择：

- 通过点击消息中显示的 :guilabel:`升级您的订阅` 链接，添加更多用户到订阅中，验证升级报价并支付额外的用户费用。
- :ref:`停用用户 <users/deactivate>`，并拒绝升级报价。

.. warning::
   如果公司使用的是按月订阅计划，数据库会自动更新以反映添加的用户。如果公司使用的是按年或多年订阅计划，数据库中会出现到期通知横幅。可以点击横幅创建升级报价以更新订阅。或者，用户可以 `发送支持工单 <https://www.odoo.com/help>`_ 以解决问题。

一旦数据库中的用户数量正确，过期消息将在几天后自动消失，下次验证时会更新状态。

.. _users/passwords-management:

密码管理
===================

密码管理是确保用户随时独立访问数据库的重要部分。Odoo 提供了几种不同的重置用户密码的方法。

.. tip::
   Odoo 有一个设置可以指定密码所需的长度。可以通过导航到 :menuselection:`设置应用 --> 权限` 部分，并在 :guilabel:`最低密码长度` 字段中输入所需的密码长度来访问该设置。默认值为 `8`。

.. image:: users/minimum-password-length.png
   :align: center
   :alt: Odoo 中常规设置中权限部分中的最低密码长度字段。

.. _users/reset-password:

重置密码
--------------

有时，用户可能希望重置其个人密码以增强安全性，从而确保只有他们自己可以访问密码。Odoo 提供了两种不同的重置选项：一种是由用户自行发起密码重置，另一种是由管理员触发的重置。

.. _users/reset-password-login:

从登录页面启用密码重置
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

可以启用/禁用直接从登录页面进行密码重置的功能。此操作由个人用户完成，并且该设置默认启用。

要更改此设置，请转到 :menuselection:`设置应用 --> 权限` 部分，激活 :guilabel:`密码重置`，然后点击 :guilabel:`保存`。

.. image:: users/password-reset-login.png
   :align: center
   :alt: 在 Odoo 设置中启用密码重置。

在登录页面，点击 :guilabel:`重置密码` 以启动密码重置过程，系统会将重置令牌发送到存档的电子邮件地址。

.. image:: users/password-reset.png
   :align: center
   :alt: Odoo 登录页面上的密码重置选项高亮显示。

.. _users/reset-password-email:

发送重置说明
~~~~~~~~~~~~~~~~~~~~~~~

导航到 :menuselection:`设置应用 --> 用户和公司 --> 用户`，从列表中选择用户，然后在用户表单上点击 :guilabel:`发送密码重置说明`。系统会自动向他们发送包含密码重置说明的电子邮件。

.. note::
   如果用户尚未确认 Odoo 邀请电子邮件，则 :guilabel:`发送密码重置说明` 按钮**不会**显示。取而代之的是 :guilabel:`重新发送邀请邮件` 按钮。

该电子邮件包含重置密码所需的所有说明以及一个重定向用户到 Odoo 登录页面的链接。

.. image:: users/password-reset-email.png
   :align: center
   :alt: Odoo 帐户密码重置链接的电子邮件示例。

.. _users/change-password:

更改用户密码
--------------------

导航至 :menuselection:`设置应用 --> 用户和公司 --> 用户`，选择一个用户以访问其表单。点击 :guilabel:`⚙️ 操作` 图标，从出现的下拉菜单中选择 :guilabel:`更改密码`。在弹出的 :guilabel:`更改密码` 窗口中的 :guilabel:`新密码` 列中输入新密码，并通过点击 :guilabel:`更改密码` 确认更改。

.. image:: users/change-password.png
   :align: center
   :alt: 在 Odoo 中更改用户密码。

.. note::
   此操作仅修改本地用户密码，**不**会影响他们的 odoo.com 帐户。

   如果需要更改 odoo.com 的密码，请使用 :ref:`发送密码重置说明 <users/reset-password-email>`。Odoo.com 密码用于访问 *我的数据库* 页面以及其他门户功能。

点击 :guilabel:`更改密码` 后，页面将重定向至 Odoo 登录页面，用户可以使用新密码重新访问数据库。

.. _users/multi-companies:

多公司管理
===============

用户表单中的 :guilabel:`多公司` 字段允许管理员为用户提供访问多个公司的权限。要为用户配置多公司环境，请通过导航至：:menuselection:`设置应用 --> 用户部分 --> 管理用户`，选择用户以打开其用户表单，并配置多公司访问权限。

在 :guilabel:`访问权限` 标签下的 :guilabel:`多公司` 中，设置 :guilabel:`允许访问的公司` 和 :guilabel:`默认公司` 字段。

:guilabel:`允许访问的公司` 字段可以包含多个公司。这些公司是用户根据设置的访问权限可以访问和编辑的公司。:guilabel:`默认公司` 是用户每次登录时默认使用的公司。此字段只能包含**一个**公司。

.. warning::
   如果多公司访问权限未正确配置，可能会导致多公司行为不一致。因此，只有经验丰富的 Odoo 用户才应对具有多公司配置的数据库的用户进行访问权限更改。有关技术说明，请参阅 :doc:`../../../developer/howtos/company` 中的开发者文档。

.. image:: users/multi-companies.png
   :align: center
   :alt: Odoo 中用户表单中的多公司字段视图。

.. seealso::
   :doc:`companies`

.. toctree::
   :titlesonly:

   users/language
   users/2fa
   users/access_rights
   users/portal
   users/facebook
   users/google
   users/azure
   users/ldap
