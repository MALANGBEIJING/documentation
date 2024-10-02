============================
第 2 章：构建仪表板
============================

本教程的第一部分向您介绍了大部分 Owl 的概念。现在是时候学习 Odoo JavaScript 框架的全部内容，尤其是其在 Web 客户端中的使用。

.. graph TD
..     subgraph "Owl"
..         C[组件]
..         T[模板]
..         H[钩子]
..         S[插槽]
..         E[事件]
..     end

..     odoo[Odoo JavaScript 框架] --> Owl

.. figure:: 02_web_framework/previously_learned.svg
   :align: center
   :width: 50%

要开始，您需要一个正在运行的 Odoo 服务器和一个开发环境的设置。在进行练习之前，请确保您已按照此 :ref:`教程介绍 <tutorials/discover_js_framework/setup>` 中描述的所有步骤进行设置。在本章中，我们将从 `awesome_dashboard` 附加模块提供的空仪表板开始。我们将逐步向其添加功能，使用 Odoo JavaScript 框架。

.. admonition:: 目标

   .. image:: 02_web_framework/overview_02.png
      :align: center

.. spoiler:: 解决方案

   本章每个练习的解决方案托管在 `官方 Odoo 教程仓库
   <https://github.com/odoo/tutorials/commits/{CURRENT_MAJOR_BRANCH}-discover-js-framework-solutions/awesome_dashboard>`_。

1. 新布局
===============

Odoo Web 客户端中的大多数屏幕使用一个通用布局：顶部有控制面板，带有一些按钮，下面是主要内容区域。这是通过 `Layout 组件
<{GITHUB_PATH}/addons/web/static/src/search/layout.js>`_ 完成的，该组件在 `@web/search/layout` 中可用。

#. 更新位于 :file:`awesome_dashboard/static/src/` 的 `AwesomeDashboard` 组件以使用 `Layout` 组件。您可以为 `Layout` 组件的 `display` props 使用 :code:`{controlPanel: {} }`。
#. 为 `Layout` 添加一个 `className` prop：`className="'o_dashboard h-100'"`。
#. 添加一个 `dashboard.scss` 文件，在其中将 `.o_dashboard` 的背景颜色设置为灰色（或您喜欢的颜色）。

打开 http://localhost:8069/web，然后打开 :guilabel:`Awesome Dashboard` 应用程序，查看结果。

.. image:: 02_web_framework/new_layout.png
   :align: center

.. 另请参阅::

   - `示例：在客户端操作中使用 Layout
     <{GITHUB_PATH}/addons/web/static/src/webclient/actions/reports/report_action.js>`_ 和
     `模板 <{GITHUB_PATH}/addons/web/static/src/webclient/actions/reports/report_action.xml>`_
   - `示例：在看板视图中使用 Layout
     <{GITHUB_PATH}/addons/web/static/src/views/kanban/kanban_controller.xml>`_

.. _tutorials/discover_js_framework/services:

理论：服务
================

实际上，除根组件外，每个组件随时可能被销毁，并被另一个组件替换（或不替换）。这意味着每个组件的内部状态不是持久的。这在许多情况下是可以的，但无疑有些情况我们希望保留一些数据。例如，所有讨论消息不应在每次显示频道时重新加载。

此外，可能会发生我们需要编写一些不是组件的代码。也许是处理所有条形码的代码，或者管理用户配置（上下文等）的代码。

Odoo 框架定义了一个 :ref:`服务 <frontend/services>` 的概念，它是一个持久的代码片段，导出状态和/或函数。每个服务可以依赖其他服务，组件可以导入服务。

以下示例注册了一个简单的服务，每 5 秒显示一次通知：

.. code-block:: js

   import { registry } from "@web/core/registry";

   const myService = {
       dependencies: ["notification"],
       start(env, { notification }) {
           let counter = 1;
           setInterval(() => {
               notification.add(`Tick Tock ${counter++}`);
           }, 5000);
       },
   };

   registry.category("services").add("myService", myService);

服务可以被任何组件访问。假设我们有一个服务来维护一些共享状态：

.. code-block:: js

   import { registry } from "@web/core/registry";

   const sharedStateService = {
       start(env) {
           let state = {};
           return {
               getValue(key) {
                   return state[key];
               },
               setValue(key, value) {
                   state[key] = value;
               },
           };
       },
   };

   registry.category("services").add("shared_state", sharedStateService);

然后，任何组件都可以这样做：

.. code-block:: js

   import { useService } from "@web/core/utils/hooks";

   setup() {
      this.sharedState = useService("shared_state");
      const value = this.sharedState.getValue("somekey");
      // 做些事情
   }

2. 为快速导航添加一些按钮
========================================

.. TODO: 添加对操作服务的引用，待文档更新。

Odoo 提供的一个重要服务是 `action` 服务：它可以执行 Odoo 定义的所有标准操作。例如，以下是如何通过其 XML ID 执行一个操作：

.. code-block:: js

   import { useService } from "@web/core/utils/hooks";
   ...
   setup() {
         this.action = useService("action");
   }
   openSettings() {
         this.action.doAction("base_setup.action_general_configuration");
   }
   ...

现在让我们在控制面板中添加两个按钮：

#. 一个按钮 `Customers`，打开一个包含所有客户的看板视图（此操作已经存在，因此您应该使用 `其 XML ID
   <https://github.com/odoo/odoo/blob/1f4e583ba20a01f4c44b0a4ada42c4d3bb074273/odoo/addons/base/views/res_partner_views.xml#L510>`_）。

#. 一个按钮 `Leads`，打开一个 `crm.lead` 模型的动态操作，包含列表和表单视图。遵循 `此处使用操作服务的示例
   <https://github.com/odoo/odoo/blob/ef424a9dc22a5abbe7b0a6eff61cf113826f04c0/addons/account
   /static/src/components/journal_dashboard_activity/journal_dashboard_activity.js#L28-L35>`_。

.. image:: 02_web_framework/navigation_buttons.png
   :align: center

.. 另请参阅::
   `代码：操作服务
   <{GITHUB_PATH}/addons/web/static/src/webclient/actions/action_service.js>`_

3. 添加仪表板项
=======================

现在让我们改善我们的内容。

#. 创建一个通用的 `DashboardItem` 组件，以优美的卡片布局显示其默认插槽。它应该接受一个可选的 `size` 数字 prop，默认为 `1`。宽度应硬编码为 `(18*size)rem`。
#. 向仪表板添加两个卡片。一个没有大小，另一个大小为 2。

.. image:: 02_web_framework/dashboard_item.png
   :align: center

.. 另请参阅::
   `Owl 的插槽系统 <{OWL_PATH}/doc/reference/slots.md>`_

4. 调用服务器，添加一些统计数据
=======================================

让我们通过添加一些仪表板项来显示 *真实* 的业务数据来改善仪表板。
`awesome_dashboard` 附加模块提供了一个 `/awesome_dashboard/statistics` 路由，旨在返回一些有趣的信息。

要调用特定的控制器，我们需要使用 :ref:`rpc 服务 <frontend/services/rpc>`。它只导出一个函数来执行请求： :code:`rpc(route, params, settings)`。
一个基本请求可能看起来像这样：

.. code-block:: js

   setup() {
         this.rpc = useService("rpc");
         onWillStart(async () => {
            const result = await this.rpc("/my/controller", {a: 1, b: 2});
            // ...
         });
   }

#. 更新 `Dashboard` 以便使用 `rpc` 服务。
#. 在 `onWillStart` 钩子中调用统计信息路由 `/awesome_dashboard/statistics`。
#. 在仪表板中显示几个卡片，包含：

   - 本月新订单数量
   - 本月新订单的总金额
   - 本月每个订单的平均 T 恤 数量
   - 本月取消的订单数量
   - 从“新”到“已发送”或“已取消”的订单平均时间

.. image:: 02_web_framework/statistics.png
   :align: center

.. 另请参阅::
   `代码：rpc 服务 <{GITHUB_PATH}/addons/web/static/src/core/network/rpc_service.js>`_

5. 缓存网络调用，创建服务
========================================

如果您打开浏览器开发工具的 :guilabel:`网络` 选项卡，您会看到对 `/awesome_dashboard/statistics` 的调用在每次显示客户端操作时都会进行。这是因为 `onWillStart` 钩子在每次 `Dashboard` 组件挂载时都会被调用。但是在这种情况下，我们希望它只在第一次进行，因此我们实际上需要在 `Dashboard` 组件外维护一些状态。这是服务的一个很好的用例！

#. 注册并导入一个新的 `awesome_dashboard.statistics` 服务。
#. 它应该提供一个函数 `loadStatistics`，在调用时执行实际的 rpc，并始终返回相同的信息。
#. 使用 `memoize <https://github.com/odoo/odoo/blob/1f4e583ba20a01f4c44b0a4ada42c4d3bb074273/
   addons/web/static/src/core/utils/functions.js#L11>`_ 工具函数，该函数允许缓存统计信息。
#. 在 `Dashboard` 组件中使用此服务。
#. 检查它是否按预期工作。

.. 另请参阅::
   - `示例：简单服务 <{GITHUB_PATH}/addons/web/static/src/core/network/http_service.js>`_
   - `示例：具有依赖关系的服务
     <{GITHUB_PATH}/addons/web/static/src/core/user_service.js>`_

6. 显示饼图
======================

每个人都喜欢图表（！），所以让我们在仪表板中添加一个饼图。它将显示每种尺寸的 T 恤 销售比例：S/M/L/XL/XXL。

在本次练习中，我们将使用 `Chart.js <https://www.chartjs.org/>`_。这是图形视图使用的图表库。但是，它默认不加载，因此我们需要将其添加到我们的资产包中，或延迟加载它。由于用户不需要时不必加载 chartjs 代码，因此延迟加载通常更好。

#. 创建一个 `PieChart` 组件。
#. 在其 `onWillStart` 方法中加载 chartjs，您可以使用 `loadJs
   <https://github.com/odoo/odoo/blob/1f4e583ba20a01f4c44b0a4ada42c4d3bb074273/
   addons/web/static/src/core/assets.js#L23>`_ 函数加载 :file:`/web/static/lib/Chart/Chart.js`。
#. 在一个 `DashboardItem` 中使用 `PieChart` 组件，显示一个饼图
   <https://www.chartjs.org/docs/2.8.0/charts/doughnut.html>`_，显示每种尺寸销售的 T 恤 数量（该信息可在 `/statistics` 路由中获得）。请注意，您可以使用 `size` 属性使其看起来更大。
#. `PieChart` 组件将需要渲染一个画布，并使用 `chart.js` 在其上绘制。
#. 使其正常工作！

.. image:: 02_web_framework/pie_chart.png
   :align: center
   :scale: 80%

.. 另请参阅::
   - `示例：懒加载 js 文件
     <https://github.com/odoo/odoo/blob/1f4e583ba20a01f4c44b0a4ada42c4d3bb074273/
     addons/web/static/src/views/graph/graph_renderer.js#L57>`_
   - `示例：在组件中渲染图表
     <https://github.com/odoo/odoo/blob/1f4e583ba20a01f4c44b0a4ada42c4d3bb074273/
     addons/web/static/src/views/graph/graph_renderer.js#L618>`_

7. 实时更新
===================

由于我们将数据加载移入缓存，因此它从未更新。但假设我们正在查看快速变化的数据，因此我们希望定期（例如每 10 分钟）重新加载新数据。

这很简单，只需在统计服务中使用 `setTimeout` 或 `setInterval` 即可实现。然而，这里有一个棘手的部分：如果仪表板当前正在显示，则应该立即更新。

为此，可以使用一个 `reactive` 对象：它就像 `useState` 返回的代理，但不链接到任何组件。然后，组件可以在其上执行 `useState` 来订阅其更改。

#. 更新统计服务以每 10 分钟重新加载数据（为测试目的，请使用 10 秒！）。
#. 修改它以返回一个 `reactive <{OWL_PATH}/doc/reference/reactivity.md#reactive>`_ 对象。重新加载数据应在原地更新反应对象。
#. `Dashboard` 组件现在可以使用它并使用 `useState`

.. 另请参阅::
  - `关于反应性的文档 <{OWL_PATH}/doc/reference/reactivity.md>`_
  - `示例：在服务中使用反应性
    <https://github.com/odoo/odoo/blob/1f4e583ba20a01f4c44b0a4ada42c4d3bb074273/
    addons/web/static/src/core/debug/profiling/profiling_service.js#L30>`_

8. 懒加载仪表板
=============================

假设我们的仪表板变得相当庞大，并且只对某些用户感兴趣。在这种情况下，懒加载我们的仪表板及所有相关资产可能是合理的，因此我们仅在实际想查看时支付加载代码的成本。

实现此目的的一种方法是使用 `LazyComponent`（来自 `@web/core/assets`）作为中间件，在显示我们的组件之前加载资产包。

.. 示例::

   :file:`example_action.js`:

   .. code-block:: javascript

      export class ExampleComponentLoader extends Component {
          static components = { LazyComponent };
          static template = xml`
              <LazyComponent bundle="'example_module.example_assets'" Component="'ExampleComponent'" />
          `;
      }

      registry.category("actions").add("example_module.example_action", ExampleComponentLoader);

#. 将所有仪表板资产移到子文件夹 :file:`/dashboard` 中，以便更容易添加到包中。
#. 创建一个 `awesome_dashboard.dashboard` 资产包，包含 :file:`/dashboard` 文件夹的所有内容。
#. 修改 :file:`dashboard.js` 以将其注册到 `lazy_components` 注册表中，而不是 `actions`。
#. 在 :file:`src/dashboard_action.js` 中，创建一个使用 `LazyComponent` 的中间组件并将其注册到 `actions` 注册表。

9. 让我们的仪表板通用
===============================

到目前为止，我们有一个不错的工作仪表板。但它目前在仪表板模板中是硬编码的。如果我们想自定义仪表板呢？也许某些用户有不同的需求，想查看其他数据。

因此，下一步是使我们的仪表板通用：它可以遍历仪表板项列表，而不是在模板中硬编码其内容。但是，接下来会出现许多问题：如何表示仪表板项，如何注册它，应该接收哪些数据，等等。有许多不同的方式来设计这样的系统，每种方式都有不同的权衡。

在本教程中，我们将说仪表板项是具有以下结构的对象：

.. code-block:: js

   const item = {
      id: "average_quantity",
      description: "平均 T 恤 数量",
      Component: StandardItem,
      // size 和 props 是可选的
      size: 3,
      props: (data) => ({
         title: "本月每个订单的平均 T 恤 数量",
         value: data.average_quantity
      }),
   };

`description` 值将在后面的练习中用于显示用户可以添加到其仪表板的项的名称。`size` 数字是可选的，仅描述将显示的仪表板项的大小。最后，`props` 函数是可选的。如果未给定，我们将简单地将 `statistics` 对象作为数据传递。但如果定义了它，则将用于计算组件的特定 props。

目标是用以下代码替换仪表板的内容：

.. code-block:: xml

   <t t-foreach="items" t-as="item" t-key="item.id">
      <DashboardItem size="item.size || 1">
         <t t-set="itemProp" t-value="item.props ? item.props(statistics) : {'data': statistics}"/>
         <t t-component="item.Component" t-props="itemProp" />
      </DashboardItem>
   </t>

请注意，上面的示例具有 Owl 的两个高级特性：动态组件和动态 props。

我们目前有两种类型的项组件：带有标题和数字的数字卡，以及带有某些标签和饼图的饼图卡。

#. 创建并实现两个组件：`NumberCard` 和 `PieChartCard`，并提供相应的 props。
#. 在 :file:`dashboard_items.js` 中创建并导出一个项列表，使用 `NumberCard`
   和 `PieChartCard` 重新创建我们当前的仪表板。
#. 在我们的 `Dashboard` 组件中导入该项列表，将其添加到组件中，并更新模板以使用上述 `t-foreach`。

   .. code-block:: js

         setup() {
            this.items = items;
         }

现在，我们的仪表板模板是通用的！

10. 让我们的仪表板可扩展
===============================

然而，我们的项列表的内容仍然是硬编码的。让我们通过使用注册表来修复它：

#. 不再导出列表，而是注册所有仪表板项到 `awesome_dashboard` 注册表中。
#. 在 `Dashboard` 组件中导入 `awesome_dashboard` 注册表中的所有项。

仪表板现在可以轻松扩展。任何希望向仪表板注册新项的 Odoo 附加模块只需将其添加到注册表中。

11. 添加和删除仪表板项
==========================

让我们看看如何使我们的仪表板可定制。为了简单起见，我们将用户仪表板配置保存在本地存储中，以便持久化，但现在我们不必处理服务器。

仪表板配置将保存为已删除项 ID 的列表。

#. 在控制面板中添加一个带有齿轮图标的按钮，以表示这是一个设置按钮。
#. 单击该按钮应打开一个对话框。
#. 在该对话框中，我们希望看到所有现有仪表板项的列表，每个项都有一个复选框。
#. 在底部应该有一个 `应用` 按钮。单击它将构建一个未选中项 ID 的列表。
#. 我们希望将该值存储在本地存储中。
#. 并修改 `Dashboard` 组件，以通过从配置中移除项 ID 来过滤当前项。

.. image:: 02_web_framework/items_configuration.png
   :width: 80%
   :align: center

12. 更进一步
=================

以下是一些您可以尝试进行的小改进的列表：

#. 确保您的应用程序可以 :ref:`翻译 <reference/translations>`（使用 `env._t`）。
#. 单击饼图的一个部分应打开一个包含所有具有相应大小的订单的列表视图。
#. 将仪表板的内容保存在服务器上的用户设置中！
#. 使其响应式：在移动模式下，每个卡片应占据 100% 的宽度。

.. 另请参阅::
   - `示例：使用 env._t 函数
     <https://github.com/odoo/odoo/blob/1f4e583ba20a01f4c44b0a4ada42c4d3bb074273/
     addons/account/static/src/components/bills_upload/bills_upload.js#L64>`_
   - `代码：web/中的翻译代码
     <https://github.com/odoo/odoo/blob/1f4e583ba20a01f4c44b0a4ada42c4d3bb074273/
     addons/web/static/src/core/l10n/translation.js#L16>`_
