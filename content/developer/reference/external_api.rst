============
外部 API
============

Odoo 通常通过模块进行内部扩展，但它的许多功能和所有数据也可供外部分析或与各种工具集成。部分 :ref:`reference/orm/model` API 可以通过 XML-RPC_ 轻松访问，并可通过多种语言进行访问。

.. important::
   从 PHP8 开始，XML-RPC 扩展可能默认不可用。请查看 `手册 <https://www.php.net/manual/en/xmlrpc.installation.php>`_ 了解安装步骤。

.. note::
   通过外部 API 访问数据仅在 *Custom* Odoo 定价计划上可用。在 *One App Free* 或 *Standard* 计划中无法访问外部 API。有关更多信息，请访问 `Odoo 定价页面 <https://www.odoo.com/pricing-plan>`_ 或联系您的客户成功经理。

.. seealso::
   - :doc:`网络服务教程 <../howtos/web_services>`

连接
==========

配置
-------------

如果您已经安装了 Odoo 服务器，可以直接使用其参数。

.. important::

    对于 Odoo 在线实例（<domain>.odoo.com），用户是在没有 *local* 密码的情况下创建的（作为用户，您是通过 Odoo 在线身份验证系统登录，而不是通过实例本身）。要在 Odoo 在线实例上使用 XML-RPC，您需要为要使用的用户帐户设置密码：

    * 使用管理员帐户登录到您的实例。
    * 转到 :menuselection:`设置 --> 用户与公司 --> 用户`。
    * 点击您希望用于 XML-RPC 访问的用户。
    * 点击 :guilabel:`操作` 并选择 :guilabel:`更改密码`。
    * 设置 :guilabel:`新密码` 值，然后点击 :guilabel:`更改密码`。

    *server url* 是实例的域名（例如 *https://mycompany.odoo.com*），*database name* 是实例的名称（例如 *mycompany*）。*username* 是配置用户的登录名，如 *更改密码* 屏幕所示。

.. tabs::

   .. code-tab:: python

       url = <插入服务器 URL>
       db = <插入数据库名称>
       username = 'admin'
       password = <插入管理员用户的密码（默认：admin）>

   .. code-tab:: ruby

       url = <插入服务器 URL>
       db = <插入数据库名称>
       username = "admin"
       password = <插入管理员用户的密码（默认：admin）>

   .. code-tab:: php

       $url = <插入服务器 URL>;
       $db = <插入数据库名称>;
       $username = "admin";
       $password = <插入管理员用户的密码（默认：admin)>;

   .. code-tab:: java

       final String url = <插入服务器 URL>,
                     db = <插入数据库名称>,
               username = "admin",
               password = <插入管理员用户的密码（默认：admin）>;

   .. code-tab:: go

       var (
           url = <插入服务器 URL>
           db = <插入数据库名称>
           username = "admin"
           password = <插入管理员用户的密码（默认：admin）>
       )

.. _api/external_api/keys:

API 密钥
~~~~~~~~

.. versionadded:: 14.0

Odoo 支持 **api keys**，并且（根据模块或设置）可能 **需要** 这些密钥来执行 Web 服务操作。

在脚本中使用 API 密钥的方式是简单地用密钥替换 **password**。登录仍然保持使用。您应像密码一样小心存储 API 密钥，因为它们本质上提供对您的用户帐户的相同访问权限（尽管不能通过界面登录）。

要向您的帐户添加密钥，只需转到您的 :guilabel:`偏好设置`（或 :guilabel:`我的资料`）：

.. image:: external_api/preferences.png
   :align: center

然后打开 :guilabel:`帐户安全` 标签，并点击 :guilabel:`新 API 密钥`：

.. image:: external_api/account-security.png
   :align: center

输入密钥的描述，**此描述应尽可能清晰和完整**：这是您以后识别密钥的唯一方式，并知道是否应该删除它们或保留它们。

点击 :guilabel:`生成密钥`，然后复制提供的密钥。**小心存储此密钥**：它相当于您的密码，和您的密码一样，系统将无法在稍后检索或显示密钥。如果您丢失此密钥，您将必须创建一个新的密钥（并可能删除您丢失的那个）。

配置后，密钥将显示在 :guilabel:`新 API 密钥` 按钮上方，您将能够删除它们：

.. image:: external_api/delete-key.png
   :align: center

**已删除的 API 密钥无法恢复或重置**。您将不得不生成新的密钥，并更新您使用旧密钥的所有地方。

测试数据库
~~~~~~~~~~~~~

为了简化探索，您还可以请求 https://demo.odoo.com 获取测试数据库：

.. tabs::

   .. code-tab:: python

      import xmlrpc.client
      info = xmlrpc.client.ServerProxy('https://demo.odoo.com/start').start()
      url, db, username, password = info['host'], info['database'], info['user'], info['password']

   .. code-tab:: ruby

      require "xmlrpc/client"
      info = XMLRPC::Client.new2('https://demo.odoo.com/start').call('start')
      url, db, username, password = info['host'], info['database'], info['user'], info['password']

   .. group-tab:: PHP

      .. code-block:: php

         require_once('ripcord.php');
         $info = ripcord::client('https://demo.odoo.com/start')->start();
         list($url, $db, $username, $password) = array($info['host'], $info['database'], $info['user'], $info['password']);

      .. note::
         这些示例使用 `Ripcord <https://code.google.com/p/ripcord/>`_ 库，该库提供简单的 XML-RPC API。Ripcord 要求在您的 PHP 安装中启用 `XML-RPC 支持 <https://php.net/manual/en/xmlrpc.installation.php>`_。

         由于调用是通过 `HTTPS <https://en.wikipedia.org/wiki/HTTP_Secure>`_ 执行的，因此还要求启用 `OpenSSL 扩展 <https://php.net/manual/en/openssl.installation.php>`_。

   .. group-tab:: Java

      .. code-block:: java

         final XmlRpcClient client = new XmlRpcClient();

         final XmlRpcClientConfigImpl start_config = new XmlRpcClientConfigImpl();
         start_config.setServerURL(new URL("https://demo.odoo.com/start"));
         final Map<String, String> info = (Map<String, String>)client.execute(
             start_config, "start", emptyList());

         final String url = info.get("host"),
                       db = info.get("database"),
                 username = info.get("user"),
                 password = info.get("password");

      .. note::
         这些示例使用 `Apache XML-RPC 库 <https://ws.apache.org/xmlrpc/>`_。

         示例不包括导入，因为这些导入无法粘贴在代码中。

   .. group-tab:: Go

      .. code-block:: go

         client, err := xmlrpc.NewClient("https://demo.odoo.com/start", nil)
         if err != nil {
             log.Fatal(err)
         }
         info := map[string]string{}
         client.Call("start", nil, &info)
         url = info["host"].(string)
         db = info["database"].(string)
         username = info["user"].(string)
         password = info["password"].(string)

      .. note::
         这些示例使用 `github.com/kolo/xmlrpc 库 <https://github.com/kolo/xmlrpc>`_。

         示例不包括导入，因为这些导入无法粘贴在代码中。

登录
----------

Odoo 要求 API 用户在查询大多数数据之前进行身份验证。

``xmlrpc/2/common`` 端点提供无需身份验证的元调用，例如身份验证本身或获取版本信息。要在尝试身份验证之前验证连接信息是否正确，最简单的调用是请求服务器的版本。身份验证本身是通过 ``authenticate`` 函数完成的，并返回用于认证调用的用户标识符（``uid``），而不是登录名。

.. tabs::

   .. code-tab:: python

      common = xmlrpc.client.ServerProxy('{}/xmlrpc/2/common'.format(url))
      common.version()

   .. code-tab:: ruby

      common = XMLRPC::Client.new2("#{url}/xmlrpc/2/common")
      common.call('version')

   .. code-tab:: php

      $common = ripcord::client("$url/xmlrpc/2/common");
      $common->version();

   .. code-tab:: java

      final XmlRpcClientConfigImpl common_config = new XmlRpcClientConfigImpl();
      common_config.setServerURL(new URL(String.format("%s/xmlrpc/2/common", url)));
      client.execute(common_config, "version", emptyList());

   .. code-tab:: go

      client, err := xmlrpc.NewClient(fmt.Sprintf("%s/xmlrpc/2/common", url), nil)
      if err != nil {
          log.Fatal(err)
      }
      common := map[string]any{}
      if err := client.Call("version", nil, &common); err != nil {
          log.Fatal(err)
      }

结果：

.. code-block:: json

   {
       "server_version": "13.0",
       "server_version_info": [13, 0, 0, "final", 0],
       "server_serie": "13.0",
       "protocol_version": 1,
   }


.. tabs::

   .. code-tab:: python

      uid = common.authenticate(db, username, password, {})

   .. code-tab:: ruby

      uid = common.call('authenticate', db, username, password, {})

   .. code-tab:: php

      $uid = $common->authenticate($db, $username, $password, array());

   .. code-tab:: java

      int uid = (int)client.execute(common_config, "authenticate", asList(db, username, password, emptyMap()));

   .. code-tab:: go

      var uid int64
      if err := client.Call("authenticate", []any{
          db, username, password,
          map[string]any{},
      }, &uid); err != nil {
          log.Fatal(err)
      }

.. _api/external_api/calling_methods:

调用方法
===============

第二个端点是 ``xmlrpc/2/object``。它用于通过 ``execute_kw`` RPC 函数调用 Odoo 模型的方法。

每次调用 ``execute_kw`` 都需要以下参数：

* 要使用的数据库，字符串
* 用户 ID（通过 ``authenticate`` 检索），整数
* 用户的密码，字符串
* 模型名称，字符串
* 方法名称，字符串
* 按位置传递的参数数组/列表
* 传递的关键字参数映射/字典（可选）

.. example::

   例如，要查看我们是否可以读取 ``res.partner`` 模型，我们可以调用 ``check_access_rights``，将 ``operation`` 按位置传递，将 ``raise_exception`` 作为关键字传递（以便获得 true/false 结果，而不是 true/error）：

   .. tabs::

      .. code-tab:: python

         models = xmlrpc.client.ServerProxy('{}/xmlrpc/2/object'.format(url))
         models.execute_kw(db, uid, password, 'res.partner', 'check_access_rights', ['read'], {'raise_exception': False})

      .. code-tab:: ruby

         models = XMLRPC::Client.new2("#{url}/xmlrpc/2/object").proxy
         models.execute_kw(db, uid, password, 'res.partner', 'check_access_rights', ['read'], {raise_exception: false})

      .. code-tab:: php

         $models = ripcord::client("$url/xmlrpc/2/object");
         $models->execute_kw($db, $uid, $password, 'res.partner', 'check_access_rights', array('read'), array('raise_exception' => false));

      .. code-tab:: java

         final XmlRpcClient models = new XmlRpcClient() {{
             setConfig(new XmlRpcClientConfigImpl() {{
                 setServerURL(new URL(String.format("%s/xmlrpc/2/object", url)));
             }});
         }};
         models.execute("execute_kw", asList(
             db, uid, password,
             "res.partner", "check_access_rights",
             asList("read"),
             new HashMap() {{ put("raise_exception", false); }}
         ));

      .. code-tab:: go

         models, err := xmlrpc.NewClient(fmt.Sprintf("%s/xmlrpc/2/object", url), nil)
         if err != nil {
             log.Fatal(err)
         }
         var result bool
         if err := models.Call("execute_kw", []any{
             db, uid, password,
             "res.partner", "check_access_rights",
             []string{"read"},
             map[string]bool{"raise_exception": false},
         }, &result); err != nil {
             log.Fatal(err)
         }

   结果：

   .. code-block:: json

      true

列出记录
------------

可以通过 :meth:`~odoo.models.Model.search` 列出和过滤记录。

:meth:`~odoo.models.Model.search` 需要一个强制的
:ref:`domain <reference/orm/domains>` 过滤器（可以为空），并返回与过滤器匹配的所有记录的数据库标识符。

.. example::

   例如，要列出客户公司：

   .. tabs::

      .. code-tab:: python

         models.execute_kw(db, uid, password, 'res.partner', 'search', [[['is_company', '=', True]]])

      .. code-tab:: ruby

         models.execute_kw(db, uid, password, 'res.partner', 'search', [[['is_company', '=', true]]])

      .. code-tab:: php

         $models->execute_kw($db, $uid, $password, 'res.partner', 'search', array(array(array('is_company', '=', true))));

      .. code-tab:: java

         asList((Object[])models.execute("execute_kw", asList(
             db, uid, password,
             "res.partner", "search",
             asList(asList(
                 asList("is_company", "=", true)))
         )));

      .. code-tab:: go

         var records []int64
         if err := models.Call("execute_kw", []any{
             db, uid, password,
             "res.partner", "search",
             []any{[]any{
                 []any{"is_company", "=", true},
             }},
         }, &records); err != nil {
             log.Fatal(err)
         }

   结果：

   .. code-block:: json

      [7, 18, 12, 14, 17, 19, 8, 31, 26, 16, 13, 20, 30, 22, 29, 15, 23, 28, 74]

分页
~~~~~~~~~~

默认情况下，搜索将返回所有匹配条件的记录的 ID，这可能是一个巨大的数字。 ``offset`` 和 ``limit`` 参数可用于仅检索所有匹配记录的子集。

.. example::

   .. tabs::

      .. code-tab:: python

         models.execute_kw(db, uid, password, 'res.partner', 'search', [[['is_company', '=', True]]], {'offset': 10, 'limit': 5})

      .. code-tab:: ruby

         models.execute_kw(db, uid, password, 'res.partner', 'search', [[['is_company', '=', true]]], {offset: 10, limit: 5})

      .. code-tab:: php

         $models->execute_kw($db, $uid, $password, 'res.partner', 'search', array(array(array('is_company', '=', true))), array('offset'=>10, 'limit'=>5));

      .. code-tab:: java

         asList((Object[])models.execute("execute_kw", asList(
             db, uid, password,
             "res.partner", "search",
             asList(asList(
                 asList("is_company", "=", true))),
             new HashMap() {{ put("offset", 10); put("limit", 5); }}
         )));

      .. code-tab:: go

         var records []int64
         if err := models.Call("execute_kw", []any{
             db, uid, password,
             "res.partner", "search",
             []any{[]any{
                 []any{"is_company", "=", true},
             }},
             map[string]int64{"offset": 10, "limit":  5},
         }, &records); err != nil {
             log.Fatal(err)
         }

   结果：

   .. code-block:: json

      [13, 20, 30, 22, 29]

计数记录
-------------

与其检索可能庞大的记录列表并对其进行计数，不如使用 :meth:`~odoo.models.Model.search_count` 来仅检索与查询匹配的记录数量。它接受与 :meth:`~odoo.models.Model.search` 相同的 :ref:`domain <reference/orm/domains>` 过滤器，而不需要其他参数。

.. example::

   .. tabs::

      .. code-tab:: python

         models.execute_kw(db, uid, password, 'res.partner', 'search_count', [[['is_company', '=', True]]])

      .. code-tab:: ruby

         models.execute_kw(db, uid, password, 'res.partner', 'search_count', [[['is_company', '=', true]]])

      .. code-tab:: php

         $models->execute_kw($db, $uid, $password, 'res.partner', 'search_count', array(array(array('is_company', '=', true))));

      .. code-tab:: java

         (Integer)models.execute("execute_kw", asList(
             db, uid, password,
             "res.partner", "search_count",
             asList(asList(
                 asList("is_company", "=", true)))
         ));

      .. code-tab:: go

         var counter int64
         if err := models.Call("execute_kw", []any{
             db, uid, password,
             "res.partner", "search_count",
             []any{[]any{
                 []any{"is_company", "=", true},
             }},
         }, &counter); err != nil {
             log.Fatal(err)
         }

   结果：

   .. code-block:: json

      19

.. note::
   调用 ``search`` 然后 ``search_count``（或反之）可能不会产生一致的结果，如果其他用户正在使用服务器：在调用之间存储的数据可能已更改。

读取记录
------------

记录数据可以通过 :meth:`~odoo.models.Model.read` 方法访问，该方法接受一个 ID 列表（如 :meth:`~odoo.models.Model.search` 返回的），并可选地接受要获取的字段列表。默认情况下，它获取当前用户可以读取的所有字段，这通常是大量字段。

.. example::

   .. tabs::

      .. code-tab:: python

          ids = models.execute_kw(db, uid, password, 'res.partner', 'search', [[['is_company', '=', True]]], {'limit': 1})
          [record] = models.execute_kw(db, uid, password, 'res.partner', 'read', [ids])
          # 计数默认获取的字段数量
          len(record)

      .. code-tab:: ruby

          ids = models.execute_kw(db, uid, password, 'res.partner', 'search', [[['is_company', '=', true]]], {limit: 1})
          record = models.execute_kw(db, uid, password, 'res.partner', 'read', [ids]).first
          # 计数默认获取的字段数量
          record.length

      .. code-tab:: php

          $ids = $models->execute_kw($db, $uid, $password, 'res.partner', 'search', array(array(array('is_company', '=', true))), array('limit'=>1));
          $records = $models->execute_kw($db, $uid, $password, 'res.partner', 'read', array($ids));
          // 计数默认获取的字段数量
          count($records[0]);

      .. code-tab:: java

          final List ids = asList((Object[])models.execute(
              "execute_kw", asList(
                  db, uid, password,
                  "res.partner", "search",
                  asList(asList(
                      asList("is_company", "=", true))),
                  new HashMap() {{ put("limit", 1); }})));
          final Map record = (Map)((Object[])models.execute(
              "execute_kw", asList(
                  db, uid, password,
                  "res.partner", "read",
                  asList(ids)
              )
          ))[0];
          // 计数默认获取的字段数量
          record.size();

      .. code-tab:: go

         var ids []int64
         if err := models.Call("execute_kw", []any{
             db, uid, password,
             "res.partner", "search",
             []any{[]any{
                 []any{"is_company", "=", true},
             }},
             map[string]int64{"limit": 1},
         }, &ids); err != nil {
             log.Fatal(err)
         }
         var records []any
         if err := models.Call("execute_kw", []any{
             db, uid, password,
             "res.partner", "read",
             ids,
         }, &records); err != nil {
             log.Fatal(err)
         }
         // 计数默认获取的字段数量
         count := len(records)

   结果：

   .. code-block:: json

      121

   相反，只选择三个认为有趣的字段。

   .. tabs::

      .. code-tab:: python

         models.execute_kw(db, uid, password, 'res.partner', 'read', [ids], {'fields': ['name', 'country_id', 'comment']})

      .. code-tab:: ruby

         models.execute_kw(db, uid, password, 'res.partner', 'read', [ids], {fields: %w(name country_id comment)})

      .. code-tab:: php

         $models->execute_kw($db, $uid, $password, 'res.partner', 'read', array($ids), array('fields'=>array('name', 'country_id', 'comment')));

      .. code-tab:: java

         asList((Object[])models.execute("execute_kw", asList(
             db, uid, password,
             "res.partner", "read",
             asList(ids),
             new HashMap() {{
                 put("fields", asList("name", "country_id", "comment"));
             }}
         )));

      .. code-tab:: go

         var recordFields []map[string]any
         if err := models.Call("execute_kw", []any{
             db, uid, password,
             "res.partner", "read",
             ids,
             map[string][]string{
                 "fields": {"name", "country_id", "comment"},
             },
         }, &recordFields); err != nil {
             log.Fatal(err)
         }

   结果：

   .. code-block:: json

      [{"comment": false, "country_id": [21, "Belgium"], "id": 7, "name": "Agrolait"}]

.. note::
   即使 ``id`` 字段未请求，它也始终会返回。

列出记录字段
------------------

:meth:`~odoo.models.Model.fields_get` 可用于检查模型的字段并查看哪些字段似乎感兴趣。

因为它返回大量的元信息（也被客户端程序使用），所以应该在打印之前进行过滤，对人类用户来说最感兴趣的项目是 ``string``（字段的标签）、``help``（如果可用，帮助文本）和 ``type``（以了解期望的值，或在更新记录时发送的值）。

.. example::

   .. tabs::

       .. code-tab:: python

           models.execute_kw(db, uid, password, 'res.partner', 'fields_get', [], {'attributes': ['string', 'help', 'type']})

       .. code-tab:: ruby

           models.execute_kw(db, uid, password, 'res.partner', 'fields_get', [], {attributes: %w(string help type)})

       .. code-tab:: php

           $models->execute_kw($db, $uid, $password, 'res.partner', 'fields_get', array(), array('attributes' => array('string', 'help', 'type')));

       .. code-tab:: java

           (Map<String, Map<String, Object>>)models.execute("execute_kw", asList(
               db, uid, password,
               "res.partner", "fields_get",
               emptyList(),
               new HashMap() {{
                   put("attributes", asList("string", "help", "type"));
               }}
           ));

       .. code-tab:: go

           recordFields := map[string]string{}
           if err := models.Call("execute_kw", []any{
               db, uid, password,
               "res.partner", "fields_get",
               []any{},
               map[string][]string{
                   "attributes": {"string", "help", "type"},
               },
           }, &recordFields); err != nil {
               log.Fatal(err)
           }

   结果：

   .. code-block:: json

      {
          "ean13": {
              "type": "char",
              "help": "条形码",
              "string": "EAN13"
          },
          "property_account_position_id": {
              "type": "many2one",
              "help": "财务位置将确定用于合作伙伴的税收和帐户。",
              "string": "财务位置"
          },
          "signup_valid": {
              "type": "boolean",
              "help": "",
              "string": "注册令牌有效"
          },
          "date_localization": {
              "type": "date",
              "help": "",
              "string": "地理定位日期"
          },
          "ref_company_ids": {
              "type": "one2many",
              "help": "",
              "string": "指向合作伙伴的公司"
          },
          "sale_order_count": {
              "type": "integer",
              "help": "",
              "string": "# 销售订单"
          },
          "purchase_order_count": {
              "type": "integer",
              "help": "",
              "string": "# 采购订单"
          },

搜索和读取
---------------

由于这是一个非常常见的任务，Odoo 提供了一个 :meth:`~odoo.models.Model.search_read` 快捷方式，顾名思义，它等同于 :meth:`~odoo.models.Model.search` 后跟 :meth:`~odoo.models.Model.read`，但避免了执行两个请求并保留 ID。

其参数与 :meth:`~odoo.models.Model.search` 的参数类似，但它还可以接受一个 ``fields`` 列表（如 :meth:`~odoo.models.Model.read`，如果未提供该列表，则会获取所有匹配记录的字段）。

.. example::

   .. tabs::

      .. code-tab:: python

         models.execute_kw(db, uid, password, 'res.partner', 'search_read', [[['is_company', '=', True]]], {'fields': ['name', 'country_id', 'comment'], 'limit': 5})

      .. code-tab:: ruby

         models.execute_kw(db, uid, password, 'res.partner', 'search_read', [[['is_company', '=', true]]], {fields: %w(name country_id comment), limit: 5})

      .. code-tab:: php

         $models->execute_kw($db, $uid, $password, 'res.partner', 'search_read', array(array(array('is_company', '=', true))), array('fields'=>array('name', 'country_id', 'comment'), 'limit'=>5));

      .. code-tab:: java

         asList((Object[])models.execute("execute_kw", asList(
             db, uid, password,
             "res.partner", "search_read",
             asList(asList(
                 asList("is_company", "=", true))),
             new HashMap() {{
                 put("fields", asList("name", "country_id", "comment"));
                 put("limit", 5);
             }}
         )));

      .. code-tab:: go

         var recordFields []map[string]any
         if err := models.Call("execute_kw", []any{
             db, uid, password,
             "res.partner", "search_read",
             []any{[]any{
                 []any{"is_company", "=", true},
             }},
             map[string]any{
                 "fields": []string{"name", "country_id", "comment"},
                 "limit":  5,
             },
         }, &recordFields); err != nil {
             log.Fatal(err)
         }

   结果：

   .. code-block:: json

      [
          {
              "comment": false,
              "country_id": [ 21, "Belgium" ],
              "id": 7,
              "name": "Agrolait"
          },
          {
              "comment": false,
              "country_id": [ 76, "France" ],
              "id": 18,
              "name": "Axelor"
          },
          {
              "comment": false,
              "country_id": [ 233, "United Kingdom" ],
              "id": 12,
              "name": "Bank Wealthy and sons"
          },
          {
              "comment": false,
              "country_id": [ 105, "India" ],
              "id": 14,
              "name": "Best Designers"
          },
          {
              "comment": false,
              "country_id": [ 76, "France" ],
              "id": 17,
              "name": "Camptocamp"
          }
      ]

创建记录
--------------

可以使用 :meth:`~odoo.models.Model.create` 创建模型的记录。该方法创建一条记录并返回其数据库标识符。

:meth:`~odoo.models.Model.create` 接受一个字段到值的映射，用于初始化记录。对于任何具有默认值并且未通过映射参数设置的字段，将使用默认值。

.. example::

   .. tabs::

      .. code-tab:: python

         id = models.execute_kw(db, uid, password, 'res.partner', 'create', [{'name': "新伙伴"}])

      .. code-tab:: ruby

         id = models.execute_kw(db, uid, password, 'res.partner', 'create', [{name: "新伙伴"}])

      .. code-tab:: php

         $id = $models->execute_kw($db, $uid, $password, 'res.partner', 'create', array(array('name'=>"新伙伴")));

      .. code-tab:: java

         final Integer id = (Integer)models.execute("execute_kw", asList(
             db, uid, password,
             "res.partner", "create",
             asList(new HashMap() {{ put("name", "新伙伴"); }})
         ));

      .. code-tab:: go

         var id int64
         if err := models.Call("execute_kw", []any{
             db, uid, password,
             "res.partner", "create",
             []map[string]string{
                 {"name": "新伙伴"},
             },
         }, &id); err != nil {
             log.Fatal(err)
         }

   结果：

   .. code-block:: json

      78

.. warning::
   虽然大多数值类型是您期望的（整型用于 :class:`~odoo.fields.Integer`，字符串用于 :class:`~odoo.fields.Char` 或 :class:`~odoo.fields.Text`），

   - :class:`~odoo.fields.Date`、:class:`~odoo.fields.Datetime` 和
     :class:`~odoo.fields.Binary` 字段使用字符串值
   - :class:`~odoo.fields.One2many` 和 :class:`~odoo.fields.Many2many`
     使用详细说明在 :meth:`写入方法的文档 <odoo.models.Model.write>` 中的特殊命令协议。

更新记录
--------------

可以使用 :meth:`~odoo.models.Model.write` 更新记录。它接受要更新的记录列表和字段到值的映射，类似于 :meth:`~odoo.models.Model.create`。

可以同时更新多个记录，但它们将对设置的字段获得相同的值。无法执行“计算”更新（即设置的值依赖于记录的现有值）。

.. example::

   .. tabs::

      .. code-tab:: python

         models.execute_kw(db, uid, password, 'res.partner', 'write', [[id], {'name': "更新的伙伴"}])
         # 获取更改后的记录名称
         models.execute_kw(db, uid, password, 'res.partner', 'read', [[id], ['display_name']])

      .. code-tab:: ruby

         models.execute_kw(db, uid, password, 'res.partner', 'write', [[id], {name: "更新的伙伴"}])
         # 获取更改后的记录名称
         models.execute_kw(db, uid, password, 'res.partner', 'read', [[id], ['display_name']])

      .. code-tab:: php

         $models->execute_kw($db, $uid, $password, 'res.partner', 'write', array(array($id), array('name'=>"更新的伙伴")));
         // 获取更改后的记录名称
         $models->execute_kw($db, $uid, $password,
             'res.partner', 'read', array(array($id), array('display_name')));

      .. code-tab:: java

         models.execute("execute_kw", asList(
             db, uid, password,
             "res.partner", "write",
             asList(
                 asList(id),
                 new HashMap() {{ put("name", "更新的伙伴"); }}
             )
         ));
         // 获取更改后的记录名称
         asList((Object[])models.execute("execute_kw", asList(
             db, uid, password,
             "res.partner", "read",
             asList(asList(id), asList("display_name"))
         )));

      .. code-tab:: go

         var result bool
         if err := models.Call("execute_kw", []any{
             db, uid, password,
             "res.partner", "write",
             []any{
                 []int64{id},
                 map[string]string{"name": "更新的伙伴"},
             },
         }, &result); err != nil {
             log.Fatal(err)
         }
         // 获取更改后的记录名称
         var record []any
         if err := models.Call("execute_kw", []any{
             db, uid, password,
             "res.partner", "name_get",
             []any{
                 []int64{id},
             },
         }, &record); err != nil {
             log.Fatal(err)
         }

   结果：

   .. code-block:: json

      [[78, "更新的伙伴"]]

删除记录
--------------

可以通过提供其 ID 批量删除记录，使用 :meth:`~odoo.models.Model.unlink`。

.. example::

   .. tabs::

      .. code-tab:: python

         models.execute_kw(db, uid, password, 'res.partner', 'unlink', [[id]])
         # 检查已删除记录是否仍在数据库中
         models.execute_kw(db, uid, password, 'res.partner', 'search', [[['id', '=', id]]])

      .. code-tab:: ruby

         models.execute_kw(db, uid, password, 'res.partner', 'unlink', [[id]])
         # 检查已删除记录是否仍在数据库中
         models.execute_kw(db, uid, password, 'res.partner', 'search', [[['id', '=', id]]])

      .. code-tab:: php

         $models->execute_kw($db, $uid, $password, 'res.partner', 'unlink', array(array($id)));
         // 检查已删除记录是否仍在数据库中
         $models->execute_kw(
             $db, $uid, $password, 'res.partner', 'search', array(array(array('id', '=', $id)))
         );

      .. code-tab:: java

         models.execute("execute_kw", asList(
             db, uid, password,
             "res.partner", "unlink",
             asList(asList(id))));
         // 检查已删除记录是否仍在数据库中
         asList((Object[])models.execute("execute_kw", asList(
             db, uid, password,
             "res.partner", "search",
             asList(asList(asList("id", "=", 78)))
         )));

      .. code-tab:: go

         var result bool
         if err := models.Call("execute_kw", []any{
             db, uid, password,
             "res.partner", "unlink",
             []any{
                 []int64{id},
             },
         }, &result); err != nil {
             log.Fatal(err)
         }
         // 检查已删除记录是否仍在数据库中
         var record []any
         if err := models.Call("execute_kw", []any{
             db, uid, password,
             "res.partner", "search",
             []any{[]any{
                 []any{"id", "=", id},
             }},
         }, &record); err != nil {
             log.Fatal(err)
         }

   结果：

   .. code-block:: json

      []

检查和自省
----------------------------

在我们之前使用 :meth:`~odoo.models.Model.fields_get` 查询模型并从一开始就使用任意模型的同时，Odoo 将大多数模型元数据存储在少数元模型中，这些元模型允许通过 XML-RPC 查询系统并动态修改模型和字段（有限制）。

.. _reference/webservice/inspection/models:

``ir.model``
~~~~~~~~~~~~

通过其各个字段提供有关 Odoo 模型的信息。

``name``
    模型的可读描述
``model``
    系统中每个模型的名称
``state``
    模型是通过 Python 代码生成的（``base``）还是通过创建 ``ir.model`` 记录（``manual``）
``field_id``
    通过 :class:`~odoo.fields.One2many` 列出模型的字段，指向 :ref:`reference/webservice/inspection/fields`
``view_ids``
    :class:`~odoo.fields.One2many` 指向为模型定义的 :doc:`../reference/user_interface/view_architectures`
``access_ids``
    :class:`~odoo.fields.One2many` 关系，指向模型上设置的 :ref:`reference/security/acl`

``ir.model`` 可以用来

- 查询系统中已安装的模型（作为对模型操作的先决条件或探索系统内容）。
- 获取有关特定模型的信息（通常通过列出与之关联的字段）。
- 通过 RPC 动态创建新模型。

.. important::
   * 自定义模型名称必须以 ``x_`` 开头。
   * 必须提供 ``state`` 并将其设置为 ``manual``，否则模型将无法加载。
   * 不能通过自定义模型添加新的 *方法*，只能添加字段。

.. example::

   自定义模型最初将仅包含所有模型上的“内置”字段：

   .. tabs::

      .. code-tab:: python

         models.execute_kw(db, uid, password, 'ir.model', 'create', [{
             'name': "自定义模型",
             'model': "x_custom_model",
             'state': 'manual',
         }])
         models.execute_kw(db, uid, password, 'x_custom_model', 'fields_get', [], {'attributes': ['string', 'help', 'type']})

      .. code-tab:: php

         $models->execute_kw($db, $uid, $password, 'ir.model', 'create', array(array(
             'name' => "自定义模型",
             'model' => 'x_custom_model',
             'state' => 'manual'
         )));
         $models->execute_kw($db, $uid, $password, 'x_custom_model', 'fields_get', array(), array('attributes' => array('string', 'help', 'type')));

      .. code-tab:: ruby

         models.execute_kw(db, uid, password, 'ir.model', 'create', [{
             name: "自定义模型",
             model: 'x_custom_model',
             state: 'manual'
         }])
         fields = models.execute_kw(db, uid, password, 'x_custom_model', 'fields_get', [], {attributes: %w(string help type)})

      .. code-tab:: java

         models.execute(
             "execute_kw", asList(
                 db, uid, password,
                 "ir.model", "create",
                 asList(new HashMap<String, Object>() {{
                     put("name", "自定义模型");
                     put("model", "x_custom_model");
                     put("state", "manual");
                 }})
         ));
         final Object fields = models.execute(
             "execute_kw", asList(
                 db, uid, password,
                 "x_custom_model", "fields_get",
                 emptyList(),
                 new HashMap<String, Object> () {{
                     put("attributes", asList(
                             "string",
                             "help",
                             "type"));
                 }}
         ));

      .. code-tab:: go

         var id int64
         if err := models.Call("execute_kw", []any{
             db, uid, password,
             "ir.model", "create",
             []map[string]string{
                 {
                     "name":  "自定义模型",
                     "model": "x_custom_model",
                     "state": "manual",
                 },
             },
         }, &id); err != nil {
             log.Fatal(err)
         }
         recordFields := map[string]string{}
         if err := models.Call("execute_kw", []any{
             db, uid, password,
             "x_custom_model", "fields_get",
             []any{},
             map[string][]string{
                 "attributes": {"string", "help", "type"},
             },
         }, &recordFields); err != nil {
             log.Fatal(err)
         }

   结果：

   .. code-block:: json

      {
          "create_uid": {
              "type": "many2one",
              "string": "创建者"
          },
          "create_date": {
              "type": "datetime",
              "string": "创建时间"
          },
          "__last_update": {
              "type": "datetime",
              "string": "最后修改时间"
          },
          "write_uid": {
              "type": "many2one",
              "string": "最后更新者"
          },
          "write_date": {
              "type": "datetime",
              "string": "最后更新时间"
          },
          "display_name": {
              "type": "char",
              "string": "显示名称"
          },
          "id": {
              "type": "integer",
              "string": "ID"
          }
      }

.. _reference/webservice/inspection/fields:

``ir.model.fields``
~~~~~~~~~~~~~~~~~~~

提供有关 Odoo 模型字段的信息，并允许添加自定义字段，而无需使用 Python 代码。

``model_id``
    :class:`~odoo.fields.Many2one` 指向 :ref:`reference/webservice/inspection/models` 的字段
``name``
    字段的技术名称（用于 ``read`` 或 ``write``）
``field_description``
    字段的用户可读标签（例如，``fields_get`` 中的 ``string``）
``ttype``
    创建字段的 :ref:`type <reference/orm/fields>`
``state``
    字段是通过 Python 代码创建的（``base``）还是通过 ``ir.model.fields``（``manual``）
``required``、``readonly``、``translate``
    启用字段上的相应标志
``groups``
    :ref:`字段级访问控制 <reference/security/fields>`，指向 ``res.groups`` 的 :class:`~odoo.fields.Many2many`
``selection``、``size``、``on_delete``、``relation``、``relation_field``、``domain``
    特定于类型的属性和自定义，详见 :ref:`字段文档 <reference/orm/fields>`。

.. important::
   - 与自定义模型一样，仅使用 ``state="manual"`` 创建的新字段才会作为实际字段在模型上激活。
   - 计算字段无法通过 ``ir.model.fields`` 添加，一些字段元信息（默认值、onchange）也无法设置。

.. example::

   .. tabs::

      .. code-tab:: python

         id = models.execute_kw(db, uid, password, 'ir.model', 'create', [{
             'name': "自定义模型",
             'model': "x_custom",
             'state': 'manual',
         }])
         models.execute_kw(db, uid, password, 'ir.model.fields', 'create', [{
             'model_id': id,
             'name': 'x_name',
             'ttype': 'char',
             'state': 'manual',
             'required': True,
         }])
         record_id = models.execute_kw(db, uid, password, 'x_custom', 'create', [{'x_name': "测试记录"}])
         models.execute_kw(db, uid, password, 'x_custom', 'read', [[record_id]])

      .. code-tab:: php

         $id = $models->execute_kw($db, $uid, $password, 'ir.model', 'create', array(array(
             'name' => "自定义模型",
             'model' => 'x_custom',
             'state' => 'manual'
         )));
         $models->execute_kw($db, $uid, $password, 'ir.model.fields', 'create', array(array(
             'model_id' => $id,
             'name' => 'x_name',
             'ttype' => 'char',
             'state' => 'manual',
             'required' => true
         )));
         $record_id = $models->execute_kw($db, $uid, $password, 'x_custom', 'create', array(array('x_name' => "测试记录")));
         $models->execute_kw($db, $uid, $password, 'x_custom', 'read', array(array($record_id)));

      .. code-tab:: ruby

         id = models.execute_kw(db, uid, password, 'ir.model', 'create', [{
             name: "自定义模型",
             model: "x_custom",
             state: 'manual'
         }])
         models.execute_kw(db, uid, password, 'ir.model.fields', 'create', [{
             model_id: id,
             name: "x_name",
             ttype: "char",
             state: "manual",
             required: true
         }])
         record_id = models.execute_kw(db, uid, password, 'x_custom', 'create', [{x_name: "测试记录"}])
         models.execute_kw(db, uid, password, 'x_custom', 'read', [[record_id]])

      .. code-tab:: java

         final Integer id = (Integer)models.execute(
             "execute_kw", asList(
                 db, uid, password,
                 "ir.model", "create",
                 asList(new HashMap<String, Object>() {{
                     put("name", "自定义模型");
                     put("model", "x_custom");
                     put("state", "manual");
                 }})
         ));
         models.execute(
             "execute_kw", asList(
                 db, uid, password,
                 "ir.model.fields", "create",
                 asList(new HashMap<String, Object>() {{
                     put("model_id", id);
                     put("name", "x_name");
                     put("ttype", "char");
                     put("state", "manual");
                     put("required", true);
                 }})
         ));
         final Integer record_id = (Integer)models.execute(
             "execute_kw", asList(
                 db, uid, password,
                 "x_custom", "create",
                 asList(new HashMap<String, Object>() {{
                     put("x_name", "测试记录");
                 }})
         ));

         client.execute(
             "execute_kw", asList(
                 db, uid, password,
                 "x_custom", "read",
                 asList(asList(record_id))
         ));

      .. code-tab:: go

         var id int64
         if err := models.Call("execute_kw", []any{
             db, uid, password,
             "ir.model", "create",
             []map[string]string{
                 {
                     "name":  "自定义模型",
                     "model": "x_custom",
                     "state": "manual",
                 },
             },
         }, &id); err != nil {
             log.Fatal(err)
         }
         var fieldId int64
         if err := models.Call("execute_kw", []any{
             db, uid, password,
             "ir.model.fields", "create",
             []map[string]any{
                 {
                     "model_id": id,
                     "name":     "x_name",
                     "ttype":    "char",
                     "state":    "manual",
                     "required": true,
                 },
             },
         }, &fieldId); err != nil {
             log.Fatal(err)
         }
         var recordId int64
         if err := models.Call("execute_kw", []any{
             db, uid, password,
             "x_custom", "create",
             []map[string]string{
                 {"x_name": "测试记录"},
             },
         }, &recordId); err != nil {
             log.Fatal(err)
         }
         var recordFields []map[string]any
         if err := models.Call("execute_kw", []any{
             db, uid, password,
             "x_custom", "read",
             [][]int64{{recordId}},
         }, recordFields); err != nil {
             log.Fatal(err)
         }

   结果：

   .. code-block:: json

      [
          {
              "create_uid": [1, "管理员"],
              "x_name": "测试记录",
              "__last_update": "2014-11-12 16:32:13",
              "write_uid": [1, "管理员"],
              "write_date": "2014-11-12 16:32:13",
              "create_date": "2014-11-12 16:32:13",
              "id": 1,
              "display_name": "测试记录"
          }
      ]

.. _PostgreSQL: https://www.postgresql.org
.. _XML-RPC: https://en.wikipedia.org/wiki/XML-RPC
.. _base64: https://en.wikipedia.org/wiki/Base64
