=================
自定义字段
=================

继承现有字段组件
====================================

让我们以扩展 `BooleanField` 为例，创建一个布尔字段，当复选框被选中时显示“迟到！”的红色文字。

#. 创建一个扩展所需字段组件的新小部件组件。

   .. code-block:: javascript
      
      :caption: :file:`late_order_boolean_field.js`

      /** @odoo-module */

      import { registry } from "@web/core/registry";
      import { BooleanField } from "@web/views/fields/boolean/boolean_field";
      import { Component, xml } from "@odoo/owl";

      class LateOrderBooleanField extends BooleanField {}
      LateOrderBooleanField.template = "my_module.LateOrderBooleanField";

#. 创建字段模板。

   该组件使用一个名为 `my_module.LateOrderBooleanField` 的新模板。通过继承 `BooleanField` 的当前模板来创建它。

   .. code-block:: xml
      :caption: :file:`late_order_boolean_field.xml`

      <?xml version="1.0" encoding="UTF-8" ?>
      <templates xml:space="preserve">
          <t t-name="my_module.LateOrderBooleanField" t-inherit="web.BooleanField">
              <xpath expr="//CheckBox" position="after">
                    <span t-if="props.value" class="text-danger"> 迟到！ </span>
              </xpath>
          </t>
      </templates>

#. 将组件注册到字段注册表中。

   .. code-block::
      :caption: :file:`late_order_boolean_field.js`

      registry.category("fields").add("late_boolean", LateOrderBooleanField);

#. 在视图arch中添加小部件作为字段的属性。

   .. code-block:: xml

      <field name="somefield" widget="late_boolean"/>

创建新的字段组件
============================

假设我们想创建一个显示红色简单文本的字段。

#. 创建一个表示新字段的 Owl 组件。

   .. code-block:: js
      :caption: :file:`my_text_field.js`

      /** @odoo-module */

      import { standardFieldProps } from "@web/views/fields/standard_field_props";
      import { Component, xml } from "@odoo/owl";
      import { registry } from "@web/core/registry";

      export class MyTextField extends Component {

          /**
          * @param {boolean} newValue
          */
          onChange(newValue) {
              this.props.update(newValue);
          }
      }

      MyTextField.template = xml`
          <input t-att-id="props.id" class="text-danger" t-att-value="props.value" onChange.bind="onChange" />
      `;
      MyTextField.props = {
          ...standardFieldProps,
      };
      MyTextField.supportedTypes = ["char"];

   导入的 `standardFieldProps` 包含由 `View` 传递的标准属性，例如用于更新值的 `update` 函数、模型中字段的 `type`、 `readonly` 布尔值等。

#. 在同一个文件中，将组件注册到字段注册表中。

   .. code-block:: js
      :caption: :file:`my_text_field.js`

      registry.category("fields").add("my_text_field", MyTextField);

   这将arch中的小部件名称映射到其实际组件。

#. 在视图arch中添加小部件作为字段的属性。

   .. code-block:: xml

      <field name="somefield" widget="my_text_field"/>
