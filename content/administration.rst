===================
数据库管理
===================

这些指南提供了关于如何安装、维护和升级 Odoo 数据库的说明。

.. seealso::
    :doc:`版本历史 <administration/supported_versions>`

安装
====

根据预期的使用场景，有多种方式来安装 Odoo，或者根本不需要安装。

- :doc:`在线版 <administration/odoo_online>` 是最简单的方式，可以将 Odoo 用于生产或进行试用。

- :doc:`打包安装程序 <administration/on_premise/packages>` 适合测试 Odoo 和开发模块。经过额外的部署和维护工作后，也可以用于长期生产。

- :doc:`源代码安装 <administration/on_premise/source>` 提供了更大的灵活性，例如可以在同一系统上运行多个 Odoo 版本。它适合开发模块，并且可以用作生产部署的基础。

- 可供开发或部署使用的 `Docker <https://hub.docker.com/_/odoo/>`_ 基础镜像也可用。

.. _install/editions:

版本
====

Odoo 有两个不同的版本。

**Odoo Community** 是软件的免费开源版本，遵循 `GNU LGPLv3 <https://github.com/odoo/odoo/blob/master/LICENSE>`_ 许可协议。它是 Odoo Enterprise 构建的核心。

**Odoo Enterprise** 是软件的共享源代码版本，提供更多功能，包括功能支持、升级和托管服务。`价格 <https://www.odoo.com/pricing-plan>`_ 从一个免费应用开始。

.. tip::
   :doc:`随时从社区版切换到企业版 <administration/on_premise/community_to_enterprise>`（源代码安装除外）。

.. toctree::
    :titlesonly:

    administration/hosting
    administration/odoo_online
    administration/odoo_sh
    administration/on_premise
    administration/upgrade
    administration/neutralized_database
    administration/supported_versions
    administration/mobile
    administration/odoo_accounts
