==========
容器
==========

概述
========

每个构建都被隔离在其自己的容器（Linux 命名空间容器）中。

基础是一个 Ubuntu 系统，其中安装了 Odoo 所需的所有依赖项以及常用的有用软件包。

如果您的项目需要额外的 Python 依赖项或更新版本，您可以在分支的根目录中定义一个 :file:`requirements.txt` 文件来列出它们。平台将负责在您的容器中安装这些依赖项。
`pip 依赖项规范 <https://pip.pypa.io/en/stable/reference/pip_install/#requirement-specifiers>`_ 文档可以帮助您编写 :file:`requirements.txt` 文件。
要了解具体示例，请查看 `Odoo 的 requirements.txt 文件 <{GITHUB_PATH}/requirements.txt>`_。

子模块中的 :file:`requirements.txt` 文件也会被考虑在内。平台会在每个包含 Odoo 模块的文件夹中查找 :file:`requirements.txt` 文件：不是在模块文件夹本身，而是在它们的父文件夹中。

目录结构
===================

由于容器基于 Ubuntu 系统，它们的目录结构遵循 Linux 文件系统层次结构标准。
`Ubuntu 文件系统树概述 <https://help.ubuntu.com/community/LinuxFilesystemTreeOverview#Main_directories>`_ 解释了主要目录。

以下是 Odoo.sh 相关的目录：

::

  .
  ├── home
  │    └── odoo
  │         ├── src
  │         │    ├── odoo                Odoo 社区版源码
  │         │    │    └── odoo-bin       Odoo 服务器可执行文件
  │         │    ├── enterprise          Odoo 企业版源码
  │         │    ├── themes              Odoo 主题源码
  │         │    └── user                您的存储库分支源码
  │         ├── data
  │         │    ├── filestore           数据库附件及二进制字段文件
  │         │    └── sessions            访问者和用户会话
  │         └── logs
  │              ├── install.log         数据库安装日志
  │              ├── odoo.log            服务器运行日志
  │              ├── update.log          数据库更新日志
  │              └── pip.log             Python 包安装日志
  └── usr
       ├── lib
       │    ├── python2.7
       │         └── dist-packages       Python 2.7 标准库
       │    ├── python3
       │         └── dist-packages       Python 3 标准库
       │    └── python3.5
       │         └── dist-packages       Python 3.5 标准库
       ├── local
       │    └── lib
       │         ├── python2.7
       │         │    └── dist-packages  Python 2.7 第三方库
       │         └── python3.5
       │              └── dist-packages  Python 3.5 第三方库
       └── usr
            └── bin
                 ├── python2.7           Python 2.7 可执行文件
                 └── python3.5           Python 3.5 可执行文件

容器中安装了 Python 2.7 和 3.5。然而：

* 如果您的项目配置为使用 Odoo 10.0，Odoo 服务器将使用 Python 2.7 运行。
* 如果您的项目配置为使用 Odoo 11.0 或更高版本，Odoo 服务器将使用 Python 3.5 运行。

数据库 shell
==============

在访问带有 shell 的容器时，您可以使用 *psql* 访问数据库。

.. code-block:: bash

  odoo@odoo-addons-master-1.odoo.sh:~$ psql
  psql (9.5.2, server 9.5.11)
  SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
  Type "help" for help.

  odoo-addons-master-1=>

**请注意！**
对于任何会导致更改的 *sql* 语句（如 *UPDATE*、*DELETE*、*ALTER* 等），
请使用 `事务机制 <https://www.postgresql.org/docs/current/static/sql-begin.html>`_ (*BEGIN...COMMIT/ROLLBACK*)。
特别是对于生产数据库，这是您的安全网。
只需回滚您的更改，即可将数据库恢复到之前的状态。

例如，您可能会忘记设置 *WHERE* 条件。

.. code-block:: sql

  odoo-addons-master-1=> BEGIN;
  BEGIN
  odoo-addons-master-1=> UPDATE res_users SET password = '***';
  UPDATE 457
  odoo-addons-master-1=> ROLLBACK;
  ROLLBACK

在这种情况下，您可以回滚来撤销刚才误操作的更改，并重写语句：

.. code-block:: sql

  odoo-addons-master-1=> BEGIN;
  BEGIN
  odoo-addons-master-1=> UPDATE res_users SET password = '***' WHERE id = 1;
  UPDATE 1
  odoo-addons-master-1=> COMMIT;
  COMMIT

但是，不要忘记在完成操作后提交或回滚您的事务。未关闭的事务可能会锁定表中的记录，
导致数据库挂起，服务器可能会无限期等待。

此外，尽可能在您的预发布数据库中测试语句，这为您提供了额外的安全保障。

运行 Odoo 服务器
==================

您可以从容器 shell 启动 Odoo 服务器实例。虽然无法通过浏览器从外部访问该实例，
但您可以执行以下操作：

* 使用 Odoo shell，

.. code-block:: bash

  $  odoo-bin shell
  >>> partner = env['res.partner'].search([('email', '=', 'asusteK@yourcompany.example.com')], limit=1)
  >>> partner.name
  'ASUSTeK'
  >>> partner.name = 'Odoo'
  >>> env['res.partner'].search([('email', '=', 'asusteK@yourcompany.example.com')], limit=1).name
  'Odoo'

* 安装模块，

.. code-block:: bash

  $  odoo-bin -i sale --without-demo=all --stop-after-init

* 更新模块，

.. code-block:: bash

  $  odoo-bin -u sale --stop-after-init

* 运行模块测试，

.. code-block:: bash

  $  odoo-bin -i sale --test-enable --log-level=test --stop-after-init

在上述命令中，参数：

* ``--without-demo=all`` 阻止加载所有模块的演示数据
* ``--stop-after-init`` 会在完成您要求的操作后立即关闭服务器实例。

更多可用选项和详细信息，请参阅
:doc:`CLI 文档 </developer/reference/cli>`。

您可以在日志文件 (*~/logs/odoo.log*) 中找到 Odoo.sh 用于运行服务器的插件路径。
查找 "*odoo: addons paths*"：

::

  2018-02-19 10:51:39,267 4 INFO ? odoo: Odoo version {BRANCH}
  2018-02-19 10:51:39,268 4 INFO ? odoo: Using configuration file at /home/odoo/.config/odoo/odoo.conf
  2018-02-19 10:51:39,268 4 INFO ? odoo: addons paths: ['/home/odoo/data/addons/{BRANCH}', '/home/odoo/src/user', '/home/odoo/src/enterprise', '/home/odoo/src/themes', '/home/odoo/src/odoo/addons', '/home/odoo/src/odoo/odoo/addons']

**请小心**，特别是在处理生产数据库时。
运行此 Odoo 服务器实例执行的操作并非隔离的：
更改将生效于数据库中。始终在您的预发布数据库中进行测试。

在 Odoo.sh 中调试
====================

调试 Odoo.sh 构建与调试其他 Python 应用程序没有太大区别。本文仅解释 Odoo.sh 平台的特殊性和限制，并假设您已经知道如何使用调试器。

.. note:: 如果您还不知道如何调试 Python 应用程序，可以在互联网上轻松找到多个入门课程。

您可以使用 ``pdb``、``pudb`` 或 ``ipdb`` 在 Odoo.sh 上调试代码。
由于服务器在 shell 外运行，您无法直接从 Odoo 实例后端启动调试器，因为调试器需要 shell 来运行。

- `pdb <https://docs.python.org/3/library/pdb.html>`_ 已预装在每个容器中。

- 如果您想使用 `pudb <https://pypi.org/project/pudb/>`_ 或 `ipdb <https://pypi.org/project/ipdb/>`_，则需要先安装它们。

  您有两个选择：

    - 临时安装（仅限当前构建）：

      .. code-block:: bash

        $  pip install pudb --user

      或者

      .. code-block:: bash

        $  pip install ipdb --user

    - 永久安装：将 ``pudb`` 或 ``ipdb`` 添加到项目的 ``requirements.txt`` 文件中。

然后在要触发调试器的代码中添加以下内容：

.. code-block:: python

  import sys
  if sys.__stdin__.isatty():
      import pdb; pdb.set_trace()

条件 :code:`sys.__stdin__.isatty()` 是一个检测您是否从 shell 运行 Odoo 的小技巧。

保存文件，然后运行 Odoo Shell：

.. code-block:: bash

  $ odoo-bin shell

最后，通过 Odoo Shell，您可以触发要调试的代码/函数/方法。

.. image:: containers/pdb_sh.png
    :align: center
    :alt: 控制台截图显示 Odoo.sh shell 中运行的 ``pdb``。
