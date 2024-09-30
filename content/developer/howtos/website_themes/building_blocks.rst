===============
构建块
===============

构建块，也称为片段，是用户设计和布局页面的方式。它们是设计中重要的 XML 元素。

构建块分为四类：

#. **结构块**：为网站提供基本结构
#. **功能块**：用于描述产品或服务的功能
#. **动态内容块**：带有动画或与后端交互的块
#. **内部内容块**：用于嵌入在其他构建块中的块

在本章中，您将学习如何创建自定义构建块和选项。

文件结构
==============

布局的文件结构如下所示。

::

    views
    ├── snippets
    │   └── options.xml
    │   └── s_snippet_name.xml

样式的文件结构如下所示。

::

    static
    ├── src
    │   └── snippets
    │       └── options.scss
    │       └── s_snippet_name
    │           └── 000.js
    │           └── 000.scss
    │           └── 000.xml
    │           └── option.js

.. seealso::
   `不同片段的 XML 模板
   <{GITHUB_PATH}/addons/website/views/snippets/snippets.xml>`_

.. admonition:: 示例页面

   http://localhost:8069/website/demo/snippets

布局
======

用户可以使用网站构建器编辑片段。某些 Bootstrap 类非常重要，因为**它们会触发网站构建器的某些选项**。

包装器
-------

任何片段的标准主容器是 `section`。任何 section 元素都可以像内容块一样进行编辑，您可以移动或复制它。

.. code-block:: xml

   <section class="s_snippet_name" data-name="..." data-snippet="...">
       <!-- 内容 -->
   </section>

对于内部内容片段，可以使用任何其他 HTML 标签。

.. code-block:: xml

   <div class="s_snippet_name" data-name="..." data-snippet="...">
       <!-- 内容 -->
   </div>

.. todo:: 缺少表格描述...

.. list-table::
   :header-rows: 1
   :stub-columns: 1
   :widths: 20 80

   * - 属性
     - 描述
   * - class
     - 此片段的唯一类名
   * - data-name
     - 在右侧面板中显示为片段的名称。如果找不到，将回退为 *Block*。
   * - data-snippet
     - 系统用于标识片段

系统会在拖放过程中根据模板的名称自动添加 `data-name` 和 `data-snippet` 属性。

.. warning::
   声明片段时，应特别添加这些属性。

.. warning::
   避免在一个 `section` 标签中嵌套另一个 `section` 标签：这将触发两次网站构建器的选项。您可以改为使用内部内容片段。

列
-------

直接从 `.row` 元素派生的任何大 Bootstrap 列（遵循 Bootstrap 结构）将由网站构建器触发，使其可调整大小。

.. code-block:: css

   .row > .col-lg-*

为列和段添加内边距。

.. code-block:: xml

   class="pt80 pb80"

为列和段添加基于调色板的背景。

.. code-block:: xml

   class="o_cc o_cc*"

使元素不可编辑。

.. code-block:: xml

   <div class="o_not_editable">

启用列选择器。

.. code-block:: xml

   <div class="container s_allow_columns">

禁用列选项。

.. code-block:: xml

   <div class="row s_nb_column_fixed">

禁用所有子列的大小选项。

.. code-block:: xml

   <div class="row s_col_no_resize">

禁用单列的大小选项。

.. code-block:: xml

   <div class="col-lg-* s_col_no_resize">

禁用所有列的背景颜色选项。

.. code-block:: xml

   <div class="row s_col_no_bgcolor">

禁用单列的背景颜色选项。

.. code-block:: xml

   <div class="col-lg-* s_col_no_bgcolor">

添加视差效果。

.. code-block:: xml

   <section class="parallax s_parallax_is_fixed s_parallax_no_overflow_hidden" data-scroll-background-ratio="1">
       <span class="s_parallax_bg oe_img_bg o_bg_img_center" style="background-image: url('...'); background-position: 50% 75%;"/>
       <div class="container">
           <!-- 内容 -->
       </div>
   </section>

添加不透明度为 50% 的黑色滤镜。

.. code-block:: xml

   <section>
       <div class="o_we_bg_filter bg-black-50"/>
       <div class="container">
           <!-- 内容 -->
       </div>
   </section>

添加不透明度为 85% 的白色滤镜。

.. code-block:: xml

   <section>
       <div class="o_we_bg_filter bg-white-85"/>
       <div class="container">
           <!-- 内容 -->
       </div>
   </section>

添加自定义颜色滤镜。

.. code-block:: xml

   <section>
       <div class="o_we_bg_filter" style="background-color: rgba(39, 110, 114, 0.54) !important;"/>
       <div class="container">
           <!-- 内容 -->
       </div>
   </section>

添加自定义渐变滤镜。

.. code-block:: xml

   <section>
       <div class="o_we_bg_filter" style="background-image: linear-gradient(135deg, rgba(255, 204, 51, 0.5) 0%, rgba(226, 51, 255, 0.5) 100%) !important;"/>
       <div class="container">
           <!-- 内容 -->
       </div>
   </section>

样式
======

兼容性系统
--------------------

当片段具有 `data-vcss` 或 `data-vjs` 属性时，表示它是更新版本，而不是原始版本。

.. code-block:: xml

   <section class="s_snippet_name" data-vcss="..." data-js="...">
       <!-- 内容 -->
   </section>

`data-vcss` 和 `data-js` 属性指示系统应加载该片段的哪个文件版本（例如，:file:`001.js`，:file:`002.scss`）。

自定义
======

创建片段的内容。

**声明**

.. code-block:: xml
   :caption: ``/website_airproof/views/snippets/s_airproof_snippet.xml``

   <?xml version="1.0" encoding="utf-8"?>
   <odoo>

       <template id="s_airproof_snippet" name="...">
           <section class="s_airproof_snippet">
               <!-- 内容 -->
           </section>
       </template>

   </odoo>

.. warning::
   当片段在主题页面上声明时，必须指定 `data-name` 和 `data-snippet` 属性。

.. tip::
   - 尽可能多使用 Bootstrap 原生类。
   - 为所有自定义类添加前缀。
   - 使用下划线小写命名法命名类，例如 `.x_nav`，`.x_nav_item`。
   - 避免使用 ID 标签。

将自定义片段添加到默认片段列表中，以便用户可以直接从编辑面板中拖放到页面上。

.. code-block:: xml
   :caption: ``/website_airproof/views/snippets/options.xml``

   <template id="snippets" inherit_id="website.snippets" name="Custom Snippets">
       <xpath expr="//*[@id='default_snippets']" position="before">
           <t id="x_theme_snippets">
               <div id="x_theme_snippets_category" class="o_panel">
                   <div class="o_panel_header">主题</div>
                   <div class="o_panel_body">
                       <t t-snippet="website_airproof.s_airproof_snippet" t-thumbnail="/website_airproof/static/src/img/wbuilder/s_airproof_snippet.svg">
                           <keywords>片段</keywords>
                       </t>
                   </div>
               </div>
           </t>
       </xpath>
   </template>

.. list-table::
   :header-rows: 1
   :stub-columns: 1
   :widths: 20 80

   * - 属性
     - 描述
   * - t-snippet
     - 要使用的模板
   * - t-thumbnail
     - 片段缩略图的路径

选项
-------

选项允许用户使用网站构建器编辑片段的外观。您可以轻松创建片段选项，并自动将其添加到网站构建器中。

组属性
-----------------

选项封装在组中。组可以具有定义包含的选项如何与用户界面交互的属性。

`data-selector` 绑定组中包含的所有选项到特定元素。它可以与 `data-target` 和 `data-exclude` 一起使用。

.. code-block:: xml

   <div data-selector="section, h1, .custom_class, #custom_id">

`data-js` 绑定自定义 JavaScript 方法。

.. code-block:: xml

   <div data-js="CustomMethodName" data-selector="...">

`data-drop-in` 定义片段可以放入的元素列表。

.. todo:: 没有 CSS 选择器...

.. code-block:: xml

   <div data-selector="..." data-drop-in="...">

`data-drop-near` 定义片段可以放在旁边的元素列表。

.. code-block:: xml

   <div data-selector="..." data-drop-near="...">

SCSS 选项
------------

选项可以为片段应用标准或自定义的 CSS 类。根据您选择的方法，用户界面的行为将有所不同。

`data-select-class="..."`

在同一组中使用多个 `data-select-class` 定义用户可以应用的类列表。一次只能启用一个选项。

.. code-block:: xml
   :caption: ``/website_airproof/views/snippets/options.xml``

   <template id="snippet_options" inherit_id="website.snippet_options" name="...">
       <xpath expr="." position="inside">

           <div data-selector="h1, h2, h3, h4, h5, h6">
               <we-select string="标题">
                   <we-button data-select-class="">默认</we-button>
                   <we-button data-select-class="x_custom_class_01">01</we-button>
                   <we-button data-select-class="x_custom_class_02">02</we-button>
               </we-select>
           </div>

       </xpath>
   </template>

.. seealso::
   `不同片段的 XML 模板
   <{GITHUB_PATH}/addons/website/views/snippets/snippets.xml>`_

JavaScript 选项
------------------

可以为选项组分配 `data-js` 属性以定义自定义方法。

.. code-block:: javascript

   /** @odoo-module */

   import options from 'web_editor.snippets.options';

   options.registry.CustomMethodName = options.Class.extend({
       //
   });

网站构建器提供了多个事件，您可以使用它们来触发自定义功能。

.. list-table::
   :header-rows: 1
   :stub-columns: 1
   :widths: 20 80

   * - 事件
     - 描述
   * - start
     - 发生在发布者在编辑会话中首次选择片段或将片段拖放到页面上时。
   * - onFocus
     - 每次用户选择片段时发生，或当片段被拖放到页面上时发生。
   * - onBlur
     - 当片段失去焦点时发生。
   * - onClone
     - 片段复制后立即发生。
   * - onRemove
     - 片段被删除之前发生。
   * - onBuilt
     - 片段在放置区域拖放完成后发生。当触发此事件时，内容已经插入页面。
   * - cleanForSave
     - 发布者保存页面之前发生。

动态内容模板
-------------------------

默认情况下，动态内容块在网站构建器中有一些可用的模板。您还可以将自己的模板添加到列表中。

博客文章
~~~~~~~~~~

.. code-block:: xml
   :caption: ``/website_airproof/views/snippets/options.xml``

   <template id="dynamic_filter_template_blog_post_airproof" name="...">
       <div t-foreach="records" t-as="data" class="s_blog_posts_post">
           <t t-set="record" t-value="data['_record']"/>
           <!-- 内容 -->
       </div>
   </template>

.. list-table::
   :header-rows: 1
   :stub-columns: 1
   :widths: 20 80

   * - 属性
     - 描述
   * - id
     - 模板的 ID。必须以 `dynamic_filter_template_blog_post_` 开头。
   * - name
     - 模板的可读名称

产品
~~~~~~~~

.. code-block:: xml
   :caption: ``/website_airproof/views/snippets/options.xml``

   <template id="dynamic_filter_template_product_product_airproof" name="...">
       <t t-foreach="records" t-as="data" data-number-of-elements="4" data-number-of-elements-sm="1" data-number-of-elements-fetch="8">
           <t t-set="record" t-value="data['_record']"/>
           <!-- 内容 -->
       </t>
   </template>

.. list-table::
   :header-rows: 1
   :stub-columns: 1
   :widths: 40 60

   * - 属性
     - 描述
   * - id
     - 模板的 ID。必须以 `dynamic_filter_template_product_product_` 开头。
   * - name
     - 模板的可读名称
   * - data-number-of-elements
     - 桌面上的每张幻灯片显示的产品数量
   * - data-number-of-elements-sm
     - 移动设备上的每张幻灯片显示的产品数量
   * - data-number-of-elements-fetch
     - 获取的总产品数量

活动
~~~~~~

.. code-block:: xml
   :caption: ``/website_airproof/views/snippets/options.xml``

   <template id="dynamic_filter_template_event_event_airproof" name="...">
       <div t-foreach="records" t-as="data" class="s_events_event">
           <t t-set="record" t-value="data['_record']._set_tz_context()"/>
           <!-- 内容 -->
       </div>
   </template>

.. list-table::
   :header-rows: 1
   :stub-columns: 1
   :widths: 20 80

   * - 属性
     - 描述
   * - id
     - 模板的 ID。必须以 `dynamic_filter_template_event_event_` 开头。
   * - name
     - 模板的可读名称
