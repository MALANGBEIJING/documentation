框架概述
==========

引言
----

Odoo JavaScript框架是由 ``web/`` 插件提供的一套功能和构建模块，用于构建在浏览器中运行的Odoo应用程序。同时，Odoo JavaScript框架是一个单页面应用程序，通常称为*web客户端*（可在URL ``/web`` 访问）。

web客户端最初是一个基于自定义类和小部件系统构建的应用程序，但现在正在过渡到使用本机JavaScript类和Owl组件系统。这解释了为什么在代码库中同时使用这两种系统。

从高层次的视角看，web客户端作为单页面应用程序运行，这意味着它不需要每次用户操作时从服务器请求完整页面。相反，它只请求所需的数据，然后相应地替换或更新当前屏幕。此外，它管理URL以保持与当前状态的同步。

JavaScript框架还用于其他情况，例如Odoo网站或销售点，但本参考主要集中在web客户端。

.. note::

    在Odoo生态系统中，*前端*和*后端*这两个术语通常分别指代Odoo网站（公共）和web客户端。请勿将此与浏览器代码（前端）和服务器代码（后端）的更常见定义混淆。

.. note::

    在本文档中，*组件*始终指新的Owl组件，而*小部件*则指旧的Odoo小部件。

.. note::

    如果可能，所有新开发应优先使用Owl。

代码结构
--------

``web/static/src`` 文件夹包含所有 ``web/`` 的JavaScript（以及CSS和模板）代码库。以下是一些重要文件夹的列表：

- ``core/``：大多数底层功能。
- ``fields/``：所有字段组件。
- ``views/`` ：所有JavaScript视图组件（例如， ``form`` 、 ``list``）。
- ``search/``：控制面板、搜索栏、搜索面板等。
- ``webclient/``：特定于web客户端的代码，例如导航栏、用户菜单、操作服务等。

`web/static/src` 文件夹是根文件夹。内部的所有内容都可以通过使用 `@web` 前缀简单导入。例如，以下是如何导入位于 `web/static/src/core/utils/functions` 中的 `memoize` 函数：

    .. code-block:: javascript
        import { memoize } from "@web/core/utils/functions";

WebClient架构
-------------

如上所述，web客户端是一个Owl应用程序。以下是其模板的简化版本：

    .. code-block:: xml
        <t t-name="web.WebClient">
            <body class="o_web_client">
                <NavBar/>
                <ActionContainer/>
                <MainComponentsContainer/>
            </body>
        </t>

该结构基本上是导航栏、当前操作和一些附加组件的包装器。`ActionContainer` 是一个高阶组件，负责显示当前的操作控制器，因此一个客户端操作或特定视图在操作类型为 `act_window` 的情况下。在这方面，管理操作是它工作的一个重要部分：操作服务在内存中保持所有活动操作的堆栈（在面包屑中表示），并协调每次更改。

另一个有趣的地方是 `MainComponentsContainer`：它只是一个组件，用于显示在 `main_components` 注册表中注册的所有组件。这使得系统的其他部分可以扩展web客户端。

环境
----

作为一个Owl应用程序，Odoo web客户端定义了自己的环境（组件可以使用`this.env`访问它）。以下是Odoo为共享`env`对象添加的内容：

.. list-table::
   :widths: 25 75
   :header-rows: 1

   * - Key
     - Value
   * - `qweb`
     - Owl所需，包含所有模板。
   * - `bus`
     - :ref:`主事件总线 <frontend/framework/bus>`，用于协调一些通用事件。
   * - `services`
     - 所有已部署的 :ref:`服务 <frontend/services>`（通常通过 `useService` 钩子访问）。
   * - `debug`
     - 字符串。如果非空，则web客户端处于 :ref:`调试模式 <frontend/framework/debug_mode>`。
   * - `_t`
     - 翻译函数。
   * - `isSmall`
     - 布尔值。如果为真，则web客户端当前处于移动模式（屏幕宽度 ≤ 767px）。

例如，在组件中使用翻译函数：

    .. code-block:: javascript
        const someString = this.env._t('some text');

构建模块
--------

web客户端依赖于几种抽象类型：注册表、服务、组件和钩子。

注册表
~~~~~~

注册表作为一个键/值映射存储特定类型的对象，是UI可扩展性的重要组成部分。一旦某个对象被注册，web客户端的其余部分就可以使用它。例如，字段注册表包含所有可以在视图中使用的字段组件：

    .. code-block:: javascript
        import { registry } from "./core/registry";

        class MyFieldChar extends owl.Component {
            // some code
        }

        registry.category("fields").add("my_field_char", MyFieldChar);


服务
~~~~~

服务是提供特性的一段长寿命代码，可以被组件（使用`useService`）或其他服务导入。此外，它们可以声明一组依赖项。在这个意义上，服务基本上是一个依赖注入（DI）系统。例如，`notification` 服务提供了一种显示通知的方式：

    .. code-block:: javascript

        import { registry } from "./core/registry";

        const serviceRegistry = registry.category("services");

        const myService = {
            dependencies: ["notification"],
            start(env, { notification }) {
                let counter = 1;
                setInterval(() => {
                    notification.add(`Tick Tock ${counter++}`);
                }, 5000);
            }
        };

        serviceRegistry.add("myService", myService);

组件和钩子
~~~~~~~~~~~

Odoo组件是集成到web客户端的Owl组件。钩子提供了一种封装代码的方法，特别是当涉及生命周期时：

    .. code-block:: javascript

        function useCurrentTime() {
            const state = useState({ now: new Date() });
            const update = () => state.now = new Date();
            let timer;
            onWillStart(() => timer = setInterval(update, 1000));
            onWillUnmount(() => clearInterval(timer));
            return state;
        }

上下文
------

Odoo JavaScript中的*上下文*概念允许代码为函数调用或RPC提供更多信息，以便系统的其他部分可以正确响应。这在某种程度上像是一个信息包，被传播到各个地方。它在一些情况下非常有用，例如让Odoo服务器知道某个模型RPC来自于特定的表单视图，或者在组件中激活/禁用某些功能。

Odoo web客户端有两种不同的上下文：*用户上下文*和*操作上下文*（因此，当使用*上下文*这个词时，我们应该小心，因为在不同情况下它可能意味着不同的东西）。

用户上下文
~~~~~~~~~~~

*用户上下文*是一个小对象，包含与当前用户相关的各种信息。它可以通过 `user` 服务访问：

    .. code-block:: javascript
        class MyComponent extends Component {
            setup() {
                const user = useService("user");
                console.log(user.context);
            }
        }

它包含以下信息：

.. list-table::
    :widths: 20 20 60
    :header-rows: 1

    * - Name
      - Type
      - Description
    * - `allowed_company_ids`
      - `number[]`
      - 用户的活动公司ID列表。
    * - `lang`
      - `string`
      - 用户语言代码（例如，“en_us”）。
    * - `tz`
      - `string`
      - 用户当前时区（例如，“Europe/Brussels”）。

实际上，`orm` 服务会自动将用户上下文添加到其请求中。这就是为什么在大多数情况下不需要直接导入它。

操作上下文
~~~~~~~~~~~

`ir.actions.act_window` 和 `ir.actions.client` 支持一个可选的 `context` 字段。该字段是一个`char`，表示一个对象。每当相应的操作在web客户端中加载时，这个上下文字段将被评估为一个对象，并传递给与操作对应的组件。

    .. code-block:: xml
        <field name="context">{'search_default_customer': 1}</field>

它可以以多种不同方式使用。例如，视图将操作上下文添加到所有发往服务器的请求中。另一个重要用途是默认启用某些搜索过滤器（如上例所示）。

有时，当我们手动（因此，程序性地，在JavaScript中）执行新操作时，能够扩展操作上下文是有用的。这可以通过 `additional_context` 参数完成。

    .. code-block:: javascript
        // in setup
        let actionService = useService("action");

        // in some event handler
        actionService.doAction("addon_name.something", {
            additional_context: {
                default_period_id: defaultPeriodId
            }
        });

在这个示例中，xml_id为

`addon_name.something`的操作将被加载，并且其上下文将与`default_period_id`值一起扩展。这是一个非常重要的用例，允许开发者通过提供一些信息来组合操作。

Python解释器
===================

Odoo框架提供了一个内置的小型Python解释器。其目的是评估小的Python表达式。这很重要，因为Odoo中的视图具有用Python编写的修饰符，但它们需要由浏览器进行评估。

例如：

    .. code-block:: javascript
        import { evaluateExpr } from "@web/core/py_js/py";

        evaluateExpr("1 + 2*{'a': 1}.get('b', 54) + v", { v: 33 }); // 返回 142

`py` JavaScript代码导出了五个函数：

.. js:function:: tokenize(expr)

   :param string expr: 要标记化的表达式
   :returns: Token[] 一个标记列表

.. js:function:: parse(tokens)

   :param Token[] tokens: 一组标记
   :returns: AST 一个表示表达式的抽象语法树结构

.. js:function:: parseExpr(expr)

   :param string expr: 一个有效的Python表达式字符串
   :returns: AST 一个表示表达式的抽象语法树结构

.. js:function:: evaluate(ast[, context])

   :param AST ast: 一个表示表达式的AST结构
   :param Object context: 提供额外评估上下文的对象
   :returns: any 表达式结果值，基于上下文

.. js:function:: evaluateExpr(expr[, context])

   :param string expr: 一个有效的Python表达式字符串
   :param Object context: 提供额外评估上下文的对象
   :returns: any 表达式结果值，基于上下文

域
====

广义上讲，Odoo中的域表示与指定条件匹配的一组记录。在JavaScript中，它们通常表示为条件列表（或前缀表示法中的操作符：`|`、`&`或`!`），或作为字符串表达式。它们不必被标准化（如果必要，`&`操作符是隐含的）。例如：

    .. code-block:: javascript

        // 条件列表
        []
        [["a", "=", 3]]
        [["a", "=", 1], ["b", "=", 2], ["c", "=", 3]]
        ["&", "&", ["a", "=", 1], ["b", "=", 2], ["c", "=", 3]]
        ["&", "!", ["a", "=", 1], "|", ["a", "=", 2], ["a", "=", 3]]

        // 字符串表达式
        "[('some_file', '>', a)]"
        "[('date','>=', (context_today() - datetime.timedelta(days=30)).strftime('%Y-%m-%d'))]"
        "[('date', '!=', False)]"


字符串表达式比列表表达式更强大：它们可以包含Python表达式和未评估的值，这取决于某些评估上下文。然而，操作字符串表达式更困难。

由于域在web客户端中非常重要，Odoo提供了一个`Domain`类：

    .. code-block:: javascript

        new Domain([["a", "=", 3]]).contains({ a: 3 }); // true

        const domain = new Domain(["&", "&", ["a", "=", 1], ["b", "=", 2], ["c", "=", 3]]);
        domain.contains({ a: 1, b: 2, c: 3 }); // true
        domain.contains({ a: -1, b: 2, c: 3 }); // false

        // 下一个表达式返回 ["|", ("a", "=", 1), ("b", "<=", 3)]
        Domain.or([[["a", "=", 1]], "[('b', '<=', 3)]"]).toString();


以下是`Domain`类的描述：

.. js:class:: Domain([descr])

   :param descr: 域描述
   :type descr: string | any[] | Domain

   .. js:method:: contains(record)

      :param Object record: 记录对象
      :returns: boolean

      返回true如果记录匹配域指定的所有条件

   .. js:method:: toString()

      :returns: string

      返回域的字符串描述

   .. js:method:: toList([context])

      :param Object context: 评估上下文
      :returns: any[]

      返回域的列表描述。请注意，此方法接受一个可选的`context`对象，该对象将用于替换所有自由变量。

    .. code-block:: javascript
        new Domain(`[('a', '>', b)]`).toList({ b: 3 }); // [['a', '>', 3]]

### 组合域的静态方法

.. staticmethod:: Domain.and(domains)

   :param domains: 域表示的列表
   :type domains: string[] | any[][] | Domain[]
   :returns: Domain

   返回表示所有域交集的域。

.. staticmethod:: Domain.or(domains)

   :param domains: 域表示的列表
   :type domains: string[] | any[][] | Domain[]
   :returns: Domain

   返回表示所有域并集的域。

.. staticmethod:: Domain.not(domain)

   :param domain: 域表示
   :type domain: string | any[] | Domain
   :returns: Domain

   返回表示域参数的否定的域。

.. staticmethod:: Domain.combine(domains, operator)

   :param domains: 域表示的列表
   :type domains: string[] | any[][] | Domain[]
   :param operator: 操作符
   :type operator: 'AND' or 'OR'

   :returns: Domain

   返回表示所有域的交集或并集的域，具体取决于操作符的值。

总线
====

web客户端的`env`对象包含一个事件总线，命名为`bus`。其目的是允许系统各部分在不耦合的情况下进行协调。`env.bus`是一个Owl的`EventBus`，应当用于感兴趣的全局事件。

    .. code-block:: javascript
        // 例如，在某些服务代码中：
        env.bus.on("WEB_CLIENT_READY", null, doSomething);

### 事件列表

.. list-table::
   :header-rows: 1

   * - 消息
     - 负载
     - 触发
   * - ``ACTION_MANAGER:UI-UPDATED``
     - 表示UI更新的模式（'current'、'new'或'fullscreen'）
     - 操作请求完成后，更新操作管理器的呈现
   * - ``ACTION_MANAGER:UPDATE``
     - 下一次呈现信息
     - 操作管理器完成下一界面的计算
   * - ``MENUS:APP-CHANGED``
     - 无
     - 菜单服务的当前应用程序已更改
   * - ``ROUTE_CHANGE``
     - 无
     - URL哈希已更改
   * - ``RPC:REQUEST``
     - rpc ID
     - RPC请求刚刚开始
   * - ``RPC:RESPONSE``
     - rpc ID
     - RPC请求已完成
   * - ``WEB_CLIENT_READY``
     - 无
     - web客户端已挂载
   * - ``FOCUS-VIEW``
     - 无
     - 主视图应聚焦
   * - ``CLEAR-CACHES``
     - 无
     - 应该清除所有内部缓存
   * - ``CLEAR-UNCOMMITTED-CHANGES``
     - 函数列表
     - 所有具有未提交更改的视图应清除它们，并在列表中推送回调

浏览器对象
==========

JavaScript框架还提供了一个特殊的对象 ``browser`` ，它提供对许多浏览器API的访问，例如 ``location`` 、 ``localStorage`` 或 ``setTimeout`` 。例如，以下是如何使用 ``browser.setTimeout``函数：

    .. code-block:: javascript

        import { browser } from "@web/core/browser/browser";

        // 代码中的某处
        browser.setTimeout(someFunction, 1000);

它主要在测试过程中很有用：使用浏览器对象的所有代码都可以轻松通过模拟相关功能来进行测试。

它包含以下内容：

.. list-table::
   :header-rows: 1

   * - addEventListener
     - cancelAnimationFrame
     - clearInterval
   * - clearTimeout
     - console
     - Date
   * - fetch
     - history
     - localStorage
   * - location
     - navigator
     - open
   * - random
     - removeEventListener
     - requestAnimationFrame
   * - sessionStorage
     - setInterval
     - setTimeout
   * - XMLHttpRequest
     - 

调试模式
========

Odoo可以在一种特殊模式下运行，称为`调试`模式。它主要用于两个目的：

- 显示某些特定屏幕的附加信息/字段。
- 提供一些额外工具来帮助开发人员调试Odoo界面。

`调试`模式由一个字符串描述。空字符串表示`调试`模式不活动。否则，它是活动的。如果字符串包含`assets`或`tests`，则

相应的特定子模式被激活（见下文）。这两种模式可以同时激活，例如字符串为 `assets,tests`。

可以在 :ref:`环境 <frontend/framework/environment>` 中读取当前的 `debug` 模式值：
`env.debug`。

.. tip::

    要仅在调试模式下显示菜单、字段或视图元素，应定位到组 `base.group_no_one`：

    .. code-block:: xml
        <field name="fname" groups="base.group_no_one"/>

.. seealso::
   - :ref:`激活调试模式 <developer-mode>`

资产模式
~~~~~~~~~

`debug=assets` 子模式对于调试JavaScript代码非常有用：一旦激活， :ref:`资产 <reference/assets>` 包将不再被压缩，并且会生成源映射。这对于调试各种JavaScript代码很有用。

测试模式
~~~~~~~~~

还有一个名为 `tests` 的子模式；如果启用，服务器将在页面中注入 `web.assets_tests` 包。此包主要包含测试游览（其目的是测试功能，而不是为用户展示有趣的内容）。`tests`模式对运行这些游览很有用。

.. seealso::
    - `Owl Repository <https://github.com/odoo/owl>`_
