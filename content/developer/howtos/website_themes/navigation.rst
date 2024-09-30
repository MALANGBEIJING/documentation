==========
导航
==========

你可以使用网站构建器轻松修改导航以满足你的需求。

在本章中，你将学习如何：

- 删除和创建菜单项。
- 创建下拉菜单。
- 创建超级菜单。

默认
=======

Odoo 会根据你安装的应用自动生成一些基础的菜单项。例如，网站应用会在主菜单中添加两个项目。这些项目链接到自动创建的页面。

删除默认菜单项。

.. code-block:: xml
   :caption: ``/website_airproof/data/menu.xml``

   <!-- 联系我们 -->
   <delete model="website.menu" search="[('url','in', ['/', '/contactus']),
   ('website_id', '=', 1)]"/>

   <!-- 商店 -->
   <delete model="website.menu" search="[('url','in', ['/', '/shop']),
   ('website_id', '=', 1)]"/>

菜单项
=========

**声明**

.. code-block:: xml
   :caption: ``/website_airproof/data/menu.xml``

   <record id="menu_about_us" model="website.menu">
       <field name="name">关于我们</field>
       <field name="url">/about-us</field>
       <field name="parent_id" search="[
           ('url', '=', '/default-main-menu'),
           ('website_id', '=', 1)]"/>
       <field name="website_id">1</field>
       <field name="sequence" type="int">10</field>
   </record>

.. list-table::
   :header-rows: 1
   :stub-columns: 1
   :widths: 20 80

   * - 字段
     - 描述
   * - name
     - 链接文本
   * - url
     - href 属性的值
   * - parent_id
     - 添加此项的菜单。
   * - website_id
     - 添加此项的网站。
   * - sequence
     - 定义链接在顶级菜单中的位置。

新窗口
----------

在新标签页中打开链接的 URL。

.. code-block:: xml

   <record id="..." model="website.menu">
       <field name="new_window" eval="True"/>
   </record>

外部链接
--------------

添加指向外部网站的链接。

.. code-block:: xml

   <record id="..." model="website.menu">
       <field name="url">https://www.odoo.com</field>
   </record>

锚点
------

链接到页面的特定部分。

.. code-block:: xml

   <record id="..." model="website.menu">
       <field name="url">/about-us#our-team</field>
   </record>

下拉菜单
=============

**声明**

.. code-block:: xml
   :caption: ``/website_airproof/data/menu.xml``

   <record id="menu_services" model="website.menu">
       <field name="name">服务</field>
       <field name="website_id">1</field>
       <field name="parent_id" search="[
           ('url', '=', '/default-main-menu'),
           ('website_id', '=', 1)]"/>
       <field name="sequence" type="int">...</field>
   </record>

在下拉菜单中添加一项。

.. code-block:: xml

   <record id="menu_services_item_1" model="website.menu">
       <field name="name">项目 1</field>
       <field name="url">/dropdown/item-1</field>
       <field name="website_id">1</field>
       <field name="parent_id" ref="website_airproof.menu_services"/>
       <field name="sequence" type="int">...</field>
   </record>

.. list-table::
   :header-rows: 1
   :stub-columns: 1
   :widths: 20 80

   * - 字段
     - 描述
   * - parent_id
     - 将要添加该项的下拉菜单。

超级菜单
=========

超级菜单是具有额外可能性的下拉菜单，而不仅仅是一列链接。在超级菜单中，你可以使用任何内容（文本、图像、图标等）。

**声明**

.. code-block:: xml
   :caption: ``/website_airproof/data/menu.xml``

   <record id="menu_mega_menu" model="website.menu">
       <field name="name">超级菜单</field>
       <field name="url">/mega-menu</field>
       <field name="parent_id" search="[
           ('url', '=', '/default-main-menu'),
           ('website_id', '=', 1)]"/>
       <field name="website_id">1</field>
       <field name="sequence" type="int">..</field>
       <field name="is_mega_menu" eval="True"/>
       <field name="mega_menu_classes">...</field>
       <field name="mega_menu_content" type="html">
           <!-- 内容 -->
       </field>
   </record>

.. list-table::
   :header-rows: 1
   :stub-columns: 1
   :widths: 20 80

   * - 字段
     - 描述
   * - is_mega_menu
     - 启用超级菜单功能。
   * - mega_menu_classes
     - 要添加到主元素的自定义类
   * - mega_menu_content
     - 超级菜单的默认内容

自定义模板
---------------

创建你自己的模板并将其添加到列表中。

**布局**

.. code-block:: xml
   :caption: ``/website_airproof/views/website_templates.xml``

   <template id="s_mega_menu_airproof" name="Airproof" groups="base.group_user">
       <section class="s_mega_menu_airproof o_cc o_cc1 pt40">
           <!-- 内容 -->
       </section>
   </template>

**选项**

使用以下代码在网站构建器中为你的新自定义超级菜单添加一个选项。

.. code-block:: xml
   :caption: ``/website_airproof/data/presets.xml``

   <template id="snippet_options" inherit_id="website.snippet_options" name="Airproof - Mega Menu Options">
       <xpath expr="//*[@data-name='mega_menu_template_opt']/*" position="before">
           <t t-set="_label">Airproof</t>
           <we-button t-att-data-select-label="_label"
               data-select-template="website_website_airproof.s_mega_menu_airproof"
               data-img="/website_airproof/static/src/img/builder/header_opt.svg"
               t-out="_label"/>
       </xpath>
   </template>
