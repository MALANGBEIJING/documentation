======
布局
======

在本章中，你将学习如何：

- 创建自定义页眉。
- 创建自定义页脚。
- 修改标准模板。
- 添加版权部分。
- 改善你网站的响应性。

默认
====

Odoo 页面结合了跨页面元素和独特元素。跨页面元素在每个页面上都是相同的，而独特元素仅与特定页面相关。默认情况下，页面有两个跨页面元素，即页眉和页脚，以及一个包含该页面特定内容的独特主元素。

.. code-block:: xml

   <div id="wrapwrap">
      <header/>
         <main>
            <div id="wrap" class="oe_structure">
               <!-- 页面内容 -->
            </div>
         </main>
      <footer/>
   </div>

任何 Odoo XML 文件都以编码规范开头。之后，必须在 `<odoo>` 标签中编写代码。

.. code-block:: xml

   <?xml version="1.0" encoding="utf-8" ?>
   <odoo>
      ...
   </odoo>

.. note::
   使用精确的文件名对快速找到所有模块中的信息非常重要。文件名应仅包含小写的字母数字字符和下划线。

   文件末尾应始终添加一个空行。这可以通过配置 IDE 自动完成。

XPath
=====

XPath（XML 路径语言）是一种表达语言，可以轻松导航 XML 文档中的元素和属性。XPath 用于扩展 Odoo 标准模板。

视图的编码方式如下：

.. code-block:: xml

   <template id="..." inherit_id="..." name="...">
      <!-- 内容 -->
   </template>

.. list-table::
   :header-rows: 1
   :stub-columns: 1
   :widths: 20 80

   * - 属性
     - 描述
   * - id
     - 修改视图的 ID
   * - inherited_id
     - 标准视图的 ID
   * - name
     - 人类可读的修改视图的名称

对于每个 XPath，你可以修改两个属性：**expression** 和 **position**。

.. example::
   .. code-block:: xml
      :caption: ``/website_airproof/views/website_templates.xml``

      <template id="layout" inherit_id="website.layout" name="欢迎消息">
         <xpath expr="//header" position="before">
            <!-- 内容 -->
         </xpath>
      </template>

   该 XPath 在页面内容之前添加了一条欢迎消息。

.. warning::
   当替换默认元素的属性时要小心。由于你的主题扩展了默认主题，你的更改将优先于任何未来的 Odoo 更新。

.. note::
   - 每次创建新模板或记录时都应更新模块。
   - 继承视图的 *XML IDs* 应使用与原始记录相同的 *ID*。这样可以一目了然地找到所有继承关系。由于最终的 *XML IDs* 由创建它们的模块添加前缀，因此不存在重叠。

表达式
----------

XPath 使用路径表达式选择 XML 文档中的节点。选择器用于表达式内以目标元素。以下列出了最有用的选择器。

.. list-table::
   :header-rows: 1
   :stub-columns: 1
   :widths: 20 80

   * - 后代选择器
     - 描述
   * - /
     - 从根节点选择。
   * - //
     - 从当前节点中选择符合条件的节点，无论它们位于何处。

.. list-table::
   :header-rows: 1
   :stub-columns: 1
   :widths: 20 80

   * - 属性选择器
     - 描述
   * - \*
     - 选择任意 XML 标签。`*` 可被更精确的标签替代。
   * - \*[@id="id"]
     - 选择特定的 ID。
   * - \*[hasclass("class")]
     - 选择特定类。
   * - \*[@name="name"]
     - 选择具有特定名称的标签。
   * - \*[@t-call="t-call"]
     - 选择特定的 t-call。

位置
--------

位置定义了代码在模板中的放置位置。可能的值如下所示：

.. list-table::
   :header-rows: 1
   :stub-columns: 1
   :widths: 20 80

   * - 位置
     - 描述
   * - replace
     - 用 XPath 内容替换目标节点。
   * - inside
     - 在目标节点内添加 XPath 内容。
   * - before
     - 在目标节点之前添加 XPath 内容。
   * - after
     - 在目标节点之后添加 XPath 内容。
   * - attributes
     - 在属性中添加 XPath 内容。

.. example::
   该 XPath 在 `<header>` 的 `<nav>` 直接子元素之前添加了一个 `<div>`。

   .. code-block:: xml

      <xpath expr="//header/nav" position="before">
         <div>页眉前的一些内容</div>
      </xpath>

   该 XPath 在页眉的 class 属性中添加了 `x_airproof_header`。你还需要定义一个 `separator` 属性以在你添加的类之前添加空格。

   .. code-block:: xml

      <xpath expr="//header" position="attributes">
         <attribute name="class" add="x_airproof_header" separator=" "/>
      </xpath>

   该 XPath 从页眉的 class 属性中移除了 `x_airproof_header`。在这种情况下，不需要使用 `separator` 属性。

   .. code-block:: xml

      <xpath expr="//header" position="attributes">
         <attribute name="class" remove="x_airproof_header" />
      </xpath>

   该 XPath 移除了第一个带有 `.breadcrumb` 类的元素。

   .. code-block:: xml

      <xpath expr="//*[hasclass('breadcrumb')]" position="replace"/>

   该 XPath 在 `<ul>` 元素的最后一个子元素之后添加了一个额外的 `<li>` 元素。

   .. code-block:: xml

      <xpath expr="//ul" position="inside">
         <li>列表的最后一个元素</li>
      </xpath>

.. seealso::
   你可以在这个 `cheat sheet <https://devhints.io/xpath>`_ 中找到更多关于 XPath 的信息。

QWeb
====

QWeb 是 Odoo 使用的主要模板引擎。它是一个 XML 模板引擎，主要用于生成 HTML 片段和页面。

.. seealso::
   :doc:`QWeb 模板文档 <../../reference/frontend/qweb>`。

背景
==========

你可以定义颜色或图片作为你网站的背景。

**颜色**

.. code-block:: scss
   :caption: ``/website_airproof/static/src/scss/primary_variables.scss``

   $o-color-palettes: map-merge($o-color-palettes,
      (
         'airproof': (
            'o-cc1-bg':                     'o-color-5',
            'o-cc5-bg':                     'o-color-1',
         ),
       )
   );

**图片/图案**

.. code-block:: scss
   :caption: ``/website_airproof/static/src/scss/primary_variables.scss``

   $o-website-values-palettes: (
      (
         'body-image': '/website_airproof/static/src/img/background-lines.svg',
         'body-image-type': 'image' or 'pattern'
      )
   );

页眉
======

默认情况下，页眉包含一个响应式导航菜单和公司的标志。你可以轻松添加新元素或创建自己的模板。

标准
--------

启用默认页眉模板之一。

.. important::
   不要忘记你可能需要先禁用活动的页眉模板。

.. code-block:: scss
   :caption: ``/website_airproof/static/src/scss/primary_variables.scss``

   $o-website-values-palettes: (
      (
         'header-template': 'Contact',
      ),
   );

.. code-block:: xml
   :caption: ``/website_airproof/data/presets.xml``

   <record id="website.template_header_contact" model="ir.ui.view">
      <field name="active" eval="True"/>
   </record>

自定义
------

创建你自己的模板并将其添加到列表中。

.. important::
   不要忘记你可能需要先禁用活动的页眉模板。

**选项**

使用以下代码在网站构建器中为你的新自定义页眉添加选项。

.. code-block:: xml
   :caption: ``/website_airproof/data/presets.xml``

   <template id="template_header_opt" inherit_id="website.snippet_options" name="Header Template - Option">
      <xpath expr="//we-select[@data-variable='header-template']" position="inside">
         <we-button title="airproof"
            data-customize-website-views="website_airproof.header"
            data-customize-website-variable="'airproof'"  data-img="/website_airproof/static/src/img/wbuilder/template_header_opt.svg"/>
      </xpath>
   </template>

.. list-table::
   :header-rows: 1
   :stub-columns: 1
   :widths: 20 80

   * - 属性
     - 描述
   * - data-customize-website-views
     - 要启用的模板
   * - data-customize-website-variable
     - 赋予变量的名称
   * - data-img
     - 网站构建器中的模板缩略图

你现在必须在 Odoo SASS 变量中显式定义你要使用的自定义模板。

.. code-block:: scss
   :caption: ``/website_airproof/static/src/scss/primary_variables.scss``

   $o-website-values-palettes: (
      (
         'header-template': 'airproof',
      ),
   );

**布局**

.. code-block:: xml
   :caption: ``/website_airproof/views/website_templates.xml``

   <record id="header" model="ir.ui.view">
      <field name="name">Airproof Header</field>
      <field name="type">qweb</field>
      <field name="key">website_airproof.header</field>
      <field name="inherit_id" ref="website.layout"/>
      <field name="mode">extension</field>
      <field name="arch" type="xml">
         <xpath expr="//header//nav" position="replace">
            <!-- 静态内容 -->
            <!-- 组件 -->
            <!-- 可编辑区域 -->
         </xpath>
      </field>
   </record>

组件
----------

在你的自定义页眉中，你可以使用 QWeb 的 `t-call` 指令调用几个子模板：

徽标
~~~~

.. code-block:: xml

   <t t-call="website.placeholder_header_brand">
      <t t-set="_link_class" t-valuef="..."/>
   </t>

不要忘记在数据库中记录你网站的徽标。

.. code-block:: xml
   :caption: ``/website_airproof/data/images.xml``

   <record id="website.default_website" model="website">
      <field name="logo" type="base64" file="website_airproof/static/src/img/content/logo.png"/>
   </record>

菜单
~~~~

.. code-block:: xml

   <t t-foreach="website.menu_id.child_id" t-as="submenu">
      <t t-call="website.submenu">
         <t t-set="item_class" t-valuef="nav-item"/>
         <t t-set="link_class" t-valuef="nav-link"/>
      </t>
   </t>

登录
~~~~~~~

.. code-block:: xml

   <t t-call="portal.placeholder_user_sign_in">
      <t t-set="_item_class" t-valuef="nav-item"/>
      <t t-set="_link_class" t-valuef="nav-link"/>
   </t>

用户下拉菜单
~~~~~~~~~~~~~

.. code-block:: xml

   <t t-call="portal.user_dropdown">
      <t t-set="_user_name" t-value="true"/>
      <t t-set="_icon" t-value="false"/>
      <t t-set="_avatar" t-value="false"/>
      <t t-set="_item_class" t-valuef="nav-item dropdown"/>
      <t t-set="_link_class" t-valuef="nav-link"/>
      <t t-set="_dropdown_menu_class" t-valuef="..."/>
   </t>

语言选择器
~~~~~~~~~~~~~

.. code-block:: xml

   <t t-call="website.placeholder_header_language_selector">
      <t t-set="_div_classes" t-valuef="..."/>
   </t>

行动呼吁
~~~~~~~~~~~~~~

.. code-block:: xml

   <t t-call="website.placeholder_header_call_to_action">
      <t t-set="_div_classes" t-valuef="..."/>
   </t>

导航栏切换器
~~~~~~~~~~~~~~

.. code-block:: xml

   <t t-call="website.navbar_toggler">
      <t t-set="_toggler_class" t-valuef="..."/>
   </t>

.. seealso::
   你可以添加 :ref:`header overlay <header_overlay>` 将你的页眉定位于页面内容上方。此操作需要在每个页面上单独完成。

页脚
======

默认情况下，页脚包含一个静态内容部分。你可以轻松添加新元素或创建自己的模板。

标准
--------

启用默认页脚模板之一。不要忘记你可能需要先禁用活动的页脚模板。

.. code-block:: scss
   :caption: ``/website_airproof/static/src/scss/primary_variables.scss``

   $o-website-values-palettes: (
      (
         'header-template': 'Contact',
      ),
   );

.. code-block:: xml
   :caption: ``/website_airproof/data/presets.xml``

   <record id="website.template_header_contact" model="ir.ui.view">
      <field name="active" eval="True"/>
   </record>

自定义
------

创建你自己的模板并将其添加到列表中。不要忘记你可能需要先禁用活动的页脚模板。

**选项**

.. code-block:: xml
   :caption: ``/website_airproof/data/presets.xml``

   <template id="template_header_opt" inherit_id="website.snippet_options" name="Footer Template - Option">
      <xpath expr="//we-select[@data-variable='footer-template']" position="inside">
         <we-button title="airproof"
            data-customize-website-views="website_airproof.footer"
            data-customize-website-variable="'airproof'"
            data-img="/website_airproof/static/src/img/wbuilder/template_header_opt.svg"/>
      </xpath>
   </template>

**声明**

.. code-block:: scss
   :caption: ``/website_airproof/static/src/scss/primary_variables.scss``

   $o-website-values-palettes: (
      (
         'footer-template': 'airproof',
      ),
   );

**布局**

.. code-block:: xml
    :caption: ``/website_airproof/views/website_templates.xml``

    <record id="footer" model="ir.ui.view">
       <field name="name">Airproof Footer</field>
       <field name="type">qweb</field>
       <field name="key">website_airproof.footer</field>
       <field name="inherit_id" ref="website.layout"/>
       <field name="mode">extension</field>
       <field name="arch" type="xml">
          <xpath expr="//div[@id='footer']" position="replace">
             <div id="footer" class="oe_structure oe_structure_solo" t-ignore="true" t-if="not no_footer">
                <!-- 内容 -->
             </div>
          </xpath>
       </field>
    </record>

版权
=========

目前只有一个模板可用于版权栏。

要替换内容或修改其结构，你可以在以下 XPath 中添加自己的代码。

.. code-block:: xml
    :caption: ``/website_airproof/views/website_templates.xml``

    <template id="copyright" inherit_id="website.layout">
       <xpath expr="//div[hasclass('o_footer_copyright')]" position="replace">
          <div class="o_footer_copyright" data-name="Copyright">
             <!-- 内容 -->
          </div>
       </xpath>
    </template>

拖放区域
=========

与其定义页面的完整布局，你可以创建构建块（snippets），让用户选择将它们拖放到某处，从而创建页面布局。我们称之为*模块化设计*。

你可以定义一个空白区域，用户可以用构建块来填充它。

.. code-block:: xml

   <div id="oe_structure_layout_01" class="oe_structure"/>

.. todo:: 缺少表格中的描述 ...

.. list-table::
   :header-rows: 1
   :stub-columns: 1
   :widths: 20 80

   * - 类
     - 描述
   * - oe_structure
     - 为用户定义一个拖放区域。
   * - oe_structure_solo
     - 该区域只能放置一个构建块。

你也可以用自己的内容填充已有的拖放区域。

.. code-block:: xml

    <template id="oe_structure_layout_01" inherit_id="..." name="...">
       <xpath expr="//*[@id='oe_structure_layout_01']" position="replace">
          <div id="oe_structure_layout_01" class="oe_structure oe_structure_solo">
             <!-- 内容 -->
          </div>
       </xpath>
    </template>

响应式设计
==========

以下提示可以帮助你使网站具有响应式设计。

Bootstrap
---------

.. seealso::
   - `Bootstrap 响应式断点文档
     <https://getbootstrap.com/docs/4.6/layout/overview/#responsive-breakpoints>`_
   - `Bootstrap display 属性文档
     <https://getbootstrap.com/docs/4.6/utilities/display/>`_

**字体大小**

从 v4.3.0 开始，Bootstrap 提供了启用响应式字体大小的选项，使文本能够更自然地跨设备和视口大小缩放。通过将 `$enable-responsive-font-sizes` Sass 变量设置为 true 来启用它们。

.. seealso::
   `Responsive Font Size GitHub <https://github.com/twbs/rfs/tree/v8.1.0>`_

网站构建器
---------------

在移动设备上隐藏特定的 `<section>`。

.. code-block:: xml

    <section class="d-none d-md-block">
       <!-- 内容 -->
    </section>

在移动设备上隐藏 `<col>`。

.. code-block:: xml

   <section>
      <div class="container">
         <div class="row d-flex align-items-stretch">
            <div class="col-lg-4 d-none d-md-block">
               <!-- 内容 -->
            </div>
         </div>
      </div>
   </section>
