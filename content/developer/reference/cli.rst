.. _reference/cmdline:

============================
命令行界面 (CLI)
============================

CLI :dfn:`命令行界面` 提供与 Odoo 相关的多种功能。您可以使用它来 :ref:`运行服务器 <reference/cmdline/server>`、:ref:`以 Python 控制台环境启动 Odoo <reference/cmdline/shell>`、:ref:`搭建 Odoo 模块骨架 <reference/cmdline/scaffold>`、:ref:`填充数据库 <reference/cmdline/populate>`，或 :ref:`统计代码行数 <reference/cmdline/cloc>`。

.. important::
   调用 CLI 的命令取决于您如何安装 Odoo。在下面的示例中，我们假设您是 :doc:`从源代码运行 Odoo </administration/on_premise/source>`，使用 :file:`odoo-bin` 文件。如果您通过 :doc:`发行包安装 Odoo </administration/on_premise/packages>` 或使用 `Docker <https://hub.docker.com/_/odoo/>`，则必须调整命令。

   .. tabs::

      .. tab:: 从源代码运行 Odoo

         #. 导航到您下载 Odoo Community 源文件的目录根目录。
         #. 使用 :command:`./odoo-bin` 运行所有 CLI 命令。

      .. tab:: Odoo 从发行包安装

         当安装 Odoo 时，会将名为 `odoo` 的可执行文件添加到您的用户 PATH 中。在下面的示例中，将所有 :command:`odoo-bin` 替换为 :command:`odoo`。

      .. tab:: Odoo 使用 Docker 安装

         请参阅 `Odoo 官方 Docker 镜像的文档 <https://hub.docker.com/_/odoo/>`_。

.. _reference/cmdline/help:

帮助与版本
==============

.. program:: odoo-bin

.. option:: -h, --help

    显示所有可用选项的帮助文本

.. option:: --version

   显示 Odoo 版本，例如 "Odoo Server {BRANCH}"

.. tip:: 您可以通过运行以下命令在 shell 中启用自动补全

  .. code-block:: bash

    echo "complete -W '`./odoo-bin --help | \
      sed -e 's/[^a-z_-]\(-\+[a-z0-9_-]\+\)/\n\1\n/' | \
      grep -- '^-' | sort | uniq | tr '\n' ' '`' odoo-bin" >> ~/.bash_completion

.. _reference/cmdline/server:

运行服务器
==================

.. program:: odoo-bin

.. option:: -d <database>, --database <database>

    安装或更新模块时使用的数据库。
    提供以逗号分隔的列表可限制访问列表中的数据库。

    有关高级数据库选项，请查看 :ref:`下面 <reference/cmdline/server/database>`。

.. option:: -i <modules>, --init <modules>

    以逗号分隔的模块列表，服务器启动前安装这些模块
    （需要 :option:`-d`）。

.. option:: -u <modules>, --update <modules>

    以逗号分隔的模块列表，在服务器启动前更新这些模块。
    使用 ``all`` 表示所有模块。（需要 :option:`-d`）。

.. option:: --addons-path <directories>

    以逗号分隔的目录列表，其中存储模块。这些
    目录将被扫描以查找模块。

    .. (nb: 何时以及为何使用？)

.. option:: --upgrade-path <upgrade_path>

   指定一个额外的升级路径。

.. option:: --load <modules>

   需加载的服务器范围内模块列表。这些模块应提供
   不一定与特定数据库相关的功能。这与
   在安装时总是绑定到特定数据库的模块相对（即大多数 Odoo 附加组件）。默认是 ``base,web``.

.. option:: -c <config>, --config <config>

    指向备用 :ref:`配置文件 <reference/cmdline/config>` 的路径。
    如果未定义，Odoo 会检查 ``ODOO_RC`` 环境变量
    和默认位置 :file:`{$HOME}/.odoorc`。
    请参阅 :ref:`下面 <reference/cmdline/config>` 的配置文件部分。

.. option:: -D <data-dir-path>, --data-dir <data-dir-path>

   存储 Odoo 数据的目录路径（例如，文件存储，会话）。
   如果未指定，Odoo 将回退到预定义路径。在 Unix 系统上，
   该路径在 ``$XDG_DATA_HOME`` 环境变量中定义
   或者 :file:`~/.local/share/Odoo` 或 :file:`/var/lib/Odoo`。

.. option:: -s, --save

    将服务器配置保存到当前配置文件
    （默认是 :file:`{$HOME}/.odoorc`，可以通过
    :option:`-c` 重写）。

.. option:: --without-demo

    禁用为安装的模块加载演示数据
    以逗号分隔，使用 ``all`` 表示所有模块。
    需要 :option:`-d` 和 :option:`-i`。

.. option:: --pidfile=<pidfile>

    指向一个文件的路径，服务器 PID 将存储在该文件中。

.. option:: --stop-after-init

    服务器初始化后停止。

.. option:: --geoip-city-db <path>

   GeoIP 城市数据库文件的绝对路径。

.. option:: --geoip-country-db <path>

   GeoIP 国家数据库文件的绝对路径。


.. _reference/cmdline/testing:

测试配置
=====================

.. option:: --test-enable

    安装模块后运行测试

.. option:: --test-file <file>

    运行一个 Python 测试文件

.. option:: --test-tags [-][tag][/module][:class][.method]

    以逗号分隔的规范列表，用于过滤要执行的测试。如果设置，将启用单元测试。

    示例： `--test-tags :TestClass.test_func,/test_module,external`

    * `-` 指定是否要包括或排除匹配该规范的测试。
    * 标签将匹配在类上添加的标签，使用 :func:`~odoo.tests.common.tagged` 装饰器
      （所有 :ref:`测试类 <reference/testing>` 具有 `standard` 和 `at_install` 标签，直到明确删除，参见装饰器文档）。
    * `*` 将匹配所有标签。
    * 如果在包含模式中省略标签，其值为 `standard`。
    * 如果在排除模式中省略标签，其值为 `*`。
    * 模块、类和方法将分别匹配模块名称、测试类名称和测试方法名称。

    过滤和执行测试发生两次：在每个模块安装/更新后和模块加载结束时。在每个阶段，测试通过 `--test-tags` 规范过滤，并且额外按动态规范 `at_install` 和 `post_install` 进行过滤。

.. option:: --screenshots

    指定目录，以便在 HttpCase.browser_js 测试失败时写入截图。默认值为 :file:`/tmp/odoo_tests/{db_name}/screenshots`

.. option:: --screencasts

    启用录屏并指定目录以写入录屏文件。
    需要安装 `ffmpeg` 工具来将帧编码为视频文件。否则，帧将被保留而不是视频文件。

.. _reference/cmdline/server/database:

数据库
--------

.. option:: -r <user>, --db_user <user>

    数据库用户名，用于连接 PostgreSQL。

.. option:: -w <password>, --db_password <password>

    数据库密码，如果使用 `密码认证`_。

.. option:: --db_host <hostname>

    数据库服务器的主机

    * 在 Windows 上为 ``localhost``
    * 否则为 UNIX 套接字

.. option:: --db_port <port>

    数据库监听的端口，默认为 5432

.. option:: --db-filter <filter>

    隐藏不匹配 ``<filter>`` 的数据库。过滤器是一个
    `正则表达式`_，附加条件如下：

    - ``%h`` 被请求的主机名替换。
    - ``%d`` 被请求的子域名替换，除了 ``www`` （因此域 ``odoo.com`` 和 ``www.odoo.com`` 都匹配数据库 ``odoo``）。

      这些操作是区分大小写的。添加选项 ``(?i)`` 以匹配所有
      数据库（例如，域 ``odoo.com`` 使用 ``(?i)%d`` 匹配数据库
      ``Odoo``）。

    自版本 11 起，也可以通过使用 --database 参数和指定以逗号分隔的数据库列表来限制对给定数据库的访问。

    当组合两个参数时，db-filter 优先于逗号分隔的数据库列表来限制数据库列表，而逗号分隔的列表则用于执行请求的操作，例如模块升级。

    .. code-block:: bash

        $ odoo-bin --db-filter

 ^11.*$

    限制访问数据库名以 11 开头的数据库。

    .. code-block:: bash

        $ odoo-bin --database 11firstdatabase,11seconddatabase

    限制访问仅两个数据库，11firstdatabase 和 11seconddatabase。

    .. code-block:: bash

        $ odoo-bin --database 11firstdatabase,11seconddatabase -u base

    限制访问仅两个数据库，11firstdatabase 和 11seconddatabase，
    并在一个数据库上更新 base 模块：11firstdatabase。
    如果数据库 11seconddatabase 不存在，将创建该数据库并安装 base 模块。

    .. code-block:: bash

        $ odoo-bin --db-filter ^11.*$ --database 11firstdatabase,11seconddatabase -u base

    限制访问数据库名以 11 开头的数据库，
    并在一个数据库上更新 base 模块：11firstdatabase。
    如果数据库 11seconddatabase 不存在，将创建该数据库并安装 base 模块。

.. option:: --db-template <template>

    从数据库管理屏幕创建新数据库时，使用指定的 `模板数据库`_。默认值为 ``template0``.

.. option:: --pg_path </path/to/postgresql/binaries>

    数据库管理器用于转储和还原数据库的 PostgreSQL 二进制文件的路径。仅当这些
    二进制文件位于非标准目录中时，您需要指定此选项。

.. option:: --no-database-list

    阻止列出系统上可用的数据库

.. option:: --db_sslmode

    控制 Odoo 和 PostgreSQL 之间连接的 SSL 安全性。
    值应该是 'disable'、'allow'、'prefer'、'require'、'verify-ca' 或 'verify-full'
    默认值为 'prefer'

.. option:: --unaccent

   尝试在创建新数据库时启用 unaccent 扩展。

.. _reference/cmdline/server/emails:

电子邮件
------

.. option:: --email-from <address>

    当 Odoo 需要发送邮件时使用的 <FROM> 邮件地址。

.. option:: --from-filter <address or domain>

    定义 SMTP 配置将应用于的电子邮件地址。该字段可以是域名
    或整个电子邮件地址，或者可以保持为空。如果发件人的电子邮件地址不匹配此设置的过滤器，则邮件将通过组合两个系统参数封装：``mail.default.from`` 和 ``mail.catchall.domain``。例如，"Admin" <admin\@example.com> => "Admin" <notifications\@mycompany.com>。

.. option:: --smtp <server>

    连接到以便发送邮件的 SMTP 服务器地址。

.. option:: --smtp-port <port>

.. option:: --smtp-ssl

    如果设置，odoo 应该使用 SSL/STARTSSL SMTP 连接。

.. option:: --smtp-user <name>

    连接到 SMTP 服务器的用户名。

.. option:: --smtp-password <password>

    连接到 SMTP 服务器的密码。

.. option:: --smtp-ssl-certificate-filename <path/to/cert.pem>

    用于身份验证的 SSL 证书。如果设置，则需要 `smtp-ssl-private-key`。

.. option:: --smtp-ssl-private-key-filename <path/to/key.pem>

    用于身份验证的 SSL 私钥。如果设置，则需要 `smtp-ssl-certificate`。

.. _reference/cmdline/server/internationalisation:

国际化
--------------------

使用这些选项将 Odoo 翻译成另一种语言。有关 i18n 的说明，请参见用户手册。选项 '-d' 是强制性的。如果导入时，选项 '-l' 也是强制性的。

.. option:: --load-language <languages>

    指定要加载的翻译语言（用逗号分隔）

.. option:: -l, --language <language>

    指定翻译文件的语言。与 --i18n-export 或 --i18n-import 一起使用。

.. option:: --i18n-export <filename>

    将所有句子导出到 CSV 文件、PO 文件或 TGZ 压缩文件并退出。

.. option:: --i18n-import <filename>

    导入带翻译的 CSV 或 PO 文件并退出。需要 '-l' 选项。

.. option:: --i18n-overwrite

    在更新模块或导入 CSV 或 PO 文件时覆盖现有翻译条目。

.. option:: --modules

    指定要导出的模块。与 --i18n-export 一起使用。

.. _reference/cmdline/advanced:

高级选项
----------------

.. _reference/cmdline/dev:

开发者功能
~~~~~~~~~~~~~~~~~~

.. option:: --dev <feature,feature,...,feature>

    以逗号分隔的功能列表。仅供开发用途。请勿在生产中使用。
    可能的功能有：

    * ``all``：激活下面的所有功能

    * ``xml``：直接从 xml 文件读取 QWeb 模板，而不是从数据库中读取。
      一旦模板在数据库中被修改，它将不会从
      xml 文件中读取，直到下次更新/初始化。特别是，使用此选项时，
      模板不会被翻译。

    * ``reload``：在更新 Python 文件时重启服务器（可能无法检测到，具体取决于所使用的文本编辑器）。

    * ``qweb``：在 QWeb 模板的评估过程中断，当节点包含 ``t-debug='debugger'`` 时。

    * ``(i)p(u)db``：在引发意外错误之前，启动选定的 Python 调试器。

    * ``werkzeug``：在出现异常时，在前端页面上显示完整的回溯信息。


.. _reference/cmdline/server/http:

HTTP
~~~~

.. option:: --no-http

    不启动 HTTP 或长轮询工作进程（可能仍然启动 :ref:`cron <reference/actions/cron>` 工作进程）。

    .. warning:: 如果设置了 :option:`--test-enable`，则没有效果，因为测试
                 需要可访问的 HTTP 服务器。

.. option:: --http-interface <interface>

    HTTP 服务器监听的 TCP/IP 地址，默认为 ``0.0.0.0``
    （所有地址）。

.. option:: -p <port>
.. option:: --http-port <port>

    HTTP 服务器监听的端口，默认为 8069。

.. option:: --gevent-port <port>

    在多进程或 gevent 模式下，用于 websocket 连接的 TCP 端口，
    默认为 8072。默认（线程）模式下不使用。

.. option:: --proxy-mode

    启用通过 `Werkzeug 的代理支持`_ 使用 ``X-Forwarded-*`` 头。

    如果请求中缺少 ``X-Forwarded-Host``，则会忽略所有 ``X-Forwarded-*`` 头。

    它始终从 ``X-Forwarded-For`` 链的最后一条记录获取真实 IP。根据需要配置您的 Web 服务器，使用诸如
    nginx 的 `set_real_ip_from <https://nginx.org/en/docs/http/ngx_http_realip_module.html>`_
    指令，如果需要忽略链中的其他受信任代理。

    ``X-Forwarded-Proto`` 和 ``X-Forwarded-Host`` 用于更新请求根 URL，从而用于在成功的管理员身份验证后更新
    ``web.base.url`` 系统参数。此系统参数用于生成当前数据库的所有链接；参见 :ref:`domain-name/web-base-url`。

    .. warning:: 代理模式 *不得* 在反向代理场景之外启用。

.. option:: --x-sendfile

    将附件文件的服务委托给静态 Web 服务器，并在流响应中设置
    ``X-Sendfile``（apache）和 ``X-Accel-*``（nginx）http 头。有关 Web 服务器配置，请参见 :ref:`deploy/streaming`。

.. _reference/cmdline/server/logging:

日志记录
~~~~~~~

默认情况下，Odoo 显示所有级别为 ``INFO``、``WARNING`` 和 ``ERROR`` 的日志。所有日志
无论级别如何都输出到 ``stderr``。有多种选项可用于将日志重定向到其他目的地并自定义详细程度。

.. option:: --logfile <file>

    将日志输出发送到指定的文件，而不是 ``stderr``。在 Unix 上，该
    文件可以由外部日志轮换程序管理
    <https://docs.python.org/3/library/logging.handlers.html#watchedfilehandler>_
    并将在替换时自动重新打开。

.. option:: --syslog

    将日志记录到系统事件记录器： `UNIX 上的 syslog <https://docs.python.org/3/library/logging.handlers.html#sysloghandler>`_
    和 `Windows 上的事件日志 <https://docs.python.org/3/library/logging.handlers.html#nteventloghandler>`_。

    这两者都不可配置。

.. option:: --log-db <dbname>

    将日志记录到指定数据库的 ``ir.logging`` 模型（``ir_logging`` 表）。数据库可以是 "当前"
    PostgreSQL 中的数据库

名称，或 `PostgreSQL URI`_ 以便进行日志聚合。

.. option:: --log-handler <handler-spec>

    :samp:`{LOGGER}:{LEVEL}`，在提供的 ``LEVEL`` 下启用 ``LOGGER``
    例如 ``odoo.models:DEBUG`` 将启用模型中所有级别为或高于 ``DEBUG`` 的所有日志消息。

    * 冒号 ``:`` 是强制性的。
    * 可以省略记录器以配置根（默认）处理程序。
    * 如果省略级别，记录器将设置为 ``INFO``。

    此选项可以重复以配置多个记录器，例如：

    .. code-block:: console

        $ odoo-bin --log-handler :DEBUG --log-handler werkzeug:CRITICAL --log-handler odoo.fields:WARNING

.. option:: --log-web

    启用 HTTP 请求和响应的 DEBUG 日志记录，相当于
    ``--log-handler=odoo.http:DEBUG``。

.. option:: --log-sql

    启用 SQL 查询的 DEBUG 日志记录，相当于
    ``--log-handler=odoo.sql_db:DEBUG``。

.. option:: --log-level <level>

    快捷方式，用于更轻松地在特定记录器上设置预定义级别。 "真实" 级别（``critical``、``error``、``warn``、``debug``）设置在 ``odoo`` 和 ``werkzeug`` 记录器上（除了 ``debug``，仅在 ``odoo`` 上设置）。

    Odoo 还提供调试伪级别，适用于不同的记录器集合：

    ``debug_sql``
        将 SQL 记录器设置为 ``debug``。

        相当于 ``--log-sql``。
    ``debug_rpc``
        将 ``odoo`` 和 HTTP 请求记录器设置为 ``debug``。

        相当于 ``--log-level debug --log-request``。
    ``debug_rpc_answer``
        将 ``odoo`` 和 HTTP 请求及响应记录器设置为 ``debug``。

        相当于 ``--log-level debug --log-request --log-response``。

    .. note::

        如果 :option:`--log-level` 和
        :option:`--log-handler` 之间存在冲突，则使用后者。

.. _reference/cdmline/workers:

多进程
~~~~~~~~~~~~~~~

.. option:: --workers <count>

    如果 ``count`` 不为 0（默认为 0），则启用多进程并设置
    指定数量的 HTTP 工作进程（子进程处理 HTTP
    和 RPC 请求）。

    .. note:: 多进程模式仅在基于 Unix 的系统上可用。

    有几个选项可以限制和回收工作进程：

    .. option:: --limit-request <limit>

        工作进程在被回收和重启之前将处理的请求数量。

        默认值为 *8196*。

    .. option:: --limit-memory-soft <limit>

        每个工作进程允许的最大虚拟内存（以字节为单位）。如果超过该限制，
        工作进程将在当前请求结束时被杀死并回收。

        默认值为 *2048MiB (2048\*1024\*1024B)*。

    .. option:: --limit-memory-hard <limit>

        虚拟内存的硬限制（以字节为单位），任何超出该限制的工作进程将立即被杀死，而不等待当前请求处理的结束。

        默认值为 *2560MiB (2560\*1024\*1024B)*。

    .. option:: --limit-time-cpu <limit>

        防止工作进程在每个请求中使用超过 <limit> 的 CPU 秒。如果超过该限制，工作进程将被杀死。

        默认值为 *60*。

    .. option:: --limit-time-real <limit>

        防止工作进程处理请求所需的时间超过 <limit> 秒。如果超过该限制，工作进程将被杀死。

        与 :option:`--limit-time-cpu` 不同，这是一项“实际时间”限制，包括例如 SQL 查询。

        默认值为 *120*。

.. option:: --max-cron-threads <count>

    专用于 :ref:`cron <reference/actions/cron>` 作业的工作进程数量。默认值为 *2*。
    在多线程模式下，工作进程是线程；在多进程模式下，工作进程是进程。

    对于多进程模式，这是在 HTTP 工作进程之外的。

.. _reference/cmdline/config:

配置文件
==================

.. program:: odoo-bin

大多数命令行选项也可以通过配置文件指定。大多数情况下，它们使用类似名称，去掉前缀 ``-``，并将其他 ``-`` 替换为 ``_``，例如 :option:`--db-template` 变为 ``db_template``。

某些转换不符合该模式：

* :option:`--db-filter` 变为 ``dbfilter``。
* :option:`--no-http` 对应于 ``http_enable`` 布尔值。
* 日志预设（所有以 ``--log-`` 开头的选项，除了 :option:`--log-handler` 和 :option:`--log-db`）仅将内容添加到 ``log_handler``，直接在配置文件中使用它。
* :option:`--smtp` 存储为 ``smtp_server``。
* :option:`--database` 存储为 ``db_name``。
* :option:`--i18n-import` 和 :option:`--i18n-export` 在配置文件中根本不可用。

.. _reference/cmdline/config_file:

默认配置文件为 :file:`{$HOME}/.odoorc`，可以使用 :option:`--config <odoo-bin -c>` 重写。指定 :option:`--save <odoo-bin -s>` 将当前配置状态保存回该文件。与命令行相关的配置项应在 ``[options]`` 部分指定。

以下是示例文件：

.. code-block:: ini

   [options]
   db_user=odoo
   dbfilter=odoo

.. _jinja2: https://jinja.palletsprojects.com/
.. _regular expression: https://docs.python.org/3/library/re.html
.. _password authentication:
    https://www.postgresql.org/docs/12/static/auth-methods.html#AUTH-PASSWORD
.. _template database:
    https://www.postgresql.org/docs/12/static/manage-ag-templatedbs.html
.. _level:
    https://docs.python.org/3/library/logging.html#logging.Logger.setLevel
.. _a PostgreSQL URI:
    https://www.postgresql.org/docs/12/static/libpq-connect.html#AEN38208
.. _Werkzeug's proxy support:
    https://werkzeug.palletsprojects.com/en/0.16.x/middleware/proxy_fix/#module-werkzeug.middleware.proxy_fix
.. _pyinotify: https://github.com/seb-m/pyinotify/wiki

.. _reference/cmdline/shell:

Shell
=====

Odoo 命令行还允许将 Odoo 启动为 Python 控制台环境，能够直接与 :ref:`orm <reference/orm>` 及其功能进行交互。

.. code-block:: console

   $ odoo-bin shell

.. example::

   向所有联系人的名称添加感叹号：

   .. code-block:: python

      In [1]: records = env["res.partner"].search([])

      In [2]: records
      Out[2]: res.partner(14, 26, 33, 21, 10)

      In [3]: for partner in records:
         ...:     partner.name = "%s !" % partner.name
         ...:

      In [4]: env.cr.commit()

   .. important::
      默认情况下，shell 以事务模式运行。这意味着对数据库的任何更改在退出 shell 时都会回滚。要提交更改，请使用 `env.cr.commit()`。

.. option:: --shell-interface (ipython|ptpython|bpython|python)

   指定在 shell 模式下使用的首选 REPL。此 shell 已使用 `env` 变量初始化，以便能够访问 ORM 和其他 Odoo 模块。

.. seealso::
   :ref:`reference/orm/environment`

.. _reference/cmdline/scaffold:

搭建骨架
===========

.. program:: odoo-bin scaffold

搭建骨架是自动创建一个骨架结构，以简化（在 Odoo 的情况下是新模块的）引导过程。虽然不是必需的，但它避免了设置基本结构和查找所有启动要求的单调。

通过 :command:`odoo-bin scaffold` 子命令可以实现搭建骨架。

.. code-block:: console

    $ odoo-bin scaffold my_module /addons/

.. option:: name (required)

    要创建的模块的名称，可以以各种方式修改以生成程序化名称（例如，模块目录名称、模型名称等）。

.. option:: destination (default=current directory)

    创建新模块的目录，默认为当前目录。

.. option:: -t <template>

    一个模板目录，文件经过 jinja2_ 处理后复制到 ``destination`` 目录。

这将在目录 */addons/* 中创建模块 *my_module*。

.. _reference/cmdline/populate:

数据库填充
===================

.. program:: o

doo-bin populate

Odoo CLI 支持数据库填充功能。如果该功能在给定模型上 :ref:`实现 <reference/performance/populate/methods>`，它允许自动生成模型记录的数据，以在包含大量记录的数据库中测试您的模块。

.. code-block:: console

    $ odoo-bin populate

.. option:: --models

    数据库应填充的模型列表。

.. option:: --size (small|medium|large)

    填充大小，实际记录数量取决于模型的 `_populate_sizes` 属性。
    生成的记录内容由给定模型的 :meth:`~odoo.models._populate_factories` 方法指定（有关详细信息，请参见模块的 :file:`populate` 文件夹）。

.. seealso::
   :ref:`reference/performance/populate`

.. _reference/cmdline/cloc:

Cloc
====

.. program:: odoo-bin cloc

Odoo Cloc 是一个工具，用于计算 Python、JavaScript、CSS、SCSS 或 XML 中编写的相关行数。这可以作为定价额外模块维护的粗略指标。

命令行选项
--------------------
.. option:: -d <database>, --database <database>

| 处理提供的数据库中安装的所有额外模块的代码，以及在提供的数据库中手动创建的所有服务器操作和计算字段。
| 需要 :option:`--addons-path` 选项来指定模块文件夹的路径。
| 如果与 :option:`--path` 结合使用，则计数将是这两个选项结果的总和（可能重叠）。至少需要其中一个选项来指定要处理的代码。

.. code-block:: console

   $ odoo-bin cloc --addons-path=addons -d my_database

.. seealso::
   - :ref:`reference/cmdline/cloc/database-option`


.. option:: -p <path>, --path <path>

| 处理提供路径中的文件。
| 如果与 :option:`--database` 结合使用，则计数将是这两个选项结果的总和（可能重叠）。至少需要其中一个选项来指定要处理的代码。

.. code-block:: console

   $ odoo-bin cloc -p addons/account


可以通过重复该选项提供多个路径。

.. code-block:: console

   $ odoo-bin cloc -p addons/account -p addons/sale

.. seealso::
   - :ref:`reference/cmdline/cloc/path-option`


.. option:: --addons-path <directories>

| 以逗号分隔的目录列表，其中存储模块。这些目录
| 将被扫描以查找模块。
| 如果使用 :option:`--database` 选项，则必需。

.. option:: -c <directories>

指定一个配置文件，以替代 :option:`--addons-path` 选项。

.. code-block:: console

    $ odoo-bin cloc -c config.conf -d my_database


.. option:: -v, --verbose

显示每个文件计数的详细信息。


处理的文件
---------------

.. _reference/cmdline/cloc/database-option:

使用 :option:`--database` 选项
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Odoo Cloc 计算给定数据库中每个额外安装模块的行数。此外，它还计算直接在数据库中创建的服务器操作和自定义计算字段的 Python 行数，或导入的行数。最后，它计算 JavaScript、CSS 和 SCSS 文件的代码行数，以及导入模块的 QWeb 视图。

默认情况下，某些文件会被排除在计数之外：

- 清单文件 (:file:`__manifest__.py` 或 :file:`__openerp__.py`)
- 文件夹 :file:`static/lib` 的内容
- 在文件夹 :file:`tests` 和 :file:`static/tests` 中定义的测试
- 在文件夹 :file:`migrations` 和 `upgrades` 中定义的迁移脚本
- 在清单的 ``demo`` 或 ``demo_xml`` 部分中声明的 XML 文件

对于特殊情况，可以为每个模块定义应由 Odoo Cloc 忽略的文件列表。这通过清单的 ``cloc_exclude`` 条目指定：

.. code-block:: python

    "cloc_exclude": [
        "lib/common.py", # 排除单个文件
        "data/*.xml",    # 排除特定文件夹中的所有 XML 文件
        "example/**/*",  # 递归排除文件夹层次中的所有文件
        "**/*.scss",     # 排除模块中的所有 scss 文件
    ]

| 模式 ``**/*`` 可用于忽略整个模块。这在排除维护服务成本的模块时非常有用。
| 有关模式语法的更多信息，请参见 `glob
  <https://docs.python.org/3/library/pathlib.html#pathlib.Path.glob>`_。

.. _reference/cmdline/cloc/path-option:

使用 :option:`--path` 选项
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

如果给定文件夹中存在清单文件，则该方法的工作方式与 :ref:`--database 选项 <reference/cmdline/cloc/database-option>` 相同。否则，它将计算所有文件。


识别额外模块
-------------------------

为了区分标准模块和额外模块，Odoo Cloc 使用以下启发式方法：
位于（真实文件系统路径，跟随符号链接后）与 ``base``、``web`` 或 ``web_enterprise`` 标准模块位于同一父目录中的模块被视为标准模块。其他模块被视为额外模块。


错误处理
--------------

某些文件无法被 Odoo Cloc 计数。
这些文件在输出的末尾报告。

最大文件大小超出限制
~~~~~~~~~~~~~~~~~~~~~~

Odoo Cloc 拒绝任何大于 25MB 的文件。通常，源文件小于 1 MB。如果文件被拒绝，可能是：

- 生成的 XML 文件包含大量数据。应在清单中排除。
- JavaScript 库应放置在 :file:`static/lib` 文件夹中。

语法错误
~~~~~~~~~~~~

Odoo Cloc 无法计算存在语法问题的 Python 文件的代码行数。
如果额外模块包含此类文件，则应修复以允许模块加载。如果模块在存在这些文件的情况下仍然正常工作，则它们可能不会被加载，因此应将其从模块中删除，或至少通过 ``cloc_exclude`` 在清单中排除。

TSConfig 生成器
==================

.. program:: odoo-bin tsconfig

在处理 JavaScript 时，有一些方法可以帮助您的编辑器提供强大的自动补全。其中一种方法是使用 tsconfig.json 文件。
最初用于 TypeScript，编辑器也可以使用其信息与普通 JavaScript 一起使用。
使用此配置文件，您现在将在模块之间拥有完整的自动补全。

生成此文件的命令可以接收任意数量的未命名参数。这些参数是相对于您的插件目录的路径。在下面的示例中，我们向上移动一个文件夹，以便将 tsconfig 文件保存在包含社区和企业的文件夹中。

.. code-block:: console

   $ community/odoo-bin tsconfig --addons-path community/addons,community/odoo/addons,enterprise > tsconfig.json