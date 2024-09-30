===================================
创建独立的 Owl 应用程序
===================================

出于多种原因，您可能希望拥有一个不属于 Web 客户端的独立 Owl 应用程序。Odoo 中的一个例子是自助点餐应用程序，它允许客户通过手机点餐。在本章中，我们将介绍实现此类功能所需的步骤。

概述
========

要创建一个独立的 Owl 应用程序，需要以下几个要素：

- 应用程序的根组件
- 包含设置代码的资源包
- 调用资源包的 QWeb 视图
- 渲染视图的控制器

1. 根组件
=================

为了保持简单，让我们从一个非常简单的组件开始，它只渲染 "Hello, World!"。这将让我们一目了然地知道我们的设置是否正常工作。

首先，在 :file:`/your_module/static/src/standalone_app/root.xml` 中创建模板。

.. code-block:: xml

    <?xml version="1.0" encoding="UTF-8"?>
    <templates xml:space="preserve">
        <t t-name="your_module.Root">
            Hello, World!
        </t>
    </templates>

然后，在 :file:`/your_module/static/src/standalone_app/root.js` 中为该组件创建 JavaScript 文件。

.. code-block:: js

    /** @odoo-module */
    import { Component } from "@odoo/owl";

    export class Root extends Component {
        static template = "your_module.Root";
        static props = {};
    }

通常最好将挂载组件的应用程序设置代码放在单独的文件中。在 :file:`/your_module/static/src/standalone_app/app.js` 中创建用于挂载应用程序的 JavaScript 文件。

.. code-block:: js

    /** @odoo-module */
    import { whenReady } from "@odoo/owl";
    import { mountComponent } from "@web/env";
    import { Root } from "./root";

    whenReady(() => mountComponent(Root, document.body));

`mountComponent` 实用函数将负责创建 Owl 应用程序并正确配置它：它将创建一个环境，启动 :ref:`服务<frontend/services>`，确保应用程序翻译正确，并为应用程序提供资源包中的模板等功能。

.. seealso::
   :ref:`Owl 组件参考 <frontend/components>`。


2. 创建包含我们代码的资源包
================================================

在模块的 manifest 文件中，创建一个新的 :ref:`资源包 <reference/assets_bundle>`。
它应该包含 `web._assets_core` 包，该包包含 Odoo JavaScript 框架及其所需的核心库（例如 Owl 和 luxon），之后您可以添加一个 glob 来将应用程序的所有文件添加到该资源包中。

.. code-block:: py
    :emphasize-lines: 9-10

    {
        # ...
        'assets': {
            'your_module.assets_standalone_app': [
                ('include', 'web._assets_helpers'),
                'web/static/src/scss/pre_variables.scss',
                'web/static/lib/bootstrap/scss/_variables.scss',
                ('include', 'web._assets_bootstrap'),
                ('include', 'web._assets_core'),
                'your_module/static/src/standalone_app/**/*',
            ],
        }
    }

其他几行是使 Bootstrap 正常工作的必需资源包和 scss 文件。它们是强制性的，因为 Web 框架的组件使用 Bootstrap 类进行样式和布局。

.. caution::
    确保独立应用程序的文件仅添加到此资源包中。如果您已经为 `web.assets_backend` 或 `web.assets_frontend` 定义了资源包，并且它们有 globs，请确保这些 globs 不会匹配独立应用程序的文件，否则应用程序的启动代码会与这些资源包中的现有启动代码冲突。

.. seealso::
    :ref:`模块 manifest 参考 <reference/module/manifest>`。

3. 调用资源包的 XML 视图
========================================

现在我们已经创建了资源包，接下来需要创建一个使用该资源包的 :ref:`QWeb 视图 <reference/view_architectures/qweb>`。

.. code-block:: xml

    <?xml version="1.0" encoding="utf-8"?>
    <odoo>
        <template id="your_module.standalone_app">&lt;!DOCTYPE html&gt;
            <html>
                <head>
                    <script type="text/javascript">
                        var odoo = {
                            csrf_token: "<t t-nocache="csrf 令牌必须始终保持最新。" t-esc="request.csrf_token(None)"/>",
                            debug: "<t t-out="debug"/>",
                            __session_info__: <t t-esc="json.dumps(session_info)"/>,
                        };
                    </script>
                    <t t-call-assets="your_module.assets_standalone_app" />
                </head>
                <body/>
            </html>
        </template>
    </odoo>

此模板仅执行了两件事：初始化 `odoo` 全局变量，然后调用我们刚定义的资源包。初始化 `odoo` 全局变量是一个必要步骤。该变量应是包含以下内容的对象：

- CSRF 令牌，在许多情况下与 HTTP 控制器交互时是必需的。
- 调试值，用于在许多地方添加额外的日志记录或开发者友好的检查。
- `__session_info__`，它包含服务器的相关信息，例如当前用户的 ID（如果用户已登录）、服务器版本、Odoo 版本等。

4. 渲染视图的控制器
===================================

现在我们已经有了视图，接下来需要让用户可以访问它。为此，我们将创建一个 :ref:`HTTP 控制器 <reference/controllers>`，该控制器渲染视图并返回给用户。

.. code-block:: py

    from odoo.http import request, route, Controller

    class YourController(Controller):
        @route("/your_module/standalone_app", auth="public")
        def standalone_app(self):
            return request.render(
                'your_module.standalone_app',
                {
                    'session_info': request.env['ir.http'].get_frontend_session_info(),
                }
            )

请注意，我们将 `session_info` 提供给了模板。我们通过 `get_frontend_session_info` 方法获取它，最终包含由 Web 框架使用的信息，例如当前用户的 ID（如果用户已登录）、服务器版本、Odoo 版本等。

此时，如果您在浏览器中打开 URL `/your_module/standalone_app`，您应该会看到一个显示 "Hello, World!" 的空白页面。此时，您可以开始编写应用程序的实际代码了。
