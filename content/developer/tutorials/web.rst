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


Exercise
--------

.. exercise:: Widget Properties and Events

    Create a widget ``ColorInputWidget`` that will display 3 ``<input
    type="text">``. Each of these ``<input>`` is dedicated to type a
    hexadecimal number from 00 to FF. When any of these ``<input>`` is
    modified by the user the widget must query the content of the three
    ``<input>``, concatenate their values to have a complete CSS color code
    (ie: ``#00FF00``) and put the result in a property named ``color``. Please
    note the jQuery ``change()`` event that you can bind on any HTML
    ``<input>`` element and the ``val()`` method that can query the current
    value of that ``<input>`` could be useful to you for this exercise.

    Then, modify the ``HomePage`` widget to instantiate ``ColorInputWidget``
    and display it. The ``HomePage`` widget should also display an empty
    rectangle. That rectangle must always, at any moment, have the same
    background color as the color in the ``color`` property of the
    ``ColorInputWidget`` instance.

    Use QWeb to generate all HTML.

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
                        Red: <input type="text" class="oe_color_red" value="00"></input><br />
                        Green: <input type="text" class="oe_color_green" value="00"></input><br />
                        Blue: <input type="text" class="oe_color_blue" value="00"></input><br />
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

Modify existing widgets and classes
===================================

The class system of the Odoo web framework allows direct modification of
existing classes using the :func:`~odoo.web.Class.include` method::

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
    // will print "hello world"

This system is similar to the inheritance mechanism, except it will alter the
target class in-place instead of creating a new class.

In that case, ``this._super()`` will call the original implementation of a
method being replaced/redefined. If the class already had sub-classes, all
calls to ``this._super()`` in sub-classes will call the new implementations
defined in the call to :func:`~odoo.web.Class.include`. This will also work
if some instances of the class (or of any of its sub-classes) were created
prior to the call to :func:`~odoo.Widget.include`.

Translations
============

The process to translate text in Python and JavaScript code is very
similar. You could have noticed these lines at the beginning of the
``petstore.js`` file::

    var _t = instance.web._t,
        _lt = instance.web._lt;

These lines are simply used to import the translation functions in the current
JavaScript module. They are used thus::

    this.$el.text(_t("Hello user!"));

In Odoo, translations files are automatically generated by scanning the source
code. All piece of code that calls a certain function are detected and their
content is added to a translation file that will then be sent to the
translators. In Python, the function is ``_()``. In JavaScript the function is
:func:`~odoo.web._t` (and also :func:`~odoo.web._lt`).

``_t()`` will return the translation defined for the text it is given. If no
translation is defined for that text, it will return the original text as-is.

.. note::

    To inject user-provided values in translatable strings, it is recommended
    to use `_.str.sprintf
    <http://gabceb.github.io/underscore.string.site/#sprintf>`_ with named
    arguments *after* the translation::

        this.$el.text(_.str.sprintf(
            _t("Hello, %(user)s!"), {
            user: "Ed"
        }));

    This makes translatable strings more readable to translators, and gives
    them more flexibility to reorder or ignore parameters.

:func:`~odoo.web._lt` ("lazy translate") is similar but somewhat more
complex: instead of translating its parameter immediately, it returns
an object which, when converted to a string, will perform the translation.

It is used to define translatable terms before the translations system is
initialized, for class attributes for instance (as modules are loaded before
the user's language is configured and translations are downloaded).

Communication with the Odoo Server
==================================

Contacting Models
-----------------

Most operations with Odoo involve communicating with *models* implementing
business concern, these models will then (potentially) interact with some
storage engine (usually PostgreSQL_).

Although jQuery_ provides a `$.ajax`_ function for network interactions,
communicating with Odoo requires additional metadata whose setup before every
call would be verbose and error-prone. As a result, Odoo web provides
higher-level communication primitives.

To demonstrate this, the file ``petstore.py`` already contains a small model
with a sample method:

.. code-block:: python

    class message_of_the_day(models.Model):
        _name = "oepetstore.message_of_the_day"

        @api.model
        def my_method(self):
            return {"hello": "world"}

        message = fields.Text(),
        color = fields.Char(size=20),

This declares a model with two fields, and a method ``my_method()`` which
returns a literal dictionary.

Here is a sample widget that calls ``my_method()`` and displays the result::

    local.HomePage = instance.Widget.extend({
        start: function() {
            var self = this;
            var model = new instance.web.Model("oepetstore.message_of_the_day");
            model.call("my_method", {context: new instance.web.CompoundContext()}).then(function(result) {
                self.$el.append("<div>Hello " + result["hello"] + "</div>");
                // will show "Hello world" to the user
            });
        },
    });

The class used to call Odoo models is :class:`odoo.Model`. It is
instantiated with the Odoo model's name as first parameter
(``oepetstore.message_of_the_day`` here).

:func:`~odoo.web.Model.call` can be used to call any (public) method of an
Odoo model. It takes the following positional arguments:

``name``
  The name of the method to call, ``my_method`` here
``args``
  an array of `positional arguments`_ to provide to the method. Because the
  example has no positional argument to provide, the ``args`` parameter is not
  provided.

  Here is an other example with positional arguments:

  .. code-block:: python

      @api.model
      def my_method2(self, a, b, c): ...

  .. code-block:: javascript

      model.call("my_method", [1, 2, 3], ...
      // with this a=1, b=2 and c=3

``kwargs``
  a mapping of `keyword arguments`_ to pass. The example provides a single
  named argument ``context``.

  .. code-block:: python

      @api.model
      def my_method2(self, a, b, c): ...

  .. code-block:: javascript

      model.call("my_method", [], {a: 1, b: 2, c: 3, ...
      // with this a=1, b=2 and c=3

:func:`~odoo.Widget.call` returns a deferred resolved with the value
returned by the model's method as first argument.

CompoundContext
---------------

The previous section used a ``context`` argument which was not explained in
the method call::

    model.call("my_method", {context: new instance.web.CompoundContext()})

The context is like a "magic" argument that the web client will always give to
the server when calling a method. The context is a dictionary containing
multiple keys. One of the most important key is the language of the user, used
by the server to translate all the messages of the application. Another one is
the time zone of the user, used to compute correctly dates and times if Odoo
is used by people in different countries.

The ``argument`` is necessary in all methods, otherwise bad things could
happen (such as the application not being translated correctly). That's why,
when you call a model's method, you should always provide that argument. The
solution to achieve that is to use :class:`odoo.web.CompoundContext`.

:class:`~odoo.web.CompoundContext` is a class used to pass the user's
context (with language, time zone, etc...) to the server as well as adding new
keys to the context (some models' methods use arbitrary keys added to the
context). It is created by giving to its constructor any number of
dictionaries or other :class:`~odoo.web.CompoundContext` instances. It will
merge all those contexts before sending them to the server.

.. code-block:: javascript

    model.call("my_method", {context: new instance.web.CompoundContext({'new_key': 'key_value'})})

.. code-block:: python

    @api.model
    def my_method(self):
        print(self.env.context)
        // will print: {'lang': 'en_US', 'new_key': 'key_value', 'tz': 'Europe/Brussels', 'uid': 1}

You can see the dictionary in the argument ``context`` contains some keys that
are related to the configuration of the current user in Odoo plus the
``new_key`` key that was added when instantiating
:class:`~odoo.web.CompoundContext`.

Queries
-------

While :func:`~odoo.Model.call` is sufficient for any interaction with Odoo
models, Odoo Web provides a helper for simpler and clearer querying of models
(fetching of records based on various conditions):
:func:`~odoo.Model.query` which acts as a shortcut for the common
combination of :py:meth:`~odoo.models.Model.search` and
::py:meth:`~odoo.models.Model.read`. It provides a clearer syntax to search
and read models::

    model.query(['name', 'login', 'user_email', 'signature'])
         .filter([['active', '=', true], ['company_id', '=', main_company]])
         .limit(15)
         .all().then(function (users) {
        // do work with users records
    });

versus::

    model.call('search', [['active', '=', true], ['company_id', '=', main_company]], {limit: 15})
        .then(function (ids) {
            return model.call('read', [ids, ['name', 'login', 'user_email', 'signature']]);
        })
        .then(function (users) {
            // do work with users records
        });

* :func:`~odoo.web.Model.query` takes an optional list of fields as
  parameter (if no field is provided, all fields of the model are fetched). It
  returns a :class:`odoo.web.Query` which can be further customized before
  being executed
* :class:`~odoo.web.Query` represents the query being built. It is
  immutable, methods to customize the query actually return a modified copy,
  so it's possible to use the original and the new version side-by-side. See
  :class:`~odoo.web.Query` for its customization options.

When the query is set up as desired, simply call
:func:`~odoo.web.Query.all` to execute it and return a
deferred to its result. The result is the same as
:py:meth:`~odoo.models.Model.read`'s, an array of dictionaries where each
dictionary is a requested record, with each requested field a dictionary key.

Exercises
=========

.. exercise:: Message of the Day

    Create a ``MessageOfTheDay``  widget displaying the last record of the
    ``oepetstore.message_of_the_day`` model. The widget should fetch its
    record as soon as it is displayed.

    Display the widget in the Pet Store home page.

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

.. exercise:: Pet Toys List

    Create a ``PetToysList`` widget displaying 5 toys (using their name and
    their images).

    The pet toys are not stored in a new model, instead they're stored in
    ``product.product`` using a special category *Pet Toys*. You can see the
    pre-generated toys and add new ones by going to
    :menuselection:`Pet Store --> Pet Store --> Pet Toys`. You will probably
    need to explore ``product.product`` to create the right domain to
    select just pet toys.

    In Odoo, images are generally stored in regular fields encoded as
    base64_, HTML supports displaying images straight from base64 with
    :samp:`<img src="data:{mime_type};base64,{base64_image_data}"/>`

    The ``PetToysList`` widget should be displayed on the home page on the
    right of the ``MessageOfTheDay`` widget. You will need to make some layout
    with CSS to achieve this.

    .. only:: solutions

        .. code-block:: javascript

            odoo.oepetstore = function(instance, local) {
                var _t = instance.web._t,
                    _lt = instance.web._lt;
                var QWeb = instance.web.qweb;

                local.HomePage = instance.Widget.extend({
                    template: "HomePage",
                    start: function () {
                        return Promise.all([
                            new local.PetToysList(this).appendTo(this.$('.oe_petstore_homepage_left')),
                            new local.MessageOfTheDay(this).appendTo(this.$('.oe_petstore_homepage_right'))
                        ]);
                    }
                });
                instance.web.client_actions.add('petstore.homepage', 'instance.oepetstore.HomePage');

                local.MessageOfTheDay = instance.Widget.extend({
                    template: 'MessageOfTheDay',
                    start: function () {
                        var self = this;
                        return new instance.web.Model('oepetstore.message_of_the_day')
                            .query(["message"])
                            .order_by('-create_date', '-id')
                            .first()
                            .then(function (result) {
                                self.$(".oe_mywidget_message_of_the_day").text(result.message);
                            });
                    }
                });

                local.PetToysList = instance.Widget.extend({
                    template: 'PetToysList',
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
                    }
                });
            }

        .. code-block:: xml

            <?xml version="1.0" encoding="UTF-8"?>

            <templates xml:space="preserve">
                <t t-name="HomePage">
                    <div class="oe_petstore_homepage">
                        <div class="oe_petstore_homepage_left"></div>
                        <div class="oe_petstore_homepage_right"></div>
                    </div>
                </t>
                <t t-name="MessageOfTheDay">
                    <div class="oe_petstore_motd">
                        <p class="oe_mywidget_message_of_the_day"></p>
                    </div>
                </t>
                <t t-name="PetToysList">
                    <div class="oe_petstore_pettoyslist">
                    </div>
                </t>
                <t t-name="PetToy">
                    <div class="oe_petstore_pettoy">
                        <p><t t-esc="item.name"/></p>
                        <p><img t-att-src="'data:image/jpg;base64,'+item.image"/></p>
                    </div>
                </t>
            </templates>

        .. code-block:: css

            .oe_petstore_homepage {
                display: table;
            }

            .oe_petstore_homepage_left {
                display: table-cell;
                width : 300px;
            }

            .oe_petstore_homepage_right {
                display: table-cell;
                width : 300px;
            }

            .oe_petstore_motd {
                margin: 5px;
                padding: 5px;
                border-radius: 3px;
                background-color: #F0EEEE;
            }

            .oe_petstore_pettoyslist {
                padding: 5px;
            }

            .oe_petstore_pettoy {
                margin: 5px;
                padding: 5px;
                border-radius: 3px;
                background-color: #F0EEEE;
            }


Existing web components
=======================

The Action Manager
------------------

In Odoo, many operations start from an :doc:`action <../reference/backend/actions>`:
opening a menu item (to a view), printing a report, ...

Actions are pieces of data describing how a client should react to the
activation of a piece of content. Actions can be stored (and read through a
model) or they can be generated on-the fly (locally to the client by
javascript code, or remotely by a method of a model).

In Odoo Web, the component responsible for handling and reacting to these
actions is the *Action Manager*.

Using the Action Manager
~~~~~~~~~~~~~~~~~~~~~~~~

The action manager can be invoked explicitly from javascript code by creating
a dictionary describing :doc:`an action <../reference/backend/actions>` of the right
type, and calling an action manager instance with it.

:func:`~odoo.Widget.do_action` is a shortcut of :class:`~odoo.Widget`
looking up the "current" action manager and executing the action::

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

The most common action ``type`` is ``ir.actions.act_window`` which provides
views to a model (displays a model in various manners), its most common
attributes are:

``res_model``
  The model to display in views
``res_id`` (optional)
  For form views, a preselected record in ``res_model``
``views``
  Lists the views available through the action. A list of
  ``[view_id, view_type]``, ``view_id`` can either be the database identifier
  of a view of the right type, or ``false`` to use the view by default for
  the specified type. View types can not be present multiple times. The action
  will open the first view of the list by default.
``target``
  Either ``current`` (the default) which replaces the "content" section of the
  web client by the action, or ``new`` to open the action in a dialog box.
``context``
  Additional context data to use within the action.

.. exercise:: Jump to Product

    Modify the ``PetToysList`` component so clicking on a toy replaces the
    homepage by the toy's form view.

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

Client Actions
--------------

Throughout this guide, we used a simple ``HomePage`` widget which the web
client automatically starts when we select the right menu item. But how did
the Odoo web know to start this widget? Because the widget is registered as
a *client action*.

A client action is (as its name implies) an action type defined almost
entirely in the client, in javascript for Odoo web. The server simply sends
an action tag (an arbitrary name), and optionally adds a few parameters, but
beyond that *everything* is handled by custom client code.

Our widget is registered as the handler for the client action through this::

    instance.web.client_actions.add('petstore.homepage', 'instance.oepetstore.HomePage');


``instance.web.client_actions`` is a :class:`~odoo.web.Registry` in which
the action manager looks up client action handlers when it needs to execute
one. The first parameter of :class:`~odoo.web.Registry.add` is the name
(tag) of the client action, and the second parameter is the path to the widget
from the Odoo web client root.

When a client action must be executed, the action manager looks up its tag
in the registry, walks the specified path and displays the widget it finds at
the end.

.. note:: a client action handler can also be a regular function, in which case
          it'll be called and its result (if any) will be interpreted as the
          next action to execute.

On the server side, we had simply defined an ``ir.actions.client`` action:

.. code-block:: xml

    <record id="action_home_page" model="ir.actions.client">
        <field name="tag">petstore.homepage</field>
    </record>

and a menu opening the action:

.. code-block:: xml

    <menuitem id="home_page_petstore_menu" parent="petstore_menu"
              name="Home Page" action="action_home_page"/>

Architecture of the Views
-------------------------

Much of Odoo web's usefulness (and complexity) resides in views. Each view
type is a way of displaying a model in the client.

The View Manager
~~~~~~~~~~~~~~~~

When an ``ActionManager`` instance receive an action of type
``ir.actions.act_window``, it delegates the synchronization and handling of
the views themselves to a *view manager*, which will then set up one or
multiple views depending on the original action's requirements:

.. image:: web/viewarchitecture.png
   :align: center
   :width: 40%

The Views
~~~~~~~~~

Most :doc:`Odoo views <../reference/user_interface/view_records>` are implemented through a subclass
of :class:`odoo.web.View` which provides a bit of generic basic structure
for handling events and displaying model information.

The *search view* is considered a view type by the main Odoo framework, but
handled separately by the web client (as it's a more permanent fixture and
can interact with other views, which regular views don't do).

A view is responsible for loading its own description XML (using
:py:class:`~odoo.models.Model.fields_view_get`) and any other data source
it needs. To that purpose, views are provided with an optional view
identifier set as the :attr:`~odoo.web.View.view_id` attribute.

Views are also provided with a :class:`~odoo.web.DataSet` instance which
holds most necessary model information (the model name and possibly various
record ids).

Views may also want to handle search queries by overriding
:func:`~odoo.web.View.do_search`, and updating their
:class:`~odoo.web.DataSet` as necessary.

The Form View Fields
--------------------

A common need is the extension of the web form view to add new ways of
displaying fields.

All built-in fields have a default display implementation, a new
form widget may be necessary to correctly interact with a new field type
(e.g. a :term:`GIS` field) or to provide new representations and ways to
interact with existing field types (e.g. validate
:py:class:`~odoo.fields.Char` fields which should contain email addresses
and display them as email links).

To explicitly specify which form widget should be used to display a field,
simply use the ``widget`` attribute in the view's XML description:

.. code-block:: xml

    <field name="contact_mail" widget="email"/>

.. note::

    * the same widget is used in both "view" (read-only) and "edit" modes
      of a form view, it's not possible to use a widget in one and an other
      widget in the other
    * and a given field (name) can not be used multiple times in the same form
    * a widget may ignore the current mode of the form view and remain the
      same in both view and edit modes

.. todo:: most of this should probably move to an advanced form view guide

Fields are instantiated by the form view after it has read its XML description
and constructed the corresponding HTML representing that description. After
that, the form view will communicate with the field objects using some
methods. These methods are defined by the ``FieldInterface``
interface. Almost all fields inherit the ``AbstractField`` abstract
class. That class defines some default mechanisms that need to be implemented
by most fields.

Here are some of the responsibilities of a field class:

* The field class must display and allow the user to edit the value of the field.
* It must correctly implement the 3 field attributes available in all fields
  of Odoo. The ``AbstractField`` class already implements an algorithm that
  dynamically calculates the value of these attributes (they can change at any
  moment because their value change according to the value of other
  fields). Their values are stored in *Widget Properties* (the widget
  properties were explained earlier in this guide). It is the responsibility
  of each field class to check these widget properties and dynamically adapt
  depending of their values. Here is a description of each of these
  attributes:

  * ``required``: The field must have a value before saving. If ``required``
    is ``true`` and the field doesn't have a value, the method
    ``is_valid()`` of the field must return ``false``.
  * ``invisible``: When this is ``true``, the field must be invisible. The
    ``AbstractField`` class already has a basic implementation of this
    behavior that fits most fields.
  * ``readonly``: When ``true``, the field must not be editable by the
    user. Most fields in Odoo have a completely different behavior depending
    on the value of ``readonly``. As example, the ``FieldChar`` displays an
    HTML ``<input>`` when it is editable and simply displays the text when
    it is read-only. This also means it has much more code it would need to
    implement only one behavior, but this is necessary to ensure a good user
    experience.

* Fields have two methods, ``set_value()`` and ``get_value()``, which are
  called by the form view to give it the value to display and get back the new
  value entered by the user. These methods must be able to handle the value as
  given by the Odoo server when a ``read()`` is performed on a model and give
  back a valid value for a ``write()``.  Remember that the JavaScript/Python
  data types used to represent the values given by ``read()`` and given to
  ``write()`` is not necessarily the same in Odoo. As example, when you read a
  many2one, it is always a tuple whose first value is the id of the pointed
  record and the second one is the name get (ie: ``(15, "Agrolait")``). But
  when you write a many2one it must be a single integer, not a tuple
  anymore. ``AbstractField`` has a default implementation of these methods
  that works well for simple data type and set a widget property named
  ``value``.

Please note that, to better understand how to implement fields, you are
strongly encouraged to look at the definition of the ``FieldInterface``
interface and the ``AbstractField`` class directly in the code of the Odoo web
client.

Creating a New Type of Field
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

In this part we will explain how to create a new type of field. The example
here will be to re-implement the ``FieldChar`` class and progressively explain
each part.

Simple Read-Only Field
**********************

Here is a first implementation that will only display text. The
user will not be able to modify the content of the field.

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

In this example, we declare a class named ``FieldChar2`` inheriting from
``AbstractField``. We also register this class in the registry
``instance.web.form.widgets`` under the key ``char2``. That will allow us to
use this new field in any form view by specifying ``widget="char2"`` in the
``<field/>`` tag in the XML declaration of the view.

In this example, we define a single method: ``render_value()``. All it does is
display the widget property ``value``.  Those are two tools defined by the
``AbstractField`` class. As explained before, the form view will call the
method ``set_value()`` of the field to set the value to display. This method
already has a default implementation in ``AbstractField`` which simply sets
the widget property ``value``. ``AbstractField`` also watch the
``change:value`` event on itself and calls the ``render_value()`` when it
occurs. So, ``render_value()`` is a convenience method to implement in child
classes to perform some operation each time the value of the field changes.

In the ``init()`` method, we also define the default value of the field if
none is specified by the form view (here we assume the default value of a
``char`` field should be an empty string).

Read-Write Field
****************

Read-only fields, which only display content and don't allow the
user to modify it can be useful, but most fields in Odoo also allow editing.
This makes the field classes more complicated, mostly because fields are
supposed to handle both editable and non-editable mode, those modes are
often completely different (for design and usability purpose) and the fields
must be able to switch between modes at any moment.

To know in which mode the current field should be, the ``AbstractField`` class
sets a widget property named ``effective_readonly``. The field should watch
for changes in that widget property and display the correct mode
accordingly. Example::

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

In the ``start()`` method (which is called immediately after a widget has been
appended to the DOM), we bind on the event ``change:effective_readonly``. That
allows us to redisplay the field each time the widget property
``effective_readonly`` changes. This event handler will call
``display_field()``, which is also called directly in ``start()``. This
``display_field()`` was created specifically for this field, it's not a method
defined in ``AbstractField`` or any other class. We can use this method
to display the content of the field depending on the current mode.

From now on the conception of this field is typical, except there is a
lot of verifications to know the state of the ``effective_readonly`` property:

* In the QWeb template used to display the content of the widget, it displays
  an ``<input type="text" />`` if we are in read-write mode and nothing in
  particular in read-only mode.
* In the ``display_field()`` method, we have to bind on the ``change`` event
  of the ``<input type="text" />`` to know when the user has changed the
  value. When it happens, we call the ``internal_set_value()`` method with the
  new value of the field. This is a convenience method provided by the
  ``AbstractField`` class. That method will set a new value in the ``value``
  property but will not trigger a call to ``render_value()`` (which is not
  necessary since the ``<input type="text" />`` already contains the correct
  value).
* In ``render_value()``, we use a completely different code to display the
  value of the field depending if we are in read-only or in read-write mode.

.. exercise:: Create a Color Field

    Create a ``FieldColor`` class. The value of this field should be a string
    containing a color code like those used in CSS (example: ``#FF0000`` for
    red). In read-only mode, this color field should display a little block
    whose color corresponds to the value of the field. In read-write mode, you
    should display an ``<input type="color" />``. That type of ``<input />``
    is an HTML5 component that doesn't work in all browsers but works well in
    Google Chrome. So it's OK to use as an exercise.

    You can use that widget in the form view of the ``message_of_the_day``
    model for its field named ``color``. As a bonus, you can change the
    ``MessageOfTheDay`` widget created in the previous part of this guide to
    display the message of the day with the background color indicated in the
    ``color`` field.

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

The Form View Custom Widgets
----------------------------

Form fields are used to edit a single field, and are intrinsically linked to
a field. Because this may be limiting, it is also possible to create
*form widgets* which are not so restricted and have less ties to a specific
lifecycle.

Custom form widgets can be added to a form view through the ``widget`` tag:

.. code-block:: xml

    <widget type="xxx" />

This type of widget will simply be created by the form view during the
creation of the HTML according to the XML definition. They have properties in
common with the fields (like the ``effective_readonly`` property) but they are
not assigned a precise field. And so they don't have methods like
``get_value()`` and ``set_value()``. They must inherit from the ``FormWidget``
abstract class.

Form widgets can interact with form fields by listening for their changes and
fetching or altering their values. They can access form fields through
their :attr:`~odoo.web.form.FormWidget.field_manager` attribute::

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

:attr:`~odoo.web.form.FormWidget` is generally the
:class:`~odoo.web.form.FormView` itself, but features used from it should
be limited to those defined by :class:`~odoo.web.form.FieldManagerMixin`,
the most useful being:

* :func:`~odoo.web.form.FieldManagerMixin.get_field_value(field_name)`
  which returns the value of a field.
* :func:`~odoo.web.form.FieldManagerMixin.set_values(values)` sets multiple
  field values, takes a mapping of ``{field_name: value_to_set}``
* An event :samp:`field_changed:{field_name}` is triggered any time the value
  of the field called ``field_name`` is changed

.. exercise:: Show Coordinates on Google Map

    Add two fields to ``product.product`` storing a latitude and a longitude,
    then create a new form widget to display the latitude and longitude of
    a product's origin on a map

    To display the map, use Google Map's embedding:

    .. code-block:: html

        <iframe width="400" height="300" src="https://maps.google.com/?ie=UTF8&amp;ll=XXX,YYY&amp;output=embed">
        </iframe>

    where ``XXX`` should be replaced by the latitude and ``YYY`` by the
    longitude.

    Display the two position fields and a map widget using them in a new
    notebook page of the product's form view.

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

.. exercise:: Get the Current Coordinate

    Add a button resetting the product's coordinates to the location of the
    user, you can get these coordinates using the
    `javascript geolocation API`_.

    Now we would like to display an additional button to automatically set the
    coordinates to the location of the current user.

    To get the coordinates of the user, an easy way is to use the geolocation
    JavaScript API.  `See the online documentation to know how to use it`_.

    .. _See the online documentation to know how to use it: http://www.w3schools.com/html/html5_geolocation.asp

    Please also note that the user should not be able to
    click on that button when the form view is in read-only mode. So, this
    custom widget should handle correctly the ``effective_readonly`` property
    just like any field. One way to do this would be to make the button
    disappear when ``effective_readonly`` is true.

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
                <button>Get My Current Coordinate</button>
            </t>

.. [#classes] as a separate concept from instances. In many languages classes
              are full-fledged objects and themselves instance (of
              metaclasses) but there remains two fairly separate hierarchies
              between classes and instances
.. [#dombugs] as well as papering over cross-browser differences, although
              this has become less necessary over time

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
