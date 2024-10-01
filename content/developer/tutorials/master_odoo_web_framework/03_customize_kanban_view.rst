==================================
第 3 章：自定义看板视图
==================================

我们已经了解了网络框架提供的众多功能。接下来，我们将自定义一个看板视图。这是一个更复杂的项目，将展示框架的一些非平凡方面。目标是练习组合视图、协调用户界面的各个方面，并以可维护的方式进行操作。

Bafien 提出了一个绝妙的主意：将看板视图和列表视图结合起来，简直是完美的需求！简单来说，他希望在 CRM 看板视图的左侧显示客户列表。当您单击左侧边栏中的客户时，右侧的看板视图会过滤，仅显示与该客户相关的潜在客户。

.. admonition:: 目标

   .. image:: 03_customize_kanban_view/overview.png
      :align: center

.. spoiler:: 解决方案

   本章每个练习的解决方案托管在
   `官方 Odoo 教程仓库
   <https://github.com/odoo/tutorials/commits/{CURRENT_MAJOR_BRANCH}-master-odoo-web-framework-solutions/awesome_kanban>`_。

1. 创建一个新的看板视图
===========================

由于我们正在自定义看板视图，因此让我们开始扩展它并在 CRM 的看板视图中使用我们的扩展。

#. 创建一个新的空组件，扩展 :file:`@web/views/kanban/kanban_controller` 中的 `KanbanController` 组件。
#. 创建一个新的视图对象，并从 :file:`@web/views/kanban/kanban_view` 中分配所有键值对给 `kanbanView`。通过将新创建的控制器放入 Controller 键来覆盖它。
#. 将其注册到 `awesome_kanban` 的视图注册表中。
#. 更新 :file:`awesome_kanban/views/views.xml` 中的 crm 看板 arch，以使用扩展的视图。
   这可以通过在看板节点中指定 `js_class` 属性来完成。

.. seealso::

   `示例：通过扩展预先存在的视图创建新视图 <https://github.com/odoo/odoo/blob/0a59f37e7dd73daff2e9926542312195b3de4154/addons/todo/static/src/views/todo_conversion_form/todo_conversion_form_view.js>`_

2. 创建一个 CustomerList 组件
==================================

我们需要显示客户列表，因此我们可以创建该组件。

#. 创建一个 `CustomerList` 组件，目前只显示一个带有文本的 `div`。
#. 它应该具有一个 `selectCustomer` 属性。
#. 创建一个新的模板，通过 XPath 扩展看板控制器模板 `web.KanbanView`，将 `CustomerList` 添加到看板渲染器旁边。暂时为其提供一个空函数作为 `selectCustomer`。

   .. tip::

      您可以在模板内部使用此 XPath 在渲染器组件之前添加一个 div。

      .. code-block:: xml

            <xpath expr="//t[@t-component='props.Renderer']" position="before">
               ...
            </xpath>

#. 通过子类化看板控制器将 `CustomerList` 添加到其子组件中。
#. 确保您可以在看板视图中看到您的组件。

.. image:: 03_customize_kanban_view/customer_list_component.png
   :align: center

.. seealso::

   :ref:`模板继承 <reference/qweb/template_inheritance>`

3. 加载和显示数据
========================

#. 修改 `CustomerList` 组件以在 `onWillStart` 中获取所有客户的列表。
#. 使用 `t-foreach` 在模板中显示列表。
#. 每当选择一个客户时，调用 `selectCustomer` 函数属性。

.. image:: 03_customize_kanban_view/customer_data.png
   :align: center

.. seealso::

   - `示例：从模型中获取记录 <https://github.com/odoo/odoo/blob/986c00c1bd1b3ca16a04ab25f5a2504108136112/addons/project/static/src/views/burndown_chart/burndown_chart_model.js#L26-L31>`_

4. 更新主看板视图
==============================

#. 在看板控制器中实现 `selectCustomer` 以添加适当的域。

   .. tip::

      由于与搜索视图交互并不简单，这里有一个创建过滤器的代码片段：

      .. code-block:: js

         this.env.searchModel.createNewFilters([{
               description: partner_name,
               domain: [["partner_id", "=", partner_id]],
               isFromAwesomeKanban: true, // 这是一个自定义键，用于稍后检索我们的过滤器
         }])

#. 通过单击多个客户，您可以看到旧的客户过滤器没有被替换。
   确保通过单击客户，旧过滤器被新过滤器替换。

   .. tip::

      您可以使用此代码片段获取客户过滤器并切换它们。

      .. code-block:: js

         const customerFilters = this.env.searchModel.getSearchItems((searchItem) =>
               searchItem.isFromAwesomeKanban
         );

         for (const customerFilter of customerFilters) {
            if (customerFilter.isActive) {
                  this.env.searchModel.toggleSearchItem(customerFilter.id);
            }
         }

#. 修改模板以为 `CustomerList` 的 `selectCustomer` 属性提供真实功能。

.. note::

   您可以使用 `Symbol
   <https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol>`_
   来确保自定义的 `isFromAwesomeKanban` 键不会与任何其他代码可能添加到对象中的键冲突。

.. image:: 03_customize_kanban_view/customer_filter.png
   :align: center

5. 仅显示具有活动订单的客户
====================================================

在 `res.partner` 上有一个 `opportunity_ids` 字段。让我们允许用户筛选至少有一个机会的客户结果。

#. 在 `CustomerList` 组件中添加一个复选框类型的输入，旁边有一个标签 "Active customers"。
#. 更改复选框的值应过滤客户列表。

.. image:: 03_customize_kanban_view/active_customer.png
   :align: center
   :scale: 60%

6. 向客户列表添加搜索栏
========================================

在客户列表上方添加一个输入，允许用户输入字符串并根据其姓名过滤显示的客户。

.. tip::
   您可以使用 `fuzzyLookup` 函数从 :file:`@web/core/utils/search` 来执行过滤。

.. image:: 03_customize_kanban_view/customer_search.png
   :align: center
   :scale: 60%

.. seealso::

   - `代码：模糊查找函数 <https://github.com/odoo/odoo/blob/235fc69280a18a5805d8eb84d76ada91ba49fe67/addons/web/static/src/core/utils/search.js#L41-L54>`_
   - `示例：使用模糊查找
     <https://github.com/odoo/odoo/blob/1f4e583ba20a01f4c44b0a4ada42c4d3bb074273/
     addons/web/static/tests/core/utils/search_test.js#L17>`_

7. 重构代码以使用 `t-model`
=====================================

为了解决前两个练习，您很可能在输入上使用了事件监听器。让我们看看我们如何以更声明的方式做到这一点，使用 `t-model
<{OWL_PATH}/doc/reference/input_bindings.md>`_ 指令。

#. 确保您有一个响应式对象来表示过滤器是否处于活动状态
   （类似于
   :code:`this.state = useState({ displayActiveCustomers: false, searchString: ''})`）。
#. 修改代码以添加一个获取器 `displayedCustomers`，返回当前活动的客户列表。
#. 修改模板以使用 `t-model`。

8. 为客户添加分页！
======================

#. 在 `CustomerList` 中添加一个 :ref:`pager <frontend/pager>`，仅加载/渲染前 20 个客户。
#. 每当分页器更改时，客户列表应相应更新。

这实际上非常困难，特别是在与上一个练习中进行的过滤结合时。有许多边缘情况需要考虑。

.. image:: 03_customize_kanban_view/customer_pager.png
   :align: center
   :scale: 60%