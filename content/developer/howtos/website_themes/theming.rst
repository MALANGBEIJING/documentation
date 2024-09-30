========
主题化
========

在完成开发环境的设置后，您可以开始构建主题模块的框架。本章将帮助您了解如何：

- 启用/禁用网站构建器的标准选项和模板。
- 定义设计中要使用的颜色和字体。
- 最大化使用 Bootstrap 变量。
- 添加自定义样式和 JavaScript。

主题模块
==========

Odoo 提供了一个默认主题，提供最小的结构和布局。当您创建一个新主题时，您是在扩展默认主题。

记住，在运行 Odoo 的开发环境中，使用 `addons-path` 命令行参数添加包含模块的目录。

技术命名
------------

第一步是创建一个新目录。

.. code-block:: xml

   website_airproof

.. note::
   以 `website_` 作为前缀，并且只使用小写 ASCII 字母数字字符和下划线。

   在本文档中，我们将使用 **Airproof**（一个虚构项目）作为示例。

文件结构
--------------

主题是像 Odoo 模块一样打包的。即使您在设计一个基本的网站，您也需要像打包模块一样打包它的主题。

::

    website_airproof
    ├── data
    ├── i18n
    ├── lib
    ├── static
    │   ├── description
    │   ├── fonts
    │   ├── image_shapes // 图像形状
    │   ├── shapes // 背景形状
    │   └── src
    │       ├── img
    │       │   ├── content // 用于网站页面的图片
    │       │   └── wbuilder // 用于构建器的图片
    │       ├── js
    │       ├── scss
    │       └── snippets // 自定义代码片段
    ├── views
    ├── __init__.py
    └── __manifest__.py

.. list-table::
   :header-rows: 1
   :stub-columns: 1
   :widths: 20 80

   * - 文件夹
     - 描述
   * - data
     - 预设、菜单、页面、图像、形状等 (`*.xml`)
   * - i18n
     - 翻译文件 (`*.po`, `*.pot`)
   * - lib
     - 外部库 (`*.js`)
   * - static
     - 自定义资源文件 (`*.jpg`, `*.gif`, `*.png`, `*.svg`, `*.pdf`, `*.scss`, `*.js`)
   * - views
     - 自定义视图和模板 (`*.xml`)

初始化
--------------

Odoo 模块也是一个 Python 包，它包含一个 :file:`__init__.py` 文件，该文件包含导入指令。这个文件现在可以保持为空。

声明
-----------

Odoo 模块通过其清单文件声明。此文件将 Python 包声明为 Odoo 模块，并指定模块的元数据。它至少必须包含 `name` 字段，这是始终必需的。通常，它还包含更多信息。

.. code-block:: python
   :caption: ``/website_airproof/__manifest__.py``

   {
      'name': 'Airproof Theme',
      'description': '...',
      'category': 'Website/Theme',
      'version': '15.0.0',
      'author': '...',
      'license': '...',
      'depends': ['website'],
      'data': [
         # ...
      ],
      'assets': {
         # ...
      },
   }

.. list-table::
   :header-rows: 1
   :stub-columns: 1
   :widths: 20 80

   * - 字段
     - 描述
   * - name
     - 模块的人类可读名称（必需）
   * - description
     - 模块的扩展描述，使用 `reStructuredText
       <https://en.wikipedia.org/wiki/ReStructuredText>`_
   * - category
     - Odoo 内的分类
   * - version
     - 模块适配的 Odoo 版本
   * - author
     - 模块作者
   * - license
     - 模块的发行许可
   * - depends
     - 需要在加载此模块之前加载的 Odoo 模块，因为该模块可能使用它们创建的功能或修改它们定义的资源
   * - data
     - XML 文件列表
   * - assets
     - SCSS 和 JS 文件列表

.. note::
   要创建一个网站主题，您只需要安装网站应用。如果需要其他应用程序（博客、活动、电子商务等），您也可以添加它们。

默认选项
===============

首先，尝试使用 Odoo 的默认选项构建您的主题。这确保了两件事：

#. 您不会重复发明已经存在的东西。例如，Odoo 提供了为页脚添加边框的选项，您不需要自己编写代码，只需启用默认选项，然后根据需要进行扩展。
#. 用户在使用您的主题时，仍然可以使用 Odoo 的所有功能。例如，如果您重新编写页脚的边框，您可能会破坏默认选项或使其无用，从而给用户带来不良体验。并且，您的重新编写可能不如默认选项好用，因为其他 Odoo 功能可能依赖于它。

.. tip::
   - 每个缩进级别使用四个空格。
   - 不要使用制表符。
   - 切勿混合使用空格和制表符。

.. seealso::
   :doc:`Odoo 编码规范 <../../../contributing/development/coding_guidelines>`

Odoo 变量
--------------

Odoo 声明了许多 CSS 规则，大多数规则都可以通过重写相关的 SCSS 变量进行定制。为此，创建一个 :file:`primary_variables.scss` 文件，并将其添加到 `_assets_primary_variables` 包中。

**声明**

.. code-block:: python
   :caption: ``/website_airproof/__manifest__.py``

   'assets': {
      'web._assets_primary_variables': [
         ('prepend', 'website_airproof/static/src/scss/primary_variables.scss'),
      ],
   },

通过阅读源代码，您可以轻松找到与选项相关的变量。

.. code-block:: xml

   <we-button title="..."
      data-name="..."
      data-customize-website-views="..."
      data-customize-website-variable="'Sidebar'"
      data-img="..."/>

这些变量可以通过 `$o-website-value-palettes` 映射进行重写，例如。

全局
~~~~~~

**声明**

.. code-block:: scss
   :caption: ``/website_airproof/static/src/scss/primary_variables.scss``

   $o-website-values-palettes: (
      (
         // 模板
         // 颜色
         // 字体
         // 按钮
         // ...
      ),
   );

.. tip::
   该文件只能包含 SCSS 变量和混合宏的定义和重写。

.. seealso::
   `主变量 SCSS
   <https://github.com/odoo/odoo/blob/34c0c9c1ae00aba391932129d4cefd027a9c6bbd/addons/website/static/src/scss/primary_variables.scss#L1954>`_

字体
~~~~~

您可以在网站上嵌入任何字体。网站构建器会自动使它们可用于字体选择器中。

**声明**

.. code-block:: scss
   :caption: ``/website_airproof/static/src/scss/primary_variables.scss``

   $o-theme-font-configs: (
      <font-name>: (
         'family': <css font family list>,
         'url' (optional): <相关的 Google 字体 URL 部分>,
         'properties' (optional): (
            <font-alias>: (
               <website-value-key>: <value>,
               ...,
            ),
         ...,
      )
   )

**使用**

.. code-block:: scss
   :caption: ``/website_airproof/static/src/scss/primary_variables.scss``

   $o-website-values-palettes: (
      (
         'font':                             '<font-name>',
         'headings-font':                    '<font-name>',
         'navbar-font':                      '<font-name>',
         'buttons-font':                     '<font-name>',
      ),
   );

Google 字体
************

.. code-block:: scss
   :caption: ``/website_airproof/static/src/scss/primary_variables.scss``

   $o-theme-font-configs: (
      'Poppins': (
         'family':                         ('Poppins', sans-serif),
         'url':                            'Poppins:400,500',
         'properties' : (
            'base': (
               'font-size-base':           1rem,
            ),
         ),
      ),
   );

自定义字体
************

首先，创建一个特定的 SCSS 文件来声明您的自定义字体。

.. code-block:: python
   :caption: ``/website_airproof/__manifest__.py``

   'assets': {
      'web.assets_frontend': [
         'website_airproof/static/src/scss/font.scss',
      ],
   },

然后，使用 `@font-face` 规则使您的自定义字体能够在网站上加载。

.. code-block:: scss
   :caption: ``/website_airproof/static/src/scss/font.scss``

   @font-face {
      font-family: '<font-name>';
      ...
   }

.. code-block:: scss
   :caption: ``/website_airproof/static/src/scss/primary_variables.scss``

   $o-theme-font-configs: (
      'Proxima Nova': (
         'family':                         ('Proxima Nova', sans-serif),
         'properties' : (
            'base': (
               'font-size-base':           1rem,
            ),
         ),
      ),
   );

.. tip::
   建议使用 .woff 格式的字体。

颜色
~~~~~~

网站构建器依赖于由五种命名颜色组成的调色板。在您的主题中定义这些颜色可以确保设计的一致性。

.. list-table::
   :header-rows: 1
   :stub-columns: 1
   :widths: 20 80

   * - 颜色
     - 描述
   * - o-color-1
     - 主色
   * - o-color-2
     - 次色
   * - o-color-3
     - 额外色
   * - o-color-4
     - 白色系
   * - o-color-5
     - 黑色系

.. image:: theming/theme-colors.png
   :alt: 主题颜色
   :width: 300

**声明**

.. code-block:: scss
   :caption: ``/website_airproof/static/src/scss/primary_variables.scss``

   $o-color-palettes: map-merge($o-color-palettes,
      (
         'airproof': (
            'o-color-1':                    #bedb39,
            'o-color-2':                    #2c3e50,
            'o-color-3':                    #f2f2f2,
            'o-color-4':                    #ffffff,
            'o-color-5':                    #000000,
         ),
      )
   );

将创建的调色板添加到网站构建器提供的调色板列表中。

.. code-block:: scss

   $o-selected-color-palettes-names: append($o-selected-color-palettes-names, 'airproof');

**使用**

.. code-block:: scss
   :caption: ``/website_airproof/static/src/scss/primary_variables.scss``

   $o-website-values-palettes: (
      (
         'color-palettes-name':              'airproof',
      ),
   );

.. image:: theming/theme-colors-airproof.png
   :alt: 主题颜色 Airproof
   :width: 800

**颜色组合**

基于先前定义的五种调色板，网站构建器会自动生成五种颜色组合，每种组合分别定义背景、文本、标题、链接、主按钮和次按钮的颜色。这些颜色可以稍后由用户自定义。

.. image:: theming/theme-colors-big.png
   :alt: 主题颜色
   :width: 300

颜色组合中使用的颜色可以通过 BS `$colors map` 访问，并且可以通过特定的前缀（`o-cc` 表示 `color combination`）重写。

.. code-block:: scss
   :caption: ``/website_airproof/static/src/scss/primary_variables.scss``

   $o-color-palettes: map-merge($o-color-palettes,
      (
         'airproof': (

            'o-cc*-bg':                     'o-color-*',
            'o-cc*-text':                   'o-color-*',
            'o-cc*-headings':               'o-color-*',
            'o-cc*-h2':                     'o-color-*',
            'o-cc*-h3':                     'o-color-*',
            'o-cc*-h4':                     'o-color-*',
            'o-cc*-h5':                     'o-color-*',
            'o-cc*-h6':                     'o-color-*',
            'o-cc*-link':                   'o-color-*',
            'o-cc*-btn-primary':            'o-color-*',
            'o-cc*-btn-primary-border':     'o-color-*',
            'o-cc*-btn-secondary':          'o-color-*',
            'o-cc*-btn-secondary-border':   'o-color-*',

         ),
      )
   );

.. note::
   对于每个 `o-cc*`，将 `*` 替换为对应颜色组合的数字（1 - 5）。

   默认文本颜色是 `o-color-5`。如果背景太暗，它将自动更改为 `o-color-4` 颜色。

.. seealso::
   `颜色组合 SCSS
   <https://github.com/odoo/odoo/blob/34c0c9c1ae00aba391932129d4cefd027a9c6bbd/addons/web_editor/static/src/scss/web_editor.common.scss#L711>`_

.. admonition:: 示例页面

   网站构建器会自动生成一个页面来查看主题调色板的颜色组合：http://localhost:8069/website/demo/color-combinations

Bootstrap 变量
-------------------

Odoo 默认包含 Bootstrap。您可以使用框架的所有变量和混合宏。

如果 Odoo 没有提供您所需的变量，Bootstrap 可能提供了它。事实上，所有 Odoo 布局都遵循 Bootstrap 结构，并使用 Bootstrap 组件或其扩展。如果您自定义了一个 Bootstrap 变量，您将为整个用户网站添加通用样式。

使用一个专门的文件并将其添加到 :file:`_assets_frontend_helpers` 包中以覆盖 Bootstrap 值，而不是 :file:`primary_variables.scss` 文件。

**声明**

.. code-block:: python
   :caption: ``/website_airproof/__manifest__.py``

   'assets': {
      'web._assets_frontend_helpers': [
         ('prepend', 'website_airproof/static/src/scss/bootstrap_overridden.scss'),
      ],
   },

**使用**

.. code-block:: scss
   :caption: ``/website_airproof/static/src/scss/bootstrap_overridden.scss``

   // 排版
   $h1-font-size:                 4rem !default;

   // 导航栏
   $navbar-nav-link-padding-x:    1rem!default;

   // 按钮 + 表单
   $input-placeholder-color:      o-color('o-color-1') !default;

   // 卡片
   $card-border-width:            0 !default;

.. tip::
   该文件只能包含 SCSS 变量和混合宏的定义和重写。

.. warning::
   不要覆盖依赖 Odoo 变量的 Bootstrap 变量。否则，您可能会破坏用户通过网站构建器自定义它们的能力。

.. seealso::
   `Bootstrap 重写 SCSS
   <{GITHUB_PATH}/addons/website/static/src/scss/bootstrap_overridden.scss>`_

.. admonition:: 示例页面

   http://localhost:8069/website/demo/bootstrap

视图
-----

对于某些选项，除了网站构建器的变量之外，您还必须激活特定的视图。

通过阅读源代码，您可以轻松找到与选项相关的模板。

.. code-block:: xml

   <we-button title="..."
      data-name="..."
      data-customize-website-views="website.template_header_default"
      data-customize-website-variable="'...'"
      data-img="..."/>

.. code-block:: xml

   <template id="..." inherit_id="..." name="..." active="True"/>
   <template id="..." inherit_id="..." name="..." active="False"/>

.. example::
   **更改菜单项的水平对齐方式**

   .. code-block:: xml
      :caption: ``/website_airproof/data/presets.xml``

      <record id="website.template_header_default_align_center" model="ir.ui.view">
         <field name="active" eval="True"/>
      </record>

   同样的逻辑也适用于其他 Odoo 应用。

   **电子商务 - 显示产品分类**

   .. code-block:: xml

       <record id="website_sale.products_categories" model="ir.ui.view">
          <field name="active" eval="False"/>
       </record>

   **门户 - 禁用语言选择器**

   .. code-block:: xml

      <record id="portal.footer_language_selector" model="ir.ui.view">
         <field name="active" eval="False"/>
      </record>

资源
======

在这一部分中，我们将参考位于 `web` 模块中的 `assets_frontend` 包。此包指定网站构建器加载的资源列表，目标是将您的 SCSS 和 JS 文件添加到该包中。

样式
------

网站构建器与 Bootstrap 一起使用，能够定义您的网站的基本样式。但是，要设计一些独特的内容，您可以更进一步。为此，您可以轻松地将任何 SCSS 文件添加到您的主题中。

**声明**

.. code-block:: python
   :caption: ``/website_airproof/__manifest__.py``

   'assets': {
      'web.assets_frontend': [
         'website_airproof/static/src/scss/theme.scss',
      ],
   },

您可以随意在您的 :file:`theme.scss` 文件中重用您的 Bootstrap 文件和 Odoo 使用的变量。

.. example::
   .. code-block:: javascript
      :caption: ``/website_airproof/static/src/scss/theme.scss``

       blockquote {
         border-radius: $rounded-pill;
         color: o-color('o-color-3');
         font-family: o-website-value('headings-font');
       }

交互性
-------------

Odoo 支持三种不同类型的 JavaScript 文件：

- :ref:`普通 JavaScript 文件 <frontend/modules/plain_js>`（无模块系统），
- :ref:`原生 JavaScript 模块 <frontend/modules/native_js>`，和
- :ref:`Odoo 模块 <frontend/modules/odoo_module>`（使用自定义模块系统）。

大多数新的 Odoo JavaScript 代码应使用原生 JavaScript 模块系统。它更简单，并且可以带来更好的开发体验，与 IDE 更好地集成。

.. important::
   Odoo 需要知道哪些文件应该转换为 :ref:`Odoo 模块
   <frontend/js_modules>`，而哪些文件不应该。这是一个选择加入的系统：Odoo 检查 JavaScript 文件的第一行是否包含字符串 `@odoo-module`。如果包含，它将自动转换为 Odoo 模块。

.. code-block:: javascript

   /** @odoo-module **/

**声明**

.. code-block:: python
   :caption: ``/website_airproof/__manifest__.py``

   'assets': {
      'web.assets_frontend': [
         'website_airproof/static/src/js/theme.js',
      ],
   },

.. note::
   如果您想包含外部库中的文件，可以将它们添加到模块的 :file:`/lib` 文件夹中。

.. tip::
   - 使用代码质量检查工具（如 JSHint 等）。
   - 切勿添加压缩的 JavaScript 库。
   - 在每个 Odoo JavaScript 模块的顶部添加 `'use strict';`。
   - 变量和函数应该使用驼峰命名法（`myVariable`）而不是下划线命名法（`my_variable`）。
   - 不要将变量命名为 `event`；使用 `ev.` 代替。这样可以避免在非 Chrome 浏览器中出现错误，因为 Chrome 会自动分配一个全局的 event 变量（所以如果您使用未声明的 event 变量，在 Chrome 上可能可以正常工作，但会在其他浏览器上崩溃）。
   - 使用严格的比较运算符（使用 `===` 而不是 `==`）。
   - 对所有文本字符串使用双引号（如 `"Hello"`），而对所有其他字符串使用单引号，例如 CSS 选择器 `.x_nav_item`。
   - 始终使用 `this._super.apply(this, arguments)`。

.. seealso::
   - `Odoo JavaScript 编码规范 <https://github.com/odoo/odoo/wiki/Javascript-coding-guidelines>`_
   - :doc:`Odoo JavaScript 框架概述
     <../../reference/frontend/javascript_reference>`
   - `Odoo 体验演讲：10 个提升您网站设计的技巧！ <https://www.youtube.com/watch?v=vAgE_fPVXUQ&ab_channel=Odoo>`_
