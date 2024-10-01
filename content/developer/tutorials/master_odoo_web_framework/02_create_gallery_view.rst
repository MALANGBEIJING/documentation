创建画廊视图
===============================

让我们看看如何从零开始创建一个新视图。在某种程度上，这并不是很难，但没有好的资源来说明该如何做。请注意，大多数情况应该通过自定义现有视图或使用客户端操作来解决。

在本练习中，我们假设要创建一个 `画廊` 视图，该视图允许我们用一个图像字段表示一组记录。

这个问题当然可以通过看板视图来解决，但这意味着不能在同一操作中同时拥有我们的常规看板视图和画廊视图。

让我们创建一个画廊视图。每个画廊视图将通过其架构中的 `image_field` 属性定义：

.. code-block:: xml

   <gallery image_field="some_field"/>

要完成本章的任务，您需要安装 awesome_gallery 附加组件。该附加组件包含添加新视图所需的服务器文件。

.. admonition:: 目标

   .. image:: 02_create_gallery_view/overview.png
      :align: center

.. spoiler:: 解决方案

   本章每个练习的解决方案托管在
   `官方 Odoo 教程库
   <https://github.com/odoo/tutorials/commits/{CURRENT_MAJOR_BRANCH}-master-odoo-web-framework-solutions/awesome_gallery>`_。

1. 创建一个 Hello World 视图
==========================

第一步是创建一个 JavaScript 实现，包含一个简单的组件。

#. 在 `static/src` 中创建 `gallery_view.js`、`gallery_controller.js` 和 `gallery_controller.xml` 文件。
#. 在 `gallery_controller.js` 中实现一个简单的 Hello World 组件。
#. 在 `gallery_view.js` 中导入控制器，创建视图对象，并在视图注册表中以 `gallery` 名称注册。

   .. example::
      这是如何定义视图对象的示例：

      .. code-block:: js

         import { registry } from "@web/core/registry";
         import { MyController } from "./my_controller";

         export const myView = {
               type: "my_view",
               display_name: "MyView",
               icon: "oi oi-view-list",
               multiRecord: true,
               Controller: MyController,
         };

         registry.category("views").add("my_controller", myView);

#. 将 `gallery` 添加为 `contacts.action_contacts` 操作中的视图类型之一。
#. 确保在切换到画廊视图时能够看到您的 Hello World 组件。

.. image:: 02_create_gallery_view/view_button.png
   :align: center

.. image:: 02_create_gallery_view/new_view.png
   :align: center

2. 使用布局组件
===========================

到目前为止，我们的画廊视图看起来并不像标准视图。让我们使用 `Layout` 组件来获得与其他视图相同的标准功能。

#. 导入 `Layout` 组件并将其添加到 `GalleryController` 的组件中。
#. 更新模板以使用 `Layout`。它需要一个 `display` 属性，该属性可以在 `props.display` 中找到。

.. image:: 02_create_gallery_view/layout.png
   :align: center

3. 解析架构
=================

到目前为止，我们的画廊视图并没有做太多事情。让我们开始读取视图架构中包含的信息。

解析架构的过程通常由每个视图特定的 `ArchParser` 完成。它继承自通用的 `XMLParser` 类。

.. example::

   下面是 `ArchParser` 可能的样子：

   .. code-block:: js

      export class MyCustomArchParser {
          parse(xmlDoc) {
             const myAttribute = xmlDoc.getAttribute("my_attribute")
             return {
                 myAttribute,
             }
          }
      }

#. 在自己的文件中创建 `ArchParser` 类。
#. 使用它读取 `image_field` 信息。
#. 更新 `gallery` 视图代码，将其添加到控制器接收的 props 中。

.. note::
   这样做可能有点过头，因为我们基本上只需要从架构中读取一个属性，但这是 Odoo 中所有其他视图使用的设计，因为它让我们提取一些前期处理，而不在控制器中。

.. seealso::
   `示例：图表架构解析器
   <{GITHUB_PATH}/addons/web/static/src/views/graph/graph_arch_parser.js>`_

4. 加载一些数据
=================

现在让我们从服务器获取一些真实数据。为此，我们必须使用 `webSearchRead` 从 ORM 服务中获取。

.. example::

   这是从模型获取记录的 `webSearchRead` 的示例：

   .. code-block:: js

      const { length, records } = this.orm.webSearchRead(this.resModel, domain, {
         specification: {
              [this.fieldToFetch]: {},
              [this.secondFieldToFetch]: {},
          },
          context: {
              bin_size: true,
          }
      })

#. 在 `GalleryController` 中添加一个 :code:`loadImages(domain) {...}` 方法。它应该从 ORM 服务执行 `webSearchRead` 调用以获取与域对应的记录，并使用接收的 `imageField`。
#. 如果您没有在调用的上下文中包含 `bin_size`，您将收到以 base64 编码的图像字段。确保在上下文中放入 `bin_size` 以接收图像字段的大小。我们稍后将显示图像。
#. 修改 `setup` 代码以在 `onWillStart` 和 `onWillUpdateProps` 钩子中调用该方法。
#. 修改模板以在 `Layout` 组件的默认插槽中显示每个图像的 ID 和大小。

.. note::
   加载数据的代码将在下一次练习中移动到适当的模型中。

.. image:: 02_create_gallery_view/gallery_data.png
   :align: center

5. 解决并发问题
================================

到目前为止，我们的代码没有并发保护。如果有人两次更改域，它将触发 `loadImages(domain)` 两次。因此我们有两个请求可能在不同的时间到达，取决于不同的因素。先接收第一个请求的响应再接收第二个请求的响应将导致不一致的状态。

Odoo 的 `KeepLast` 原语解决了这个问题，它管理任务列表，并只保持最后一个任务处于活动状态。

#. 从 :file:`@web/core/utils/concurrency` 导入 `KeepLast`。
#. 在模型中实例化一个 `KeepLast` 对象。
#. 将 `webSearchRead` 调用放入 `KeepLast` 中，以便仅解析最后一个调用。

.. seealso::
   `示例：使用 KeepLast <https://github.com/odoo/odoo/blob/ebf646b44f747567ff8788c884f7f18dffd453e0/addons/web/static/src/core/model_field_selector/model_field_selector_popover.js#L164>`_

6. 重组代码
==================

真实视图有点更有组织。这在这个示例中可能有点过头，但它旨在学习如何在 Odoo 中构造代码。此外，这将更好地适应变化的需求。

#. 将所有模型代码移到自己的 `GalleryModel` 类中。
#. 将所有渲染代码移到 `GalleryRenderer` 组件中。
#. 在 `GalleryController` 中导入 `GalleryModel` 和 `GalleryRenderer` 以使其正常工作。

7. 使视图可扩展
===========================

为了扩展视图，可以导入画廊视图对象以根据个人喜好进行修改。问题是目前无法定义自定义模型或渲染器，因为它在控制器中是硬编码的。

#. 在画廊视图文件中导入 `GalleryModel` 和 `GalleryRenderer`。
#. 向画廊视图对象添加 `Model` 和 `Renderer` 键，并将其分配给 `GalleryModel` 和 `GalleryRenderer`。将 `Model` 和 `Renderer` 作为 props 传递给控制器。
#. 删除控制器中的硬编码导入，从 props 中获取它们。
#. 使用 `t-component
   <https://github.com/odoo/owl/blob/master/doc/reference/component.md#dynamic-sub-components>`_ 来拥有动态子组件。

.. note::

   这就是某人现在可以通过修改渲染器来扩展画廊视图的方式：

   .. code-block:: js

      /** @odoo-module */

      import { registry } from '@web/core/registry';
      import { galleryView } from '@awesome_gallery/gallery_view';
      import { GalleryRenderer } from '@awesome_gallery/gallery_renderer';

      export class MyExtendedGalleryRenderer extends GalleryRenderer {
         static template = "my_module.MyExtendedGalleryRenderer";
         setup() {
            super.setup();
            console.log("my gallery renderer extension");
         }
      }

      registry.category("views").add("my_gallery", {
         ...galleryView,
         Renderer: MyExtendedGalleryRenderer,
      });

8. 显示图像
=================

更新渲染器，以良好的方式显示图像，如果字段被设置。如果 `image_field` 为空，则显示一个空框。

.. tip::

      有一个控制器允许从记录中检索图像。您可以使用以下代码片段构造链接：

      .. code-block:: js

         import { url } from "@web/core/utils/urls";
         const url = url("/web/image", {
            model: resModel,
            id: image_id,
            field: imageField,
         });

.. image:: 02_create_gallery_view/tshirt_images.png
   :align: center

9. 点击时切换到表单视图
===============================

更新渲染器以响应图像上的点击并切换到表单视图。您可以使用动作服务中的 `switchView` 函数。

.. seealso::
   `代码：switchView 函数 <https://github.com/odoo/odoo/blob/db2092d8d389fdd285f54e9b34a5a99cc9523d27/addons/web/static/src/webclient/actions/action_service.js#L1064>`_

10. 添加可选工具提示
===========================

在鼠标悬停时获得一些额外信息是有用的。

#. 更新代码以允许架构上的可选额外属性：

   .. code-block:: xml

      <gallery image_field="some_field" tooltip_field="some_other_field"/>

#. 鼠标悬停时显示工具提示字段的内容。如果字段是字符字段、数字字段或多对一字段，则应该可以工作。要为 HTML 元素添加工具提示，您可以将字符串放入元素的 `data-tooltip` 属性中。
#. 更新客户画廊视图架构以添加客户作为工具提示字段。

.. image:: 02_create_gallery_view/image_tooltip.png
   :align: center
   :scale: 50%

.. seealso::
   `示例：使用 t-att-data-tooltip <https://github.com/odoo/odoo/blob/145fe958c212ddef9fab56a232c8b2d3db635c8e/addons/survey/static/src/views/widgets/survey_question_trigger/survey_question_trigger.xml#L8>`_

11. 添加分页
==================

让我们在控制面板上添加一个分页器，并像正常 Odoo 视图那样管理所有分页。

.. image:: 02_create_gallery_view/pagination.png
   :align: center

.. seealso::
   - `代码：使用 Pager 钩子 <{GITHUB_PATH}/addons/web/static/src/search/pager_hook.js>`_
   - `示例：在列表控制器中使用 usePager <https://github.com/odoo/odoo/blob/48ef812a635f70571b395f82ffdb2969ce99da9e/addons/web/static/src/views/list/list_controller.js#L109-L128>`_

12. 验证视图
=====================

到目前为止，我们有一个漂亮而有用的视图。但是在现实生活中，我们可能会遇到用户不正确编码其画廊视图的情况：它目前仅是一个不结构化的 XML 段。

让我们添加一些验证！在 Odoo 中，XML 文档可以用 RN 文件 :dfn:`（Relax NG 文件）` 描述，然后进行验证。

#. 添加一个描述当前语法的 RNG 文件：

   - 一个必需的属性 `image_field`。
   - 一个可选属性：`tooltip_field`。

#. 添加一些代码以确保所有视图都根据此 RNG 文件进行验证。
#. 在此期间，让我们确保 `image_field` 和 `tooltip_field` 是当前模型中的字段。

由于验证 RNG 文件并不简单，这里有一个帮助的代码片段：

.. code-block:: python

   # -*- coding: utf-8 -*-
   import logging
   import os

   from lxml import etree

   from odoo.loglevels import ustr
   from odoo.tools import misc, view_validation

   _logger = logging.getLogger(__name__)

   _viewname_validator = None

   @view_validation.validate('viewname')
   def schema_viewname(arch, **kwargs):
         """ 检查画廊视图是否符合其架构

         :type arch: etree._Element
         """
         global _viewname_validator

         if _viewname_validator is None:
            with misc.file_open(os.path.join('modulename', 'rng', 'viewname.rng')) as f:
               _viewname_validator = etree.RelaxNG(etree.parse(f))

         if _viewname_validator.validate(arch):
            return True

         for error in _viewname_validator.error_log:
            _logger.error(ustr(error))
         return False

.. seealso::
   `示例：图表视图的 RNG 文件 <https://github.com/odoo/odoo/blob/70942e4cfb7a8993904b4d142e3b1749a40db806/odoo/addons/base/rng/graph_view.rng>`_

13. 上传图像
======================

我们的画廊视图不允许用户上传图像。让我们实现这一点。

#. 在每个图像上添加一个按钮，使用 `FileUploader` 组件。
#. `FileUploader` 组件接受 `onUploaded` 属性，当用户上传图像时会调用该属性。确保调用 ORM 服务中的 `webSave` 来上传新图像。
#. 您可能注意到图像被上传但浏览器未重新渲染。这是因为图像链接没有更改，因此浏览器不会重新获取它们。在图像 URL 中包含记录的 `write_date`。
#. 确保单击上传按钮不会触发切换视图。

.. image:: 02_create_gallery_view/upload_image.png
   :align: center
   :scale: 50%

.. seealso::
   - `示例：使用 FileUploader <https://github.com/odoo/odoo/blob/7710c3331ebd22f8396870bd0731f8c1152d9c41/addons/mail/static/src/web/activity/activity.xml#L48-L52>`_
   - `Odoo：webSave 定义 <https://github.com/odoo/odoo/blob/ebd538a1942c532bcf1c9deeab3c25efe23b6893/addons/web/static/src/core/orm_service.js#L312>`_

14. 高级工具提示模板
=============================

到目前为止，我们只能指定一个工具提示字段。但是如果我们想允许为其编写特定模板呢？

.. example::

   这是一个画廊架构视图的示例，经过本练习后应该可以工作。

   .. code-block:: xml

      <record id="contacts_gallery_view" model="ir.ui.view">
         <field name="name">awesome_gallery.orders.gallery</field>
         <field name="model">res.partner</field>
         <field name="arch" type="xml">
            <gallery image_field="image_1920" tooltip_field="name">
               <field name="email"/> <!-- 指定模型以提取 email -->
               <field name="name"/>  <!-- 指定模型以提取 name -->
               <tooltip-template> <!-- 指定工具提示的 owl 模板 -->
                  <p class="m-0">name: <field name="name"/></p> <!-- field 编译为 t-esc-->
                  <p class="m-0">e-mail: <field name="email"/></p>
               </tooltip-template>
            </gallery>
         </field>
      </record>

#. 用上述示例中的架构替换 :file:`awesome_gallery/views/views.xml` 中的 `res.partner` 画廊架构。即使不通过 rng 验证也没关系。
#. 修改画廊 rng 验证器以接受新的架构结构。

   .. tip::

      您可以使用此 rng 代码段来验证 tooltip-template 标签

      .. code-block:: xml

         <rng:define name="tooltip-template">
            <rng:element name="tooltip-template">
                  <rng:zeroOrMore>
                     <rng:text/>
                     <rng:ref name="any"/>
                  </rng:zeroOrMore>
            </rng:element>
         </rng:define>

         <rng:define name="any">
            <rng:element>
                  <rng:anyName/>
                  <rng:zeroOrMore>
                     <rng:choice>
                        <rng:attribute>
                              <rng:anyName/>
                        </rng:attribute>
                        <rng:text/>
                        <rng:ref name="any"/>
                     </rng:choice>
                  </rng:zeroOrMore>
            </rng:element>
         </rng:define>
#. 架构解析器应解析字段和工具提示模板。从 :file:`@web/core/utils/xml` 导入 `visitXML` 并使用它解析字段名和工具提示模板。
#. 确保模型通过在规范中包含解析的字段名来调用 `webSearchRead`。
#. 渲染器（或您为其创建的任何子组件）应接收解析的工具提示模板。操作该模板以替换 `<field>` 元素为 `<t t-esc="x">` 元素。

   .. tip::

      模板是 `Element` 对象，因此可以像 HTML 元素一样操作。

#. 使用 :file:`@odoo/owl` 中的 `xml` 函数将模板注册到 Owl。
#. 使用 :file:`@web/core/tooltip/tooltip_hook` 中的 `useTooltip` 钩子来显示工具提示。该钩子将 Owl 模板和模板所需的变量作为参数。

.. image:: 02_create_gallery_view/advanced_tooltip.png
   :align: center
   :scale: 50%

.. seealso::
   - `示例：在看板中使用 useTooltip <https://github.com/odoo/odoo/blob/0e6481f359e2e4dd4f5b5147a1754bb3cca57311/addons/web/static/src/views/kanban/kanban_record.js#L189-L192>`_
   - `示例：visitXML 使用 <https://github.com/odoo/odoo/blob/48ef812a635f70571b395f82ffdb2969ce99da9e/addons/web/static/src/views/list/list_arch_parser.js#L19>`_
   - `Owl：使用 xml 辅助函数的内联模板 <https://github.com/odoo/owl/blob/master/doc/reference/templates.md#inline-templates>`_
