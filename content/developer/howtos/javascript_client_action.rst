.. _howtos/javascript_client_action:

======================
创建客户端操作
======================

客户端操作触发的操作完全在客户端实现。使用客户端操作的一个好处是能够轻松创建高度定制的界面。客户端操作通常由一个 OWL 组件定义；我们还可以使用 Web 框架，并使用服务、核心组件、钩子等。

#. 创建 :ref:`客户端操作 <reference/actions/client>`，并确保其可访问。

   .. code-block:: xml

      <record model="ir.actions.client" id="my_client_action">
          <field name="name">我的客户端操作</field>
          <field name="tag">my_module.MyClientAction</field>
      </record>

#. 创建一个表示客户端操作的组件。

   .. code-block:: js
      :caption: :file:`my_client_action.js`

      /** @odoo-module **/

      import { registry } from "@web/core/registry";
      import { Component } from  "@odoo/owl";

      class MyClientAction extends Component {}
      MyClientAction.template = "my_module.clientaction";

      // 记住我们在第一步中设置的标签名称
      registry.category("actions").add("my_module.MyClientAction", MyClientAction);

   .. code-block:: xml
      :caption: :file:`my_client_action.xml`

      <?xml version="1.0" encoding="UTF-8" ?>
      <templates xml:space="preserve">
          <t t-name="awesome_tshirt.clientaction">
              你好，世界
          </t>
      </templates>
