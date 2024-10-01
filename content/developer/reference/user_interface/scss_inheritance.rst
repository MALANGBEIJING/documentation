================
SCSS 继承
================

概述
========

在Odoo中管理SCSS资产并不像在其他某些环境中那样简单，但它的效率极高。

模块化是关键。进一步描述的继承方案允许Odoo：

- 自定义Bootstrap CSS框架；
- 处理两种不同的Web客户端设计（社区版和企业版）；
- 分别处理后端和前端捆绑（包括用户的网站设计）；
- 上下文加载仅必要的资产；
- 处理多种配色方案（例如：暗黑模式）；

SCSS的 `!default` 指令
===========================

在SCSS中，"直接变量覆盖" 在技术上是可能的，但在像Odoo这样的复杂环境中可能会导致不一致的结果。

.. example::

   .. code-block:: scss
      :caption: :file:`library.scss`

      $foo: red;

   .. code-block:: scss
      :caption: :file:`customization_layer.scss`

      $foo: blue; // -> 不要这样做！

事实上，由于编译过程在不同的相互依赖的捆绑之间进行，因此在"错误的位置"重新分配变量可能导致意想不到的级联结果。

SCSS提供了几种技术来克服这些问题（例如：`shadowing <https://sass-lang.com/documentation/variables#shadowing>`_），但在Odoo中最关键的过程是使用 `!default` 标志。

使用 `!default` 标志时，编译器仅在该变量尚未定义的情况下分配一个值。

由于这一技术，变量分配的优先级与资产的加载顺序相匹配。

.. example::

   .. code-block:: scss
      :caption: :file:`customization_layer.scss`

      $foo: red !default;

   .. code-block:: scss
      :caption: :file:`library.scss`

      $foo: blue !default; // -> 已经定义，行被忽略。
      $bar: black !default; // -> 尚未定义，值被分配。

   .. code-block::
      :caption: :file:`component.scss`

      .component {
         color: $foo; // -> 'color: red;'
         background: $bar; // -> 'background: black;'
      }

.. seealso::
   `!default` 标志在 `SASS 文档 <https://sass-lang.com/documentation/variables#default-values>`_ 中的说明

Odoo的SCSS继承系统
==============================

以下图表概念性地说明了定义CSS和SCSS变量的编译顺序。

.. code-block:: text

    ↓ [编译开始]
    ⏐
    ↓ web.dark_mode_variables
    ⏐   ├─ 主要变量
    ⏐   └─ 组件变量
    ⏐
    ↓ web._assets_primary_variables
    ⏐   ├─ 主要变量（企业版）
    ⏐   ├─ 组件变量（企业版）
    ⏐   ├─ 主要变量（社区版）
    ⏐   └─ 组件变量（社区版）
    ⏐
    ↓ web._assets_bootstrap
    ⏐
    ↓ web.assets_backend
    ⏐   ├─ ...
    ⏐   ├─ CSS变量定义
    ⏐   └─ CSS变量上下文适配
    ⏐
    ● [在屏幕上的可视结果]

.. important::
   该图表不完整，且与当前捆绑的组织结构不符。有关更多信息，请阅读：:ref:`资产捆绑 <reference/assets_bundle>`。
