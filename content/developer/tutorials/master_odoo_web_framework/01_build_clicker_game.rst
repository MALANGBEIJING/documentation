构建点击游戏
===============================

在本项目中，我们将一起构建一个 `点击游戏 <https://en.wikipedia.org/wiki/Incremental_game>`_，
与 Odoo 完全集成。在这个游戏中，目标是积累大量点击，并自动化系统。有趣的是，我们将使用 Odoo 用户界面作为我们的游乐场。例如，我们将在网页客户端的某些随机位置隐藏奖金。

要开始，您需要一个正在运行的 Odoo 服务器和一个开发环境。在进行练习之前，请确保您遵循了
:ref:`教程介绍 <tutorials/master_odoo_web_framework/setup>` 中描述的所有步骤。

.. admonition:: 目标

   .. image:: 01_build_clicker_game/final.png
      :align: center

.. spoiler:: 解决方案

   本章每个练习的解决方案托管在
   `官方 Odoo 教程库
   <https://github.com/odoo/tutorials/commits/{CURRENT_MAJOR_BRANCH}-master-odoo-web-framework-solutions/awesome_clicker>`_。


1. 创建一个系统托盘项目
========================

要开始，我们希望在系统托盘中显示一个计数器。

#. 创建一个 `clicker_systray_item.js`（和 `xml`）文件，包含一个 Hello World Owl 组件。
#. 将其注册到系统托盘注册表，并确保其可见。
#. 更新项目的内容，使其显示以下字符串：`点击次数：0`，并在右侧添加一个按钮以递增该值。

.. image:: 01_build_clicker_game/systray.png
   :align: center

就这样，我们有了一个完全运行的点击游戏！

.. seealso::

   - :ref:`系统托盘注册表的文档 <frontend/registries/systray>`
   - `示例：将系统托盘项目添加到注册表
     <https://github.com/odoo/odoo/blob/c4fb9c92d7826ddbc183d38b867ca4446b2fb709/addons/web/static/src/webclient/user_menu/user_menu.js#L41-L42>`_

2. 计算外部点击
========================

老实说，现在还没有太多乐趣。所以让我们添加一个新功能：我们希望用户界面中的所有点击都计入，以便激励用户尽可能多地使用 Odoo！但显然，主要计数器上的故意点击应该算得更多。

#. 使用 `useExternalListener` 监听 `document.body` 上的所有点击。
#. 每个点击都应该将计数器值增加 1。
#. 修改代码，使得每次点击计数器时增加 10
#. 确保点击计数器不会增加 11！
#. 另外，额外的挑战：确保外部监听器捕获事件，以便我们不会错过任何点击。

.. seealso::

   - `Owl 文档中的 useExternalListener <https://github.com/odoo/owl/blob/master/doc/reference/hooks.md#useexternallistener>`_
   - `MDN 关于事件捕获的页面 <https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Building_blocks/Events#event_capture>`_

3. 创建客户端操作
=========================

目前，当前用户界面相当小：它只是一个系统托盘项目。我们当然需要更多的空间来显示我们的游戏。为此，让我们创建一个客户端操作。客户端操作是由 web 客户端管理的主要操作，用于显示组件。

#. 创建一个 `client_action.js`（和 `xml`）文件，包含一个 Hello World 组件。
#. 在操作注册表中注册该客户端操作，名称为 `awesome_clicker.client_action`
#. 在系统托盘项目上添加一个文本为 `打开` 的按钮。点击它应该打开客户端操作 `awesome_clicker.client_action`（使用操作服务来做到这一点）。
#. 为了避免干扰员工的工作流程，我们更喜欢客户端操作在弹出窗口中打开，而不是全屏模式。修改 `doAction` 调用以在弹出窗口中打开。

   .. tip::

      您可以在 `doAction` 中使用 `target: "new"` 来在弹出窗口中打开操作：

      .. code-block:: js

         {
            type: "ir.actions.client",
            tag: "awesome_clicker.client_action",
            target: "new",
            name: "Clicker"
         }

.. image:: 01_build_clicker_game/client_action.png
   :align: center

.. seealso::

   - :ref:`如何创建客户端操作 <howtos/javascript_client_action>`

4. 将状态移动到服务
==============================

目前，我们的客户端操作只是一个 Hello World 组件。我们希望它显示我们的游戏状态，但该状态目前仅在系统托盘项目中可用。因此，这意味着我们需要更改状态的位置，以使其可用于我们所有的组件。这是服务的完美用例。

#. 创建一个 `clicker_service.js` 文件，包含相应的服务。
#. 该服务应导出一个反应值（点击次数）和一些更新它的函数：

   .. code-block:: js

         const state = reactive({ clicks: 0 });
         ...
         return {
            state,
            increment(inc) {
               state.clicks += inc
            }
         };

#. 在系统托盘项目和客户端操作中访问状态（别忘了 `useState` 它）。修改系统托盘项目以删除其自己的本地状态并使用它。此外，您可以删除 `+10 clicks` 按钮。
#. 在客户端操作中显示状态，并在其中添加一个 `+10` 点击的按钮。

.. image:: 01_build_clicker_game/increment_button.png
   :align: center

.. seealso::

   - :ref:`关于服务的简短解释 <tutorials/discover_js_framework/services>`

5. 使用自定义钩子
====================

现在，每个需要使用我们的点击服务的代码部分都必须导入 `useService` 和 `useState`。由于这相当常见，让我们使用一个自定义钩子。将点击部分放在更显著的位置，而不是服务部分。

#. 导出一个 `useClicker` 钩子。
#. 更新所有当前使用点击服务的代码以使用新钩子：

   .. code-block:: js

      this.clicker = useClicker();

.. seealso::

   - `关于钩子的文档： <https://github.com/odoo/owl/blob/master/doc/reference/hooks.md>`_

6. 使显示值更易读
===============================

我们将来会显示较大的数字，因此让我们为此做好准备。可以使用 `humanNumber` 函数以更易于理解的方式格式化数字：例如，`1234` 可以格式化为 `1.2k`

#. 使用它来显示我们的计数器（在系统托盘项目和客户端操作中）。
#. 创建一个 `ClickValue` 组件来显示值。

   .. note::

      Owl 允许只包含文本节点的组件！

.. image:: 01_build_clicker_game/humanized_number.png
   :align: center

.. seealso::

   - `humanNumber 函数的定义 <https://github.com/odoo/odoo/blob/c638913df191dfcc5547f90b8b899e7738c386f1/addons/web/static/src/core/utils/numbers.js#L119>`_

7. 在 `ClickValue` 组件中添加工具提示
==========================================

使用 `humanNumber` 函数，我们实际上在界面上失去了一些精度。让我们作为工具提示显示真实数字。

#. 工具提示需要一个 HTML 元素。将 `ClickValue` 更改为将值包装在 `<span/>` 元素中。
#. 添加动态 `data-tooltip` 属性以显示确切值。

.. image:: 01_build_clicker_game/humanized_tooltip.png
   :align: center

.. seealso::

   - `工具提示服务的文档 <https://github.com/odoo/odoo/blob/c638913df191dfcc5547f90b8b899e7738c386f1/addons/web/static/src/core/tooltip/tooltip_service.js#L17>`_

8. 购买 ClickBots
================

让我们让游戏更有趣：一旦玩家第一次获得 1000 次点击，游戏应该解锁一个新功能：玩家可以用 1000 次点击购买机器人。这些机器人每 10 秒会生成 10 次点击。

#. 在我们的状态中添加一个 `level` 数字。这个数字将在某些里程碑时增加，并解锁新功能。
#. 在我们的状态中添加一个 `clickBots` 数字。它表示已购买的机器人数量。
#. 修改客户端操作以显示点击机器人数量（仅在 `level >= 1` 时），并添加一个按钮 `购买`，该按钮在 `clicks >= 1000` 时可用。`购买`按钮应该将点击机器人的数量增加 1。
#. 在服务中设置一个 10 秒的间隔，每 10 秒将点击次数增加 `10 * clickBots`。
#. 确保当玩家没有足够的点击时，购买按钮被禁用。

.. image:: 01_build_clicker_game/clickbot.png
   :align: center

9. 重构为类模型
============================

当前代码以某种函数式风格编写。但为此，我们必须在点击对象中导出状态及其所有更新函数。随着项目的增长，这可能会变得越来越复杂。为了简化，让我们将业务逻辑从服务中分离出来，放入一个类中。

#. 创建一个 `clicker_model` 文件，导出一个反应类。将所有状态和更新函数从服务移到模型中。

   .. tip::

      您可以使用 :file:`@web/core/utils/reactive` 中的 `Reactive` 类扩展 ClickerModel。`Reactive` 类将模型包装到一个响应代理中。

#. 重写点击服务以实例化和导出点击模型类。

.. seealso::

   - `子类化 Reactive 的示例 <https://github.com/odoo/odoo/blob/c638913df191dfcc5547f90b8b899e7738c386f1/addons/web/static/src/model/relational_model/datapoint.js#L32>`_

10. 达到里程碑时通知
======================================

当我们达到 1000 次点击时，几乎没有反馈说明发生了变化。让我们使用 `effect` 服务清晰地传达该信息。问题是我们的点击模型无法访问服务。此外，我们希望尽可能将 UI 问题与模型分开。因此，我们可以探索一种新的通信策略：事件总线。

#. 更新点击模型以实例化一个总线，并在我们第一次达到 1000 次点击时触发 `MILESTONE_1k` 事件。
#. 更改点击服务以侦听模型总线上的同一事件。
#. 当发生这种情况时，使用 `effect` 服务显示彩虹人。
#. 添加一些文本以解释用户现在可以购买点击机器人。

.. image:: 01_build_clicker_game/milestone.png
   :align: center

.. seealso::

   - `Owl 文档中的事件总线 <https://github.com/odoo/owl/blob/master/doc/reference/utils.md#eventbus>`_
   - :ref:`效果服务的文档 <frontend/services/effect>`

11. 添加 BigBots
===============

显然，我们需要一种方法为玩家提供更多选择。让我们添加一种新的点击机器人类型：`BigBots`，它们更强大：每 10 秒提供 100 次点击，但它们的成本是 5000 次点击。

#. 当它达到 5000 时增加 `level`（所以应该是 2）。
#. 更新状态以跟踪 bigbots。
#. 当 `level >= 2` 时应提供 bigbots。
#. 在客户端操作中显示相应的信息。

.. tip::

   如果您需要在模板中使用 `<` 或 `>` 作为 JavaScript 表达式，请小心，因为它可能与 XML 解析器冲突。为了解决这个问题，您可以使用其中一个特殊别名：`gt, gte, lt` 或 `lte`。请参阅 `Owl 文档页面上的模板表达式 <https://github.com/odoo/owl/blob/master/doc/reference/templates.md#expression-evaluation>`_。

.. image:: 01_build_clicker_game/bigbot.png
   :align: center

12. 添加一种新类型的资源：能量
=====================================

现在，为了增加一个新的扩展点，让我们添加一种新类型的资源：能量乘数。这是一个可以在 `level >= 3` 时增加的数字，并且会乘以机器人的操作（因此，点击机器人现在提供 `乘数` 次点击）。

#. 当它达到 100000 时增加 `level`（所以应该是 3）。
#. 更新状态以跟踪能量（初始值为 1）。
#. 让机器人使用该数字作为乘数。
#. 更新用户界面以显示并让用户购买新的能量级别（成本：50000）。

.. image:: 01_build_clicker_game/bigbot.png
   :align: center

13. 定义一些随机奖励
==============================

我们希望用户偶尔获得一些奖金，以奖励使用 Odoo。

#. 在 `click_rewards.js` 中定义一个奖励列表。奖励是一个包含以下内容的对象：
   - 一个 `description` 字符串。
   - 一个 `apply` 函数，它接受游戏状态作为参数并可以修改它。
   - 一个 `minLevel` 数字（可选），描述该奖金可用的解锁级别。
   - 一个 `maxLevel` 数字（可选），描述该奖金不再可用的解锁级别。

   例如：

   .. code-block:: js

      export const rewards = [
         {
            description: "获得 1 个点击机器人",
            apply(clicker) {
                  clicker.increment(1);
            },
            maxLevel: 3,
         },
         {
            description: "获得 10 个点击机器人",
            apply(clicker) {
                  clicker.increment(10);
            },
            minLevel: 3,
            maxLevel: 4,
         },
         {
            description: "增加机器人能量！",
            apply(clicker) {
                  clicker.multipler += 1;
            },
            minLevel: 3,
         },
      ];

   您可以随意向该列表添加内容！

#. 定义一个 `getReward` 函数，从当前解锁级别的奖励列表中随机选择一个奖励。
#. 将选择随机数组的代码提取到一个 `choose` 函数中，您可以将其移到另一个 `utils.js` 文件中。

14. 打开表单视图时提供奖励
=============================================

#. 修补表单控制器。每次创建表单控制器时，它应该随机决定（1% 的机会）是否应该给予奖励。
#. 如果答案是肯定的，请在模型上调用 `getReward` 方法。
#. 该方法应该选择一个奖励，发送一个粘性通知，附带一个 `收集` 按钮，然后应用奖励，最后打开 `clicker` 客户端操作。

.. image:: 01_build_clicker_game/reward.png
   :align: center

.. seealso::

   - :ref:`关于修补类的文档 <frontend/patching_class>`
   - `修补函数的定义 <https://github.com/odoo/odoo/blob/c638913df191dfcc5547f90b8b899e7738c386f1/addons/web/static/src/core/utils/patch.js#L71>`_
   - `修补类的示例 <https://github.com/odoo/odoo/blob/c638913df191dfcc5547f90b8b899e7738c386f1/addons/pos_mercury/static/src/app/screens/receipt_screen/receipt_screen.js#L6>`_

15. 在命令面板中添加命令
===================================

#. 向命令面板添加命令 `打开点击游戏`。
#. 添加另一个命令：`购买 1 个点击机器人`。

.. image:: 01_build_clicker_game/command_palette.png
   :align: center

.. seealso::

   - `命令提供者注册表的使用示例 <https://github.com/odoo/odoo/blob/c638913df191dfcc5547f90b8b899e7738c386f1/addons/web/static/src/core/debug/debug_providers.js#L10>`_

16. 添加另一种资源：树
===================================

现在是引入一种全新类型的资源的时候了。这里有一个不应该太有争议的类型：树。
我们现在允许用户种植（收集？）果树。一棵树的成本是 100 万点击，但它将为我们提供
水果（梨或樱桃）。

#. 更新状态以跟踪各种类型的树：梨/樱桃及其水果。
#. 添加一个计算树和水果总数的函数。
#. 定义一个新的解锁级别，点击次数 >= 1000000。
#. 更新客户端用户界面以显示树和水果的数量，并允许购买树。
#. 每 30 秒将水果数量增加 1。

.. image:: 01_build_clicker_game/trees.png
   :align: center

17. 在系统托盘项目中使用下拉菜单
============================================

我们的游戏开始变得有趣。但目前，系统托盘只显示总点击次数。我们希望看到更多信息：树和水果的总数。此外，快速访问一些命令和更多信息也很有用。让我们使用下拉菜单！

#. 用下拉菜单替换系统托盘项目。
#. 它应该显示点击次数、树木和水果的数量，每个数量都有一个漂亮的图标。
#. 点击它应该打开一个下拉菜单，显示更详细的信息：每种类型的树和水果。
#. 此外，几项下拉项目包括一些命令：打开点击游戏，购买点击机器人，...

.. image:: 01_build_clicker_game/dropdown.png
   :align: center

18. 使用 Notebook 组件
============================

现在我们跟踪的信息更多了。让我们通过在不同选项卡中组织信息和功能来改善我们的客户端界面，使用 `Notebook` 组件：

#. 使用 `Notebook` 组件。
#. 所有 `click` 内容应显示在一个选项卡中。
#. 所有 `tree/fruits` 内容应显示在另一个选项卡中。

.. image:: 01_build_clicker_game/notebook.png
   :align: center
