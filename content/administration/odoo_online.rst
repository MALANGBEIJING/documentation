===========
Odoo 在线
===========

`Odoo 在线 <https://www.odoo.com/trial>`_ 提供由 Odoo 完全管理和托管的私有数据库。它可以用于长期生产或彻底测试 Odoo，包括不需要代码的自定义。

.. note::
   Odoo 在线与自定义模块或 Odoo 应用商店不兼容。

Odoo 在线数据库可以通过任何网络浏览器访问，不需要本地安装。

要快速试用 Odoo，可使用共享的 `演示 <https://demo.odoo.com>`_ 实例。无需注册，但每个实例仅存在几个小时。

数据库管理
===================

要管理数据库，请访问 `数据库管理器 <https://www.odoo.com/my/databases>`_ 并以数据库管理员身份登录。

点击数据库名称可以访问所有主要的数据库管理选项，升级选项除外，该选项可以通过点击数据库名称旁边的 **箭头圈** 图标访问。该图标仅在有可用升级时显示。

.. image:: odoo_online/database-manager.png
   :alt: 访问数据库管理选项

- :ref:`odoo_online/upgrade`
- :ref:`odoo_online/duplicate`
- :ref:`odoo_online/rename`
- :ref:`odoo_online/download`
- :ref:`odoo_online/domains`
- :ref:`odoo_online/tags`
- :ref:`odoo_online/delete`
- :ref:`odoo_online/contact-support`
- :ref:`odoo_online/users`

.. _odoo_online/upgrade:

升级
=======

触发数据库升级。

.. seealso::
   欲了解更多关于升级流程的信息，请查阅 :ref:`Odoo 在线升级文档 <upgrade-request-test>`。

.. _odoo_online/duplicate:

复制
=========

创建数据库的精确副本，可用于测试而不影响日常操作。

.. important::
   - 勾选 :guilabel:`用于测试目的`，默认情况下会在复制的数据库上禁用所有外部操作（邮件、支付、发货订单等）。
   - 复制的数据库将在 15 天后自动过期。
   - 每个数据库最多可以创建五个副本。在特殊情况下，请联系 `支持 <https://www.odoo.com/help>`_ 以提高限制。

.. _odoo_online/rename:

重命名
======

重命名数据库及其 URL。

.. _odoo_online/download:

下载
========

下载包含数据库备份的 ZIP 文件。

.. note::
   根据 `Odoo 云托管 SLA <https://www.odoo.com/cloud-sla>`_，数据库会每日备份。

.. _odoo_online/domains:

域名
============

使用自定义 :doc:`域名 </applications/websites/website/configuration/domain_names>` 通过另一个 URL 访问数据库。

.. tip::
   您可以 :ref:`免费注册域名 <domain-name/register>`。

.. _odoo_online/tags:

标签
====

添加标签以便轻松识别和排序数据库。

.. tip::
   您可以在搜索栏中搜索标签。

.. _odoo_online/delete:

删除
======

立即删除数据库。

.. danger::
   删除数据库意味着所有数据将被永久删除。删除操作是即时的，适用于所有用户。建议在删除数据库之前创建其备份。

仔细阅读警告信息，只有在完全理解删除数据库的后果时才继续操作。

.. image:: odoo_online/delete.png
   :alt: 删除数据库前显示的警告信息

.. note::
   - 只有管理员可以删除数据库。
   - 数据库名称会立即对任何人开放。
   - 如果数据库已过期或与订阅相关联，无法删除。在这种情况下，请联系 `Odoo 支持 <https://www.odoo.com/help>`_。

.. _odoo_online/contact-support:

联系我们
==========

访问带有数据库详细信息的 `Odoo.com 支持页面 <https://www.odoo.com/help>`_。

.. _odoo_online/users:

邀请 / 移除用户
=====================

要邀请用户，请填写新用户的电子邮件地址并点击 :guilabel:`邀请`。要添加多个用户，请点击 :guilabel:`添加更多用户`。

.. image:: odoo_online/invite-users.png
   :alt: 在数据库上邀请用户

要移除用户，请选择他们并点击 :guilabel:`移除`。

.. seealso::
   - :doc:`/applications/general/users`
   - :doc:`odoo_accounts`
