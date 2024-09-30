:orphan:

==========================
定制Web客户端
==========================

.. danger::
   本教程已过时。

.. highlight:: javascript

.. default-domain:: js

本指南介绍如何为 Odoo 的 Web 客户端创建模块。

要使用 Odoo 创建网站，请参阅 :doc:`website`；要添加业务功能或扩展 Odoo 的现有业务系统，请参阅 :doc:`backend`。

.. warning::

    本指南假设您具备以下知识：

    * Javascript 基础知识和良好实践
    * jQuery_
    * `Underscore.js`_

    还需要 :doc:`已安装的 Odoo </administration/on_premise>` 和 Git_。

一个简单的模块
===============

让我们从一个简单的 Odoo 模块开始，该模块包含基本的 Web 组件配置，并让我们测试 Web 框架。

示例模块可以在线获取，并可通过以下命令下载：

.. code-block:: console

    $ git clone http://github.com/odoo/petstore

这将创建一个 ``petstore`` 文件夹，该文件夹会放置在您执行该命令的位置。
然后，您需要将该文件夹添加到 Odoo 的
:option:`addons path <odoo-bin --addons-path>`，创建一个新数据库并安装 ``oepetstore`` 模块。

如果您浏览 ``petstore`` 文件夹，您应该会看到以下内容：

.. code-block:: text

    oepetstore
    |-- images
    |   |-- alligator.jpg
    |   |-- ball.jpg
    |   |-- crazy_circle.jpg
    |   |-- fish.jpg
    |   `-- mice.jpg
    |-- __init__.py
    |-- oepetstore.message_of_the_day.csv
    |-- __manifest__.py
    |-- petstore_data.xml
    |-- petstore.py
    |-- petstore.xml
    `-- static
        `-- src
            |-- css
            |   `-- petstore.css
            |-- js
            |   `-- petstore.js
            `-- xml
                `-- petstore.xml

该模块已经包含了各种服务器自定义内容。我们稍后再讨论这些内容，目前让我们重点关注 ``static`` 文件夹中的 Web 相关内容。

Odoo 模块中用于 "Web" 部分的文件必须放置在 ``static`` 文件夹中，以便 Web 浏览器可以访问这些文件。放置在该文件夹之外的文件无法被浏览器获取。``src/css``、``src/js`` 和 ``src/xml`` 子文件夹是约定俗成的，并非必须。

``oepetstore/static/css/petstore.css``
    当前为空，将用于存放宠物商店内容的 CSS_ 样式
``oepetstore/static/xml/petstore.xml``
    大部分为空，将用于存放 :ref:`reference/qweb` 模板
``oepetstore/static/js/petstore.js``
    最重要（也是最有趣）的部分，包含应用程序的逻辑（至少是其 Web 浏览器部分），以 Javascript 编写。当前应如下所示::

        odoo.oepetstore = function(instance, local) {
            var _t = instance.web._t,
                _lt = instance.web._lt;
            var QWeb = instance.web.qweb;

            local.HomePage = instance.Widget.extend({
                start: function() {
                    console.log("pet store home page loaded");
                },
            });

            instance.web.client_actions.add(
                'petstore.homepage', 'instance.oepetstore.HomePage');
        }

它只在浏览器的控制台中打印一条消息。

``static`` 文件夹中的文件需要在模块内进行定义，以便正确加载。``src/xml`` 中的所有内容都在 ``__manifest__.py`` 中定义，而 ``src/css`` 和 ``src/js`` 的内容则定义在 ``petstore.xml`` 或类似文件中。

.. warning::

    所有的 JavaScript 文件都会被合并和 :term:`minified`（压缩）以提高应用程序的加载速度。

    其缺点之一是调试变得更加困难，因为单独的文件消失了，并且代码变得显著不易读。可以通过启用 "开发者模式" 来禁用此过程：登录您的 Odoo 实例（默认情况下，用户为 *admin*，密码为 *admin*），打开用户菜单（在 Odoo 界面的右上角），然后选择 :guilabel:`关于 Odoo`，然后点击 :guilabel:`激活开发者模式`：

    .. image:: web/about_odoo.png
        :align: center

    .. image:: web/devmode.png
        :align: center

    这将重新加载 Web 客户端，禁用优化，使开发和调试更加舒适。

.. todo:: qweb 文件通过 ``__manifest__.py`` 挂钩，但 js 和 CSS 使用的是 bundle（捆绑包）


Odoo JavaScript 模块
======================

JavaScript 没有内置的模块。因此，不同文件中定义的变量可能会被混合在一起并导致冲突。这促使开发者设计了各种模块模式，用于构建干净的命名空间并限制命名冲突的风险。

Odoo 框架使用其中的一种模式来在 Web 插件中定义模块，以便为代码命名空间，并正确地对其加载顺序进行排序。

``oepetstore/static/js/petstore.js`` 中包含一个模块声明::

    odoo.oepetstore = function(instance, local) {
        local.xxx = ...;
    }

在 Odoo Web 中，模块作为全局 ``odoo`` 变量上的函数声明。函数名称必须与插件名称相同（在此例中为 ``oepetstore``），这样框架才能找到它并自动初始化。

当 Web 客户端加载您的模块时，它将调用根函数并提供两个参数：

* 第一个参数是当前的 Odoo Web 客户端实例，它提供对 Odoo 定义的各种功能的访问（如翻译、网络服务），以及核心或其他模块定义的对象。
* 第二个参数是由 Web 客户端自动创建的本地命名空间。应将需要从模块外部访问的对象和变量（无论是 Odoo Web 客户端需要调用它们还是其他模块可能需要定制它们）设置在该命名空间中。

类
=======

与模块类似，并且与大多数面向对象语言不同，JavaScript 并不内置 *类* \ [#classes]_，虽然它提供了大致等效（如果不是更低级且冗长）的机制。

为了简化和提高开发者的友好度，Odoo Web 提供了一个基于 John Resig 的 `Simple JavaScript Inheritance`_ 的类系统。

通过调用 :func:`~odoo.web.Class.extend` 方法来定义新类::

    var MyClass = instance.web.Class.extend({
        say_hello: function() {
            console.log("hello");
        },
    });

:func:`~odoo.web.Class.extend` 方法接受一个字典，描述新类的内容（方法和静态属性）。在此例中，它将只有一个 ``say_hello`` 方法，该方法不接受任何参数。

类的实例是使用 ``new`` 操作符来创建的::

    var my_object = new MyClass();
    my_object.say_hello();
    // 在控制台打印 "hello"

并且类的属性可以通过 ``this`` 访问::

    var MyClass = instance.web.Class.extend({
        say_hello: function() {
            console.log("hello", this.name);
        },
    });

    var my_object = new MyClass();
    my_object.name = "Bob";
    my_object.say_hello();
    // 在控制台打印 "hello Bob"

类可以通过定义 ``init()`` 方法提供初始化器来执行实例的初始设置。初始化器接收通过 ``new`` 操作符传递的参数::

    var MyClass = instance.web.Class.extend({
        init: function(name) {
            this.name = name;
        },
        say_hello: function() {
            console.log("hello", this.name);
        },
    });

    var my_object = new MyClass("Bob");
    my_object.say_hello();
    // 在控制台打印 "hello Bob"

还可以通过调用父类的 :func:`~odoo.web.Class.extend` 来从现有的（用户定义的）类创建子类，就像对 :class:`~odoo.web.Class` 进行子类化一样::

    var MySpanishClass = MyClass.extend({
        say_hello: function() {
            console.log("hola", this.name);
        },
    });

    var my_object = new MySpanishClass("Bob");
    my_object.say_hello();
    // 在控制台打印 "hola Bob"

当使用继承重写方法时，可以使用 ``this._super()`` 调用原始方法::

    var MySpanishClass = MyClass.extend({
        say_hello: function() {
            this._super();
            console.log("translation in Spanish: hola", this.name);
        },
    });

    var my_object = new MySpanishClass("Bob");
    my_object.say_hello();
    // 在控制台打印 "hello Bob \n translation in Spanish: hola Bob"

.. warning::

    ``_super`` 不是标准方法，它在当前继承链的下一个方法上动态设置（如果有的话）。它仅在方法调用的 *同步* 部分定义，对于异步处理程序（如网络调用之后或 ``setTimeout`` 回调中），应保留对其值的引用，而不应通过 ``this`` 访问::

        // 错误示例，将生成错误
        say_hello: function () {
            setTimeout(function () {
                this._super();
            }.bind(this), 0);
        }

        // 正确示例
        say_hello: function () {
            // 不要忘记 .bind()
            var _super = this._super.bind(this);
            setTimeout(function () {
                _super();
            }.bind(this), 0);
        }


Widget 基础
==============

Odoo Web 客户端捆绑了 jQuery_，用于简化 DOM 操作。它比标准的 `W3C DOM`_ 提供了更好的 API，但在构建复杂应用时仍显不足，可能导致难以维护。

类似于面向对象的桌面 UI 工具包（如 Qt_、Cocoa_ 或 GTK_），Odoo Web 使得特定组件负责页面的部分区域。在 Odoo Web 中，这类组件的基础是 :class:`~odoo.Widget` 类，该类专门负责处理页面的一部分并显示信息给用户。

您的第一个 Widget
-----------------

初始示例模块已经提供了一个基本的 widget::

    local.HomePage = instance.Widget.extend({
        start: function() {
            console.log("pet store home page loaded");
        },
    });

它继承了 :class:`~odoo.Widget`，并重写了标准方法 :func:`~odoo.Widget.start`，与前面的 ``MyClass`` 类似，这个方法暂时没有执行太多功能。

文件末尾的这一行代码::

    instance.web.client_actions.add(
        'petstore.homepage', 'instance.oepetstore.HomePage');

将我们的基本 widget 注册为一个客户端动作。客户端动作稍后会进行解释，目前这一行代码只是为了确保我们的 widget 在选择菜单项时能够被调用并显示：
:menuselection:`Pet Store --> Pet Store --> Home Page`。

.. warning::

    因为 widget 将从我们的模块外部被调用，Web 客户端需要其“完全限定名”，而不是本地版本。

显示内容
---------------

Widgets 具有许多方法和功能，但其基础非常简单：

* 设置 widget
* 格式化 widget 的数据
* 显示 widget

``HomePage`` widget 已经有一个 :func:`~odoo.Widget.start` 方法。该方法是 widget 生命周期的一部分，当 widget 被插入页面时自动调用。我们可以用它来显示一些内容。

所有的 widget 都有一个 :attr:`~odoo.Widget.$el`，它表示页面中 widget 负责的区域（作为 jQuery_ 对象）。Widget 的内容应该插入在其中。默认情况下，:attr:`~odoo.Widget.$el` 是一个空的 ``<div>`` 元素。

如果一个 ``<div>`` 元素没有内容（或没有特定样式来定义大小），通常它对用户是不可见的，这就是为什么当 ``HomePage`` 启动时页面上什么也不显示的原因。

让我们使用 jQuery 向 widget 的根元素添加一些内容::

    local.HomePage = instance.Widget.extend({
        start: function() {
            this.$el.append("<div>Hello dear Odoo user!</div>");
        },
    });

当您打开 :menuselection:`Pet Store --> Pet Store --> Home Page` 时，该消息现在将出现在页面上。

.. note::

    要刷新 Odoo Web 中加载的 JavaScript 代码，您需要重新加载页面。没有必要重新启动 Odoo 服务器。

``HomePage`` widget 被 Odoo Web 使用并自动管理。为了学习如何从头开始使用 widget，让我们创建一个新的 widget::

    local.GreetingsWidget = instance.Widget.extend({
        start: function() {
            this.$el.append("<div>We are so happy to see you again in this menu!</div>");
        },
    });

现在我们可以通过使用 ``GreetingsWidget`` 的 :func:`~odoo.Widget.appendTo` 方法将 ``GreetingsWidget`` 添加到 ``HomePage`` 中::

    local.HomePage = instance.Widget.extend({
        start: function() {
            this.$el.append("<div>Hello dear Odoo user!</div>");
            var greeting = new local.GreetingsWidget(this);
            return greeting.appendTo(this.$el);
        },
    });

* ``HomePage`` 首先将自己的内容添加到其 DOM 根元素中
* 然后 ``HomePage`` 实例化 ``GreetingsWidget``
* 最后它告诉 ``GreetingsWidget`` 插入自己，将其 :attr:`~odoo.Widget.$el` 的一部分委派给 ``GreetingsWidget``。

当调用 :func:`~odoo.Widget.appendTo` 方法时，它请求 widget 将自己插入指定位置并显示其内容。在调用 :func:`~odoo.Widget.appendTo` 期间，:func:`~odoo.Widget.start` 方法将被调用。

为了查看显示界面背后的内容，我们将使用浏览器的 DOM Explorer。首先让我们稍微修改我们的 widget，以便更容易地找到它们的位置，方法是 :attr:`为它们的根元素添加类 <odoo.Widget.className>`::

    local.HomePage = instance.Widget.extend({
        className: 'oe_petstore_homepage',
        ...
    });
    local.GreetingsWidget = instance.Widget.extend({
        className: 'oe_petstore_greetings',
        ...
    });

如果您能找到 DOM 的相关部分（右键单击文本然后选择 :guilabel:`检查元素`），它应该看起来像这样：

.. code-block:: html

    <div class="oe_petstore_homepage">
        <div>Hello dear Odoo user!</div>
        <div class="oe_petstore_greetings">
            <div>We are so happy to see you again in this menu!</div>
        </div>
    </div>

这清楚地显示了通过 :class:`~odoo.Widget` 自动创建的两个 ``<div>`` 元素，因为我们在它们上添加了一些类。

我们还可以看到我们自己添加的两个包含消息的 div 元素。

最后，请注意 ``<div class="oe_petstore_greetings">`` 元素，它表示 ``GreetingsWidget`` 实例，*在* ``<div class="oe_petstore_homepage">`` 元素内，该元素表示 ``HomePage`` 实例，因为我们将其附加到其中。

Widget 的父组件和子组件
---------------------------

在前面的部分中，我们使用以下语法实例化了一个 widget::

    new local.GreetingsWidget(this);

第一个参数是 ``this``，在这种情况下是一个 ``HomePage`` 实例。这告诉正在创建的 widget 其*父组件*是什么。

正如我们所见，widgets 通常由另一个 widget 插入 DOM，并且*在*另一个 widget 的根元素内。这意味着大多数 widget 是另一个 widget 的“组成部分”，并且代表另一个 widget 存在。我们称容器为*父组件*，被包含的 widget 为*子组件*。

由于多种技术和概念上的原因，widget 有必要知道其父组件是谁以及子组件是谁。

:func:`~odoo.Widget.getParent`
    可用于获取 widget 的父组件::

        local.GreetingsWidget = instance.Widget.extend({
            start: function() {
                console.log(this.getParent().$el );
                // 将在控制台打印 "div.oe_petstore_homepage"
            },
        });

:func:`~odoo.Widget.getChildren`
    可用于获取其子组件的列表::

        local.HomePage = instance.Widget.extend({
            start: function() {
                var greeting = new local.GreetingsWidget(this);
                greeting.appendTo(this.$el);
                console.log(this.getChildren()[0].$el);
                // 将在控制台打印 "div.oe_petstore_greetings"
            },
        });

在覆盖 widget 的 :func:`~odoo.Widget.init` 方法时，*非常重要的是*将父组件传递给 ``this._super()`` 调用，否则关系将无法正确设置::

    local.GreetingsWidget = instance.Widget.extend({
        init: function(parent, name) {
            this._super(parent);
            this.name = name;
        },
    });

最后，如果 widget 没有父组件（例如因为它是应用程序的根 widget），可以将 ``null`` 作为父组件提供::

    new local.GreetingsWidget(null);

销毁 Widgets
------------------

如果您能够向用户显示内容，那么您也应该能够将其删除。这可以通过 :func:`~odoo.Widget.destroy` 方法完成::

    greeting.destroy();

当一个 widget 被销毁时，它首先会在所有子组件上调用 :func:`~odoo.Widget.destroy`，然后它会从 DOM 中删除自身。如果您在 :func:`~odoo.Widget.init` 或 :func:`~odoo.Widget.start` 中设置了永久结构，需要显式清理（因为垃圾回收器无法处理它们），则可以重写 :func:`~odoo.Widget.destroy`。

.. danger::
   在重写 :func:`~odoo.Widget.destroy` 时，必须调用 ``_super()``，否则 widget 及其子组件将不会被正确清理，可能会导致内存泄漏和“幽灵事件”，即使没有显示错误。

QWeb 模板引擎
========================

在上一节中，我们通过直接操作并添加到 widget 的 DOM 中来添加内容::

    this.$el.append("<div>Hello dear Odoo user!</div>");

这种方法允许生成并显示任意类型的内容，但在生成大量 DOM 时会变得笨拙（例如大量重复、引用问题等）。

像许多其他环境一样，Odoo 的解决方案是使用 `模板引擎`_。Odoo 的模板引擎称为 :ref:`reference/qweb`。

QWeb 是基于 XML 的模板语言，类似于 `Genshi <http://en.wikipedia.org/wiki/Genshi_(templating_language)>`_、`Thymeleaf <http://en.wikipedia.org/wiki/Thymeleaf>`_ 或 `Facelets <http://en.wikipedia.org/wiki/Facelets>`_。它具有以下特性：

* 它完全用 JavaScript 实现并在浏览器中渲染
* 每个模板文件（XML 文件）包含多个模板
* 它在 Odoo Web 的 :class:`~odoo.Widget` 中有特殊支持，尽管它也可以在 Odoo Web 客户端之外使用（并且可以不依赖 QWeb 使用 :class:`~odoo.Widget`）

.. note::
   选择使用 QWeb 而不是现有的 JavaScript 模板引擎的理由在于其对现有（第三方）模板的可扩展性，类似于 Odoo :doc:`视图 <../reference/user_interface/view_records>`。

   大多数 JavaScript 模板引擎是基于文本的，这使得结构化扩展变得困难，而基于 XML 的模板引擎可以通过例如 XPath 或 CSS 和树形操作 DSL（甚至 XSLT）来通用地修改。这种灵活性和可扩展性是 Odoo 的核心特性，失去它被认为是不可接受的。

使用 QWeb
----------

首先，在几乎空的 ``oepetstore/static/src/xml/petstore.xml`` 文件中定义一个简单的 QWeb 模板：

.. code-block:: xml

    <?xml version="1.0" encoding="UTF-8"?>
    <templates xml:space="preserve">
        <t t-name="HomePageTemplate">
            <div style="background-color: red;">This is some simple HTML</div>
        </t>
    </templates>

现在可以在 ``HomePage`` widget 中使用该模板。使用页面顶部定义的 ``QWeb`` 加载器变量，可以调用 XML 文件中定义的模板::

    local.HomePage = instance.Widget.extend({
        start: function() {
            this.$el.append(QWeb.render("HomePageTemplate"));
        },
    });

:func:`QWeb.render` 查找指定的模板，将其渲染为字符串并返回结果。

不过，由于 :class:`~odoo.Widget` 对 QWeb 进行了特殊集成，模板可以直接通过 widget 的 :attr:`~odoo.Widget.template` 属性设置::

    local.HomePage = instance.Widget.extend({
        template: "HomePageTemplate",
        start: function() {
            ...
        },
    });

虽然结果看起来相似，但这两种用法有两个区别：

* 在第二种方式中，模板在调用 :func:`~odoo.Widget.start` 之前渲染
* 在第一种方式中，模板的内容被添加到 widget 的根元素，而在第二种方式中，模板的根元素直接 *被设置为* widget 的根元素。因此，“greetings”子 widget 也会获得红色背景。

.. warning::
   模板应有一个单一的非 ``t`` 根元素，尤其是作为 widget 的 :attr:`~odoo.Widget.template` 设置时。如果有多个“根元素”，结果将不确定（通常只使用第一个根元素，其他将被忽略）。

QWeb 上下文
~~~~~~~~~~~~

QWeb 模板可以接收数据并包含基本的显示逻辑。

对于显式调用 :func:`QWeb.render`，模板数据作为第二个参数传递::

    QWeb.render("HomePageTemplate", {name: "Klaus"});

将模板修改为：

.. code-block:: xml

    <t t-name="HomePageTemplate">
        <div>Hello <t t-esc="name"/></div>
    </t>

渲染结果为：

.. code-block:: html

    <div>Hello Klaus</div>

当使用 :class:`~odoo.Widget` 的集成时，无法向模板提供额外数据。模板将接收一个 ``widget`` 上下文变量，引用在调用 :func:`~odoo.Widget.start` 之前渲染的 widget（widget 的状态基本上是由 :func:`~odoo.Widget.init` 设置的）：

.. code-block:: xml

    <t t-name="HomePageTemplate">
        <div>Hello <t t-esc="widget.name"/></div>
    </t>

::

    local.HomePage = instance.Widget.extend({
        template: "HomePageTemplate",
        init: function(parent) {
            this._super(parent);
            this.name = "Mordecai";
        },
        start: function() {
        },
    });

结果：

.. code-block:: html

    <div>Hello Mordecai</div>

模板声明
~~~~~~~~~~~~~~~~~~~~

我们已经看到了如何 *渲染* QWeb 模板，现在来看一下模板本身的语法。

QWeb 模板由常规 XML 和 QWeb *指令* 组成。QWeb 指令通过 XML 属性声明，属性以 ``t-`` 开头。

最基本的指令是 ``t-name``，用于在模板文件中声明新模板：

.. code-block:: xml

    <templates>
        <t t-name="HomePageTemplate">
            <div>This is some simple HTML</div>
        </t>
    </templates>

``t-name`` 获取要定义的模板名称，并声明可以通过 :func:`QWeb.render` 调用它。它只能用于模板文件的顶级元素。

转义
~~~~~~~~

``t-esc`` 指令用于输出文本：

.. code-block:: xml

    <div>Hello <t t-esc="name"/></div>

它接收一个 JavaScript 表达式，该表达式的结果将被 HTML 转义并插入到文档中。由于它是一个表达式，可以提供一个变量名称，如上所示，也可以是更复杂的表达式，如计算：

.. code-block:: xml

    <div><t t-esc="3+5"/></div>

或方法调用：

.. code-block:: xml

    <div><t t-esc="name.toUpperCase()"/></div>

输出 HTML
~~~~~~~~~~~~~~~

要将 HTML 注入到渲染的页面中，使用 ``t-raw``。与 ``t-esc`` 类似，它接受一个任意的 JavaScript 表达式作为参数，但不会执行 HTML 转义步骤。

.. code-block:: xml

    <div><t t-raw="name.link(user_account)"/></div>

.. danger::

    ``t-raw`` *绝不* 应该用于任何可能包含未转义用户提供内容的数据，因为这会导致 `跨站脚本攻击`_ 的漏洞。

条件判断
~~~~~~~~~~~~

QWeb 可以通过 ``t-if`` 指令使用条件块。该指令接受一个任意表达式，如果表达式为假（``false``、``null``、``0`` 或空字符串），整个块将被抑制，否则将显示。

.. code-block:: xml

    <div>
        <t t-if="true == true">
            true is true
        </t>
        <t t-if="true == false">
            true is not true
        </t>
    </div>

.. note::

    QWeb 没有 "else" 结构，请使用第二个 ``t-if``，并将原始条件反转。如果表达式很复杂或开销较大，您可能希望将其存储在局部变量中。

迭代
~~~~~~~~~

要迭代一个列表，请使用 ``t-foreach`` 和 ``t-as``。``t-foreach`` 接受一个返回列表的表达式，``t-as`` 接受一个变量名，用于在迭代时绑定到每个项目。

.. code-block:: xml

    <div>
        <t t-foreach="names" t-as="name">
            <div>
                Hello <t t-esc="name"/>
            </div>
        </t>
    </div>

.. note:: ``t-foreach`` 也可以用于数字和对象（字典）。

定义属性
~~~~~~~~~~~~~~~~~~~

QWeb 提供了两个相关的指令来定义计算属性：
:samp:`t-att-{name}` 和 :samp:`t-attf-{name}`。在这两种情况下，*name* 是要创建的属性名称（例如，``t-att-id`` 定义渲染后的 ``id`` 属性）。

``t-att-`` 接受一个 JavaScript 表达式，其结果被设置为属性的值，当属性的值全部是计算得出时最有用：

.. code-block:: xml

    <div>
        输入你的名字：
        <input type="text" t-att-value="defaultName"/>
    </div>

``t-attf-`` 接受一个 *格式字符串*。格式字符串是包含插值块的文本，插值块是位于 ``{{`` 和 ``}}`` 之间的 JavaScript 表达式，该表达式的结果将替换插值块。这对于部分是字面值、部分是计算值的属性（例如类名）最为有用：

.. code-block:: xml

    <div t-attf-class="container {{ left ? 'text-left' : '' }} {{ extra_class }}">
        插入内容在此处
    </div>

调用其他模板
~~~~~~~~~~~~~~~~~~~

模板可以拆分为子模板（为简单性、可维护性、可重用性，或避免过多的标记嵌套）。

这可以通过 ``t-call`` 指令完成，它接受要渲染的模板名称：

.. code-block:: xml

    <t t-name="A">
        <div class="i-am-a">
            <t t-call="B"/>
        </div>
    </t>
    <t t-name="B">
        <div class="i-am-b"/>
    </t>

渲染 ``A`` 模板的结果将是：

.. code-block:: xml

    <div class="i-am-a">
        <div class="i-am-b"/>
    </div>

子模板继承其调用者的渲染上下文。

了解更多关于 QWeb
~~~~~~~~~~~~~~~~~~~~~~~~

有关 QWeb 的参考资料，请参阅 :ref:`reference/qweb`。

练习
~~~~~~~~

.. exercise:: 在 Widgets 中使用 QWeb

    创建一个 widget，构造函数接受两个参数（除 ``parent`` 外）：``product_names`` 和 ``color``。

    * ``product_names`` 应该是一个字符串数组，每个字符串是一个产品的名称。
    * ``color`` 是一个包含 CSS 颜色格式（例如：``#000000`` 表示黑色）的字符串。

    该 widget 应该显示给定的产品名称，每个名称都在一个单独的框中，框的背景颜色为 ``color`` 值，并带有边框。您应使用 QWeb 来渲染 HTML。任何必要的 CSS 应放置在 ``oepetstore/static/src/css/petstore.css`` 中。

    在 ``HomePage`` 中使用 widget，显示六个左右的产品。

    .. only:: solutions

        ::

            odoo.oepetstore = function(instance, local) {
                var _t = instance.web._t,
                    _lt = instance.web._lt;
                var QWeb = instance.web.qweb;

                local.HomePage = instance.Widget.extend({
                    start: function() {
                        var products = new local.ProductsWidget(
                            this, ["cpu", "mouse", "keyboard", "graphic card", "screen"], "#00FF00");
                        products.appendTo(this.$el);
                    },
                });

                local.ProductsWidget = instance.Widget.extend({
                    template: "ProductsWidget",
                    init: function(parent, products, color) {
                        this._super(parent);
                        this.products = products;
                        this.color = color;
                    },
                });

                instance.web.client_actions.add(
                    'petstore.homepage', 'instance.oepetstore.HomePage');
            }

        .. code-block:: xml

            <?xml version="1.0" encoding="UTF-8"?>
            <templates xml:space="preserve">
                <t t-name="ProductsWidget">
                    <div>
                        <t t-foreach="widget.products" t-as="product">
                            <span class="oe_products_item"
                                  t-attf-style="background-color: {{ widget.color }};">
                                <t t-esc="product"/>
                            </span>
                            <br/>
                        </t>
                    </div>
                </t>
            </templates>

        .. code-block:: css

            .oe_products_item {
                display: inline-block;
                padding: 3px;
                margin: 5px;
                border: 1px solid black;
                border-radius: 3px;
            }

        .. image:: web/qweb.png
           :align: center
           :width: 70%

Widget 辅助工具
===============

``Widget`` 的 jQuery 选择器
----------------------------

可以通过在小部件的 DOM 根上调用 ``find()`` 方法来选择小部件内的 DOM 元素::

    this.$el.find("input.my_input")...

由于这是一个常见的操作，:class:`~odoo.Widget` 提供了一个等效的快捷方式，通过 :func:`~odoo.Widget.$` 方法::

    local.MyWidget = instance.Widget.extend({
        start: function() {
            this.$("input.my_input")...
        },
    });

.. 警告::

    全局的 jQuery 函数 ``$()`` 应 *永远不* 使用，除非这是绝对必要的：在小部件的根上进行选择是针对小部件的并且是局部的，但使用 ``$()`` 进行选择是全局的，并且可能匹配其他小部件和视图的部分，导致奇怪或危险的副作用。由于小部件通常只应操作它拥有的 DOM 部分，因此没有理由进行全局选择。

更简单的 DOM 事件绑定
-------------------------

我们之前使用正常的 jQuery 事件处理程序（例如 ``.click()`` 或 ``.change()``）在小部件元素上绑定 DOM 事件::

    local.MyWidget = instance.Widget.extend({
        start: function() {
            var self = this;
            this.$(".my_button").click(function() {
                self.button_clicked();
            });
        },
        button_clicked: function() {
            ..
        },
    });

虽然这可以工作，但它有一些问题：

1. 它相当冗长
2. 它不支持在运行时替换小部件的根元素，因为绑定只在 ``start()`` 运行时进行（在小部件初始化期间）
3. 它需要处理 ``this`` 绑定问题

因此，小部件提供了一个通过 :attr:`~odoo.Widget.events` 进行 DOM 事件绑定的快捷方式::

    local.MyWidget = instance.Widget.extend({
        events: {
            "click .my_button": "button_clicked",
        },
        button_clicked: function() {
            ..
        }
    });

:attr:`~odoo.Widget.events` 是一个将事件映射到当事件触发时调用的函数或方法的对象：

* 键是事件名称，可以通过 CSS 选择器进一步细化，在这种情况下，只有当事件发生在选定的子元素上时，函数或方法才会运行：``click`` 将处理小部件内的所有点击，但 ``click .my_button`` 只处理带有 ``my_button`` 类的元素上的点击
* 值是事件触发时要执行的操作

  它可以是一个函数::

      events: {
          'click': function (e) { /* 这里是代码 */ }
      }

  也可以是对象上的方法名称（参见上面的例子）。

  在任一情况下，``this`` 都是小部件实例，处理程序将接收一个参数，即事件的 `jQuery 事件对象`_。

Widget 事件和属性
============================

事件
------

小部件提供了一个事件系统（与上面描述的 DOM/jQuery 事件系统分开）：小部件可以在自身上触发事件，其他小部件（或自身）可以绑定并监听这些事件::

    local.ConfirmWidget = instance.Widget.extend({
        events: {
            'click button.ok_button': function () {
                this.trigger('user_chose', true);
            },
            'click button.cancel_button': function () {
                this.trigger('user_chose', false);
            }
        },
        start: function() {
            this.$el.append("<div>您确定要执行此操作吗？</div>" +
                "<button class='ok_button'>确定</button>" +
                "<button class='cancel_button'>取消</button>");
        },
    });

此小部件作为一个外观，将用户输入（通过 DOM 事件）转换为可以记录的内部事件，父小部件可以绑定这些事件。

:func:`~odoo.Widget.trigger` 接受事件名称作为第一个（必需的）参数，任何进一步的参数都作为事件数据直接传递给监听器。

然后，我们可以设置一个父事件，实例化我们的通用小部件并使用 :func:`~odoo.Widget.on` 监听 ``user_chose`` 事件::

    local.HomePage = instance.Widget.extend({
        start: function() {
            var widget = new local.ConfirmWidget(this);
            widget.on("user_chose", this, this.user_chose);
            widget.appendTo(this.$el);
        },
        user_chose: function(confirm) {
            if (confirm) {
                console.log("用户同意继续");
            } else {
                console.log("用户拒绝继续");
            }
        },
    });

:func:`~odoo.Widget.on` 绑定一个函数，当事件由 ``event_name`` 标识时调用。``func`` 参数是要调用的函数，``object`` 是该函数所属的对象（如果它是一个方法）。绑定的函数将与 :func:`~odoo.Widget.trigger` 的附加参数一起调用，如果有的话。例如::

    start: function() {
        var widget = ...
        widget.on("my_event", this, this.my_event_triggered);
        widget.trigger("my_event", 1, 2, 3);
    },
    my_event_triggered: function(a, b, c) {
        console.log(a, b, c);
        // 将打印 "1 2 3"
    }

.. 注意::

    在其他小部件上触发事件通常是一个坏主意。该规则的主要例外是 ``odoo.web.bus``，它专门用于在 Odoo Web 应用程序中广播任何小部件可能感兴趣的事件。

属性
----------

属性非常类似于普通的对象属性，因为它们允许在小部件实例上存储数据，然而，它们有一个额外的功能，即设置值时会触发事件::

    start: function() {
        this.widget = ...
        this.widget.on("change:name", this, this.name_changed);
        this.widget.set("name", "Nicolas");
    },
    name_changed: function() {
        console.log("属性 'name' 的新值是", this.widget.get("name"));
    }

* :func:`~odoo.Widget.set` 设置属性的值并触发 :samp:`change:{propname}`（其中 *propname* 是传递给 :func:`~odoo.Widget.set` 的属性名称）和 ``change`` 事件
* :func:`~odoo.Widget.get` 检索属性的值。

练习
--------

.. exercise:: 小部件属性和事件

    创建一个 ``ColorInputWidget`` 小部件，它将显示 3 个 ``<input type="text">``。每个 ``<input>`` 用于输入一个从 00 到 FF 的十六进制数字。当用户修改任何一个 ``<input>`` 时，小部件必须查询这三个 ``<input>`` 的内容，将它们的值拼接起来，形成一个完整的 CSS 颜色代码（例如：``#00FF00``），并将结果存储在一个名为 ``color`` 的属性中。请注意，jQuery 的 ``change()`` 事件可以绑定到任何 HTML 的 ``<input>`` 元素上，``val()`` 方法可以查询该 ``<input>`` 的当前值，这对于完成该练习可能非常有用。

    然后，修改 ``HomePage`` 小部件以实例化 ``ColorInputWidget`` 并显示它。 ``HomePage`` 小部件还应显示一个空矩形。该矩形应始终与 ``ColorInputWidget`` 实例的 ``color`` 属性中的颜色保持一致。

    使用 QWeb 生成所有 HTML。

    .. only:: solutions

        ::

            odoo.oepetstore = function(instance, local) {
                var _t = instance.web._t,
                    _lt = instance.web._lt;
                var QWeb = instance.web.qweb;

                local.ColorInputWidget = instance.Widget.extend({
                    template: "ColorInputWidget",
                    events: {
                        'change input': 'input_changed'
                    },
                    start: function() {
                        this.input_changed();
                        return this._super();
                    },
                    input_changed: function() {
                        var color = [
                            "#",
                            this.$(".oe_color_red").val(),
                            this.$(".oe_color_green").val(),
                            this.$(".oe_color_blue").val()
                        ].join('');
                        this.set("color", color);
                    },
                });

                local.HomePage = instance.Widget.extend({
                    template: "HomePage",
                    start: function() {
                        this.colorInput = new local.ColorInputWidget(this);
                        this.colorInput.on("change:color", this, this.color_changed);
                        return this.colorInput.appendTo(this.$el);
                    },
                    color_changed: function() {
                        this.$(".oe_color_div").css("background-color", this.colorInput.get("color"));
                    },
                });

                instance.web.client_actions.add('petstore.homepage', 'instance.oepetstore.HomePage');
            }

        .. code-block:: xml

            <?xml version="1.0" encoding="UTF-8"?>
            <templates xml:space="preserve">
                <t t-name="ColorInputWidget">
                    <div>
                        红色: <input type="text" class="oe_color_red" value="00"></input><br />
                        绿色: <input type="text" class="oe_color_green" value="00"></input><br />
                        蓝色: <input type="text" class="oe_color_blue" value="00"></input><br />
                    </div>
                </t>
                <t t-name="HomePage">
                    <div>
                        <div class="oe_color_div"></div>
                    </div>
                </t>
            </templates>

        .. code-block:: css

            .oe_color_div {
                width: 100px;
                height: 100px;
                margin: 10px;
            }

修改现有的小部件和类
===================================

Odoo web 框架的类系统允许使用 :func:`~odoo.web.Class.include` 方法直接修改现有的类::

    var TestClass = instance.web.Class.extend({
        testMethod: function() {
            return "hello";
        },
    });

    TestClass.include({
        testMethod: function() {
            return this._super() + " world";
        },
    });

    console.log(new TestClass().testMethod());
    // 将打印 "hello world"

这个系统类似于继承机制，不同之处在于它会在原位修改目标类，而不是创建一个新类。

在这种情况下，``this._super()`` 将调用被替换/重新定义的方法的原始实现。如果类已经有子类，所有在子类中对 ``this._super()`` 的调用都将调用在调用 :func:`~odoo.web.Class.include` 时定义的新实现。如果该类（或其任何子类）的某些实例是在调用 :func:`~odoo.Widget.include` 之前创建的，这种方法也将起作用。

翻译
============

在 Python 和 JavaScript 代码中翻译文本的过程非常相似。你可能已经注意到在 ``petstore.js`` 文件开头有这些行::

    var _t = instance.web._t,
        _lt = instance.web._lt;

这些行仅用于在当前 JavaScript 模块中导入翻译函数。它们的用法如下::

    this.$el.text(_t("Hello user!"));

在 Odoo 中，翻译文件是通过扫描源代码自动生成的。所有调用某个函数的代码段都会被检测到，它们的内容将添加到翻译文件中，然后会发送给翻译人员。在 Python 中，函数是 ``_()``。在 JavaScript 中，函数是 :func:`~odoo.web._t`（以及 :func:`~odoo.web._lt`）。

``_t()`` 将返回定义的翻译。如果没有为该文本定义翻译，它将按原样返回原始文本。

.. note::

    为了在可翻译的字符串中注入用户提供的值，建议在翻译之后使用带命名参数的 `_.str.sprintf <http://gabceb.github.io/underscore.string.site/#sprintf>`_::

        this.$el.text(_.str.sprintf(
            _t("Hello, %(user)s!"), {
            user: "Ed"
        }));

    这使得可翻译的字符串对翻译人员更具可读性，并使他们有更多的灵活性来重新排序或忽略参数。

:func:`~odoo.web._lt`（"lazy translate"）与 ``_t`` 类似，但稍微复杂一些：它不会立即翻译其参数，而是返回一个对象，该对象在转换为字符串时将执行翻译。

它用于在翻译系统初始化之前定义可翻译的术语，例如类属性（因为模块是在用户的语言配置和翻译文件下载之前加载的）。
与 Odoo 服务器通信
=========================

与模型交互
-----------------

大多数 Odoo 的操作都涉及与实现业务需求的*模型*通信，这些模型随后可能会与某些存储引擎（通常是 PostgreSQL_）进行交互。

尽管 jQuery_ 提供了用于网络交互的 `$.ajax`_ 函数，但与 Odoo 通信需要额外的元数据，每次调用之前都要进行设置，这会非常冗长且容易出错。因此，Odoo web 提供了更高级别的通信原语。

为了演示这一点，文件 ``petstore.py`` 中已经包含了一个带有示例方法的小模型：

.. code-block:: python

    class message_of_the_day(models.Model):
        _name = "oepetstore.message_of_the_day"

        @api.model
        def my_method(self):
            return {"hello": "world"}

        message = fields.Text(),
        color = fields.Char(size=20),

这声明了一个具有两个字段的模型，以及一个返回字典的 ``my_method()`` 方法。

以下是调用 ``my_method()`` 并显示结果的示例小部件::

    local.HomePage = instance.Widget.extend({
        start: function() {
            var self = this;
            var model = new instance.web.Model("oepetstore.message_of_the_day");
            model.call("my_method", {context: new instance.web.CompoundContext()}).then(function(result) {
                self.$el.append("<div>Hello " + result["hello"] + "</div>");
                // 将向用户显示 "Hello world"
            });
        },
    });

用于调用 Odoo 模型的类是 :class:`odoo.Model`。它的第一个参数是 Odoo 模型的名称（这里是 ``oepetstore.message_of_the_day``）。

:func:`~odoo.web.Model.call` 可用于调用 Odoo 模型的任何（公共）方法。它接收以下位置参数：

``name``
  要调用的方法名称，这里是 ``my_method``。
``args``
  一个数组，用于向方法提供`位置参数`_。由于示例中没有需要提供的位置参数，因此未提供 ``args`` 参数。

  以下是带有位置参数的另一个示例：

  .. code-block:: python

      @api.model
      def my_method2(self, a, b, c): ...

  .. code-block:: javascript

      model.call("my_method", [1, 2, 3], ...
      // 这时 a=1，b=2，c=3

``kwargs``
  一个映射，用于传递 `关键字参数`_。示例中提供了一个名为 ``context`` 的参数。

  .. code-block:: python

      @api.model
      def my_method2(self, a, b, c): ...

  .. code-block:: javascript

      model.call("my_method", [], {a: 1, b: 2, c: 3, ...
      // 这时 a=1，b=2，c=3

:func:`~odoo.Widget.call` 返回一个延迟对象，解析后的第一个参数是模型方法返回的值。
与 Odoo 服务器的通信
======================

与模型交互
----------------

大多数与 Odoo 的操作都涉及与实现业务逻辑的*模型*通信，这些模型可能与某些存储引擎（通常是 PostgreSQL_）进行交互。

虽然 jQuery_ 提供了 `$.ajax`_ 函数进行网络交互，但与 Odoo 的通信需要额外的元数据，如果每次调用前都进行设置将会非常冗长且容易出错。因此，Odoo web 提供了更高级的通信原语。

为了演示这一点，文件 ``petstore.py`` 中已经包含了一个带有示例方法的小模型：

.. code-block:: python

    class message_of_the_day(models.Model):
        _name = "oepetstore.message_of_the_day"

        @api.model
        def my_method(self):
            return {"hello": "world"}

        message = fields.Text(),
        color = fields.Char(size=20),

这个模型声明了两个字段，并且包含一个返回字典的 ``my_method()`` 方法。

以下是调用 ``my_method()`` 并显示结果的示例小部件::

    local.HomePage = instance.Widget.extend({
        start: function() {
            var self = this;
            var model = new instance.web.Model("oepetstore.message_of_the_day");
            model.call("my_method", {context: new instance.web.CompoundContext()}).then(function(result) {
                self.$el.append("<div>Hello " + result["hello"] + "</div>");
                // 将向用户显示 "Hello world"
            });
        },
    });

用于调用 Odoo 模型的类是 :class:`odoo.Model`。它的第一个参数是 Odoo 模型的名称（此处为 ``oepetstore.message_of_the_day``）。

:func:`~odoo.web.Model.call` 可用于调用 Odoo 模型的任何（公共）方法。它接收以下位置参数：

``name``
  要调用的方法名称，这里是 ``my_method``。
``args``
  一个数组，用于向方法提供`位置参数`。由于示例中没有需要提供的位置参数，因此未提供 ``args`` 参数。

  下面是一个带有位置参数的示例：

  .. code-block:: python

      @api.model
      def my_method2(self, a, b, c): ...

  .. code-block:: javascript

      model.call("my_method", [1, 2, 3], ...
      // 这时 a=1，b=2，c=3

``kwargs``
  一个映射，用于传递`关键字参数`。在示例中，提供了一个名为 ``context`` 的参数。

  .. code-block:: python

      @api.model
      def my_method2(self, a, b, c): ...

  .. code-block:: javascript

      model.call("my_method", [], {a: 1, b: 2, c: 3, ...
      // 这时 a=1，b=2，c=3

:func:`~odoo.Widget.call` 返回一个延迟对象，其第一个解析参数是模型方法返回的值。

CompoundContext
----------------

前一节中使用了一个未解释的 ``context`` 参数：

.. code-block:: javascript

    model.call("my_method", {context: new instance.web.CompoundContext()})

context 就像是一个 "魔法" 参数，Web 客户端在调用方法时总是会将其传递给服务器。context 是一个包含多个键的字典，其中一个重要的键是用户的语言，服务器使用该键来翻译应用程序的所有消息。另一个重要的键是用户的时区，如果 Odoo 被不同国家的用户使用，则用于正确计算日期和时间。

这个参数在所有方法中都是必要的，否则可能会发生错误（例如应用程序无法正确翻译）。因此，当您调用模型的方法时，始终应该提供该参数。解决方法是使用 :class:`odoo.web.CompoundContext`。

:class:`~odoo.web.CompoundContext` 是用于将用户的 context（语言、时区等）传递给服务器的类，还可以向 context 中添加新键（某些模型方法使用添加到 context 的任意键）。它可以通过其构造函数传递任意数量的字典或其他 :class:`~odoo.web.CompoundContext` 实例。在发送给服务器之前，它将合并所有这些上下文。

.. code-block:: javascript

    model.call("my_method", {context: new instance.web.CompoundContext({'new_key': 'key_value'})})

.. code-block:: python

    @api.model
    def my_method(self):
        print(self.env.context)
        // 将打印: {'lang': 'en_US', 'new_key': 'key_value', 'tz': 'Europe/Brussels', 'uid': 1}

你可以看到 ``context`` 参数中的字典包含了一些与当前 Odoo 用户配置相关的键，以及在实例化 :class:`~odoo.web.CompoundContext` 时添加的 ``new_key`` 键。

查询
--------

尽管 :func:`~odoo.Model.call` 已经足够用于与 Odoo 模型的所有交互，但 Odoo Web 提供了一个帮助工具，简化并清晰化了对模型的查询（根据各种条件获取记录）：:func:`~odoo.Model.query`，它是常见组合 :py:meth:`~odoo.models.Model.search` 和 :py:meth:`~odoo.models.Model.read` 的快捷方式。它提供了一种更清晰的语法来搜索和读取模型：

.. code-block:: javascript

    model.query(['name', 'login', 'user_email', 'signature'])
         .filter([['active', '=', true], ['company_id', '=', main_company]])
         .limit(15)
         .all().then(function (users) {
        // 使用 users 记录进行操作
    });

与以下相比：

.. code-block:: javascript

    model.call('search', [['active', '=', true], ['company_id', '=', main_company]], {limit: 15})
        .then(function (ids) {
            return model.call('read', [ids, ['name', 'login', 'user_email', 'signature']]);
        })
        .then(function (users) {
            // 使用 users 记录进行操作
        });

* :func:`~odoo.web.Model.query` 接收一个可选的字段列表作为参数（如果未提供字段，则获取模型的所有字段）。它返回一个 :class:`odoo.web.Query`，可以在执行之前进一步自定义。
* :class:`~odoo.web.Query` 表示正在构建的查询。它是不可变的，自定义查询的方法实际上返回一个修改后的副本，因此可以并排使用原始查询和新版本。有关其自定义选项，请参阅 :class:`~odoo.web.Query`。

当查询设置完毕后，只需调用 :func:`~odoo.web.Query.all` 来执行它，并返回结果的延迟对象。结果与 :py:meth:`~odoo.models.Model.read` 相同，是字典数组，其中每个字典表示一个请求的记录，每个请求的字段都是字典的键。

练习
=====

.. exercise:: 今日消息

    创建一个 ``MessageOfTheDay`` 小部件，显示 ``oepetstore.message_of_the_day`` 模型的最后一条记录。小部件应在显示时立即获取其记录。

    将该小部件显示在宠物店主页上。

    .. only:: solutions

        .. code-block:: javascript

            odoo.oepetstore = function(instance, local) {
                var _t = instance.web._t,
                    _lt = instance.web._lt;
                var QWeb = instance.web.qweb;

                local.HomePage = instance.Widget.extend({
                    template: "HomePage",
                    start: function() {
                        return new local.MessageOfTheDay(this).appendTo(this.$el);
                    },
                });

                instance.web.client_actions.add('petstore.homepage', 'instance.oepetstore.HomePage');

                local.MessageOfTheDay = instance.Widget.extend({
                    template: "MessageOfTheDay",
                    start: function() {
                        var self = this;
                        return new instance.web.Model("oepetstore.message_of_the_day")
                            .query(["message"])
                            .order_by('-create_date', '-id')
                            .first()
                            .then(function(result) {
                                self.$(".oe_mywidget_message_of_the_day").text(result.message);
                            });
                    },
                });

            }

        .. code-block:: xml

            <?xml version="1.0" encoding="UTF-8"?>
            <templates xml:space="preserve">
                <t t-name="HomePage">
                    <div class="oe_petstore_homepage">
                    </div>
                </t>
                <t t-name="MessageOfTheDay">
                    <div class="oe_petstore_motd">
                        <p class="oe_mywidget_message_of_the_day"></p>
                    </div>
                </t>
            </templates>

        .. code-block:: css

            .oe_petstore_motd {
                margin: 5px;
                padding: 5px;
                border-radius: 3px;
                background-color: #F0EEEE;
            }

现有的 Web 组件
================

操作管理器
------------

在 Odoo 中，许多操作都从一个 :doc:`操作 <../reference/backend/actions>` 开始，例如打开菜单项（到视图）、打印报告等。

操作是描述客户端如何响应某一内容激活的数据片段。操作可以存储（通过模型读取），也可以动态生成（由 JavaScript 代码在客户端本地生成，或通过模型方法在远程生成）。

在 Odoo Web 中，负责处理和响应这些操作的组件是 *操作管理器*。

使用操作管理器
~~~~~~~~~~~~~~~~

可以通过在 JavaScript 代码中显式调用操作管理器，创建一个描述 :doc:`操作 <../reference/backend/actions>` 的字典，并用它调用操作管理器实例。

:func:`~odoo.Widget.do_action` 是 :class:`~odoo.Widget` 的一个快捷方式，它查找“当前”操作管理器并执行操作::

    instance.web.TestWidget = instance.Widget.extend({
        dispatch_to_new_action: function() {
            this.do_action({
                type: 'ir.actions.act_window',
                res_model: "product.product",
                res_id: 1,
                views: [[false, 'form']],
                target: 'current',
                context: {},
            });
        },
    });

最常见的操作 ``type`` 是 ``ir.actions.act_window``，它提供了用于模型的视图（以各种方式显示模型），其最常见的属性有：

``res_model``
  要在视图中显示的模型
``res_id`` （可选）
  对于表单视图，``res_model`` 中预先选择的记录
``views``
  列出操作中可用的视图。它是一个 ``[view_id, view_type]`` 的列表，``view_id`` 可以是合适类型的视图的数据库标识符，也可以是 ``false``，表示使用该类型的默认视图。视图类型不能重复出现。操作将默认打开列表中的第一个视图。
``target``
  可以是 ``current``（默认值），将替换 Web 客户端的“内容”部分为该操作，或是 ``new``，以对话框的形式打开操作。
``context``
  在操作中使用的附加上下文数据。

.. exercise:: 跳转到产品

    修改 ``PetToysList`` 组件，使点击玩具时，主页被该玩具的表单视图替代。

    .. only:: solutions

        .. code-block:: javascript

            local.PetToysList = instance.Widget.extend({
                template: 'PetToysList',
                events: {
                    'click .oe_petstore_pettoy': 'selected_item',
                },
                start: function () {
                    var self = this;
                    return new instance.web.Model('product.product')
                        .query(['name', 'image'])
                        .filter([['categ_id.name', '=', "Pet Toys"]])
                        .limit(5)
                        .all()
                        .then(function (results) {
                            _(results).each(function (item) {
                                self.$el.append(QWeb.render('PetToy', {item: item}));
                            });
                        });
                },
                selected_item: function (event) {
                    this.do_action({
                        type: 'ir.actions.act_window',
                        res_model: 'product.product',
                        res_id: $(event.currentTarget).data('id'),
                        views: [[false, 'form']],
                    });
                },
            });

        .. code-block:: xml

            <t t-name="PetToy">
                <div class="oe_petstore_pettoy" t-att-data-id="item.id">
                    <p><t t-esc="item.name"/></p>
                    <p><img t-attf-src="data:image/jpg;base64,{{item.image}}"/></p>
                </div>
            </t>

.. _howtos/web/client_actions:
客户端操作
--------------

在本指南中，我们使用了一个简单的 ``HomePage`` 小部件，当我们选择正确的菜单项时，Web 客户端会自动启动该小部件。那么 Odoo Web 是如何知道要启动这个小部件的呢？这是因为该小部件被注册为一个 *客户端操作*。

顾名思义，客户端操作是一种几乎完全在客户端（JavaScript）中定义的操作类型，专为 Odoo Web 设计。服务器仅仅发送一个操作标签（任意名称），并可选地添加一些参数，除此之外 *一切* 都由自定义客户端代码处理。

我们的部件通过以下方式注册为客户端操作的处理程序::

    instance.web.client_actions.add('petstore.homepage', 'instance.oepetstore.HomePage');

``instance.web.client_actions`` 是一个 :class:`~odoo.web.Registry`，当操作管理器需要执行一个客户端操作时，它会在其中查找客户端操作处理程序。:class:`~odoo.web.Registry.add` 的第一个参数是客户端操作的名称（标签），第二个参数是 Odoo Web 客户端根路径到小部件的路径。

当必须执行客户端操作时，操作管理器会在注册表中查找其标签，按照指定的路径查找并显示路径末端的小部件。

.. note:: 客户端操作处理程序也可以是一个常规函数，在这种情况下，它会被调用，其结果（如果有）将被解释为要执行的下一个操作。

在服务器端，我们简单地定义了一个 ``ir.actions.client`` 操作：

.. code-block:: xml

    <record id="action_home_page" model="ir.actions.client">
        <field name="tag">petstore.homepage</field>
    </record>

并定义了一个菜单来打开此操作：

.. code-block:: xml

    <menuitem id="home_page_petstore_menu" parent="petstore_menu"
              name="Home Page" action="action_home_page"/>

视图的架构
-------------------------

Odoo Web 的许多有用功能（以及复杂性）都体现在视图中。每种视图类型都是一种在客户端显示模型的方式。

视图管理器
~~~~~~~~~~~~~~~~

当 ``ActionManager`` 实例收到 ``ir.actions.act_window`` 类型的操作时，它会将视图本身的同步和处理委托给一个 *视图管理器*，然后视图管理器会根据原始操作的要求设置一个或多个视图：

.. image:: web/viewarchitecture.png
   :align: center
   :width: 40%
视图
~~~~~~~~~

大多数 :doc:`Odoo 视图 <../reference/user_interface/view_records>` 通过 :class:`odoo.web.View` 的子类实现，后者为处理事件和显示模型信息提供了一些通用的基础结构。

*搜索视图* 被 Odoo 主框架认为是一种视图类型，但在 Web 客户端中单独处理（因为它是一个更为固定的部分，可以与其他视图进行交互，而常规视图则不会这样做）。

视图负责加载自己的描述 XML（使用 :py:class:`~odoo.models.Model.fields_view_get`）以及它需要的任何其他数据源。为此，视图会被提供一个可选的视图标识符，作为 :attr:`~odoo.web.View.view_id` 属性。

视图还会被提供一个 :class:`~odoo.web.DataSet` 实例，该实例包含大多数必要的模型信息（模型名称和可能的各种记录 ID）。

视图可能还需要通过重写 :func:`~odoo.web.View.do_search` 来处理搜索查询，并根据需要更新其 :class:`~odoo.web.DataSet`。

表单视图中的字段
--------------------

常见的需求是扩展 Web 表单视图，以添加新的显示字段的方式。

所有内置字段都有默认的显示实现，可能需要一个新的表单小部件来正确与新字段类型交互（例如，:term:`GIS` 字段）或提供现有字段类型的新表示和交互方式（例如验证 :py:class:`~odoo.fields.Char` 字段是否应包含电子邮件地址并将其显示为电子邮件链接）。

要明确指定哪个表单小部件应用于显示某个字段，只需在视图的 XML 描述中使用 ``widget`` 属性：

.. code-block:: xml

    <field name="contact_mail" widget="email"/>

.. note::

    * 在表单视图的"查看"（只读）和"编辑"模式中，使用的是相同的小部件，无法在一个模式中使用一个小部件，而在另一个模式中使用其他小部件。
    * 同一字段（名称）不能在同一个表单中多次使用。
    * 小部件可能会忽略表单视图的当前模式，并在查看和编辑模式中保持不变。

.. todo:: 这些大多数应该移至高级表单视图指南。

字段由表单视图实例化，表单视图读取其 XML 描述并构建代表该描述的相应 HTML。之后，表单视图将通过某些方法与字段对象进行通信。这些方法由 ``FieldInterface`` 接口定义。几乎所有字段都继承自 ``AbstractField`` 抽象类。该类定义了大多数字段需要实现的一些默认机制。

以下是字段类的一些职责：

* 字段类必须显示并允许用户编辑字段的值。
* 它必须正确实现 Odoo 中所有字段都具有的 3 个字段属性。``AbstractField`` 类已经实现了一个算法，可以动态计算这些属性的值（它们的值可能会随其他字段的值的变化而随时改变）。这些值存储在 *小部件属性* 中（小部件属性在本指南前面已经解释过）。每个字段类有责任检查这些小部件属性，并根据其值动态适应。以下是对每个属性的描述：

  * ``required``: 字段在保存前必须有值。如果 ``required`` 为 ``true`` 且字段没有值，则字段的 ``is_valid()`` 方法必须返回 ``false``。
  * ``invisible``: 当此属性为 ``true`` 时，字段必须是不可见的。``AbstractField`` 类已经对大多数字段实现了此行为的基本版本。
  * ``readonly``: 当 ``true`` 时，用户不得编辑字段。Odoo 中的大多数字段在 ``readonly`` 的值不同的情况下会有完全不同的行为。例如，``FieldChar`` 在可编辑时显示为 HTML ``<input>``，而在只读时仅显示文本。这也意味着它需要实现更多代码才能只实现一种行为，但这是确保良好用户体验所必需的。

* 字段有两个方法，``set_value()`` 和 ``get_value()``，表单视图调用这些方法以提供要显示的值并获取用户输入的新值。这些方法必须能够处理 Odoo 服务器在对模型执行 ``read()`` 时提供的值，并返回适合 ``write()`` 的有效值。请记住，在 Odoo 中，用于表示 ``read()`` 给出的值和传递给 ``write()`` 的 JavaScript/Python 数据类型不一定相同。例如，当读取 many2one 时，它始终是一个元组，其中第一个值是指向的记录的 ID，第二个是名称（例如：``(15, "Agrolait")``）。但在写入 many2one 时，它必须是单个整数，而不是元组。``AbstractField`` 对这些方法的默认实现适用于简单的数据类型，并设置了名为 ``value`` 的小部件属性。

请注意，要更好地理解如何实现字段，强烈建议您直接在 Odoo Web 客户端的代码中查看 ``FieldInterface`` 接口和 ``AbstractField`` 类的定义。
创建新类型的字段
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

在这一部分中，我们将解释如何创建新类型的字段。这里的例子是重新实现 ``FieldChar`` 类，并逐步解释每个部分。

简单的只读字段
**********************

这是一个仅显示文本的初步实现，用户无法修改字段的内容。

.. code-block:: javascript

    local.FieldChar2 = instance.web.form.AbstractField.extend({
        init: function() {
            this._super.apply(this, arguments);
            this.set("value", "");
        },
        render_value: function() {
            this.$el.text(this.get("value"));
        },
    });

    instance.web.form.widgets.add('char2', 'instance.oepetstore.FieldChar2');

在这个例子中，我们声明了一个名为 ``FieldChar2`` 的类，它继承自 ``AbstractField``。我们还在注册表 ``instance.web.form.widgets`` 中将这个类注册为 ``char2`` 键。这使我们能够在任何表单视图中通过在 XML 声明的 ``<field/>`` 标签中指定 ``widget="char2"`` 来使用这个新字段。

在这个例子中，我们只定义了一个方法：``render_value()``。该方法仅显示小部件属性 ``value``。这些工具由 ``AbstractField`` 类定义。正如前面所解释的，表单视图会调用字段的 ``set_value()`` 方法来设置显示的值。此方法在 ``AbstractField`` 中已经有默认实现，它只是简单地设置小部件属性 ``value``。``AbstractField`` 还会监听自身上的 ``change:value`` 事件，并在发生时调用 ``render_value()``。因此，``render_value()`` 是一个方便的方法，用于在字段值每次发生变化时执行某些操作。

在 ``init()`` 方法中，我们还定义了字段的默认值（如果表单视图未指定任何值），在这里我们假设 ``char`` 字段的默认值应为空字符串。

可读写的字段
****************

只读字段，只显示内容且不允许用户修改，可能会有用，但 Odoo 中的大多数字段也允许编辑。这使得字段类更复杂，主要是因为字段应当处理可编辑和不可编辑模式，且这些模式通常完全不同（为了设计和可用性考虑），字段必须能够随时在这两种模式之间切换。

为了知道当前字段应该处于哪种模式，``AbstractField`` 类设置了一个名为 ``effective_readonly`` 的小部件属性。字段应该监听该小部件属性的变化，并根据需要正确地显示模式。例如：

.. code-block:: javascript

    local.FieldChar2 = instance.web.form.AbstractField.extend({
        init: function() {
            this._super.apply(this, arguments);
            this.set("value", "");
        },
        start: function() {
            this.on("change:effective_readonly", this, function() {
                this.display_field();
                this.render_value();
            });
            this.display_field();
            return this._super();
        },
        display_field: function() {
            var self = this;
            this.$el.html(QWeb.render("FieldChar2", {widget: this}));
            if (! this.get("effective_readonly")) {
                this.$("input").change(function() {
                    self.internal_set_value(self.$("input").val());
                });
            }
        },
        render_value: function() {
            if (this.get("effective_readonly")) {
                this.$el.text(this.get("value"));
            } else {
                this.$("input").val(this.get("value"));
            }
        },
    });

    instance.web.form.widgets.add('char2', 'instance.oepetstore.FieldChar2');

.. code-block:: xml

    <t t-name="FieldChar2">
        <div class="oe_field_char2">
            <t t-if="! widget.get('effective_readonly')">
                <input type="text"></input>
            </t>
        </div>
    </t>

在 ``start()`` 方法中（它在小部件被附加到 DOM 后立即调用），我们绑定了 ``change:effective_readonly`` 事件。这允许我们在小部件属性 ``effective_readonly`` 发生变化时重新显示字段。此事件处理程序将调用 ``display_field()``，该方法也直接在 ``start()`` 中调用。这个 ``display_field()`` 是为此字段专门创建的，它不是 ``AbstractField`` 或任何其他类中定义的方法。我们可以使用此方法根据当前模式显示字段的内容。

从现在开始，设计此字段是典型的，除了有许多检查用于知道 ``effective_readonly`` 属性的状态：

* 在用于显示小部件内容的 QWeb 模板中，如果我们处于读写模式，则显示一个 ``<input type="text" />``，而在只读模式中不显示任何特殊内容。
* 在 ``display_field()`` 方法中，我们必须绑定 ``<input type="text" />`` 的 ``change`` 事件，以便知道用户何时更改了值。当这种情况发生时，我们调用 ``internal_set_value()`` 方法，并传入字段的新值。这是由 ``AbstractField`` 类提供的方便方法。该方法会在 ``value`` 属性中设置新值，但不会触发调用 ``render_value()``（这不是必要的，因为 ``<input type="text" />`` 已经包含了正确的值）。
* 在 ``render_value()`` 中，我们使用完全不同的代码来显示字段的值，具体取决于我们是处于只读模式还是读写模式。

.. exercise:: 创建颜色字段

    创建一个 ``FieldColor`` 类。该字段的值应该是一个字符串，包含一个类似于 CSS 中使用的颜色代码（例如：``#FF0000`` 表示红色）。在只读模式下，此颜色字段应显示一个小块，其颜色与字段的值相对应。在读写模式下，你应该显示一个 ``<input type="color" />``。这种 ``<input />`` 类型是一个 HTML5 组件，并不适用于所有浏览器，但在 Google Chrome 中运行良好。作为练习使用它是可以的。

    你可以在 ``message_of_the_day`` 模型的表单视图中为名为 ``color`` 的字段使用该小部件。作为奖励，你可以更改在本指南前面部分创建的 ``MessageOfTheDay`` 小部件，以根据 ``color`` 字段中指示的背景颜色显示当天的信息。

    .. only:: solutions

        .. code-block:: javascript

            local.FieldColor = instance.web.form.AbstractField.extend({
                events: {
                    'change input': function (e) {
                        if (!this.get('effective_readonly')) {
                            this.internal_set_value($(e.currentTarget).val());
                        }
                    }
                },
                init: function() {
                    this._super.apply(this, arguments);
                    this.set("value", "");
                },
                start: function() {
                    this.on("change:effective_readonly", this, function() {
                        this.display_field();
                        this.render_value();
                    });
                    this.display_field();
                    return this._super();
                },
                display_field: function() {
                    this.$el.html(QWeb.render("FieldColor", {widget: this}));
                },
                render_value: function() {
                    if (this.get("effective_readonly")) {
                        this.$(".oe_field_color_content").css("background-color", this.get("value") || "#FFFFFF");
                    } else {
                        this.$("input").val(this.get("value") || "#FFFFFF");
                    }
                },
            });
            instance.web.form.widgets.add('color', 'instance.oepetstore.FieldColor');

        .. code-block:: xml

            <t t-name="FieldColor">
                <div class="oe_field_color">
                    <t t-if="widget.get('effective_readonly')">
                        <div class="oe_field_color_content" />
                    </t>
                    <t t-if="! widget.get('effective_readonly')">
                        <input type="color"></input>
                    </t>
                </div>
            </t>

        .. code-block:: css

            .oe_field_color_content {
                height: 20px;
                width: 50px;
                border: 1px solid black;
            }
表单视图自定义小部件
--------------------

表单字段用于编辑单个字段，并且与字段紧密关联。由于这种约束，有时可能会觉得限制性较强。因此，也可以创建*表单小部件*，这些小部件与特定字段的生命周期关联较小并且限制更少。

自定义表单小部件可以通过 ``widget`` 标签添加到表单视图中：

.. code-block:: xml

    <widget type="xxx" />

这种类型的小部件将通过表单视图在创建 HTML 时根据 XML 定义创建。它们与字段有一些共同属性（例如 ``effective_readonly`` 属性），但它们未分配到具体的字段。因此，它们没有类似 ``get_value()`` 和 ``set_value()`` 这样的方法。它们必须继承自 ``FormWidget`` 抽象类。

表单小部件可以通过监听字段的更改来与表单字段交互，并获取或更改字段的值。它们可以通过其 :attr:`~odoo.web.form.FormWidget.field_manager` 属性访问表单字段：

.. code-block:: javascript

    local.WidgetMultiplication = instance.web.form.FormWidget.extend({
        start: function() {
            this._super();
            this.field_manager.on("field_changed:integer_a", this, this.display_result);
            this.field_manager.on("field_changed:integer_b", this, this.display_result);
            this.display_result();
        },
        display_result: function() {
            var result = this.field_manager.get_field_value("integer_a") *
                         this.field_manager.get_field_value("integer_b");
            this.$el.text("a*b = " + result);
        }
    });

    instance.web.form.custom_widgets.add('multiplication', 'instance.oepetstore.WidgetMultiplication');

:attr:`~odoo.web.form.FormWidget` 通常是 :class:`~odoo.web.form.FormView` 本身，但应尽量限制使用其中的功能，主要使用 :class:`~odoo.web.form.FieldManagerMixin` 定义的功能，其中最有用的是：

* :func:`~odoo.web.form.FieldManagerMixin.get_field_value(field_name)` 返回字段的值。
* :func:`~odoo.web.form.FieldManagerMixin.set_values(values)` 设置多个字段值，接收一个 ``{field_name: value_to_set}`` 的映射。
* 任何时候字段的值改变时，都会触发事件 :samp:`field_changed:{field_name}`。

.. exercise:: 在 Google 地图上显示坐标

    给 ``product.product`` 添加两个字段，分别存储纬度和经度，然后创建一个新的表单小部件，用于显示产品起源地的纬度和经度在地图上。

    要显示地图，使用 Google Map 的嵌入代码：

    .. code-block:: html

        <iframe width="400" height="300" src="https://maps.google.com/?ie=UTF8&amp;ll=XXX,YYY&amp;output=embed">
        </iframe>

    其中 ``XXX`` 应替换为纬度，``YYY`` 应替换为经度。

    在产品的表单视图中新建一个标签页来显示这两个位置字段和地图小部件。

    .. only:: solutions

        .. code-block:: javascript

            local.WidgetCoordinates = instance.web.form.FormWidget.extend({
                start: function() {
                    this._super();
                    this.field_manager.on("field_changed:provider_latitude", this, this.display_map);
                    this.field_manager.on("field_changed:provider_longitude", this, this.display_map);
                    this.display_map();
                },
                display_map: function() {
                    this.$el.html(QWeb.render("WidgetCoordinates", {
                        "latitude": this.field_manager.get_field_value("provider_latitude") || 0,
                        "longitude": this.field_manager.get_field_value("provider_longitude") || 0,
                    }));
                }
            });

            instance.web.form.custom_widgets.add('coordinates', 'instance.oepetstore.WidgetCoordinates');

        .. code-block:: xml

            <t t-name="WidgetCoordinates">
                <iframe width="400" height="300"
                    t-attf-src="https://maps.google.com/?ie=UTF8&amp;ll={{latitude}},{{longitude}}&amp;output=embed">
                </iframe>
            </t>

.. exercise:: 获取当前位置的坐标

    添加一个按钮，用于将产品的坐标重置为用户的当前位置。可以使用 `javascript geolocation API`_ 获取这些坐标。

    我们希望添加一个额外的按钮，自动将坐标设置为当前用户的位置。

    要获取用户的坐标，简单的方法是使用地理定位的 JavaScript API。`请参阅在线文档了解如何使用它`_。

    .. _请参阅在线文档了解如何使用它: http://www.w3schools.com/html/html5_geolocation.asp

    还请注意，当表单视图处于只读模式时，用户不应能够点击该按钮。因此，这个自定义小部件应该正确处理 ``effective_readonly`` 属性，就像任何字段一样。可以使按钮在 ``effective_readonly`` 为真时消失。

    .. only:: solutions

        .. code-block:: javascript

            local.WidgetCoordinates = instance.web.form.FormWidget.extend({
                events: {
                    'click button': function () {
                        navigator.geolocation.getCurrentPosition(
                            this.proxy('received_position'));
                    }
                },
                start: function() {
                    var sup = this._super();
                    this.field_manager.on("field_changed:provider_latitude", this, this.display_map);
                    this.field_manager.on("field_changed:provider_longitude", this, this.display_map);
                    this.on("change:effective_readonly", this, this.display_map);
                    this.display_map();
                    return sup;
                },
                display_map: function() {
                    this.$el.html(QWeb.render("WidgetCoordinates", {
                        "latitude": this.field_manager.get_field_value("provider_latitude") || 0,
                        "longitude": this.field_manager.get_field_value("provider_longitude") || 0,
                    }));
                    this.$("button").toggle(! this.get("effective_readonly"));
                },
                received_position: function(obj) {
                    this.field_manager.set_values({
                        "provider_latitude": obj.coords.latitude,
                        "provider_longitude": obj.coords.longitude,
                    });
                },
            });

            instance.web.form.custom_widgets.add('coordinates', 'instance.oepetstore.WidgetCoordinates');

        .. code-block:: xml

            <t t-name="WidgetCoordinates">
                <iframe width="400" height="300"
                    t-attf-src="https://maps.google.com/?ie=UTF8&amp;ll={{latitude}},{{longitude}}&amp;output=embed">
                </iframe>
                <button>获取我的当前位置</button>
            </t>
            
.. [#classes] 作为实例的概念。在许多语言中，类是完全成熟的对象，并且它们本身是（元类的）实例，但类和实例之间往往存在两个相对独立的层次结构。
.. [#dombugs] 以及跨浏览器差异的修补，尽管随着时间的推移，这变得不那么必要了。

.. _jQuery: http://jquery.org
.. _Underscore.js: http://underscorejs.org
.. _git: http://git-scm.com
.. _CSS: http://www.w3.org/Style/CSS/Overview.en.html
.. _Simple JavaScript Inheritance:
    http://ejohn.org/blog/simple-javascript-inheritance/
.. _W3C DOM: http://www.w3.org/TR/DOM-Level-3-Core/
.. _Qt: http://qt-project.org
.. _Cocoa: https://developer.apple.com/technologies/mac/cocoa.html
.. _GTK: http://www.gtk.org
.. _template engine: http://en.wikipedia.org/wiki/Web_template_system
.. _cross-site scripting: http://en.wikipedia.org/wiki/Cross-site_scripting
.. _jQuery event object: http://api.jquery.com/category/events/event-object/
.. _$.ajax: http://api.jquery.com/jquery.ajax/
.. _base64: http://en.wikipedia.org/wiki/Base64
.. _javascript geolocation API:
    http://diveintohtml5.info/geolocation.html
.. _PostgreSQL: http://en.wikipedia.org/wiki/PostgreSQL
.. _positional arguments:
.. _keyword arguments:
    https://docs.python.org/2/glossary.html#term-argument
