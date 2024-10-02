=========================
第1章：Owl组件
=========================

本章介绍了 `Owl框架 <https://github.com/odoo/owl>`_，这是为Odoo量身定制的组件系统。Owl的主要构建块是 `组件 <{OWL_PATH}/doc/reference/component.md>`_ 和 `模板 <{OWL_PATH}/doc/reference/templates.md>`_。

在Owl中，用户界面的每个部分都是由一个组件管理的：它们保存逻辑并定义用于渲染用户界面的模板。在实践中，组件由一个小的JavaScript类表示，该类是 `Component` 类的子类。

要开始使用，您需要一个运行中的Odoo服务器和一个开发环境设置。在进行练习之前，请确保您已按照此 :ref:`教程介绍 <tutorials/discover_js_framework/setup>` 中描述的所有步骤操作。

.. 提示::
   如果您使用Chrome作为网页浏览器，可以安装 `Owl Devtools` 扩展。该扩展提供了许多功能，可以帮助您理解和分析任何Owl应用程序。

   `视频：如何使用DevTools <https://www.youtube.com/watch?v=IUyQjwnrpzM>`_

在本章中，我们使用 `awesome_owl` 插件，它提供了一个简化的环境，只包含Owl和一些其他文件。目标是学习Owl本身，而不依赖于Odoo网页客户端代码。

.. 剧透:: 解决方案

   本章每个练习的解决方案托管在 `官方Odoo教程仓库 <https://github.com/odoo/tutorials/commits/{CURRENT_MAJOR_BRANCH}-discover-js-framework-solutions/awesome_owl>`_ 中。建议您先尝试自己解决，然后再查看解决方案！

示例：`Counter` 组件
==============================

首先，让我们看看一个简单的例子。下面的 `Counter` 组件是一个维护内部数字值、显示该值并在用户单击按钮时更新它的组件。

.. code-block:: js

   /** @odoo-module **/

   import { Component, useState } from "@odoo/owl";

   export class Counter extends Component {
       static template = "my_module.Counter";

       setup() {
           this.state = useState({ value: 0 });
       }

       increment() {
           this.state.value++;
       }
   }

`Counter` 组件指定了一个代表其HTML的模板名称。它是用XML编写的，使用QWeb语言：

.. code-block:: xml

   <templates xml:space="preserve">
      <t t-name="my_module.Counter">
         <p>计数器: <t t-esc="state.value"/></p>
         <button class="btn btn-primary" t-on-click="increment">增加</button>
      </t>
   </templates>

1. 显示一个计数器
=======================

.. image:: 01_owl_components/counter.png
   :align: center

作为第一个练习，让我们修改位于 :file:`awesome_owl/static/src/` 的 `Playground` 组件，使其变成一个计数器。要查看结果，可以在浏览器中访问 `/awesome_owl` 路由。

#. 修改 :file:`playground.js` 使其像上面的示例一样作为一个计数器。保持 `Playground` 作为类名。您需要使用 `useState hook <{OWL_PATH}/doc/reference/hooks.md#usestate>`_ 以便在该组件读取的状态对象的任何部分被修改时重新渲染组件。
#. 在同一组件中，创建一个 `increment` 方法。
#. 修改 :file:`playground.xml` 中的模板，以便显示您的计数器变量。使用 `t-esc <{OWL_PATH}/doc/reference/templates.md#outputting-data>`_ 输出数据。
#. 在模板中添加一个按钮，并在按钮中指定 `t-on-click <{OWL_PATH}/doc/reference/event_handling.md#event-handling>`_ 属性，以在单击按钮时触发 `increment` 方法。

.. 重要::
   不要忘记在JavaScript文件中包含 :code:`/** @odoo-module **/`。有关更多信息，请参见 :ref:`此处 <frontend/modules/native_js>`。

.. 提示::
   浏览器下载的Odoo JavaScript文件是压缩的。为了调试方便，当文件未被压缩时更容易。切换到 :ref:`调试模式与资产 <developer-mode/activation>`，这样文件就不会被压缩。

这个练习展示了Owl的一个重要特性：`反应性系统 <{OWL_PATH}/doc/reference/reactivity.md>`_。
`useState` 函数将一个值包装在一个代理中，以便Owl可以跟踪哪些组件需要状态的哪个部分，因此在值发生变化时可以更新它。尝试去掉 `useState` 函数，看看会发生什么。

2. 将 `Counter` 提取到子组件中
=======================================

现在我们在 `Playground` 组件中有计数器的逻辑，但它不可重用。让我们看看如何将其创建为一个 `子组件 <{OWL_PATH}/doc/reference/component.md#sub-components>`_：

#. 将计数器代码从 `Playground` 组件中提取到一个新的 `Counter` 组件中。
#. 您可以先在同一文件中完成，但完成后请更新代码，将 `Counter` 移动到自己的文件夹和文件中。从 `./counter/counter` 相对导入它。确保模板在自己的文件中，名称相同。
#. 在 `Playground` 组件的模板中使用 `<Counter/>` 添加两个计数器。

.. image:: 01_owl_components/double_counter.png
   :align: center

.. 提示::
   按惯例，大多数组件代码、模板和 CSS 应该具有与组件相同的蛇形命名。例如，如果我们有一个 `TodoList` 组件，其代码应该在 `todo_list.js`、`todo_list.xml`，如有必要，`todo_list.scss`。

.. _tutorials/discover_js_framework/simple_card:

3. 一个简单的 `Card` 组件
============================

组件实际上是将复杂用户界面分割成多个可重用部分的最自然方式。但为了使它们真正有用，必须能够在它们之间传达一些信息。让我们看看一个父组件如何通过使用属性（通常称为 `props <{OWL_PATH}/doc/reference/props.md>`_）向子组件提供信息。

这个练习的目标是创建一个 `Card` 组件，该组件接收两个属性：`title` 和 `content`。例如，它可以如下使用：

.. code-block:: xml

   <Card title="'我的标题'" content="'一些内容'"/>

上述示例应该生成一些使用bootstrap的html，如下所示：

.. code-block:: html

         <div class="card d-inline-block m-2" style="width: 18rem;">
             <div class="card-body">
                 <h5 class="card-title">我的标题</h5>
                 <p class="card-text">
                  一些内容
                 </p>
             </div>
         </div>

#. 创建一个 `Card` 组件。
#. 在 `Playground` 中导入它，并在其模板中显示几个卡片。

.. image:: 01_owl_components/simple_card.png
   :align: center

4. 使用 `markup` 显示HTML
=================================

如果您在上一个练习中使用了 `t-esc`，那么您可能注意到Owl会自动转义其内容。例如，如果您尝试这样显示一些html：`<Card title="'我的标题'" content="this.html"/>`，其中 `this.html = "<div>一些内容</div>"`，最终输出将简单地将html作为字符串显示。

在这种情况下，由于 `Card` 组件可以用于显示任何类型的内容，因此允许用户显示一些html是有意义的。这是通过使用 `t-out 指令 <{OWL_PATH}/doc/reference/templates.md#outputting-data>`_ 实现的。

然而，作为HTML显示任意内容是危险的，它可能被用来注入恶意代码，因此默认情况下，Owl将始终转义字符串，除非它明确标记为安全，通过 `markup` 函数。

#. 更新 `Card` 以使用 `t-out`。
#. 更新 `Playground` 以导入 `markup`，并在某些html值上使用它。
#. 确保正常字符串始终被转义，而标记的字符串则不会。

.. note::

   在Owl模板中仍然可以使用 `t-esc` 指令。它的速度稍快于 `t-out`。

.. image:: 01_owl_components/markup.png
   :align: center

5. 属性验证
===================


`Card` 组件有一个隐式API。它期望在其属性中接收两个字符串：`title` 和 `content`。让我们使该API更显式。我们可以添加一个属性定义，让Owl在 `开发模式 <{OWL_PATH}/doc/reference/app.md#dev-mode>`_ 中执行验证步骤。您可以在 `App配置 <{OWL_PATH}/doc/reference/app.md#configuration>`_ 中激活开发模式（但在 `awesome_owl` Playground中默认已激活）。

对每个组件进行属性验证是一种良好的实践。

#. 为 `Card` 组件添加 `props validation <{OWL_PATH}/doc/reference/props.md#props-validation>`_。
#. 在playground模板中将 `title` 属性重命名为其他名称，然后检查浏览器开发者工具的 :guilabel:`Console` 选项卡，查看是否有错误。

6. 两个 `Counter` 的和
===========================

我们在之前的练习中看到，`props` 可以用于从父组件向子组件提供信息。现在，让我们看看如何在相反的方向上传达信息：在这个练习中，我们想显示两个 `Counter` 组件，并在它们下面显示它们值的和。因此，父组件（`Playground`）需要在每次 `Counter` 值发生更改时被告知。

这可以通过使用一个 `callback prop <{OWL_PATH}/doc/reference/props.md#binding-function-props>`_ 来实现：一个作为函数的属性，旨在被调用。子组件可以选择用任何参数调用该函数。在我们的例子中，我们将简单地添加一个可选的 `onChange` 属性，每当 `Counter` 组件被递增时将被调用。

#. 为 `Counter` 组件添加属性验证：它应该接受一个可选的 `onChange` 函数属性。
#. 更新 `Counter` 组件，以便在每次递增时调用 `onChange` 属性（如果存在）。
#. 修改 `Playground` 组件以维护一个局部状态值（`sum`），初始设置为2，并在其模板中显示。
#. 在 `Playground` 中实现一个 `incrementSum` 方法。
#. 将该方法作为属性传递给两个（或更多个！）子 `Counter` 组件。

.. image:: 01_owl_components/sum_counter.png
   :align: center

.. 重要::

   关于回调属性的一个细微之处：它们通常应该使用 `.bind` 后缀定义。请参阅 `文档 <{OWL_PATH}/doc/reference/props.md#binding-function-props>`_。

7. 任务列表
==============

现在让我们通过创建一个任务列表来发现Owl的各种功能。我们需要两个组件：一个 `TodoList` 组件，用于显示 `TodoItem` 组件的列表。待办事项列表是一个状态，应该由 `TodoList` 维护。

在本教程中，`todo` 是一个包含三个值的对象：一个 `id`（数字）、一个 `description`（字符串）和一个 `isCompleted`（布尔值）标志：

.. code-block:: js

   { id: 3, description: "购买牛奶", isCompleted: false }

#. 创建 `TodoList` 和 `TodoItem` 组件。
#. `TodoItem` 组件应该接收一个 `todo` 作为属性，并在 `div` 中显示其 `id` 和 `description`。
#. 现在，硬编码待办事项列表：

   .. code-block:: js

      // 在 TodoList 中
      this.todos = useState([{ id: 3, description: "购买牛奶", isCompleted: false }]);

#. 使用 `t-foreach <{OWL_PATH}/doc/reference/templates.md#loops>`_ 显示每个待办事项在 `TodoItem` 中。
#. 在playground中显示 `TodoList`。
#. 为 `TodoItem` 添加属性验证。

.. image:: 01_owl_components/todo_list.png
   :align: center

.. 提示::
   由于 `TodoList` 和 `TodoItem` 组件是如此紧密耦合，因此将它们放在同一文件夹中是合乎逻辑的。

.. note::
   `t-foreach` 指令在Owl中的实现与QWeb的Python实现并不完全相同：它需要一个唯一的 `t-key` 值，以便Owl能够正确地识别每个元素。

8. 使用动态属性
=========================

目前，`TodoItem` 组件并未直观显示待办事项是否已完成。让我们通过使用 `动态属性 <{OWL_PATH}/doc/reference/templates.md#dynamic-attributes>`_ 来做到这一点。

#. 如果待办事项已完成，则在 `TodoItem` 根元素上添加Bootstrap类 `text-muted` 和 `text-decoration-line-through`。
#. 更改硬编码的 `this.todos` 值，以检查其是否正确显示。

尽管指令被命名为 `t-att`（用于属性），但它可以用于设置 `class` 值（以及输入的html属性，如 `value`）。

.. image:: 01_owl_components/muted_todo.png
   :align: center

.. 提示::

   Owl允许您将静态类值与动态值结合。以下示例将按预期工作：

   .. code-block:: xml

      <div class="a" t-att-class="someExpression"/>

   另见：`Owl：动态类属性 <{OWL_PATH}/doc/reference/templates.md#dynamic-class-attribute>`_

9. 添加待办事项
================
目前，我们列表中的待办事项是硬编码的。让我们通过允许用户向列表中添加待办事项，使其更有用。

#. 在 `TodoList` 组件中移除硬编码的值：

   .. code-block:: javascript

      this.todos = useState([]);

#. 在任务列表上方添加一个输入框，提示文本为 *输入新任务*。
#. 在 `keyup` 事件上添加一个事件处理程序 <{OWL_PATH}/doc/reference/event_handling.md>_，命名为 `addTodo`。
#. 实现 `addTodo` 以检查是否按下了回车键（：code:`ev.keyCode === 13`），在这种情况下，使用当前输入内容作为描述创建一个新的待办事项，并清除输入框中的所有内容。
#. 确保待办事项具有唯一的ID。它可以是每个待办事项递增的计数器。
#. 奖励点：如果输入为空，则不执行任何操作。

.. image:: 01_owl_components/create_todo.png
   :align: center

.. 另请参见::
   `Owl：反应性 <{OWL_PATH}/doc/reference/reactivity.md>`_

理论：组件生命周期和钩子
=====================

到目前为止，我们已经看到了一个钩子函数的示例：`useState`。`钩子 <{OWL_PATH}/doc/reference/hooks.md>`_ 是一个特殊函数，可以 *挂钩* 到组件的内部。以 `useState` 为例，它生成一个与当前组件链接的代理对象。这就是为什么钩子函数必须在 `setup` 方法中调用，而不能在之后调用的原因！

.. flowchart LR

..     classDef hook fill:#ccf

..     subgraph "creation"
..     direction TB
..     A:::hook
..     B:::hook
..     M:::hook
..     A[setup]-->B
..     B[onWillStart] --> C(render)
..     C --> D("mount (in DOM)")
..     D --> M[onMounted]
..     end

..     subgraph updates
..     direction TB
..     E:::hook
..     F:::hook
..     H:::hook
..     E["(onWillUpdateProps)"] --> L(render)
..     L --> F[onWillPatch]
..     F --> G(patch DOM)
..     G --> H[onPatched]
..     end

..     subgraph destruction
..     direction TB
..     I:::hook
..     J:::hook
..     I[onWillUnmount] --> J[onWillDestroy]
..     J --> N(removed from DOM)

..     end

..     creation --> updates
..     updates --> destruction

.. figure:: 01_owl_components/component_lifecycle.svg
   :align: center
   :width: 50%

Owl 组件经历了很多阶段：它可以被实例化、渲染、挂载、更新、分离、销毁……这是 `组件生命周期 <{OWL_PATH}/doc/reference/component.md#lifecycle>`_。
上图显示了组件生命周期中最重要的事件（钩子以紫色显示）。
大致来说，组件被创建，然后更新（可能多次），最后被销毁。

Owl 提供了多种内置的 `钩子函数 <{OWL_PATH}/doc/reference/hooks.md>`_。它们都必须在 `setup` 函数中调用。例如，如果您想在组件挂载时执行一些代码，可以使用 `onMounted` 钩子：

.. code-block:: javascript

   setup() {
     onMounted(() => {
       // 在这里执行某些操作
     });
   }

.. 提示::

   所有钩子函数以 `use` 或 `on` 开头。例如：`useState` 或 `onMounted`。

10. 聚焦输入框
======================
让我们看看如何使用 `t-ref <{OWL_PATH}/doc/reference/refs.md>`_ 和 `useRef <{OWL_PATH}/doc/reference/hooks.md#useref>`_ 访问 DOM。主要思想是您需要在组件模板中用 `t-ref` 标记目标元素：

.. code-block:: xml

   <div t-ref="some_name">hello</div>

然后您可以在 JS 中使用 `useRef hook <{OWL_PATH}/doc/reference/hooks.md#useref>`_ 访问它。
但是，如果您考虑一下，就会发现一个问题：组件的实际 HTML 元素在创建组件时并不存在。它只有在组件挂载时才存在。但是钩子必须在 `setup` 方法中调用。所以，`useRef` 返回一个对象，该对象包含一个 `el`（表示元素）键，该键仅在组件挂载时定义。

.. code-block:: js

   setup() {
      this.myRef = useRef('some_name');
      onMounted(() => {
         console.log(this.myRef.el);
      });
   }


#. 从之前的练习中聚焦 `input`。这应该在 `TodoList` 组件中完成（注意输入 HTML 元素上有一个 `focus` 方法）。
#. 奖励点：将代码提取到一个专用的 `hook <{OWL_PATH}/doc/reference/hooks.md>`_ `useAutofocus` 中，在新的 :file:`awesome_owl/utils.js` 文件中。

.. image:: 01_owl_components/autofocus.png
   :align: center

.. 提示::

   Refs 通常以 `Ref` 结尾，以使其明显是特殊对象：

   .. code-block:: js

      this.inputRef = useRef('input');

11. 切换待办事项
==================

现在，让我们添加一个新功能：将待办事项标记为已完成。这实际上比想象的要复杂。状态的拥有者与显示状态的组件不是同一个。因此，`TodoItem` 组件需要与其父组件通信，表明需要切换待办事项的状态。实现此目的的一种经典方法是添加一个 `callback prop <{OWL_PATH}/doc/reference/props.md#binding-function-props>`_ `toggleState`。

#. 在任务的 ID 之前添加一个属性为 :code:`type="checkbox"` 的输入框，如果状态 `isCompleted` 为 true，则必须选中该框。

   .. 提示::
      如果 `t-att` 指令计算出的属性为假值，Owl 不会创建该属性。

#. 将回调 props `toggleState` 添加到 `TodoItem`。
#. 在 `TodoItem` 组件中，在输入框上添加 `change` 事件处理程序，确保它调用 `toggleState` 函数并传入待办事项 ID。
#. 使其正常工作！

.. image:: 01_owl_components/toggle_todo.png
   :align: center

12. 删除待办事项
==================
最后一步是让用户能够删除待办事项。

#. 在 `TodoItem` 中添加一个新的回调 prop `removeTodo`。
#. 在 `TodoItem` 组件的模板中插入 :code:`<span class="fa fa-remove"/>`。
#. 每当用户点击它时，应该调用 `removeTodo` 方法。
#. 使其正常工作！

   .. 提示::
      如果您使用数组来存储待办事项列表，可以使用 JavaScript 的 `splice` 函数从中删除待办事项。

.. code-block::

   // 找到要删除的元素的索引
   const index = list.findIndex((elem) => elem.id === elemId);
   if (index >= 0) {
         // 从列表中删除索引处的元素
         list.splice(index, 1);
   }

.. image:: 01_owl_components/delete_todo.png
   :align: center

.. _tutorials/discover_js_framework/generic_card:

通用 `Card` 组件与插槽
=============================

在 :ref:`之前的练习 <tutorials/discover_js_framework/simple_card>` 中，我们构建了一个简单的 `Card` 组件。但它的确相当有限。如果我们想在卡片内部显示一些任意内容，比如一个子组件呢？这就行不通，因为卡片的内容是由一个字符串描述的。然而，如果我们能够将内容描述为一个模板片段，那将非常方便。

这正是 Owl 的 `slot <{OWL_PATH}/doc/reference/slots.md>`_ 系统所设计的：允许编写通用组件。

让我们修改 `Card` 组件以使用插槽：

#. 移除 `content` prop。
#. 使用默认插槽定义主体。
#. 插入一些包含任意内容的卡片，例如 `Counter` 组件。
#. （奖励）添加 prop 验证。

.. image:: 01_owl_components/generic_card.png
   :align: center

.. 另请参阅::
   `Bootstrap: 关于卡片的文档 <https://getbootstrap.com/docs/5.2/components/card/>`_

最小化卡片内容
===========================

.. TODO: 该练习没有展示新概念；可能应该移除。

最后，让我们为 `Card` 组件添加一个功能，使其更有趣：我们想要一个按钮来切换其内容（显示或隐藏）。

#. 在 `Card` 组件中添加一个状态，以跟踪它是打开（默认）还是关闭。
#. 在模板中添加一个 `t-if` 来有条件地渲染内容。
#. 在标题中添加一个按钮，并修改代码以在按钮被点击时翻转状态。

.. image:: 01_owl_components/toggle_card.png
   :scale: 90%
   :align: center
