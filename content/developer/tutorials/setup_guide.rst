===========
安装指南
===========

根据使用案例的不同，安装 Odoo 有多种方式。对于 Odoo 社区开发人员和 Odoo 员工而言，首选的安装方式是源代码安装 (:dfn:`从源代码运行 Odoo`)。

.. important::
   请参阅贡献指南中的 :ref:`contributing/development/setup` 部分，为推送本地更改到 Odoo 存储库做好环境准备。

为教程调整环境
==================

到现在为止，您应该已经将源代码下载到两个本地存储库，一个用于 `odoo/odoo`，另一个用于 `odoo/enterprise`。这些存储库已设置为将更改推送到 GitHub 上的预定义 fork。当您开始为代码库做出贡献时，这将非常方便，但在跟随教程的过程中，我们希望避免用训练材料污染它们。让我们将您的更改推送到第三个存储库：`odoo/tutorials`。与前两个存储库一样，它将成为引用 Odoo 模块的 `addons-path` 的一部分。

.. note::
   根据您正在跟随的教程，您可能不需要安装该存储库中包含的所有模块。

#. 按照与 `odoo/odoo` 和 `odoo/enterprise` 存储库相同的过程，将 `odoo/tutorials` 存储库克隆到您的计算机上：

   .. code-block:: console

      $ git clone git@github.com:odoo/tutorials.git

#. 配置您的 fork 和 Git，将更改推送到您的 fork，而不是主代码库。如果您在 Odoo 工作，请将 Git 配置为将更改推送到账户 **odoo-dev** 上创建的共享 fork。

   .. tabs::

      .. tab:: 将 Git 与您的 fork 链接

         #. 访问 `github.com/odoo/tutorials <https://github.com/odoo/tutorials>`_ 并点击
            :guilabel:`Fork` 按钮在您的帐户上创建该存储库的 fork。

         #. 在以下命令中，将 `<your_github_account>` 替换为您创建 fork 的 GitHub 帐户名称。

            .. code-block:: console

               $ cd /TutorialsPath
               $ git remote add dev git@github.com:<your_github_account>/tutorials.git

      .. tab:: 将 Git 与 odoo-dev 链接

         .. code-block:: console

            $ cd /tutorials
            $ git remote add dev git@github.com:odoo-dev/tutorials.git
            $ git remote set-url --push origin you_should_not_push_on_this_repository

就这样！您的环境现在已准备好从源代码运行 Odoo，并且您已成功创建一个存储库，作为 addons 目录。这样您就可以将您的工作推送到 GitHub。

.. important::

   **仅适用于 Odoo 员工：**

   #. 确保仔细阅读 :ref:`contributing/development/first-contribution`。尤其是，您的分支名称必须遵循我们的命名约定。

   #. 一旦您将第一个更改推送到 **odoo-dev** 上的共享 fork，请创建 :abbr:`PR (Pull Request)`。请在 PR 标题中注明您的四字母代码 (例如 "abcd - 技术培训")。

      这样您就可以分享即将完成的工作，并从您的教练那里获得反馈。为了确保持续的反馈循环，我们建议您在完成教程的一章后尽快推送新的提交。请注意，PR 会自动更新为您推送到 **odoo-dev** 的提交，您无需打开多个 PR。

   #. 在 Odoo，我们广泛使用 `Runbot <https://runbot.odoo.com>`_ 进行 :abbr:`CI (持续集成)` 测试。当您将更改推送到 **odoo-dev** 时，Runbot 会创建一个新的构建并测试您的代码。登录后，您可以在 `Tutorials 项目 <https://runbot.odoo.com/runbot/tutorials-12>`_ 中看到您的分支。

.. note::

   您的存储库在文件系统上的特定位置并不重要。但是，为了简化操作，我们假设您已将所有存储库克隆到同一目录下。如果不是这种情况，请确保相应地调整以下命令，并提供从 `odoo/odoo` 存储库到 `odoo/tutorials` 存储库的适当相对路径。

运行服务器
===========

使用 `odoo-bin` 启动
----------------------

所有依赖项设置完成后，可以通过运行 `odoo-bin` 启动 Odoo，`odoo-bin` 是服务器的命令行接口。

.. code-block:: console

    $ cd $HOME/src/odoo/
    $ ./odoo-bin --addons-path="addons/,../enterprise/,../tutorials" -d rd-demo

运行服务器时，您可以使用多个 :ref:`命令行参数 <reference/cmdline/server>`。在本次培训中，您只需要其中的一部分。

.. option:: -d <database>

   要使用的数据库。

.. option:: --addons-path <directories>

   存储模块的目录的逗号分隔列表。这些目录将被扫描以查找模块。

.. option:: --limit-time-cpu <limit>

   防止 worker 为每个请求使用超过 <limit> 秒的 CPU 时间。

.. option:: --limit-time-real <limit>

   防止 worker 处理请求所用时间超过 <limit> 秒。

.. tip::
   - :option:`--limit-time-cpu` 和 :option:`--limit-time-real` 参数可用于防止在调试源代码时 worker 被终止。
   - | 您可能会遇到类似 `AttributeError: module '<MODULE_NAME>' has no attribute '<$ATTRIBUTE'>` 的错误。在这种情况下，您可能需要使用 :command:`$ pip install --upgrade --force-reinstall <MODULE_NAME>` 重新安装该模块。
     | 如果此错误发生在多个模块上，您可能需要使用 :command:`$ pip install --upgrade --force-reinstall -r requirements.txt` 重新安装所有依赖项。
     | 您还可以通过清除 Python 缓存来解决此问题：

       .. code-block:: console

          $ cd $HOME/.local/lib/python3.8/site-packages/
          $ find -name '*.pyc' -type f -delete

   - 其他常用参数包括：

     - :option:`-i <odoo-bin --init>`：在运行服务器之前安装一些模块（逗号分隔列表）。这相当于在用户界面的 :guilabel:`Apps` 中安装模块。
     - :option:`-u <odoo-bin --update>`：在运行服务器之前更新一些模块（逗号分隔列表）。这相当于在用户界面的 :guilabel:`Apps` 中选择一个模块，并从那里升级它。

登录 Odoo
---------

在浏览器中打开 http://localhost:8069/。我们推荐使用 `Chrome <https://www.google.com/intl/en/chrome/>`_，`Firefox <https://www.mozilla.org/firefox/new/>`_，或任何其他带有开发工具的浏览器。

作为管理员用户登录，使用以下凭据：

- 电子邮件：`admin`
- 密码：`admin`

启用开发者模式
=================

开发者模式或调试模式对于培训非常有用，因为它提供了额外的（高级）工具。现在 :ref:`启用开发者模式 <developer-mode>`。选择您喜欢的方法，它们都是等效的。

其他工具
========

有用的 Git 命令
----------------

以下是一些日常工作中有用的 Git 命令。

- | 切换分支：
  | 切换分支时，两个存储库（odoo 和 enterprise）必须同步，即它们都需要处于同一分支。

  .. code-block:: console

     $ cd $HOME/src/odoo
     $ git switch {BRANCH}

     $ cd $HOME/src/enterprise
     $ git switch {BRANCH}

- 获取和变基：

  .. code-block:: console

     $ cd $HOME/src/odoo
     $ git fetch --all --prune
     $ git rebase --autostash odoo/{BRANCH}

     $ cd $HOME/src/enterprise
     $ git fetch --all --prune
     $ git rebase --autostash enterprise/{BRANCH}

代码编辑器
-----------

如果您在 Odoo 工作，许多同事使用 `VSCode <https://code.visualstudio.com>`_，`VSCodium <https://vscodium.com>`_（开源等效版），`PyCharm <https://www.jetbrains.com/pycharm/download/#section=linux>`_，或 `Sublime Text <https://www.sublimetext.com>`_。但是，您可以自由选择自己喜欢的编辑器。

正确配置您的代码格式检查工具非常重要。使用 linter 可以通过显示语法和语义警告或错误来帮助您。Odoo 源代码尝试遵守 Python 和 JavaScript 的标准，但某些标准可以忽略。

对于 Python，我们使用 PEP8，并忽略以下选项：

- `E501`: 行太长
- `E301`: 期望有 1 个空行，找到了 0 个
- `E302`: 期望有 2 个空行，找到了 1 个

对于 JavaScript，我们使用 ESLint，您可以在 `此处找到配置文件示例 <https://github.com/odoo/odoo/wiki/Javascript-coding-guidelines#use-a-linter>`_。

PostgreSQL 管理员工具
----------------------

您可以使用命令行管理 PostgreSQL 数据库，也可以使用 GUI 应用程序，如 `pgAdmin <https://www.pgadmin.org/download/pgadmin-4-apt/>`_ 或 `DBeaver <https://dbeaver.io/>`_。

建议您使用 Unix 套接字连接 GUI 应用程序到数据库。

- 主机名/地址：`/var/run/postgresql`
- 端口：`5432`
- 用户名：`$USER`

Python 调试
-----------

在遇到 bug 或尝试了解代码工作原理时，简单地输出内容可以起到很大作用，但使用调试器可以节省大量时间。

您可以使用经典的 Python 调试库（`pdb <https://docs.python.org/3/library/pdb.html>`_，`pudb <https://pypi.org/project/pudb/>`_ 或 `ipdb <https://pypi.org/project/ipdb/>`_），或者使用编辑器的调试工具。

在以下示例中，我们使用 ipdb，但使用其他库的过程类似。

#. 安装库：

   .. code-block:: console

      pip install ipdb

#. 设置触发器（断点）：

   .. code-block:: python

      import ipdb; ipdb.set_trace()

   .. example::

      .. code-block:: python
         :emphasize-lines: 2

         def copy(self, default=None):
             import ipdb; ipdb.set_trace()
             self.ensure_one()
             chosen_name = default.get('name') if default else ''
             new_name = chosen_name or _('%s (copy)') % self.name
             default = dict(default or {}, name=new_name)
             return super(Partner, self).copy(default)

以下是命令列表：

.. option:: h(elp) [command]

   如果未提供参数，则打印可用命令列表。如果提供了命令作为参数，则打印有关该命令的帮助信息。

.. option:: pp expression

   使用 `pprint` 模块对 `expression` 的值进行漂亮打印。

.. option:: w(here)

   打印堆栈跟踪，最近的帧在底部。

.. option:: d(own)

   将当前帧向下移动一层（到较新的帧）。

.. option:: u(p)

   将当前帧向上移动一层（到较旧的帧）。

.. option:: n(ext)

   继续执行，直到到达当前函数中的下一行或返回。

.. option:: c(ontinue)

   继续执行，只有在遇到断点时才会停止。

.. option:: s(tep)

   执行当前行。在首次可能的情况下停止（无论是在调用的函数中，还是在当前函数中的下一行）。

.. option:: q(uit)

   退出调试器。正在执行的程序将被中止。
