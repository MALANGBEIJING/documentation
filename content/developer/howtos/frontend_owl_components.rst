============================================
在门户和网站上使用 Owl 组件
============================================

在本文中，您将学习如何在门户和网站上利用 Owl 组件。

概述
========

要在网站或门户上使用 Owl 组件，您需要做以下几件事：

- 创建您的 Owl 组件并将其注册到 `public_components` 注册表中
- 将该组件添加到 `web.assets_frontend` 资源包中
- 在网站或门户页面中添加 `<owl-component>` 标签来使用该组件

1. 创建 Owl 组件
=============================

为了保持简单，让我们从一个非常简单的组件开始，它只渲染 "Hello, World!"。这将让我们一目了然地知道我们的设置是否正常工作。

首先，在 :file:`/your_module/static/src/portal_component/your_component.xml` 中创建模板。

.. code-block:: xml

    <?xml version="1.0" encoding="UTF-8"?>
    <templates xml:space="preserve">
        <t t-name="your_module.YourComponent">
            Hello, World!
        </t>
    </templates>

然后为该组件创建 JavaScript 文件，路径为 :file:`/your_module/static/src/portal_component/your_component.js`，并将其添加到 `public_components` 注册表中：

.. code-block:: js

    /** @odoo-module */
    import { Component } from "@odoo/owl";
    import { registry } from "@web/core/registry"

    export class YourComponent extends Component {
        static template = "your_module.YourComponent";
        static props = {};
    }

    registry.category("public_components").add("your_module.YourComponent", YourComponent);


.. seealso::
   :ref:`Owl 组件参考 <frontend/components>`.


2. 将组件添加到 `web.assets_frontend` 资源包中
============================================================

`web.assets_frontend` 资源包是门户和网站使用的资源包，您需要将组件的代码添加到该资源包中，以便公共组件服务可以找到您的组件并挂载它。在您的模块的 manifest 中，在资产部分，添加 `web.assets_frontend` 的条目，并添加您的组件文件：

.. code-block:: py

    {
        # ...
        'assets': {
            'web.assets_frontend': [
                'your_module/static/src/portal_component/**/*',
            ],
        }
    }

.. seealso::
    :ref:`模块 manifest 参考 <reference/module/manifest>`。

3. 在页面中添加 `<owl-component>` 标签
============================================

现在我们需要添加一个 `<owl-component>` 标签，它将作为组件挂载的目标。为了这个示例，我们将通过 xpath 直接将其添加到门户的首页，路径为 :file:`/your_module/views/templates.xml`。

.. code-block:: xml

    <?xml version="1.0" encoding="utf-8"?>
    <odoo>
        <template id="your_module.portal_my_home" inherit_id="portal.portal_my_home">
            <xpath expr="//*[hasclass('o_portal_my_home')]" position="before">
                <owl-component name="your_module.YourComponent" />
            </xpath>
        </template>
    </odoo>

不要忘记将此文件添加到资产包的 data 部分：

.. code-block:: py

    {
        # ...
        'data': {
            'views/templates.xml',
        }
    }

完成了！如果您打开门户的首页，您应该会在页面顶部看到 "Hello, World!" 的信息。

注意事项
=================

Owl 组件完全由浏览器使用 JavaScript 渲染。这可能会导致一些问题：

- 布局偏移
- 搜索引擎索引效果较差

因此，您应该仅在以下特定使用情况下，在门户和网站上使用 Owl 组件。

布局偏移
------------

当页面初始渲染内容，然后该内容在页面中移动（“偏移”）时，这被称为布局偏移。使用 Owl 组件时，所有围绕 Owl 组件的 HTML 都是由服务器渲染的，并且是用户首先看到的内容。当 JavaScript 开始运行时，Owl 会挂载您的组件，这可能会导致周围元素在页面上移动。这会导致用户体验不佳：用户看到页面上初始渲染的元素，并希望与之交互，于是将光标或手指移到该元素上。就在他们即将点击时，Owl 组件挂载了，导致他们想要交互的元素移动了。结果，他们点击了 Owl 应用而不是他们想要的元素。

这可能会让用户感到沮丧，因此在设计页面时要小心，确保 Owl 组件不会移动页面上的其他元素。可以通过多种方式实现这一点，例如将其定位于所有现有元素之下、避免在其周围放置其他交互元素，或者通过 CSS 为 Owl 组件预留固定空间。

.. seealso::
  `Cumulative Layout Shift on web.dev <https://web.dev/articles/cls>`_


搜索引擎索引效果较差
---------------------------------

搜索引擎在构建网页内容的索引时，会使用网络爬虫查找页面并分析其内容以在搜索结果中显示这些页面。虽然现代搜索引擎通常能够执行 JavaScript 代码，并且通常能够查看和索引 JavaScript 渲染的内容，但它们可能不会像处理静态内容那样快速地索引这些页面，从而导致搜索结果中的页面排名受到影响。

由于大多数搜索引擎不会透露它们抓取和索引网页的确切方式，因此不总是很容易知道客户端渲染对您的搜索引擎评分的影响程度。虽然这不太可能决定您的 SEO 策略的成败，但您仍应仅在客户端渲染能真正为您的页面带来价值时才使用 Owl 组件。

在门户和网站上使用 Owl 组件的时机
====================================================

如前述章节所述，使用 Owl 组件如果不小心，可能会稍微降低用户体验，也可能影响您的 SEO。那么什么时候应该在这些地方使用 Owl 组件呢？这里有一些通用的指导原则。

当您不关心 SEO 时
-----------------------------

如果一个页面不能被搜索引擎索引，因为它不对公众开放，例如用户门户中的任何内容，那么 SEO 表现就不是问题，因为网络爬虫无法访问这些页面。还有一些内容您可能不希望被索引，例如，如果您想要创建一个用户可以选择预约日期和时间的页面，您可能不希望搜索引擎索引某一特定时刻可预约的日期。

当您需要强大的交互性时
----------------------------------

使用 Owl 是在权衡前述缺点与 Owl 带来的好处，即让您更轻松地构建丰富的交互式用户体验。使用 Owl 的主要原因是您希望构建一个可以实时响应用户输入的界面，而无需重新加载页面。如果您主要是想向用户展示静态内容，那么您可能不应该使用 Owl 组件。
