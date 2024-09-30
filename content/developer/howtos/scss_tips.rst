===============================
编写简洁易维护的CSS
===============================

有很多方法可以简化和精简SCSS。第一步是确定是否真的需要自定义代码。
There are many ways to lean and simplify SCSS. The first step is to establish if custom code is
needed at all.

Odoo的web客户端设计为模块化，这意味着（几乎所有的）类可以在不同视图中共享。在创建新类之前，检查现有代码。很可能已经有一个类或HTML标签正好能实现您想要的效果。
Odoo's webclient has been designed to be modular, meaning that (potentially all) classes can be
shared across views. Check the code before creating a new class. Chances are that there is already a
class or an HTML tag doing exactly what you're looking for.

此外，Odoo依赖于Bootstrap <https://getbootstrap.com/docs/5.1/getting-started/introduction/>_ (BS)，这是最完整的CSS框架之一。这个框架已经根据Odoo的设计进行了定制（适用于社区版和企业版），这意味着您可以直接在Odoo中使用任何BS类，并获得与我们的UI一致的视觉效果。

On top of that, Odoo relies on `Bootstrap
<https://getbootstrap.com/docs/5.1/getting-started/introduction/>`_ (BS), one of the most complete
CSS frameworks available. The framework has been customized in order to match Odoo's design (both
community and enterprise versions), meaning that you can use any BS class directly in Odoo and
achieve a visual result that is consistent with our UI.

.. warning::
   - 某个类虽然实现了所需的视觉效果，但不一定意味着它适用于当前任务。例如，注意可能触发JS行为的类。
   - The fact that a class achieves the desired visual result doesn't necessarily mean that it's the
     right one for the job. Be aware of classes triggering JS behaviors, for example.
   - 注意类的语义。将一个按钮类应用于标题不仅语义上错误，还可能导致迁移问题和视觉不一致。
   - Be careful about class semantics. Applying a **button class** to a **title** is not only
     semantically wrong, it may also lead to migration issues and visual inconsistencies.

以下章节描述了 **在必须使用自定义代码时** 精简SCSS代码的技巧。
The following sections describe tips to strip-down SCSS lines **when custom-code is the only way to
go**.

.. _tutorials/scss_tips/browser_defaults:

浏览器默认设置
================

默认情况下，每个浏览器使用用户代理样式表渲染内容。为了解决不同浏览器之间的不一致性，其中一些规则由Bootstrap Reboot <https://getbootstrap.com/docs/5.1/content/reboot/>_ 覆盖。
By default, each browser renders content using a *user agent stylesheet*. To overcome
inconsistencies between browsers, some of these rules are overridden by `Bootstrap Reboot
<https://getbootstrap.com/docs/5.1/content/reboot/>`_.

在此阶段，所有“浏览器特定装饰”规则都已被删除，但仍保留了定义基本布局信息的大部分规则（或由Reboot为了一致性原因加强了这些规则）。
At this stage all "browser-specific-decoration" rules have been stripped away, but a big chunk of
rules defining basic layout information is maintained (or reinforced by *Reboot* for consistency
reasons).

您可以依赖这些规则。
You can rely on these rules.

.. example::

   通常不需要为<div/>应用display: block;。
   Applying `display: block;` to a `<div/>` is normally not necessary.

   .. code-block:: css

      div.element {
         display: block;
         /* not needed 99% of the time */
      }

.. example::

   在这种情况下，您可以选择切换HTML标签，而不是添加新CSS规则。
   In this instance, you may opt to switching the HTML tag rather than adding a new CSS rule.

   .. code-block:: css

      span.element {
         display: block;
         /* replace <span> with <div> instead
            to get 'display: block' by default */
      }

以下是基本并非很全面的规则列表：
Here's a non-comprehensive list of default rules:

.. list-table::
   :header-rows: 1

   * - Tag / Attribute
     - Defaults
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
   `Bootstrap Reboot on GitHub
   <https://github.com/twbs/bootstrap/blob/1a6fdfae6b/scss/_reboot.scss>`_

.. _tutorials/scss_tips/html_tags:

HTML 标签
=========

这可能显而易见，但使文本看起来像标题的最简单、最一致的方法是使用标题标签 (<h1>, <h2>...)。除了重置规则外，几乎所有标签都带有Odoo定义的装饰样式。
It may seem obvious, but the simplest and most **consistent** way of making text look like a title
is to use a header tag (`<h1>`, `<h2>`, ...). Besides reboot rules, mostly all tags carry decorative
styles defined by Odoo.

.. rst-class:: bg-light
.. example::

   .. container:: alert alert-danger

      Don't

      .. tabs::

         .. code-tab:: html XML

            <span class="o_module_custom_title">
               Hello There!
            </span>

            <span class="o_module_custom_subtitle">
               I'm a subtitle.
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

      Do

      .. tabs::

         .. code-tab:: html XML

            <h5 class="o_module_custom_title">
               Hello There!
            </h5>

            <div class="o_module_custom_subtitle">
               <b><small>I'm a subtitle.</small></b>
            </div>

         .. code-tab:: css SCSS

            .o_module_custom_title {
               animation: 1s linear 1s mycustomAnimation;
            }

            .o_module_custom_subtitle {
               animation: 2s linear 1s mycustomAnimation;
            }

.. note::
   此外，模块化设计方法（使用类、标签、混合类等）不仅减少了代码量，还能保持视觉效果的一致性，并且易于维护。
   Besides reducing the amount of code, a modular-design approach (use classes, tags, mixins...)
   keeps the visual result consistent and easily **maintainable**.

   参考上一个示例，如果Odoo标题的设计发生变化，这些变化也会应用于o_module_custom_title元素，因为它使用了<h5>标签。
   Following the last example, if Odoo titles' design changes, these changes will be applied in the
   `o_module_custom_title` element too since it's using an `<h5>` tag.

.. _tutorials/scss_tips/utility_classes:

实用类
===============

我们的框架定义了大量的实用类，旨在涵盖几乎所有布局、设计和交互需求。由于这些类已经存在，因此在可能的情况下，应该优先使用它们，而不是编写自定义CSS。
Our framework defines a multitude of utility classes designed to cover almost all
layout/design/interaction needs. The simple fact that a class already exists justifies its use over
custom CSS whenever possible.

以 `position-relative`. 示例为例

.. code-block:: css

   position-relative {
      position: relative !important;
   }

由于已经定义了实用类，任何带有 `position: relative` 声明的CSS代码 **可能** 都是多余的。

Odoo 依赖于默认的 [Bootstrap 实用类](https://getbootstrap.com/docs/5.1/utilities/background/) 集合，并使用 [Bootstrap API](https://getbootstrap.com/docs/5.1/utilities/api/) 定义了自己的实用类。
Odoo relies on the default `Bootstrap utility-classes

.. seealso::
   - `Bootstrap utility classes <https://getbootstrap.com/docs/5.1/utilities/api/>`_
   - `Odoo custom utilities on github
     <{GITHUB_PATH}/addons/web/static/src/scss/utilities_custom.scss>`_

.. _tutorials/scss_tips/utility_classes/downside:


处理实用类的冗长性
----------------------------------

实用类的一个缺点是可能会导致代码可读性下降。

.. example::

   .. code-block:: html

      <myComponent t-attf-class="d-flex border px-lg-2 card
      {{props.readonly ? 'o_myComponent_disabled' : ''}}
      card d-lg-block position-absolute {{props.active ?
      'o_myComponent_active' : ''}}  myComponent px-3"/>


为了解决这个问题，您可以结合不同的方法：

- 在 Qweb 属性中，只使用需要**动态切换**的类；
- 每个属性使用单独的换行；
- 按照 `[odoo 组件] [bootstrap 组件] [CSS 声明顺序]` 的约定来排列类。

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
   :ref:`Odoo CSS properties order <contributing/coding_guidelines/scss/properties_order>`
