=====
设置
=====

在本章中，你将学习：

- 设置本地开发环境。
- Odoo 数据库结构概述。
- 在本地环境中导出和导入 Odoo 数据库。
- 使 Odoo 实例正常运行。

安装
=======

根据预期用途，有多种方式来 :doc:`安装 Odoo </administration/on_premise>`。本文档假设你使用 :doc:`源代码安装
</administration/on_premise/source>`（从源代码运行 Odoo），这种方法最适合 Odoo 设计师和开发人员。

数据库
=========

结构
---------

每个 Odoo 应用程序的工作原理类似；它们是基于相同的逻辑构建的。一个模型包含字段和关系字段，这些字段链接到其他模型。每个模型都有表示其所有字段的视图，包含后端视图和前端视图。

模型
~~~~~~

Odoo 的基础是模型。模型使用字段来记录数据。记录存储在数据库中：因此它们与模型相关联。在 Odoo 中，你可以通过启用 :ref:`开发者模式 <developer-mode>` 并进入 :menuselection:`设置 --> 技术 --> 数据库结构：模型` 来查看不同的模型。

.. image:: setup/models-page.png
   :alt: 模型页面

字段
~~~~~~

在模型中，我们将字段集中（我们在代码中需要针对的字段名称）。

.. seealso::
   :doc:`/applications/studio/fields`

经典字段
**************

- 日期
- 字符串
- 选择
- …

关系字段
*****************

关系字段从另一个模型调用字段。它们允许你轻松链接模型并使其交互。换句话说，当你使用关系字段时，你将一个记录与另一个（位于另一个模型中的）记录链接，使你能够检索该链接记录中的字段内容。

- **Many2one** 字段通过从另一个模型中的记录列表中选择一个记录来填写（从*许多*记录中选择*一个*）。例如，报价单上的*客户*字段使你可以从*联系人*模型的多个客户中选择一个客户。
- **One2many** 字段是现有 many2one 关系的反向搜索。例如，你可以在联系人上列出他们的所有现有报价单（从*一个*记录显示*多个*）。
- **Many2many** 字段通过从另一个模型中的记录列表中选择一个或多个记录来填写。例如，你可以在一个产品上添加多个标签，多个产品可以使用相同的标签（从*许多*记录中选择*许多*）。

视图
~~~~~

视图定义了记录应如何向终端用户显示。它们在 XML 中指定，这意味着它们可以独立于它们表示的模型进行编辑。它们灵活且允许对它们控制的屏幕进行深度定制。

后端 vs. 前端
********************

- **后端视图**：看板、列表、表单等。
- **前端视图**：QWeb

静态 vs. 动态
******************

- **静态页面** 具有稳定的内容，例如首页。你可以定义它们的 URL 并设置一些属性，例如发布、索引等。
- **动态页面** 动态生成，例如产品页面。它们的 URL 是动态的，默认情况下对所有人可访问（可以通过配置访问权限更改）。

标准 vs. 继承
**********************

- **标准视图** 是 Odoo 实现的基本视图。它们直接派生自其模型。你永远不应更改它们，因为它们允许在不覆盖客户修改的情况下更新 Odoo 数据库。
- **继承视图** 是复制的视图。修改始终发生在继承视图中。如果存在重复视图，数据库中将有两个具有相同名称的视图，但重复的视图不会像标准视图那样具有 ID。

导入现有数据库
---------------------------

.. note::
   如果你不需要导入现有数据库，可以直接跳到 :doc:`主题` 章节。

导出
~~~~

Odoo SaaS
*********

访问 `<database_url>/saas_worker/dump`。

Odoo.sh
*******

#. 连接到 Odoo.sh。
#. 选择要备份的分支。
#. 选择 :guilabel:`备份` 标签。
#. 点击 :guilabel:`创建备份` 按钮。
#. 备份完成后，会出现一个通知。打开它并点击 :guilabel:`前往备份` 按钮。
#. 点击 :guilabel:`下载` 图标。在 :guilabel:`用途` 下选择 :guilabel:`测试`，在 :guilabel:`文件存储` 下选择 :guilabel:`包含文件存储`。

   .. image:: setup/download-backup.png
     :alt: 下载备份

#. 当转储准备好下载时，你将收到通知。打开它并点击 :guilabel:`下载` 获取转储。

   .. image:: setup/database-backup.png
     :alt: 数据库备份

移动文件存储
~~~~~~~~~~~~~~

将文件存储文件夹中的所有文件夹复制并粘贴到计算机上的以下位置：

- macOS: `/Users/<User>/Library/Application Support/Odoo/filestore/<database_name>`
- Linux: `/home/<User>/.local/share/Odoo/filestore/<database_name>`

.. note::
   `/Library` 是一个隐藏文件夹。

数据库设置
~~~~~~~~~~~~~~

创建一个空数据库。

.. code-block:: xml

   createdb <database_name>

将 SQL 文件导入你刚创建的数据库。

.. code-block:: xml

   psql <database_name> < dump.sql

重置管理员用户密码。

.. code-block:: xml

   psql \c
   <database_name>
   update res_users set login='admin', password='admin' where id=2;

开始使用
===============

运行 Odoo
------------

一旦设置了所有依赖项，可以通过运行 `odoo-bin` 启动 Odoo，这是服务器的命令行接口。它位于 Odoo Community 目录的根目录下。

- :ref:`运行 Odoo <install/source/running_odoo>`
- `Docker <https://hub.docker.com/_/odoo/>`_

要配置服务器，你可以指定命令行参数或配置文件。下面介绍第一种方法。

:ref:`CLI <reference/cmdline>` 提供了与 Odoo 相关的多种功能。你可以用它来 :ref:`运行服务器 <reference/cmdline/server>`，搭建 Odoo 主题，填充数据库或统计代码行数。

Shell 脚本
------------

运行服务器的典型方式是将所有命令行参数添加到 `.sh` 脚本中。

.. example::
   .. code-block:: xml

      ./odoo-bin --addons-path=../enterprise,addons --db-filter=<database> -d <database> --without-demo=all -i website --dev=xml

.. list-table::
   :header-rows: 1
   :stub-columns: 1
   :widths: 20 80

   * - 文件夹
     - 描述
   * - :option:`--addons-path <odoo-bin --addons-path>`
     - 以逗号分隔的存储模块的目录列表。这些目录会被扫描以查找模块。
   * - :option:`-d <odoo-bin --database>`

       :option:`--database <odoo-bin --database>`
     - 安装或更新模块时使用的数据库。
   * - :option:`--db-filter <odoo-bin --db-filter>`
     - 隐藏与过滤器不匹配的数据库。
   * - :option:`-i <odoo-bin --init>`

       :option:`--init <odoo-bin --init>`
     - 要在运行服务器之前安装的模块的逗号分隔列表。（需要 `-d`）
   * - :option:`-u <odoo-bin --update>`

       :option:`--update <odoo-bin --update>`
     - 要在运行服务器之前更新的模块的逗号分隔列表。（需要 `-d`）
   * - :option:`--without-demo <odoo-bin --without-demo>`
     - 禁用为已安装模块加载演示数据；使用 `all` 表示所有模块。（需要 `-d` 和 `-i`）
   * - :option:`--dev <odoo-bin --dev>`
     - 以逗号分隔的功能列表。仅用于开发目的。:ref:`更多信息 <reference/cmdline/dev>`

登录
-------

服务器启动后（打印 INFO 日志 `odoo.modules.loading: Modules loaded.`），在浏览器中打开 http://localhost:8069 并使用基本管理员帐户登录。

电子邮件输入 **admin**，密码输入 **admin**。

.. image:: setup/welcome-homepage.png
   :alt: 欢迎首页

.. tip::
   按 *CTRL+C* 停止服务器。如果需要，请按两次。

开发者模式
--------------

开发者模式，也称为调试模式，在开发过程中非常有用，因为它提供了额外的工具。在接下来的章节中，假设你已经启用了开发者模式。

.. seealso::
   :doc:`/applications/general/developer_mode`
