.. highlight:: javascript

.. default-domain:: js

====================
Javascript Reference
====================

本文档介绍了 Odoo JavaScript 框架。该框架在代码行数上并不是一个大型应用程序，但它相当通用，因为它基本上是一个将声明性接口描述转变为实时应用程序的机器，能够与数据库中的每个模型和记录进行交互。甚至可以使用网页客户端来修改网页客户端的界面。

Overview
========

JavaScript 框架旨在处理三个主要用例：

- *网页客户端*：这是私有 web 应用程序，用户可以查看和编辑业务数据。这是一个单页面应用程序（页面永远不会重新加载，只有在需要时才从服务器获取新数据）。
- *网站*：这是 Odoo 的公共部分。它允许未识别的用户浏览一些内容、购物或执行许多操作，作为客户。这是一个经典的网站：具有各种路由和控制器及一些 JavaScript 以使其正常工作。
- *销售点*：这是销售点的界面。它是一个专用的单页面应用程序。

一些 JavaScript 代码在这三种用例中是通用的，并被捆绑在一起（见下面的资产部分）。本文档将主要关注网页客户端的架构。

Web client
==========

Single Page Application
-----------------------

网页客户端是一个单页面应用程序：用户执行操作时，不是从服务器请求完整页面，而是仅加载更新用户界面（UI）所需的内容。在此过程中，它还负责更新 URL 中的信息，因此，在大多数情况下，刷新页面或关闭浏览器后重新打开页面将显示相同的内容。

Overview of web client JS code
------------------------------

在这里，我们快速概述网页客户端代码，位于 :file:`web` 附加模块。路径将相对于 :file:`web/static/src` 进行描述。以下描述故意不详尽；目标只是给读者一个整体的架构视图。

- :file:`module_loader.js`：这是定义 Odoo JavaScript 模块系统的文件。它需要在任何其他 JS 模块之前加载。
- :file:`core/`：该文件夹包含构成 JavaScript 框架最低级别的代码，可用于网页客户端、网站、门户和销售点应用程序。
- :file:`webclient/`：该文件夹包含特定于网页客户端的文件，不能在网站或销售点中使用，例如操作管理器和操作服务。
- :file:`webclient/webclient.js`：这是网页客户端组件本身。它主要是操作容器和导航栏的包装器，并执行启动应用程序时所需的一些操作，例如加载 URL 的状态。
- :file:`webclient/actions/`：该文件夹包含负责显示和切换操作的代码。
- :file:`views/`：该文件夹包含视图基础设施的代码以及大多数视图（某些类型的视图由其他附加模块添加）。
- :file:`views/fields/`：包含各种字段组件的定义，以及多个字段使用的一些实用程序。
- :file:`search/`：所有这些文件定义搜索视图（从网页客户端的角度看，这不是视图，仅从服务器的角度看）。

What to do if a file is not loaded/updated
------------------------------------------

有许多不同的原因可能导致文件未正确加载。以下是一些可以尝试解决该问题的方法：

- 确保您已保存文件；忘记保存发生在我们每个人身上。
- 查看控制台（在开发工具中，通常通过 F12 打开），检查是否有错误。
- 尝试在文件的开头添加 `console.log()`，以便查看文件是否已加载。如果未加载，可能是因为它不在正确的资产捆绑中，或者资产捆绑可能未更新。
- 根据您的设置，服务器可能在文件修改后不会重新生成资产捆绑；有几种选项可以解决此问题：

  - 重启服务器将强制其在下次请求资产捆绑时检查是否最新。
  - 在调试模式下，调试菜单中有一个选项（导航栏中的 :icon:`fa-bug` 按钮），可以强制服务器在不重启的情况下动态生成资产捆绑。
  - 使用 `--dev=xml` 选项启动服务器将强制服务器在每次请求资产捆绑时检查是否最新。我们建议您在主动开发时使用此选项，但在生产中不要使用。

- 更改代码后，确保刷新页面。 Odoo 当前没有任何热模块重载机制。

Loading Javascript Code
=======================

大型应用程序通常分解为较小的文件，这些文件需要连接在一起。有些文件可能需要使用其他文件中定义的代码。共享代码有两种方法：

- 使用全局作用域（*window* 对象）读取/写入对某些对象或函数的引用。

- 使用模块系统，为每个模块提供一种导出或导入值的方式，并确保它们以适当的顺序加载。

虽然可以在全局作用域中工作，但这存在许多问题：

- 难以确保实现细节不被暴露：全局作用域中的函数声明对所有其他代码可访问。

- 只有一个命名空间，容易发生命名冲突。

- 依赖关系是隐式的：如果某段代码依赖于另一段代码，则加载的顺序很重要，但很难保证。

使用模块系统有助于解决这些问题：因为模块指定了它们的依赖关系，模块系统可以以适当的顺序加载它们，或者在缺失或循环依赖时发出错误。模块还形成自己的命名空间，并可以选择导出内容，从而防止暴露实现细节和命名冲突。

虽然我们可以直接使用 ECMAScript (ES) 模块，但这种方法有许多缺点：每个 ES 模块需要网络往返，当您拥有数百个文件时，这变得非常慢，许多 Odoo 中的文件需要存在，尽管没有被任何东西导入，因为它们仅仅是为框架使用的代码，而不是反之。

因此，Odoo 有一个资产捆绑系统。在这些捆绑中，JavaScript 文件是带有特殊注释的 ES 模块。这些模块将被捆绑在一起并转译为可供我们的模块加载器使用。虽然您可以编写不使用此模块系统的代码，但通常不推荐这样做。

（见 :ref:`frontend/modules/native_js`）

Patching classes
----------------

虽然我们尽力提供不需要的扩展点，但有时必须“就地”修改现有类的行为。目标是拥有一个机制来更改类及所有未来/当前实例。这是通过使用 `patch` 工具函数来完成的：

.. code-block:: javascript

    /** @odoo-module */
    import { Hamster } from "@web/core/hamster";
    import { patch } from "@web/core/utils/patch";

    patch(Hamster.prototype, {
        sleep() {
            super.sleep(...arguments);
            console.log("zzzz");
        },
    });

在打补丁方法时，您需要打补丁类的原型，但如果您想打补丁类的静态属性，则需要打补丁类本身。

打补丁是一项危险的操作，应该谨慎进行，因为它会修改类的所有实例，即使它们已经被创建。为了避免奇怪的问题，补丁应尽早应用，在模块的顶层进行。运行时对类进行打补丁可能会导致极其难以调试的问题，如果该类已经实例化。

Registries
==========

在 Odoo 生态系统中，常见的需求是从外部扩展/更改基本系统的行为（通过安装应用程序，即不同模块）。例如，您可能需要在某些视图中添加新的字段小部件。在这种情况下，以及许多其他情况下，通常的过程是创建所需的组件，然后将其添加到注册表中（注册步骤），以使其余的网页客户端意识到它的存在。

系统中有几个注册表。框架使用的注册表是主注册表中的类别，可以从 :js:data:`@web/core/registry` 导入。

- 字段注册表
  字段注册表包含网页客户端已知的所有字段小部件。每当视图（通常是表单或列表/看板）需要字段小部件时，这里就是它要查找的地方。一个典型的用例如下所示：

  .. code-block:: javascript

      import { registry } from "@web/core/registry";
      class PadField extends Component { ... }

      registry.category("fields").add("pad", {
        component: PadField,
        supportedTypes: ["char"],
        // ...
      });

- 视图注册表
  此注册表包含网页客户端已知的所有 JS 视图。

- 操作注册表
  我们在此注册表中跟踪所有客户端操作。每当操作管理器需要创建客户端操作时，它会在这里查找。客户端操作可以是一个函数——当调用操作时将调用该函数，返回的值将在需要时作为后续操作执行——或在执行该操作时将显示的 Owl 组件。

Services
========

在网页客户端中，有些问题无法由单个组件处理，因为该问题是横向的，涉及多个组件，或需要在整个应用程序的生命周期内保持某些状态。

服务是解决这些问题的方案：它们在应用程序启动期间创建，通过钩子 `useService` 使组件可用，并在应用程序的整个生命周期内保持活跃。

例如，我们有 *orm* 服务，其工作是允许与服务器上的业务对象进行交互。

以下是如何实现 orm 服务的简化示例：

.. code-block:: javascript

    import { registry } from "@web/core/registry";
    export const OrmService = {
        start() {
            return {
                read(...) { ... },
                write(...) { ... },
                unlink(...) { ... },
                ...
            }
        },
    };
    registry.category("services").add("orm", OrmService);

Using services
--------------

服务在环境中可用，但通常应通过 `useService` 钩子使用，该钩子防止在组件被销毁后调用服务上的方法，并防止在方法调用后如果组件在调用过程中被销毁，则进一步代码执行。

.. code-block:: javascript

    class SomeComponent extends Component {
        setup() {
            this.orm = useService("orm");
        }
        // ...
        getActivityModelViewID(model) {
            return this.orm.call(model, "get_activity_view_id", this.params);
        }
    }

Talking to the server
---------------------

在 Odoo 上工作时通常有两个用例：用户可能需要调用某个（python）模型上的方法（通过控制器 `/web/dataset/call_kw`），或者用户可能需要直接调用控制器（在某些路由上可用）。

* 调用 Python 模型上的方法通过 orm 服务完成：

  .. code-block:: javascript

      return this.orm.call("some.model", "some_method", [some, args]);

* 直接调用控制器通过 rpc 服务完成：

  .. code-block:: javascript

      return this.rpc("/some/route/", {
          some: param,
      });

.. note::
    rpc 服务并不真正执行通常理解的远程过程调用 (RPC)，但出于历史原因，在 Odoo 中我们通常将 JavaScript 中执行的任何网络请求称为 RPC。正如前面段落所强调的那样，如果您想调用模型上的方法，则应使用 orm 服务。

Notifications
=============

Odoo 框架有一种标准方式来向用户传达各种信息：通知，这些通知显示在用户界面的右上角。通知的类型遵循 bootstrap 通知：

- *信息*：用于显示作为某个操作结果的某些信息反馈，该操作无法失败。

- *成功*：用户执行的操作有时可能失败，但没有。

- *警告*：用户执行的操作可能只部分完成。如果出现问题，但不是由用户直接引起的，或不是特别可操作，也可以使用此功能。

- *成功*：用户尝试执行某个操作，但无法完成。

通知还可以用于在不打扰用户工作流程的情况下向用户询问问题：例如，接收到通过 VOIP 的电话：可以显示一个粘性通知，并带有两个按钮以 *接受* 或 *拒绝*。

Displaying notifications
------------------------

在 Odoo 中，有两种显示通知的方法：

- *通知* 服务允许组件通过调用 add 方法从 JS 代码显示通知。

- *display_notification* 客户端操作允许从 python 触发通知的显示（例如，在用户单击对象类型按钮时调用的方法）。此客户端操作使用通知服务。

通知有一些 *选项*：

- *title*：字符串，可选。将显示在顶部作为标题。

- *message*：字符串， 可选。通知的内容。可以是显示格式化文本的标记对象。

- *sticky*：布尔值，可选（默认 false）。如果为 true，通知将在用户关闭之前保持。否则，通知将在短时间后自动关闭。

- *type*：字符串， 可选（默认 "warning"）。确定通知的样式。可能的值："info"，"success"，"warning"，"danger"。

- *className*：字符串， 可选。这是将自动添加到通知的 CSS 类名。这对样式目的可能很有用，但不鼓励使用。

以下是如何在 JS 中显示通知的一些示例：

.. code-block:: javascript

    // 请注意，我们在文本上调用 _t 以确保它被正确翻译。
    this.notification.add({
        title: _t("Success"),
        message: _t("Your signature request has been sent.")
    });
    this.notification.add({
        title: _t("Error"),
        message: _t("Filter name is required."),
        type: "danger",
    });

在 Python 中：

.. code-block:: python

    # 请注意，我们在文本上调用 _(string) 以确保它被正确翻译。
    def show_notification(self):
        return {
            'type': 'ir.actions.client',
            'tag': 'display_notification',
            'params': {
                'title': _('Success'),
                'message': _('Your signature request has been sent.'),
                'sticky': False,
            }
        }

Systray
=======

Systray 是界面导航栏的右侧部分，网页客户端在此显示一些小部件，例如消息菜单。

当 Systray 被导航栏创建时，它会查找所有已注册的 Systray 项并显示它们。

目前没有针对 Systray 项的特定 API。它们是 Owl 组件，可以像其他组件一样与其环境进行通信，例如与服务进行交互。

Adding a new Systray Item
-------------------------

可以通过将其添加到 "systray" 注册表来添加项目到 Systray：

.. code-block:: javascript

    import { registry } from "@web/core/registry";
    class MySystrayComponent extends Component {
        ...
    }
    registry.category("systray").add("MySystrayComponent", MySystrayComponent, { sequence: 1 });

根据在 Systray 注册表中的顺序，项目在 Systray 中按顺序排列。

Translation management
======================

一些翻译是在服务器端完成的（基本上是服务器渲染或处理的所有文本字符串），但静态文件中有一些字符串需要翻译。当前的工作方式如下：

- 每个可翻译字符串都标记为特殊函数 *_t*。
- 这些字符串由服务器使用，以生成适当的 PO 文件。
- 每当网页客户端加载时，它会调用路由 */web/webclient/translations*，该路由返回所有可翻译术语的列表。
- 在运行时，每当调用 `_t` 函数时，它将查找此列表，以便找到翻译，并返回翻译的字符串或原始字符串（如果未找到）。

请注意，从服务器的角度更详细地解释了翻译，在文档 :doc:`/developer/howtos/translations` 中。

.. code-block:: javascript

    import { _t } from "@web/core/l10n/translation";

    class SomeComponent extends Component {
        static exampleString = _t("this should be translated");
        ...
        someMethod() {
            const str = _t("some text");
        }
    }

请注意，使用翻译函数需要小心：作为参数给出的字符串不能是动态的，因为它在代码中静态提取以生成 PO 文件，并作为翻译术语的标识符。如果您需要将某些动态内容注入字符串，`_t` 支持占位符：

.. code-block:: javascript

    import { _t } from "@web/core/l10n/translation";
    const str = _t("Hello %s, you have %s unread messages.", user.name, unreadCount);

注意字符串本身是固定的。这允许翻译函数在使用占位符之前检索翻译字符串。

Session
=======

网页客户端需要一些来自 Python 的信息以正常运作。为了避免通过 JavaScript 进行网络请求而进行额外的往返，该信息直接序列化到页面中，并可以通过 `@web/session` 模块在 JS 中访问。

Adding information to the session
---------------------------------

当加载 `/web` 路由时，服务器会在脚本标签中注入此信息。该信息是通过调用模型 `ir.http` 的 `session_info` 方法获得的。您可以重写此方法以将信息添加到返回的字典中。

.. code-block:: python

    from odoo import models
    from odoo.http import request

    class IrHttp(models.AbstractModel):
        _inherit = 'ir.http'

        def session_info(self):
            result = super(IrHttp, self).session_info()
            result['some_key'] = get_some_value_from_db()
            return result

现在，可以通过在会话中读取它的值来在 JavaScript 中获得：

.. code-block:: javascript

    import { session } from "@web/session";
    const myValue = session.some_key;
    ...

请注意，此机制旨在减少网页客户端准备所需的通信量。它仅适用于计算成本较低的数据（缓慢的 session_info 调用会延迟网页客户端的加载），以及在初始化过程中需要的数据。

Views
=====

“视图”一词有多个含义。本节关于 JavaScript 代码的视图设计，而不是 *arch* 的结构或其他任何内容。

虽然视图只是 owl 组件，但内置视图通常具有相同的结构：一个名为 "SomethingController" 的组件作为视图的根。该组件创建一个某种“模型”的实例（负责管理数据的对象），并具有一个名为“渲染器”的子组件，处理显示逻辑。

.. _reference/js/widgets:

Fields
======

网页客户端体验的很大一部分是关于编辑和创建数据的。大部分工作是借助字段小部件来完成的，这些小部件了解字段类型以及值的具体细节应该如何显示和编辑。

.. _reference/javascript_reference/field_decoration:

Decorations
-----------

像列表视图一样，字段小部件对装饰有简单的支持。装饰的目标是简单指定文本颜色，具体取决于记录的当前状态。例如：

.. code-block:: xml

    <field name="state" decoration-danger="amount &lt; 10000"/>

有效的装饰名称包括：

- `decoration-bf`
- `decoration-it`
- `decoration-danger`
- `decoration-info`
- `decoration-muted`
- `decoration-primary`
- `decoration-success`
- `decoration-warning`

每个装饰 *decoration-X* 将映射到 CSS 类 *text-X*，这是标准的 bootstrap CSS 类（除了 *text-it* 和 *text-bf*，它们由 odoo 处理，并分别对应于斜体和加粗）。请注意，装饰属性的值应该是有效的 Python 表达式，将作为评估上下文来评估记录。

Non-relational fields
---------------------

我们在这里按无特定顺序记录所有默认的非关系字段。

Integer (`integer`)
    这是字段类型为 `integer` 的字段的默认字段类型。

    - 支持的字段类型：`integer`

    选项：

    - `type`：设置输入类型（默认 `"text"`，可以设置为 `"number"`）。

        在编辑模式下，字段作为输入呈现，HTML 属性类型设置为 `"number"`（因此用户可以受益于原生支持，特别是在移动设备上）。在这种情况下，默认格式化将被禁用，以避免不兼容。

        .. code-block:: xml

            <field name="int_value" options="{'type': 'number'}" />

    - `step`：设置当用户点击按钮时上升和下降的值（仅限于类型为数字的输入，默认值为 `1`）。

        .. code-block:: xml

            <field name="int_value" options="{'type': 'number', 'step': 100}" />

    - `format`：数字是否应该被格式化（默认值为 `true`）。

        默认情况下，数字根据区域设置参数进行格式化。此选项将阻止字段的值被格式化。

        .. code-block:: xml

            <field name="int_value" options='{"format": false}' />

Float (`float`)
    这是字段类型为 `float` 的字段的默认字段类型。

    - 支持的字段类型：`float`

    属性：

    - `digits`：显示精度。

        .. code-block:: xml

            <field name="factor" digits="[42,5]" />

    选项：

    - `type`：设置输入类型（默认 `"text"`，可以设置为 `"number"`）。

        在编辑模式下，字段作为输入呈现，HTML 属性类型设置为 `"number"`（因此用户可以受益于原生支持，特别是在移动设备上）。在这种情况下，默认格式化将被禁用，以避免不兼容。

        .. code-block:: xml

            <field name="int_value" options="{'type': 'number'}" />

    - `step`：设置当用户点击按钮时上升和下降的值（仅限于类型为数字的输入，默认值为 `1`）。

        .. code-block:: xml

            <field name="int_value" options="{'type': 'number', 'step': 0.1}" />

    - `format`：数字是否应该被格式化（默认值为 `true`）。

        默认情况下，数字根据区域设置参数进行格式化。此选项将阻止字段的值被格式化。

        .. code-block:: xml

            <field name="int_value" options="{'format': false}" />

Time (`float_time`)
    该小部件的目标是以适当的方式显示一个表示时间间隔（以小时为单位）的浮动值。因此，例如，`0.5` 应格式化为 `0:30`，或 `4.75` 对应于 `4:45`。

Float Factor (`float_factor`)
    此小部件旨在以适当的方式显示一个浮动值，该值使用给定的因子转换。因此，例如，数据库中保存的值为 `0.5`，因子为 `3`，小部件的值应格式化为 `1.5`。

    - 支持的字段类型：`float`

Float Toggle (`float_toggle`)
    该小部件的目标是用包含一系列可能值的按钮替换输入字段（在选项中给出）。每次单击允许用户循环该范围。此处的目的是将字段值限制为预定义选择。此外，该小部件支持因子转换，就像 `float_factor` 小部件一样（范围值应是转换的结果）。

    - 支持的字段类型：`float`

    .. code-block:: xml

        <field name="days_to_close" widget="float_toggle" options="{'factor': 2, 'range': [0, 4, 8]}" />

Boolean (`boolean`)
    这是字段类型为 `boolean` 的字段的默认字段类型。

    - 支持的字段类型：`boolean`

Char (`char`)
    这是字段类型为 `char` 的字段的默认字段类型。

    - 支持的字段类型：`char`

.. _reference/javascript_reference/date_field:

Date (`date`)
    这是字段类型为 `date` 的字段的默认字段类型。它由一个文本框和一个日期选择器组成。

    - 支持的字段类型：`date`

    选项：

    - `min_date` / `max_date`：为接受值设置限制日期。默认情况下，最早接受的日期为 **1000-01-01**，最新为 **9999-12-31**。接受的值是 SQL 格式化的日期（`yyyy-MM-dd HH:mm:ss`）或 `"today"`。

        .. code-block:: xml

            <field name="datefield" options="{'min_date': 'today', 'max_date': '2023-12-31'}" />

    - warn_future：如果值在将来，则显示警告（基于今天）。

        .. code-block:: xml

            <field name="datefield" options="{'warn_future': true}" />

.. _reference/javascript_reference/datetime_field:

Date & Time (`datetime`)
    这是字段类型为 `datetime` 的字段的默认字段类型。值始终位于客户端时区。

    - 支持的字段类型：`datetime`

    选项：

    - 参见 :ref:`Date Field <reference/javascript_reference/date_field>` 选项。

    - `rounding`：用于生成时间选择器中可用分钟的增量。这不会影响实际值，仅影响选择下拉列表中的可用选项数（默认值：`5`）。

        .. code-block:: xml

            <field name="datetimefield" options="{'rounding': 10}" />

    - `show_time`：当设置为 false 时，隐藏日期时间字段的时间部分。该字段仍将接受日期时间值，但在 UI 中将隐藏时间部分（默认值：`true`）。

        .. code-block:: xml

            <field name="datetimefield" widget="datetime" options="{'show_time': false}" />

Date Range (`daterange`)
    该小部件允许用户从单个选择器选择开始和结束日期。

    - 支持的字段类型：`date`，`datetime`

    选项：

    - 请参阅 :ref:`Date Field <reference/javascript_reference/date_field>` 或 :ref:`Date & Time Field <reference/javascript_reference/datetime_field>` 选项。

    - `start_date_field`：用于获取/设置日期范围的起始值的字段（不能与 `end_date_field` 一起使用）。

        .. code-block:: xml

            <field name="end_date" widget="daterange" options="{'start_date_field': 'start_date'}" />

    - `end_date_field`：用于获取/设置日期范围的结束值的字段（不能与 `start_date_field` 一起使用）。

        .. code-block:: xml

            <field name="start_date" widget="daterange" options="{'end_date_field': 'end_date'}" />

Remaining Days (`remaining_days`)
    该小部件可以在日期和日期时间字段上使用。在只读状态下，它显示字段值与今天之间的差（以天为单位）。在编辑模式下，该小部件变成常规的日期或日期时间字段。

    - 支持的字段类型：`date`，`datetime`

Monetary (`monetary`)
    这是字段类型为 `monetary` 的字段的默认字段类型。用于显示货币。如果选项中给出了货币字段，它将使用该货币，否则将回落到默认货币（在会话中）。

    - 支持的字段类型：`monetary`，`float`

    选项：

    - `currency_field`：另一个字段名称，应为货币的 many2one。

        .. code-block:: xml

            <field name="value" widget="monetary" options="{'currency_field': 'currency_id'}" />

Text (`text`)
    这是字段类型为 `text` 的字段的默认字段类型。

    - 支持的字段类型：`text`

Handle (`handle`)
    该字段的工作是显示为 `handle`，并允许通过拖放对各种记录进行重新排序。

    .. warning:: 必须在记录排序的字段上指定此字段。
    .. warning:: 同一列表中不支持有多个字段具有句柄小部件。

    - 支持的字段类型：`integer`

Email (`email`)
    此字段显示电子邮件地址。使用它的主要原因是，它在只读模式下呈现为具有适当 href 的锚标签。

    - 支持的字段类型：`char`

Phone (`phone`)
    此字段显示电话号码。使用它的主要原因是，它在只读模式下呈现为具有适当 href 的锚标签，但仅在某些情况下：我们只希望在设备可以拨打此号码时使其可点击。

    - 支持的字段类型：`char`

URL (`url`)
    此字段在只读模式下显示 URL。使用它的主要原因是，它呈现为具有适当 CSS 类和 href 的锚标签。

    此外，锚标签的文本可以使用 *text* 属性自定义（这不会改变 href 值）。

    - 支持的字段类型：`char`

    .. code-block:: xml

        <field name="foo" widget="url" text="Some URL" />

    选项：

    - `website_path`：（默认：`false`）默认情况下，该小部件强制（如果尚未）href 值以 `"http://"` 开头，除非该选项设置为 true，从而允许重定向到数据库自己的网站。

Domain (`domain`)
    `domain` 字段允许用户通过树状界面构建一个技术前缀域，并实时查看所选记录。在调试模式下，还提供一个输入框，以便直接输入前缀字符域（或构建树状界面无法允许的高级域）。

    请注意，这仅限于 **静态** 域（没有动态表达式或访问上下文变量）。

    - 支持的字段类型：`char`

Link button (`link_button`)
    `LinkButton` 小部件实际上只是显示一个带图标和文本值的 span 作为内容。该链接是可点击的，并将打开一个新的浏览器窗口，以其值作为 URL。

    - 支持的字段类型：`char`

Image File (`image`)
    此小部件用于将二进制值表示为图像。在某些情况下，服务器返回 `bin_size` 而不是实际图像（`bin_size` 是表示文件大小的字符串，例如 `"6.5kb"`）。在这种情况下，小部件将生成具有源属性的图像，该属性对应于服务器上的图像。

    - 支持的字段类型：`binary`

    选项：

    - `preview_image`：如果图像仅作为 `bin_size` 加载，则此选项对网页客户端非常有用，因为它告知网页客户端默认字段名称不是当前字段的名称，而是另一个字段的名称。

        .. code-block:: xml

            <field name="image" widget="image" options="{'preview_image': 'image_128'}" />

    - `accepted_file_extensions`：用户可以从文件输入对话框中选择的文件扩展名（默认值为 `"image/\*"`）

        （cf: ``accept`` 属性在 `<input type="file" />` 上）

Binary File (`binary`)
    允许保存/下载二进制文件的通用小部件。

    - 支持的字段类型：`binary`

    属性：

    - `filename`：保存二进制文件将丢失其文件名，因为它仅保存二进制值。文件名可以保存在另一个字段中。为此，应该在视图中设置 `filename` 属性到字段。

        .. code-block:: xml

            <field name="datas" filename="datas_fname" />

    选项：

    - `accepted_file_extensions`：用户可以从文件输入对话框中选择的文件扩展名

        （cf: ``accept`` 属性在 `<input type="file" />` 上）

Priority (`priority`)
    此小部件显示为一组星星，允许用户单击以选择值或不选择值。这在标记任务为高优先级时非常有用。

    请注意，该小部件也可以在只读模式下使用，这很不寻常。

    - 支持的字段类型：`selection`

Image Attachment (`attachment_image`)
    针对 `many2one` 字段的图像小部件。如果字段设置，则该小部件将呈现为带有适当 src URL 的图像。此小部件在编辑或只读模式下没有不同的行为，仅用于查看图像。

    - 支持的字段类型：`many2one`

    .. code-block:: xml

        <field name="displayed_image_id" widget="attachment_image" />

Label Selection (`label_selection`)
    此小部件呈现一个简单的不可编辑标签。此功能仅用于显示一些信息，而不是编辑它。

    - 支持的字段类型：`selection`

    选项：

    - `classes`：从选择值到 CSS 类名称的映射。

        .. code-block:: xml

            <field
                name="state"
                widget="label_selection"
                options="{
                    'classes': {
                        'draft': 'default',
                        'cancel': 'default',
                        'none': 'danger',
                    },
                }"
            />

State Selection (`state_selection`)
    这是一个专用选择小部件。它假设记录具有某些硬编码字段，这些字段存在于视图中：`stage_id`、`legend_normal`、`legend_blocked`、`legend_done`。这主要用于显示和更改项目的状态，额外信息在下拉列表中显示。

    - 支持的字段类型：`selection`

    .. code-block:: xml

        <field name="kanban_state" widget="state_selection" />

State Selection - List View (`list.state_selection`)
    在列表视图中，`state_selection` 字段默认在图标旁边显示标签。

    - 支持的字段类型：`selection`

    选项：

    - `hide_label`：隐藏图标旁边的标签。

        .. code-block:: xml

            <field name="kanban_state" widget="state_selection" options="{'hide_label': true}" />

Favorite (`boolean_favorite`)
    此小部件显示为空（或不）星星，具体取决于布尔值。请注意，它也可以在只读模式下进行编辑。

    - 支持的字段类型：`boolean`

Toggle (`boolean_toggle`)
    显示一个切换开关以表示布尔值。这是布尔字段的一个子字段，主要用于拥有不同的外观。

    - 支持的字段类型：`boolean`

Stat Info (`statinfo`)
    此小部件旨在在 `stat button` 中表示统计信息。它基本上只是带有数字的标签。

    - 支持的字段类型：`integer`，`float`

    选项：

    - `label_field`：如果给出，则小部件将使用 `label_field` 的值作为文本。

        .. code-block:: xml

            <button
                name="%(act_payslip_lines)d"
                icon="fa-money"
                type="action"
            >
                <field
                    name="payslip_count"
                    widget="statinfo"
                    string="Payslip"
                    options="{'label_field': 'label_tasks'}"
                />
            </button>

Percent Pie (`percentpie`)
    此小部件旨在在 `stat button` 中表示统计信息。这与 statinfo 小部件类似，但信息以 **饼图** 形式表示（从空到满）。请注意，值被解释为百分比（介于 `0` 和 `100` 之间）。

    - 支持的字段类型：`integer`，`float`

    .. code-block:: xml

        <field name="replied_ratio" string="Replied" widget="percentpie" />

Progress Bar (`progressbar`)
    将值表示为进度条（从 `0` 到某个值）。

    - 支持的字段类型：`integer`，`float`

    选项：

    - `editable`：布尔值，确定 `value` 是否可编辑。

    - `current_value`：从字段获取当前值，必须在视图中存在。

    - `max_value`：从字段获取最大值，必须在视图中存在。

    - `edit_max_value`：布尔值，确定 `max_value` 是否可编辑。

    - `title`：条形的标题，显示在条形上方。

        -> 不翻译，如果术语必须翻译，请使用 `title` 属性（而不是选项）代替。

    .. code-block:: xml

        <field
            name="absence_of_today"
            widget="progressbar"
            options="{
                'current_value': 'absence_of_today',
                'max_value': 'total_employee',
                'editable': false,
            }"
        />

Journal Dashboard Graph (`dashboard_graph`)
    这是一个更专用的小部件，用于显示代表一组数据的图形。例如，它在会计仪表板看板视图中使用。

    它假定字段是 JSON 序列化的数据集。

    - 支持的字段类型：`char`

    属性：

    - `graph_type`：字符串，可以是 `"line"` 或 `"bar"`。

        .. code-block:: xml

            <field name="dashboard_graph_data" widget="dashboard_graph" graph_type="line" />

Ace Editor (`ace`)
    此小部件旨在用于文本字段。它为 XML 和 Python 提供 Ace 编辑器。

    - 支持的字段类型：`char`，`text`

Badge (`badge`)
    在 bootstrap 徽章药丸中显示值。

    - 支持的字段类型：`char`，`selection`，`many2one`

    默认情况下，徽章具有浅灰色背景，但可以通过使用 :ref:`Decoration <reference/javascript_reference/field_decoration>` 机制进行自定义。例如，要在给定条件下显示红色徽章：

    .. code-block:: xml

        <field name="foo" widget="badge" decoration-danger="state == 'cancel'" />

Relational fields
-----------------

Selection (`selection`)

    - 支持的字段类型：`selection`

    属性：

    - `placeholder`：一个字符串，用于在未选择值时显示一些信息。

        .. code-block:: xml

            <field name="tax_id" widget="selection" placeholder="Select a tax" />

Radio (`radio`)
    这是 `FielSelection` 的子字段，但专门用于将所有有效选择显示为单选按钮。

    请注意，如果在 many2one 记录上使用，则会进行更多的 RPC 以获取相关记录的 name_gets。

    - 支持的字段类型：`selection`，`many2one`

    选项：

    - `horizontal`：如果为 `true`，单选按钮将水平显示。

        .. code-block:: xml

            <field name="recommended_activity_type_id" widget="radio" options="{'horizontal': true}"/>

Badge Selection (`selection_badge`)
    这是 `selection` 字段的子字段，但专门用于将所有有效选择显示为矩形徽章。

    - 支持的字段类型：`selection`，`many2one`

    .. code-block:: xml

        <field name="recommended_activity_type_id" widget="selection_badge" />

Many2one (`many2one`)
    many2one 字段的默认小部件。

    - 支持的字段类型：`many2one`

    属性：

    - `can_create`：允许创建相关记录（优先于 `no_create` 选项）。

    - `can_write`：允许编辑相关记录（默认：`true`）。

    选项：

    - `quick_create`：允许快速创建相关记录（默认：`true`）。

    - `no_create`：防止创建相关记录 - 隐藏 **创建“xxx”** 和 **创建和编辑** 下拉菜单项（默认：`false`）。

    - `no_quick_create`：防止快速创建相关记录 - 隐藏 **创建“xxx”** 下拉菜单项（默认：`false`）。

    - `no_create_edit`：隐藏 **创建和编辑** 下拉菜单项（默认：`false`）。

    - `create_name_field`：在创建相关记录时，如果设置此选项，`create_name_field` 的值将填充输入的值（默认：`name`）。

    - `always_reload`：布尔值，默认为 `false`。如果为 `true`，小部件将始终进行额外的 `name_get` 以获取其名称值。此选项用于 `name_get` 方法被重写的情况（请不要这样做）。

    - `no_open`：布尔值，默认为 `false`。如果设置为 `true`，在只读模式下，many2one 将不会在单击时重定向到记录。

    .. code-block:: xml

        <field name="currency_id" options="{'no_create': true, 'no_open': true}" />

Many2one Barcode (`many2one_barcode`)
    允许打开移动设备（Android/iOS）的相机以扫描条形码的 `many2one` 字段的小部件。

    此 many2one 字段的专业化允许用户使用原生相机扫描条形码。然后使用 `name_search` 来搜索此值。

    如果设置此小部件且用户未使用移动应用程序，则将回退到常规的 many2one（`Many2OneField`）。

    - 支持的字段类型：`many2one`

Many2one Avatar (`many2one_avatar`)
    该小部件仅支持指向继承自 `image.mixin` 的模型的 `many2one` 字段。在只读模式下，它在其 `display_name` 旁边显示相关记录的图像。请注意，此时 `display_name` 不是可点击的链接。在编辑模式下，它的行为与常规的 `many2one` 完全相同。

    - 支持的字段类型：`many2one`

Many2one Avatar User (`many2one_avatar_user`)
    该小部件是 `Many2OneAvatar` 的专业化。当单击头像时，我们将打开与相应用户的聊天窗口。此小部件只能设置在指向 `res.users` 模型的 `many2one` 字段上。

    - 支持的字段类型：`many2one`（指向 `res.users`）

Many2one Avatar Employee (`many2one_avatar_employee`)
    与 `many2one_avatar_user` 相同，但适用于指向 `hr.employee` 的 `many2one` 字段。

    - 支持的字段类型：`many2one`（指向 `hr.employee`）

Many2many (`many2many`)
    many2many 字段的默认小部件。

    - 支持的字段类型：`many2many`

    属性：

    - `mode`：字符串，默认视图以显示。

    - `domain`：将数据限制为特定域。

    选项：

    - `create_text`：允许自定义添加新记录时显示的文本。

    - `link`：确定记录是否可以添加到关系的域（默认：`true`）。

    - `unlink`：确定记录是否可以从关系中删除的域（默认：`true`）。

Many2many Binary File (`many2many_binary`)
    此小部件帮助用户一次上传或删除一个或多个文件。

    请注意，此小部件特定于模型 `ir.attachment`。

    - 支持的字段类型：`many2many`

    选项：

    - `accepted_file_extensions`：用户可以从文件输入对话框中选择的文件扩展名

        （cf: ``accept`` 属性在 `<input type="file" />` 上）

Many2many Tags (`many2many_tags`)
    以标签列表的形式显示 `many2many` 字段。

    - 支持的字段类型：`many2many`

    选项：

    - `create`：确定是否可以创建新标签的域（默认：`true`）。

        .. code-block:: xml

            <field name="category_id" widget="many2many_tags" options="{'create': [['some_other_field', '>', 24]]}" />

    - `color_field`：字段名称，应该在视图中存在的数值字段。根据其值选择一种颜色。

        .. code-block:: xml

            <field name="category_id" widget="many2many_tags" options="{'color_field': 'color'}" />

    - `no_edit_color`：设置为 `true`，以删除更改标签颜色的可能性（默认：`false`）。

        .. code-block:: xml

            <field name="category_id" widget="many2many_tags" options="{'color_field': 'color', 'no_edit_color': true}" />

Many2many Tags - Form View (`form.many2many_tags`)
    `many2many_tags` 小部件在表单视图中的专业化。它有一些额外代码，允许编辑标签的颜色。

    - 支持的字段类型：`many2many`

Many2many Tags - Kanban View (`kanban.many2many_tags`)
    `many2many_tags` 小部件在看板视图中的专业化。

    - 支持的字段类型：`many2many`

Many2many Checkboxes (`many2many_checkboxes`)
    此字段显示一组复选框，允许用户选择一部分选项。请注意，显示的值数量限制为 `100`。此限制不可自定义。它仅允许处理在某些情况下，此小部件在具有庞大共同模型的字段上错误设置的极端情况。在这些情况下，列表视图更为合适，因为它允许分页和过滤。

    - 支持的字段类型：`many2many`

One2many (`one2many`)
    one2many 字段的默认小部件。它通常在子列表视图或子看板视图中显示数据。

    - 支持的字段类型：`one2many`

    选项：

    - `create`：确定是否可以创建相关记录的域（默认：`true`）。

    - `delete`：确定是否可以删除相关记录的域（默认：`true`）。

        .. code-block:: xml

            <field name="turtles" options="{'create': [['some_other_field', '>', 24]]}" />

    - `create_text`：用于自定义“添加”标签/文本的字符串。

        .. code-block:: xml

            <field name="turtles" options="{'create_text': 'Add turtle'}" />

Status Bar (`statusbar`)
    这是表单视图特定的字段。它是许多表单顶部的条，表示流程，并允许选择特定状态。

    - 支持的字段类型：`selection`，`many2one`

Reference (`reference`)
    `reference` 字段是选择（用于模型）和 `many2one` 字段（用于其值）的组合。它允许选择任意模型的记录。

    - 支持的字段类型：`char`，`reference`

    选项：

    - `model_field`：包含可以选择的记录模型的 `ir.model` 名称。当设置此选项时，`reference` 字段的选择部分不会显示。

Widgets
-------

Week Days (`week_days`)
    此小部件显示一组复选框，每周有一个复选框，允许用户选择一部分选项。

    .. code-block:: xml

        <widget name="week_days" />

Client actions
==============

客户端操作是可以作为网页客户端中的主要元素显示的组件，占据导航栏下方的所有空间，就像 `act_window_action` 一样。当您需要一个与现有视图或特定模型不密切相关的组件时，这非常有用。例如，讨论应用就是一个客户端操作。

客户端操作是一个具有不同含义的术语，具体取决于上下文：

- 从服务器的角度来看，它是模型 *ir_action* 的记录，带有一个字段 *tag* 类型为 char。
- 从网页客户端的角度来看，它是注册在操作注册表中的 Owl 组件，其标签相同。

每当菜单项与客户端操作相关联时，打开它将简单地从服务器获取操作定义，然后在操作注册表中查找其标签以获取组件定义。然后，由操作容器呈现该组件。

Adding a client action
----------------------

客户端操作是一个将控制网页客户端导航栏下方屏幕部分的组件。定义客户端操作很简单，只需创建一个 Owl 组件并将其添加到操作注册表中。

.. code-block:: javascript

    import { registry } from "@web/core/registry";
    class MyClientAction extends Component { ... }
    registry.category("actions").add("my-custom-action", ClientAction);

然后，为了在网页客户端中使用客户端操作，我们需要创建一个客户端操作记录（`ir.actions.client` 模型的记录），并带有适当的 `tag` 属性：

.. code-block:: xml

    <record id="my_client_action" model="ir.actions.client">
        <field name="name">Some Name</field>
        <field name="tag">my-custom-action</field>
    </record>
