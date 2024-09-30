=====
页面
=====

在本章中，你将学习如何声明静态页面。

默认页面
=============

在 Odoo 中，网站自带一些默认的静态页面（主页、联系我们、404 页面等）。它们是通过以下方式构建的。

.. code-block:: xml

   <template id="website.homepage" name="Homepage">
       <t t-call="website.layout">

           <!-- 变量 -->
           <t t-set="additional_title" t-value="'Home'" />

           <div id="wrap" class="oe_structure oe_empty">

               <!-- 内容 -->

           </div>

       </t>
   </template>

定义元标题。

.. code-block:: xml

   <t t-set="additional_title" t-value="'...'"/>

定义元描述。

.. code-block:: xml

   <t t-set="meta_description" t-value="'...'"/>

为页面添加 CSS 类。

.. code-block:: xml

   <t t-set="pageName" t-value="'...'"/>

隐藏头部。

.. code-block:: xml

   <t t-set="no_header" t-value="true"/>

隐藏页脚。

.. code-block:: xml

   <t t-set="no_footer" t-value="true"/>

如有需要，停用默认页面。

.. code-block:: xml
   :caption: ``/website_airproof/data/pages/home.xml``

   <record id="website.homepage" model="ir.ui.view">
       <field name="active" eval="False"/>
   </record>

.. code-block:: xml
   :caption: ``/website_airproof/data/pages/contactus.xml``

   <record id="website.contactus" model="ir.ui.view">
       <field name="active" eval="False"/>
   </record>

或者，使用 XPath 替换这些页面的默认内容。

.. code-block:: xml
   :caption: ``/website_airproof/data/pages/404.xml``

   <template id="404" inherit_id="http_routing.404">
       <xpath expr="//*[@id='wrap']" position="replace">

           <t t-set="additional_title" t-value="'404 - Not found'"/>

           <div id="wrap" class="oe_structure">
               <!-- 内容 -->
           </div>

       </xpath>
   </template>

.. seealso::
   - `Odoo 电子学习：搜索引擎优化（SEO）
     <https://www.odoo.com/slides/slide/search-engine-optimization-seo-648>`_
   - :doc:`Odoo 文档中的 SEO <../../../applications/websites/website/pages/seo>`

主题页面
===========

你可以向你的网站添加任意数量的页面。与定义 `<template>` 不同，创建一个页面对象。

**声明**

.. code-block:: xml
   :caption: ``/website_airproof/data/pages/about_us.xml``

   <record id="page_about_us" model="website.page">
       <field name="name">关于我们</field>
       <field name="is_published" eval="True"/>
       <field name="key">website_airproof.page_about_us</field>
       <field name="url">/about-us</field>
       <field name="type">qweb</field>
       <field name="arch" type="xml">
           <t t-name="website_airproof.page_about_us">

               <t t-call="website.layout">
                   <div id="wrap" class="oe_structure">

                       <!-- 内容 -->

                   </div>
               </t>

           </t>
       </field>
   </record>

.. todo:: 缺少表格描述...

.. list-table::
   :header-rows: 1
   :stub-columns: 1
   :widths: 20 80

   * - 字段
     - 描述
   * - name
     - 页面名称。
   * - is_published
     - 定义页面是否已发布（可见）。
   * - key
     - 视图键（必须唯一）
   * - url
     - 页面可访问的 URL。
   * - type
     - 视图类型
   * - arch
     - 视图架构

使用 `<t t-call="website.layout">` 时，你可以在 Odoo 的默认页面布局中插入自己的代码。

.. _header_overlay:

头部叠加
--------------

使头部背景透明并覆盖在页面内容之上。

.. code-block:: xml

   <field name="header_overlay" eval="True"/>

.. image:: pages/header-overlay.png
   :alt: 头部叠加

媒体
=====

图像
------

你可以将图像记录在数据库中，并在设计/代码中稍后使用。它们也会通过*媒体对话框*为最终用户提供。

.. image:: pages/media-window.png
   :alt: 媒体窗口

网站构建器支持以下图像文件格式：JPG、GIF、PNG 和 SVG。

**声明**

.. code-block:: xml
   :caption: ``/website_airproof/data/images.xml``

   <record id="img_about_01" model="ir.attachment">
       <field name="name">关于图片 01</field>
       <field name="datas" type="base64" file="website_airproof/static/src/img/content/img_about_01.jpg"/>
       <field name="res_model">ir.ui.view</field>
       <field name="public" eval="True"/>
   </record>

.. todo:: 缺少表格描述...

.. list-table::
   :header-rows: 1
   :stub-columns: 1
   :widths: 20 80

   * - 字段
     - 描述
   * - name
     - 图像名称
   * - datas
     - 图像文件路径
   * - res_model
     - 向导模型的名称

用作背景图片。

.. code-block:: xml

   <section style="background-image: url('/web/image/website_airproof.img_about_01');">

用作常规图片。

.. code-block:: xml

   <img src="/web/image/website_airproof.img_about_01" alt=""/>

用作带颜色滤镜的常规图片。

.. code-block:: xml

   <img src="/web/image/website.s_media_list_default_image_1"
       class="img img-fluid mx-auto" alt=""
       data-gl-filter="custom"
       data-filter-options="{'filterColor': 'rgba(0, 0, 0, 0.5)'}"/>

.. tip::
   图像大小极大地影响用户体验、搜索引擎优化和整体网站性能。因此，确保正确调整图像大小。

视频
------

将视频添加为背景。

.. code-block:: xml

   <section class="o_background_video" data-bg-video-src="...">
       <!-- 内容 -->
   </section>

.. list-table::
   :header-rows: 1
   :stub-columns: 1
   :widths: 20 80

   * - 属性
     - 描述
   * - data-bg-video-src
     - 视频 URL。

将视频作为内容添加。

.. code-block:: xml

   <div class="media_iframe_video" data-oe-expression="...">
       <div class="css_editable_mode_display">&nbsp;</div>
       <div class="media_iframe_video_size" contenteditable="false">&nbsp;</div>
       <iframe src="..."
           frameborder="0"
           contenteditable="false"
           allowfullscreen="allowfullscreen"/>
   </div>

.. list-table::
   :header-rows: 1
   :stub-columns: 1
   :widths: 20 80

   * - 属性
     - 描述
   * - data-oe-expression
     - 视频 URL。
   * - src
     - 视频 URL。

图标
-----

默认情况下，Font Awesome 图标库已包含在网站构建器中。你可以使用 CSS 前缀 `fa` 和图标名称将图标放置在任何位置。Font Awesome 设计为与内联元素一起使用。你可以简写使用 `<i>` 标签，但使用 `<span>` 在语义上更为正确。

.. code-block:: xml

   <span class="fa fa-picture-o"/>

.. seealso::
   `Font Awesome v4 图标 <https://fontawesome.com/v4/icons/>`_

启用网站构建器的样式选项。

.. code-block:: xml

   <span class="fa fa-2x fa-picture-o rounded-circle"/>

增大图标尺寸（fa-2x、fa-3x、fa-4x 或 fa-5x 类）。

.. code-block:: xml

   <span class="fa fa-2x fa-picture-o"/>

.. image:: pages/icon-options.png
   :alt: 图标选项
