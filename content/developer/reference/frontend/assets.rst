.. _reference/assets:

======
资产
======

在Odoo中管理资产并不像在其他某些应用程序中那样简单。其原因之一是我们面临着多种情况，有些资产是必需的，而有些则不是。例如，网页客户端、销售点应用程序、网站甚至移动应用程序的需求各不相同。此外，一些资产可能很大，但很少使用：在这种情况下，我们可能希望它们被 :ref:`惰性加载（按需） <frontend/assets/lazy_loading>`。

资产类型
===========

有三种不同的资产类型：代码（`js` 文件）、样式（`css` 或 `scss` 文件）和模板（`xml` 文件）。

代码
    Odoo 支持 :ref:`三种不同类型的 JavaScript 文件 <frontend/js_modules>`。所有这些文件都将被处理（本地 JS 模块被转换为 Odoo 模块），然后被压缩（如果不在 `debug=assets` :ref:`模式 <frontend/framework/assets_debug_mode>` 下），并连接在一起。结果将作为文件附件保存。这些文件附件通常通过 `<head>` 部分的 `<script>` 标签加载（作为静态文件）。

样式
    样式可以使用 `css` 或 `scss <https://sass-lang.com/>`_ 来完成。与 JavaScript 文件一样，这些文件也会被处理（`scss` 文件被转换为 `css`），然后被压缩（同样，如果不在 `debug=assets` :ref:`模式 <frontend/framework/assets_debug_mode>` 下）并连接在一起。结果随后被保存为文件附件。它们通常通过 `<head>` 部分的 `<link>` 标签加载（作为静态文件）。

模板
    模板（静态的 `xml` 文件）以不同的方式处理：它们仅在需要时从文件系统读取，并连接在一起。

    每当浏览器加载 Odoo 时，它会调用 `/web/webclient/qweb/` 控制器以获取 :ref:`模板 <reference/qweb>`。

值得一提的是，在大多数情况下，浏览器仅在第一次加载页面时才会发出请求。这是因为每个资产都与一个校验和关联，该校验和被注入到页面源代码中。校验和随后被添加到 URL，这意味着可以安全地将缓存头设置为较长的时间。

.. _reference/assets_bundle:

捆绑
=======

Odoo 资产按 *捆绑* 分组。每个捆绑（特定类型的 *文件路径列表*：`xml`、`js`、`css` 或 `scss`）在 :ref:`模块清单 <reference/module/manifest>` 中列出。可以使用 `glob <https://en.wikipedia.org/wiki/Glob_(programming)>`_ 语法声明文件，这意味着您可以使用一行声明多个资产文件。

捆绑在每个模块的 :file:`__manifest__.py` 中定义，具有一个专用的 `assets` 键，其中包含一个字典。该字典将捆绑名称（键）映射到它们包含的文件列表（值）。它看起来像这样：

.. code-block:: python

    'assets': {
        'web.assets_backend': [
            'web/static/src/xml/**/*',
        ],
        'web.assets_common': [
            'web/static/lib/bootstrap/**/*',
            'web/static/src/js/boot.js',
            'web/static/src/js/webclient.js',
            'web/static/src/xml/webclient.xml',
        ],
        'web.qunit_suite_tests': [
            'web/static/src/js/webclient_tests.js',
        ],
    },

以下是一些大多数 Odoo 开发人员需要了解的重要捆绑列表：

- `web.assets_common`：此捆绑包含大多数通用资产，这些资产适用于网页客户端、网站以及销售点。它应包含 Odoo 框架的底层构建块。请注意，它包含 :file:`boot.js` 文件，该文件定义了 Odoo 模块系统。

- `web.assets_backend`：此捆绑包含特定于网页客户端的代码（尤其是网页客户端/操作管理器/视图/静态 XML 模板）。

- `web.assets_frontend`：此捆绑与公共网站的所有内容有关：电子商务、门户、论坛、博客等。

- `web.qunit_suite_tests`：所有 JavaScript QUnit 测试代码（测试、助手、模拟）。

- `web.qunit_mobile_suite_tests`：特定于移动的 QUnit 测试代码。

操作
----------

通常，处理资产是简单的：您只需将一些新文件添加到像 `assets_common` 或 `assets_backend` 这样的常用捆绑中。但是，还有其他操作可用于处理一些更特定的用例。

请注意，所有针对特定资产文件的指令（即 `before`、`after`、`replace` 和 `remove`）都需要提前声明该文件，或者在更高层次的清单中声明，或在 ``ir.asset`` 记录中具有较低的序列。

`append`
~~~~~~~~

此操作添加一个或多个文件。由于这是最常见的操作，可以通过简单地使用文件名来完成：

.. code-block:: python

  'web.assets_common': [
      'my_addon/static/src/js/**/*',
  ],

默认情况下，将简单字符串添加到捆绑中将会将匹配的文件添加到捆绑的末尾。当然，模式也可以直接是单个文件路径。

`prepend`
~~~~~~~~~

在捆绑的开头添加一个或多个文件。

在捆绑中需要将特定文件放在其他文件之前时（例如与 CSS 文件），非常有用。`prepend` 操作通过以下语法调用： `('prepend', <path>)`。

.. code-block:: python

  'web.assets_common': [
      ('prepend', 'my_addon/static/src/css/bootstrap_overridden.scss'),
  ],

`before`
~~~~~~~~

在特定文件之前添加一个或多个文件。

在捆绑的开头插入文件可能不够精确。`before` 指令可用于将给定文件添加到目标文件的 *前面*。它通过将正常路径替换为 3 元组 `('before', <target>, <path>)` 声明。

.. code-block:: python

  'web.assets_common': [
      ('before', 'web/static/src/css/bootstrap_overridden.scss', 'my_addon/static/src/css/bootstrap_overridden.scss'),
  ],

`after`
~~~~~~~

在特定文件之后添加一个或多个文件。

与 `before` 相同，匹配的文件将在目标文件 *之后* 添加。它通过将正常路径替换为 3 元组 `('after', <target>, <path>)` 声明。

.. code-block:: python

  'web.assets_common': [
      ('after', 'web/static/src/css/list_view.scss', 'my_addon/static/src/css/list_view.scss'),
  ],

`include`
~~~~~~~~~

使用嵌套捆绑。

`include` 指令是将捆绑用于其他捆绑的一种方式，以最小化清单的大小。在 Odoo 中，我们使用子捆绑（按约定以一个下划线开头）来批量处理在多个其他捆绑中使用的文件。您可以像这样指定子捆绑：`('include', <bundle>)`：

.. code-block:: python

  'web.assets_common': [
      ('include', 'web._primary_variables'),
  ],

`remove`
~~~~~~~~

删除一个或多个文件。

在某些情况下，您可能希望从捆绑中删除一个或多个文件。这可以使用 `remove` 指令通过指定一对 `('remove', <target>)` 完成：

.. code-block:: python

  'web.assets_common': [
      ('remove', 'web/static/src/js/boot.js'),
  ],

`replace`
~~~~~~~~~

用一个或多个文件替换资产文件。

假设资产不仅需要被移除，而且您还想在同一确切位置插入您新版本的资产。这可以通过 `replace` 指令完成，使用 3 元组 `('replace', <target>, <path>)`：

.. code-block:: python

  'web.assets_common': [
      ('replace', 'web/static/src/js/boot.js', 'my_addon/static/src/js/boot.js'),
  ],


加载顺序
-------------

资产加载的顺序有时是关键的，必须是确定性的，主要用于样式表优先级和设置脚本。Odoo 中的资产处理如下：

#. 当调用资产捆绑时（例如 `t-call-assets="web.assets_common"`），将生成一个空资产列表。

#. 将获取所有与捆绑匹配的类型为 `ir.asset` 的记录，并按序列号排序。然后处理所有序列严格小于 16 的记录，并将其应用于当前资产列表。

#. 声明对该捆绑的资产的所有模块将对该列表应用它们的资产操作。这是根据模块依赖关系的顺序进行的（例如，`web` 资产在 `website` 之前处理）。如果指令尝试添加已经存在于列表中的文件，则不会对该文件进行任何操作。换句话说，列表中只保留文件的第一次出现。

#. 然后处理剩余的 `ir.asset` 记录（序列大于或等于 16）。

在清单中声明的资产可能需要以特定顺序加载，例如 :file:`jquery.js` 必须在加载 lib 文件夹中的所有其他 jquery 脚本之前加载。一种解决方案是创建一个 :ref:`ir.asset <frontend/assets/ir_asset>` 记录，具有较低的序列或使用 'prepend' 指令，但还有另一种更简单的方法。

由于资产列表中每个文件路径的唯一性得到了保证，因此您可以在包含它的 glob 之前提到任何特定文件。该文件将因此在所有其他包含在 glob 中的文件之前出现在列表中。

.. code-block:: python

    'web.assets_common': [
        'my_addon/static/lib/jquery/jquery.js',
        'my_addon/static/lib/jquery/**/*',
    ],

.. note::

    模块 *b* 删除/替换在模块 *a* 中声明的资产时，必须依赖于它。尝试对尚未声明的资产进行操作将导致错误。

.. _frontend/assets/lazy_loading:

惰性加载
============

有时动态加载文件和/或资产捆绑是有用的，例如仅在需要时加载库。为此，Odoo 框架提供了一些帮助函数，位于 :file:`@web/core/assets` 中。

.. code-block:: javascript

  await loadAssets({
      jsLibs: ["/web/static/lib/stacktracejs/stacktrace.js"],
  });


.. js:function:: loadAssets(assets)

    :param Object assets: 应该加载的各种资产的描述
    :returns: Promise<void>

    加载由 `assets` 参数描述的资产。它是一个可能包含以下键的对象：

    .. list-table::
      :widths: 20 20 60
      :header-rows: 1

      * - Key
        - Type
        - Description
      * - `jsLibs`
        - `string[]`
        - JavaScript 文件的 URL 列表
      * - `cssLibs`
        - `string[]`
        - CSS 文件的 URL 列表


.. js:function:: useAssets(assets)

    :param Object assets: 应该加载的各种资产的描述

    当组件需要在其 `onWillStart` 方法中加载某些资产时，此钩子非常有用。它会在内部调用 `loadAssets`。

.. _frontend/assets/ir_asset:

资产模型（`ir.asset`）
============================

在大多数情况下，清单中声明的资产将足够了。然而，为了获得更多灵活性，框架还支持在数据库中声明动态资产。

这通过创建 `ir.asset` 记录完成。这些记录将被处理，就像在模块清单中找到的一样，它们提供与清单对应物相同的表达能力。

.. autoclass:: odoo.addons.base.models.ir_asset.IrAsset

.. rst-class:: o-definition-list

`name`
    资产记录的名称（用于标识目的）。

`bundle`
    资产将应用的捆绑。

`directive`（默认 = `append`）
    此字段决定 `path`（以及必要时的 `target`）将如何解释。
    以下是可用指令及其所需参数的列表：

    - **append**: `path`
    - **prepend**: `path`
    - **before**: `target`, `path`
    - **after**: `target`, `path`
    - **include**: `path`（解释为 **捆绑名称**）
    - **remove**: `path`（解释为要删除的 **目标资产**）
    - **replace**: `target`, `path`

`path`
    定义以下内容的字符串：

    - 到 addons 文件系统中资产文件的 **相对路径**；
    - 到 addons 文件系统中一组资产文件的 **glob 模式**；
    - 到附件或外部资产文件的 **URL**；
    - 使用 `include` 指令时的 **捆绑名称**。

`target`
    目标文件，用于指定捆绑中的位置。只能与 `replace`、`before` 和 `after` 指令一起使用。

`active`（默认 = `True`）
    记录是否处于活动状态

`sequence`（默认 = `16`）
    资产记录的加载顺序（升序）。序列小于 16 的资产将在声明的资产之前处理。
