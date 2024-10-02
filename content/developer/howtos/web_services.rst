============
Web 服务
============

Web 服务模块为所有 Web 服务提供了一个通用接口：

- XML-RPC
- JSON-RPC

业务对象也可以通过分布式对象机制访问。它们都可以通过上下文视图在客户端界面中进行修改。

Odoo 可以通过 XML-RPC/JSON-RPC 接口访问，许多语言中都有相关的库。

XML-RPC 库
---------------

以下是一个使用库 ``xmlrpc.client`` 与 Odoo 服务器交互的 Python 3 程序示例::

   import xmlrpc.client

   root = 'http://%s:%d/xmlrpc/' % (HOST, PORT)

   uid = xmlrpc.client.ServerProxy(root + 'common').login(DB, USER, PASS)
   print("以用户 %s (uid: %d) 登录" % (USER, uid))

   # 创建一个新笔记
   sock = xmlrpc.client.ServerProxy(root + 'object')
   args = {
       'color' : 8,
       'memo' : '这是一条笔记',
       'create_uid': uid,
   }
   note_id = sock.execute(DB, uid, PASS, 'note.note', 'create', args)

.. exercise:: 为客户端添加新服务

   编写一个 Python 程序，能够向运行 Odoo 的 PC 发送 XML-RPC 请求（可以是您的 PC，也可以是讲师的 PC）。该程序应显示所有的会议及其对应的座位数量。同时，它还应为其中一个课程创建一个新的会议。

   .. only:: solutions

        .. code-block:: python

            import functools
            import xmlrpc.client
            HOST = 'localhost'
            PORT = 8069
            DB = 'openacademy'
            USER = 'admin'
            PASS = 'admin'
            ROOT = 'http://%s:%d/xmlrpc/' % (HOST,PORT)

            # 1. 登录
            uid = xmlrpc.client.ServerProxy(ROOT + 'common').login(DB,USER,PASS)
            print("以用户 %s (uid:%d) 登录" % (USER,uid))

            call = functools.partial(
                xmlrpc.client.ServerProxy(ROOT + 'object').execute,
                DB, uid, PASS)

            # 2. 读取会议
            sessions = call('openacademy.session', 'search_read', [], ['name', 'seats'])
            for session in sessions:
                print("会议 %s（%s 个座位）" % (session['name'], session['seats']))
            # 3. 创建一个新的会议
            session_id = call('openacademy.session', 'create', {
                'name': '我的会议',
                'course_id': 2,
            })

        代码可以通过按名称查找课程 ID 而不是使用硬编码的课程 ID::

            # 3. 为“功能”课程创建一个新会议
            course_id = call('openacademy.course', 'search', [('name', 'ilike', 'Functional')])[0]
            session_id = call('openacademy.session', 'create', {
                'name': '我的会议',
                'course_id': course_id,
            })

.. seealso::
   - :doc:`../reference/external_api`: 关于 XML-RPC 的深入教程，包含多个编程语言的示例。

JSON-RPC 库
----------------

以下是一个使用标准 Python 库 ``urllib.request`` 和 ``json`` 与 Odoo 服务器交互的 Python 3 程序示例。此示例假设已安装 **Productivity** 应用（ ``note``）::

    import json
    import random
    import urllib.request

    HOST = 'localhost'
    PORT = 8069
    DB = 'openacademy'
    USER = 'admin'
    PASS = 'admin'

    def json_rpc(url, method, params):
        data = {
            "jsonrpc": "2.0",
            "method": method,
            "params": params,
            "id": random.randint(0, 1000000000),
        }
        req = urllib.request.Request(url=url, data=json.dumps(data).encode(), headers={
            "Content-Type":"application/json",
        })
        reply = json.loads(urllib.request.urlopen(req).read().decode('UTF-8'))
        if reply.get("error"):
            raise Exception(reply["error"])
        return reply["result"]

    def call(url, service, method, *args):
        return json_rpc(url, "call", {"service": service, "method": method, "args": args})

    # 登录到指定数据库
    url = "http://%s:%s/jsonrpc" % (HOST, PORT)
    uid = call(url, "common", "login", DB, USER, PASS)

    # 创建一个新笔记
    args = {
        'color': 8,
        'memo': '这是另一条笔记',
        'create_uid': uid,
    }
    note_id = call(url, "object", "execute", DB, uid, PASS, 'note.note', 'create', args)

可以轻松地将示例从 XML-RPC 适配到 JSON-RPC。

.. note::

    有许多高层次的 API 可以使用多种语言访问 Odoo 系统，而无需*显式*通过 XML-RPC 或 JSON-RPC，例如：

    * https://github.com/akretion/ooor
    * https://github.com/OCA/odoorpc
    * https://github.com/nicolas-van/openerp-client-lib
    * http://pythonhosted.org/OdooRPC
    * https://github.com/abhishek-jaiswal/php-openerp-lib
