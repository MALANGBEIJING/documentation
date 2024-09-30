=========
渐变
=========

在本章中，您将学习如何：

- 为部分或标题添加渐变。
- 将自定义渐变添加到网站构建器的调色板中。

标准
========

在标准情况下，您可以直接从网站构建器中选择几种渐变。然而，对于自定义主题，您必须在部分标签中使用样式属性直接添加渐变。

**用法**

.. code-block:: xml

    <section class="s_text_image" data-snippet="s_text_image" data-name="Text - Image" style="background-image: linear-gradient(135deg, rgb(255, 204, 51) 0%, rgb(226, 51, 255) 100%) !important;">
        <!-- 内容 -->
    </section>

要将渐变应用于文本，请使用带有 `text-gradient` 类的字体标签。

.. code-block:: xml

   <h2>
       <font class="text-gradient" style="background-image: linear-gradient(135deg, rgb(203, 94, 238) 0%, rgb(75, 225, 236) 100%);">一个部分副标题</font>
   </h2>

自定义
======

您还可以将自定义渐变添加到网站构建器中。这样，用户可以轻松使用它们，而无需手动重新创建。

.. code-block:: xml
   :caption: ``/website_airproof/data/presets.xml``

   <record id="colorpicker" model="ir.ui.view">
       <field name="key">website_airproof.colorpicker</field>
       <field name="name">自定义渐变</field>
       <field name="type">qweb</field>
       <field name="inherit_id" ref="web_editor.colorpicker"/>
       <field name="website_id">1</field>
       <field name="arch" type="xml">
           <xpath expr="//*[@data-name='predefined_gradients']/*" position="before">
               <button class="w-50 o_we_color_btn" style="background-image: linear-gradient(145deg, rgb(5, 85, 94) 0%, rgb(0, 131, 148) 100%);" data-color="linear-gradient(145deg, rgb(5, 85, 94) 0%, rgb(0, 131, 148) 100%)"></button>
           </xpath>
       </field>
   </record>
