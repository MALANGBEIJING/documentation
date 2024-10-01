.. _frontend/registries:

==========
Registries
==========

Registries 是有序的键/值映射表。它们是 Odoo JavaScript 框架的主要扩展点：框架的许多功能只需在需要定义某个对象（如字段、视图、客户端动作或服务）时查找相应的注册表。自定义 Web 客户端的方式就是在正确的注册表中添加特定值。

.. code-block:: javascript

   import { Registry } from "@web/core/registry";

   const myRegistry = new Registry();

   myRegistry.add("hello", "odoo");

   console.log(myRegistry.get("hello"));

注册表的一个有用功能是它们维护了一组子注册表，通过 `category` 方法获取。如果子注册表尚不存在，则会动态创建。Web 客户端使用的所有注册表都是通过这种方式从一个根注册表（在 `@web/core/registry` 中导出）获取的。

.. code-block:: javascript

   import { registry } from "@web/core/registry";

   const fieldRegistry = registry.category("fields");
   const serviceRegistry = registry.category("services");
   const viewRegistry = registry.category("views");

Registry API
============

.. js:class:: Registry()

    创建一个新的注册表。请注意，注册表是一个事件总线，因此可以根据需要监听 `UPDATE` 事件。注册表是有序的：:js:meth:`getAll <Registry.getAll>` 方法返回按序列号排序的值列表。

    .. js:method:: add(key, value[, options])

        :param string key: 新条目的键
        :param any value: 新条目的值
        :param Object options: 选项
        :param boolean [options.force]: 如果键已存在，是否强制添加
        :param number [options.sequence]: 序列号（用于排序条目）
        :returns: Registry

        在指定键处插入一个值。如果该键已经被使用，此方法将抛出错误（除非选项 `force` 设置为 true）。`sequence` 选项用于在特定位置插入值。此方法还会触发 `UPDATE` 事件。

        返回同一注册表，因此 `add` 方法调用可以被链式调用。

    .. js:method:: get(key[, defaultValue])

        :param string key: 条目的键
        :param defaultValue any: 如果不存在该键，返回的默认值

        返回与 `key` 参数对应的值。如果注册表中没有该键，则此方法返回 `defaultValue`（如果提供），否则抛出错误。

    .. js:method:: contains(key)

        :param string key: 条目的键
        :returns: boolean

        如果 `key` 存在于注册表中，则返回 `true`。

    .. js:method:: getAll()

        :returns: any[]

        返回注册表中所有元素的列表。根据序列号排序。

    .. js:method:: remove(key)

        :param string key: 要移除的条目键

        从注册表中删除键/值对。此操作触发 `UPDATE` 事件。

    .. js:method:: category(subcategory)

        :param string subcategory: 子类别的名称
        :returns: Registry

        返回与 `subcategory` 关联的子注册表。如果尚不存在，则会动态创建子注册表。

参考列表
========

.. list-table::
   :widths: 30 70
   :header-rows: 1

   * - 类别
     - 内容
   * - :ref:`effects <frontend/registries/effects>`
     - 所有可用效果的实现
   * - :ref:`formatters <frontend/registries/formatters>`
     - 用于格式化值的实用函数（主要用于字段值）
   * - :ref:`main_components <frontend/registries/main_components>`
     - 顶级组件
   * - :ref:`parsers <frontend/registries/parsers>`
     - 用于解析值的实用函数（主要用于字段值）
   * - :ref:`services <frontend/registries/services>`
     - 应该激活的所有服务
   * - :ref:`systray <frontend/registries/systray>`
     - 在导航栏中 systray 区域中显示的组件
   * - :ref:`user_menuitems <frontend/registries/usermenu>`
     - 用户菜单中显示的菜单项（导航栏右上角）

.. _frontend/registries/effects:

效果注册表
----------

`effects` 注册表包含所有可用效果的实现。更多详细信息，请参阅 :ref:`效果服务 <frontend/services/effect_registry>` 部分。

.. _frontend/registries/formatters:

格式化程序注册表
--------------

`formatters` 注册表包含用于格式化值的函数。每个格式化程序都有以下 API：

.. js:function:: format(value[, options])

    :param value: 一个特定类型的值，如果未提供值，则为 `false`
    :type value: T | false
    :param Object options: 各种选项
    :returns: string

    格式化值并返回字符串

.. seealso::
    - :ref:`解析器注册表 <frontend/registries/parsers>`

.. _frontend/registries/main_components:

主组件注册表
------------

`main_components` 注册表用于添加 Web 客户端中的顶级组件。Web 客户端有一个 `MainComponentsContainer` 作为直接子级。该组件基本上是主组件注册表中按顺序排列的组件的实时表示。

API
    .. code-block:: text

        interface {
            Component: Owl 组件类
            props?: any
        }

例如，可以像这样将 `LoadingIndicator` 组件添加到注册表中：

.. code-block:: javascript

   registry.category("main_components").add("LoadingIndicator", {
     Component: LoadingIndicator,
   });

.. _frontend/registries/parsers:

解析器注册表
----------

`parsers` 注册表包含用于解析值的函数。每个解析器都有以下 API：

.. js:function:: parse(value[, options])
    :noindex:

    :param value: 表示值的字符串
    :type value: string
    :param Object options: 各种选项（解析器特定）
    :returns: T 一个有效值

    解析字符串并返回值。如果字符串不代表有效值，解析器可能会失败并抛出错误。

.. seealso::
    - :ref:`格式化程序注册表 <frontend/registries/formatters>`

.. _frontend/registries/services:

服务注册表
----------

`services` 注册表包含 Odoo 框架中应激活的所有 :ref:`服务 <frontend/services>`。

.. code-block:: javascript

    import { registry } from "@web/core/registry";

    const myService = {
        dependencies: [...],
        start(env, deps) {
            // 一些代码
        }
    };

    registry.category("services").add("myService", myService);

.. _frontend/registries/systray:

Systray 注册表
--------------

Systray 是导航栏右侧的区域，包含各种小组件，通常显示一些信息（如未读消息数）、通知和/或让用户与它们交互。

`systray` 注册表包含这些 systray 项的描述，作为包含以下三个键的对象：

- `Component`: 代表该项目的组件类。其根元素应为 `<li>` 标签，否则样式可能不正确。
- `props (可选)`: 应该传递给组件的属性
- `isDisplayed (可选)`: 一个函数，接受 :ref:`env <frontend/framework/environment>` 并返回布尔值。如果为 true，则显示 systray 项目。否则，它将被移除。

例如：

.. code-block:: javascript

    import { registry } from "@web/core/registry";

    class MySystrayItem extends Component {
        // 一些组件代码...
    }

    registry.category("systray").add("myAddon.myItem", {
        Component: MySystrayItem,
    });

Systray 注册表是有序的（带有 `sequence` 编号）：

.. code-block:: javascript

    const item = {
        Component: MySystrayItem
    };
    registry.category("systray").add("myaddon.some_description", item, { sequence: 43 });

序列号默认为 50。如果给定，该编号将用于对项目进行排序。序列号最小的项目在 systray 菜单的右侧，最大的项目在左侧。

.. _frontend/registries/usermenu:

用户菜单注册表
--------------

用户菜单注册表（类别：`user_menuitems`）包含在打开用户菜单时显示的所有菜单项（导航栏右上角的用户姓名元素）。

用户菜单项由一个函数定义，该函数接受 :ref:`env <frontend/framework/environment>` 并返回一个普通对象，包含以下信息：

* `description` : 菜单项文本,
* `href` : (可选) 如果提供并且为真，则项目文本将放入带有给定 `href` 属性的 `a` 标签中,
* `callback` : 选择该项目时调用的回调函数,
* `hide`: (可选) 表示该项目是否应隐藏（默认值：`false`）,
* `sequence`: (可选) 确定该项目在其他下拉菜单项中的顺序（默认值：100）。

用户菜单在每次打开时调用定义项目的所有函数。

示例：

.. code-block:: javascript

    import { registry } from "@web/core/registry";

    registry.category("user_menuitems").add("my item", (env) => {
        return {
            description: env._t("Technical Settings"),
            callback: () => { env.services.action_manager.doAction(3); },
            hide: (Math.random() < 0.5),
        };
    });
