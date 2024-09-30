======
形状
======

形状是为您的网站增添个性的一种方便方法。

在本章中，您将学习如何添加标准和自定义的背景形状和图片形状。

背景形状
=================

背景形状是 SVG 文件，您可以将其添加到不同部分的装饰背景中。每个形状都有一个或多个可定制的颜色，有些形状是动画的。

标准
--------

提供了大量默认的背景形状供选择。

**使用**

.. code-block:: xml

   <section data-oe-shape-data="{'shape':'web_editor/Zigs/06'}">
       <div class="o_we_shape o_web_editor_Zigs_06"/>
       <div class="container">
           <!-- 内容 -->
        </div>
   </section>

`data-oe-shape-data` 是您的形状的位置。

使用 X 或 Y 轴翻转形状。

.. code-block:: xml

   <section data-oe-shape-data="{'shape':'web_editor/Zigs/06','flip':[x,y]}">
       <div class="o_we_shape o_we_flip_x o_we_flip_y o_web_editor_Zigs_06"/>
       <div class="container">
           <!-- 内容 -->
       </div>
   </section>

颜色映射
~~~~~~~~~~~~~~

您还可以更改形状的默认颜色映射。

切换颜色映射
*********************

首先，设置 c* 颜色（这里为 `4`）。

然后，设置替换颜色（这里为 `3`）。这些替换颜色的范围也是 1 到 5：

- `1` = 颜色预设 1 的背景颜色 (o-cc1)。
- `2` = 颜色预设 2 的背景颜色 (o-cc2)。
- `3` = 颜色预设 3 的背景颜色 (o-cc3)。
- `4` = 颜色预设 4 的背景颜色 (o-cc4)。
- `5` = 颜色预设 5 的背景颜色 (o-cc5)。

.. code-block:: scss
   :caption: ``/website_airproof/static/src/scss/boostrap_overridden.scss``

   $o-bg-shapes: change-shape-colors-mapping('web_editor', 'Zigs/06', (4: 3, 5: 1));

添加额外颜色映射
************************

添加额外颜色映射可以为形状模板添加颜色变体，同时保留原始颜色。

.. code-block:: scss
   :caption: ``/website_airproof/static/src/scss/boostrap_overridden.scss``

   $o-bg-shapes: add-extra-shape-colors-mapping('web_editor', 'Zigs/06', 'second', (4: 3, 5: 1));

.. code-block:: xml

   <section data-oe-shape-data="{'shape':'web_editor/Zigs/06'}">
       <div class="o_we_shape o_web_editor_Zigs_06 o_second_extra_shape_mapping"/>
       <div class="container">
           <!-- 内容 -->
       </div>
   </section>

自定义
------

有时，您的设计可能需要创建一个或多个自定义形状。

首先，您需要为形状创建一个 SVG 文件。

.. code-block:: xml
   :caption: ``/website_airproof/static/shapes/hexagons/01.svg``

   <svg version="1.1" xmlns="http://www.w3.org/2000/svg" width="86" height="100">
       <polygon points="0 25, 43 0, 86 25, 86 75, 43 100, 0 75" style="fill: #3AADAA;"/>
   </svg>

确保为形状使用 Odoo 默认调色板中的颜色。

.. code-block:: scss

   default_palette = {
       '1': '#3AADAA',
       '2': '#7C6576',
       '3': '#F6F6F6',
       '4': '#FFFFFF',
       '5': '#383E45',
   }

声明您的形状文件。

.. code-block:: xml
   :caption: ``/website_airproof/data/shapes.xml``

   <record id="shape_hexagon_01" model="ir.attachment">
       <field name="name">01.svg</field>
       <field name="datas" type="base64" file="website_airproof/static/shapes/hexagons/01.svg"/>
       <field name="url">/web_editor/shape/illustration/hexagons/01.svg</field>
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
     - 形状名称
   * - datas
     - 形状路径
   * - url
     - ...
   * - public
     - 使形状可供以后编辑。

定义形状的样式。

.. code-block:: scss
   :caption: ``/website_airproof/static/src/scss/primary_variables.scss``

   $o-bg-shapes: map-merge($o-bg-shapes,
       (
           'illustration': map-merge(
               map-get($o-bg-shapes, 'illustration') or (),
               (
                   'hexagons/01': ('position': center center, 'size': auto 100%, 'colors': (1), 'repeat-x': true, 'repeat-y': true),
               ),
           ),
       )
   );

.. list-table::
   :header-rows: 1
   :stub-columns: 1
   :widths: 20 80

   * - 键
     - 描述
   * - 文件位置
     - `hexagons/01` 对应于您的文件在 `shapes` 文件夹中的位置。
   * - position
     - 定义形状的位置。
   * - size
     - 定义形状的大小。
   * - colors
     - 定义您想要的颜色 c*（这将覆盖您在 SVG 中指定的颜色）。
   * - repeat-x
     - 定义是否水平重复形状。此键是可选的，只有在设置为 `true` 时才需要定义。
   * - repeat-y
     - 定义是否垂直重复形状。此键是可选的，只有在设置为 `true` 时才需要定义。

最后，将您的形状添加到网站构建器中可用的形状列表中。

.. code-block:: xml
   :caption: ``/website_airproof/views/snippets/options.xml``

   <template id="snippet_options_background_options" inherit_id="website.snippet_options_background_options" name="Shapes">
       <xpath expr="//*[hasclass('o_we_shape_menu')]/*[last()]" position="after">
           <we-select-page string="Theme">
               <we-button data-shape="illustration/hexagons/01" data-select-label="Hexagon 01"/>
           </we-select-page>
       </xpath>
   </template>

您的自定义形状现在可以像标准形状一样使用。

图片形状
============

图片形状是 SVG 文件，您可以将其添加为图片的剪裁蒙版。有些形状可以自定义颜色，有些是动画的。

标准
--------

提供了大量默认的图片形状供选择。

**使用**

.. code-block:: xml

   <img src="..."
       class="img img-fluid mx-auto"
       alt="..."
       data-shape="web_editor/solid/blob_2_solid_str"
       data-shape-colors="#35979C;;;;"
   >

.. list-table::
   :header-rows: 1
   :stub-columns: 1
   :widths: 20 80

   * - 属性
     - 描述
   * - data-shape
     - 形状位置
   * - data-shape-colors
     - 应用于形状的颜色

.. warning::
   有时，在添加更改后，您的图片形状可能无法应用。为了解决此问题，请打开网站构建器并保存页面以强制加载形状。
