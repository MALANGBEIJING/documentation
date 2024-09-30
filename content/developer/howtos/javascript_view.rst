=====================
自定义视图类型
=====================

继承现有视图
=========================

假设我们需要创建一个通用视图的自定义版本。例如，一个带有额外的丝带样式的小部件的看板视图（用于显示一些特定的自定义信息）。在这种情况下，可以通过以下几个步骤实现：

#. 扩展看板控制器/渲染器/模型，并将其注册到视图注册表中。

   .. code-block:: js
      :caption: :file:`custom_kanban_controller.js`

      /** @odoo-module */

      import { KanbanController } from "@web/views/kanban/kanban_controller";
      import { kanbanView } from "@web/views/kanban/kanban_view";
      import { registry } from "@web/core/registry";

      // 控制器通常包含布局和渲染器。
      class CustomKanbanController extends KanbanController {
          // 在这里编写逻辑，覆盖或插入新方法...
          // 如果覆盖 setup()，别忘了调用 super.setup()
      }

      CustomKanbanController.template = "my_module.CustomKanbanView";

      export const customKanbanView = {
          ...kanbanView, // 包含默认的渲染器/控制器/模型
          Controller: CustomKanbanController,
      };

      // 将其注册到视图注册表中
      registry.category("views").add("custom_kanban", customKanbanView);

   在我们的自定义看板中，我们定义了一个新模板。我们可以继承看板控制器模板并添加我们自己的模板片段，也可以定义一个全新的模板。

   .. code-block:: xml
      :caption: :file:`custom_kanban_controller.xml`

      <?xml version="1.0" encoding="UTF-8" ?>
      <templates>
          <t t-name="my_module.CustomKanbanView" t-inherit="web.KanbanView">
              <xpath expr="//Layout" position="before">
                  <div>
                      Hello world !
                  </div>
              </xpath>
          </t>
      </templates>

#. 在 arch 中使用 `js_class` 属性来使用该视图。

   .. code-block:: xml

      <kanban js_class="custom_kanban">
          <templates>
              <t t-name="kanban-box">
                  <!--您的注释-->
              </t>
          </templates>
      </kanban>

扩展视图的可能性是无穷无尽的。尽管我们在这里只扩展了控制器，您还可以扩展渲染器以添加新按钮，修改记录的展示方式，或自定义下拉菜单，还可以扩展其他组件，如模型和 `buttonTemplate`。

从头创建新视图
==============================

创建一个新视图是一个高级话题。本指南仅强调关键步骤。

#. 创建控制器。

   控制器的主要作用是协调视图的各种组件，例如渲染器、模型和布局。

   .. code-block:: js
      :caption: :file:`beautiful_controller.js`

      /** @odoo-module */

      import { Layout } from "@web/search/layout";
      import { useService } from "@web/core/utils/hooks";
      import { Component, onWillStart, useState} from "@odoo/owl";

      export class BeautifulController extends Component {
          setup() {
              this.orm = useService("orm");

              // 控制器创建模型并使其具有反应性，因此每当访问和编辑这个模型时，它将重新渲染
              this.model = useState(
                  new this.props.Model(
                      this.orm,
                      this.props.resModel,
                      this.props.fields,
                      this.props.archInfo,
                      this.props.domain
                  )
              );

              onWillStart(async () => {
                  await this.model.load();
              });
          }
      }

      BeautifulController.template = "my_module.View";
      BeautifulController.components = { Layout };

   控制器的模板显示了带有布局的控制面板以及渲染器。

   .. code-block:: xml
      :caption: :file:`beautiful_controller.xml`

      <?xml version="1.0" encoding="UTF-8"?>
      <templates xml:space="preserve">
          <t t-name="my_module.View">
              <Layout display="props.display" className="'h-100 overflow-auto'">
                  <t t-component="props.Renderer" records="model.records" propsYouWant="'Hello world'"/>
              </Layout>
          </t>
      </templates>

#. 创建渲染器。

   渲染器的主要功能是通过渲染视图中的记录来生成数据的视觉表示。

   .. code-block:: js
      :caption: :file:`beautiful_renderer.js`

      import { Component } from "@odoo/owl";
      export class BeautifulRenderer extends Component {}

      BeautifulRenderer.template = "my_module.Renderer";

   .. code-block:: xml
      :caption: :file:`beautiful_renderer.xml`

      <?xml version="1.0" encoding="UTF-8"?>
      <templates xml:space="preserve">
          <t t-name="my_module.Renderer">
              <t t-esc="props.propsYouWant"/>
              <t t-foreach="props.records" t-as="record" t-key="record.id">
                  // 显示记录
              </t>
          </t>
      </templates>

#. 创建模型。

   模型的作用是检索和管理视图中所需的所有数据。

   .. code-block:: js
      :caption: :file:`beautiful_model.js`

      /** @odoo-module */

      import { KeepLast } from "@web/core/utils/concurrency";

      export class BeautifulModel {
          constructor(orm, resModel, fields, archInfo, domain) {
              this.orm = orm;
              this.resModel = resModel;
              // 我们可以访问由arch解析器解析的arch信息
              const { fieldFromTheArch } = archInfo;
              this.fieldFromTheArch = fieldFromTheArch;
              this.fields = fields;
              this.domain = domain;
              this.keepLast = new KeepLast();
          }

          async load() {
              // keepLast保护并发调用
              const { length, records } = await this.keepLast.add(
                  this.orm.webSearchRead(this.resModel, this.domain, [this.fieldsFromTheArch], {})
              );
              this.records = records;
              this.recordsLength = length;
          }
      }

   .. note::

      对于高级案例，您可以不从头创建模型，而是使用 `RelationalModel`，这是其他视图中使用的模型。

#. 创建arch解析器。

   arch解析器的作用是解析arch视图，使视图能够访问信息。

   .. code-block:: js
      :caption: :file:`beautiful_arch_parser.js`

      /** @odoo-module */

      import { XMLParser } from "@web/core/utils/xml";

      export class BeautifulArchParser extends XMLParser {
          parse(arch) {
              const xmlDoc = this.parseXML(arch);
              const fieldFromTheArch = xmlDoc.getAttribute("fieldFromTheArch");
              return {
                  fieldFromTheArch,
              };
          }
      }

#. 创建视图并将所有部分组合在一起，然后将视图注册到视图注册表中。

   .. code-block:: js
      :caption: :file:`beautiful_view.js`

      /** @odoo-module */

      import { registry } from "@web/core/registry";
      import { BeautifulController } from "./beautiful_controller";
      import { BeautifulArchParser } from "./beautiful_arch_parser";
      import { BeautifulModel } from "./beautiful_model";
      import { BeautifulRenderer } from "./beautiful_renderer";

      export const beautifulView = {
          type: "beautiful",
          display_name: "Beautiful",
          icon: "fa fa-picture-o", // 在布局面板中显示的图标
          multiRecord: true,
          Controller: BeautifulController,
          ArchParser: BeautifulArchParser,
          Model: BeautifulModel,
          Renderer: BeautifulRenderer,

          props(genericProps, view) {
              const { ArchParser } = view;
              const { arch } = genericProps;
              const archInfo = new ArchParser().parse(arch);

              return {
                  ...genericProps,
                  Model: view.Model,
                  Renderer: view.Renderer,
                  archInfo,
              };
          },
      };

      registry.category("views").add("beautifulView", beautifulView);

#. 在arch中声明视图。

   .. code-block:: xml

      ...
      <record id="my_beautiful_view" model="ir.ui.view">
        <field name="name">my_view</field>
        <field name="model">my_model</field>
        <field name="arch" type="xml">
            <beautiful fieldFromTheArch="res.partner"/>
        </field>
      </record>
      ...
