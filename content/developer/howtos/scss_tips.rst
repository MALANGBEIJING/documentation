===============================
编写简洁易维护的 CSS
===============================

简化和精简 SCSS 有很多方法。第一步是确定是否需要自定义代码。

Odoo 的 webclient 设计为模块化，这意味着（可能所有的）类都可以在不同视图之间共享。在创建新类之前，请先检查代码。很有可能已经有一个类或 HTML 标签可以实现您需要的效果。

除此之外，Odoo 依赖于 `Bootstrap <https://getbootstrap.com/docs/5.1/getting-started/introduction/>`_（BS），这是目前最完整的 CSS 框架之一。该框架经过定制，以符合 Odoo 的设计（适用于社区版和企业版），这意味着您可以直接在 Odoo 中使用任何 BS 类，并获得与我们的用户界面一致的视觉效果。

.. warning::
   - 一个类实现了所需的视觉效果并不意味着它就是正确的选择。请注意触发 JS 行为的类，例如。
   - 注意类的语义。将 **按钮类** 应用于 **标题** 不仅语义上是错误的，还可能导致迁移问题和视觉不一致。

以下部分介绍了 **在自定义代码是唯一可行方式时**，减少 SCSS 行数的技巧。

.. _tutorials/scss_tips/browser_defaults:

浏览器默认样式
================

默认情况下，每个浏览器都使用 *用户代理样式表* 来呈现内容。为了克服浏览器之间的不一致，`Bootstrap Reboot <https://getbootstrap.com/docs/5.1/content/reboot/>`_ 覆盖了其中的一些规则。

此时，所有 "浏览器特定装饰" 规则都已被删除，但定义基本布局信息的大部分规则仍然保留（或出于一致性原因由 *Reboot* 加强）。

您可以依赖这些规则。

.. example::

   为 `<div/>` 应用 `display: block;` 通常是没有必要的。

   .. code-block:: css

      div.element {
         display: block;
         /* 99% 的情况下不需要 */
      }

.. example::

   在这种情况下，您可以选择更改 HTML 标签，而不是添加新的 CSS 规则。

   .. code-block:: css

      span.element {
         display: block;
         /* 将 <span> 替换为 <div>，这样默认就会有 'display: block' */
      }

以下是默认规则的一个不完全列表：

.. list-table::
   :header-rows: 1

   * - 标签 / 属性
     - 默认值
   * - `<div/>`, `<section/>`, `<header/>`, `<footer/>`...
     - `display: block`
   * - `<span/>`, `<a/>`, `<em/>`, `<b/>`...
     - `display: inline`
   * - `<button/>`, `<label/>`, `<output/>`...
     - `display: inline-block`
   * - `<img/>`, `<svg/>`
     - `vertical-align: middle`
   * - `<summary/>`, `[role="button"]`
     - `cursor: pointer;`
   * - `<q/>`
     - | `:before {content: open-quote}`
       | `:after  {content: close-quote}`
   * - ...
     - ...

.. seealso::
   `GitHub 上的 Bootstrap Reboot <https://github.com/twbs/bootstrap/blob/1a6fdfae6b/scss/_reboot.scss>`_

.. _tutorials/scss_tips/html_tags:

HTML 标签
=========

这看起来可能很明显，但使文本看起来像标题的最 **一致** 方式是使用标题标签（`<h1>`, `<h2>`, ...）。除了重启规则外，几乎所有标签都携带 Odoo 定义的装饰样式。

.. rst-class:: bg-light
.. example::

   .. container:: alert alert-danger

      不推荐

      .. tabs::

         .. code-tab:: html XML

            <span class="o_module_custom_title">
               你好！
            </span>

            <span class="o_module_custom_subtitle">
               我是副标题。
            </span>

         .. code-tab:: css SCSS

            .o_module_custom_title {
               display: block;
               font-size: 120%;
               font-weight: bold;
               animation: 1s linear 1s mycustomAnimation;
            }

            .o_module_custom_subtitle {
               display: block;
               font-size: 12px;
               font-weight: bold;
               animation: 2s linear 1s mycustomAnimation;
            }

   .. container:: alert alert-success

      推荐

      .. tabs::

         .. code-tab:: html XML

            <h5 class="o_module_custom_title">
               你好！
            </h5>

            <div class="o_module_custom_subtitle">
               <b><small>我是副标题。</small></b>
            </div>

         .. code-tab:: css SCSS

            .o_module_custom_title {
               animation: 1s linear 1s mycustomAnimation;
            }

            .o_module_custom_subtitle {
               animation: 2s linear 1s mycustomAnimation;
            }

.. note::
   除了减少代码量外，模块化设计方法（使用类、标签、mixins 等）还能保持视觉效果的一致性，并且易于 **维护**。

   按照上一个示例，如果 Odoo 标题的设计发生变化，这些变化也会应用到 `o_module_custom_title` 元素中，因为它使用的是 `<h5>` 标签。

.. _tutorials/scss_tips/utility_classes:

工具类
======

我们的框架定义了大量工具类，旨在涵盖几乎所有布局/设计/交互需求。一个类已经定义的简单事实足以在可能的情况下优先使用现有类而不是自定义 CSS。

以 `position-relative` 为例。

.. code-block:: css

   position-relative {
      position: relative !important;
   }

由于已经定义了工具类，任何带有 `position: relative` 声明的 CSS 行都 **可能** 是多余的。

Odoo 依赖于默认的 `Bootstrap 工具类 <https://getbootstrap.com/docs/5.1/utilities/background/>`_ 堆栈，并使用 `Bootstrap API <https://getbootstrap.com/docs/5.1/utilities/api/>`_ 定义自己的工具类。

.. seealso::
   - `Bootstrap 工具类 <https://getbootstrap.com/docs/5.1/utilities/api/>`_
   - `GitHub 上的 Odoo 自定义工具类 <{GITHUB_PATH}/addons/web/static/src/scss/utilities_custom.scss>`_

.. _tutorials/scss_tips/utility_classes/downside:

处理工具类的冗长
----------------

工具类的缺点是可能缺乏可读性。

.. example::

   .. code-block:: html

      <myComponent t-attf-class="d-flex border px-lg-2 card
      {{props.readonly ? 'o_myComponent_disabled' : ''}}
      card d-lg-block position-absolute {{props.active ?
      'o_myComponent_active' : ''}}  myComponent px-3"/>

为了解决这个问题，您可以结合不同的方法：

- 在 Qweb 属性中，只使用需要 *动态切换* 的类；
- 每个属性使用新行；
- 按照约定 `[odoo component] [bootstrap component] [css 声明顺序]` 对类进行排序。

.. example::

   .. code-block:: html

      <myComponent
         t-att-class="{
            o_myComponent_disabled: props.readonly,
            o_myComponent_active: props.active
         }"
         class="myComponent card position-absolute d-flex d-lg-block border px-3 px-lg-2"
      />

.. seealso::
   :ref:`Odoo CSS 属性顺序 <contributing/coding_guidelines/scss/properties_order>`
