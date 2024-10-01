.. _frontend/components:

==============
Owl 组件
==============

Odoo 的 Javascript 框架使用一个自定义的组件框架，称为 Owl。它是一个声明式组件系统，灵感来源于 Vue 和 React。组件通过 :doc:`QWeb 模板 <qweb>` 定义，并结合一些 Owl 特定的指令。官方的
`Owl 文档 <https://github.com/odoo/owl/blob/master/doc/readme.md>`_ 包含完整的参考和教程。

.. important::

   虽然代码可以在 `web` 模块中找到，但它是从一个独立的 GitHub 仓库进行维护的。因此，任何对 Owl 的修改都应通过 https://github.com/odoo/owl 的 pull request 来完成。

.. note::
   目前，所有 Odoo 版本（从版本 14 开始）共享相同的 Owl 版本。

使用 Owl 组件
====================

`Owl 文档`_ 已经详细介绍了 Owl 框架，因此此页面仅提供 Odoo 的特定信息。首先，让我们看看如何在 Odoo 中创建一个简单的组件。

.. code-block:: javascript

    const { useState } = owl.hooks;
    const { xml } = owl.tags;

    class MyComponent extends Component {
        setup() {
            this.state = useState({ value: 1 });
        }

        increment() {
            this.state.value++;
        }
    }
    MyComponent.template = xml
        `<div t-on-click="increment">
            <t t-esc="state.value">
        </div>`;

此示例表明 Owl 作为库在全局命名空间中可用，命名为 `owl`：它可以像 Odoo 中的大多数库一样简单地使用。请注意，这里我们将模板定义为静态属性，但没有使用 `static` 关键字，因为某些浏览器不支持该关键字（Odoo 的 JavaScript 代码应符合 Ecmascript 2019 标准）。

我们在此示例中在 JavaScript 代码中定义了模板，借助 `xml` 帮助器。然而，这仅在初期有用。实际上，Odoo 中的模板应该定义在 XML 文件中，以便可以进行翻译。在这种情况下，组件只需定义模板名称。

实际上，大多数组件应定义 2 或 3 个文件，位于相同位置：一个 JavaScript 文件 (`my_component.js`)，一个模板文件 (`my_component.xml`)，以及可选的一个 SCSS（或 CSS）文件 (`my_component.scss`)。这些文件应添加到某个资产包中。Web 框架将负责加载 JavaScript/CSS 文件，并将模板加载到 Owl 中。

以下是如何定义上述组件的：

.. code-block:: javascript

    const { useState } = owl.hooks;

    class MyComponent extends Component {
        ...
    }
    MyComponent.template = 'myaddon.MyComponent';

模板现在位于相应的 XML 文件中：

.. code-block:: xml

    <?xml version="1.0" encoding="UTF-8" ?>
    <templates xml:space="preserve">

    <t t-name="myaddon.MyComponent">
      <div t-on-click="increment">
        <t t-esc="state.value"/>
      </div>
    </t>

    </templates>

.. note::

   模板名称应遵循 `addon_name.ComponentName` 的约定。

.. seealso::
    - `Owl 仓库 <https://github.com/odoo/owl>`_

.. _frontend/owl/best_practices:

最佳实践
==============

首先，组件是类，因此它们有构造函数。但构造函数是 JavaScript 中的特殊方法，无法以任何方式被覆盖。由于这是 Odoo 中偶尔有用的模式，因此我们需要确保 Odoo 中的任何组件都不直接使用构造函数。相反，组件应该使用 `setup` 方法：

.. code-block:: javascript

    // 正确的写法:
    class MyComponent extends Component {
        setup() {
            // 在这里初始化组件
        }
    }

    // 错误的写法，请不要这样做！
    class IncorrectComponent extends Component {
        constructor(parent, props) {
            // 在这里初始化组件
        }
    }

另一个好的做法是使用一致的模板命名约定：`addon_name.ComponentName`。这样可以防止 Odoo 插件之间的命名冲突。

参考列表
==============

Odoo Web 客户端是使用 `Owl <https://github.com/odoo/owl>`_ 组件构建的。
为了方便使用，Odoo JavaScript 框架提供了一套通用组件，可以在一些常见场景中重用，例如下拉菜单、复选框或日期选择器。本页面解释了如何使用这些通用组件。

.. list-table::
   :widths: 30 70
   :header-rows: 1

   * - 技术名称
     - 简短描述
   * - :ref:`ActionSwiper <frontend/owl/actionswiper>`
     - 用于触摸滑动执行操作的滑动组件
   * - :ref:`CheckBox <frontend/owl/checkbox>`
     - 带标签的简单复选框组件
   * - :ref:`ColorList <frontend/owl/colorlist>`
     - 颜色选择列表
   * - :ref:`Dropdown <frontend/owl/dropdown>`
     - 全功能下拉菜单
   * - :ref:`Notebook <frontend/owl/notebook>`
     - 使用选项卡导航页面的组件
   * - :ref:`Pager <frontend/pager>`
     - 小型分页组件
   * - :ref:`SelectMenu <frontend/select_menu>`
     - 用于选择不同选项的下拉菜单组件
   * - :ref:`TagsList <frontend/tags_list>`
     - 显示为圆形标签的标签列表

.. _frontend/owl/actionswiper:

ActionSwiper
------------

位置
~~~~~~~~

`@web/core/action_swiper/action_swiper`

描述
~~~~~~~~~~~

这是一个组件，当元素水平滑动时执行动作。Swiper 包裹目标元素以向其添加操作。一旦用户在滑动超过其宽度的一部分后释放滑动器，动作便会执行。

.. code-block:: xml

  <ActionSwiper onLeftSwipe="Object" onRightSwipe="Object">
    <SomeElement/>
  </ActionSwiper>

使用该组件的最简单方法是直接在 XML 模板中围绕目标元素使用它，如上所示。但是有时，你可能希望扩展现有元素，而不希望重复模板。这是可以做到的。

如果你想扩展现有元素的行为，你必须将该元素放入组件中，直接包裹它。此外，你可以有条件地添加属性，以管理元素何时可滑动、其动画以及执行操作所需滑动的最小部分。

你可以使用该组件轻松与记录、消息、列表中的项目等进行交互。

.. image:: owl_components/actionswiper.png
  :width: 400 px
  :alt: 使用 ActionSwiper 的示例
  :align: center

以下示例创建了一个基本的 ActionSwiper 组件。
在此，左右滑动均已启用。

.. code-block:: xml

  <ActionSwiper
    onRightSwipe="
      {
        action: '() => Delete item',
        icon: 'fa-delete',
        bgColor: 'bg-danger',
      }"
    onLeftSwipe="
      {
        action: '() => Star item',
        icon: 'fa-star',
        bgColor: 'bg-warning',
      }"
  >
    <div>
      可滑动项目
    </div>
  </ActionSwiper>

.. note:: 使用从右到左（RTL）语言时，操作会互换。

属性
~~~~~

.. list-table::
    :widths: 20 20 60
    :header-rows: 1

    * - 名称
      - 类型
      - 描述
    * - `animationOnMove`
      - `Boolean`
      - 可选布尔值，确定滑动过程中是否存在平移效果
    * - `animationType`
      - `String`
      - 可选动画，用于滑动结束后 (`bounce` 或 `forwards`)
    * - `onLeftSwipe`
      - `Object`
      - 如果存在，actionswiper 可以向左滑动
    * - `onRightSwipe`
      - `Object`
      - 如果存在，actionswiper 可以向右滑动
    * - `swipeDistanceRatio`
      - `Number`
      - 可选的最小宽度比例，必须滑动才能执行操作

你可以同时使用 `onLeftSwipe` 和 `onRightSwipe` 属性。

用于左右滑动的 `Object` 必须包含：

    - `action`，这是可调用的 `Function`，作为回调。一旦滑动在给定方向完成，便执行该操作。
    - `icon` 是使用的图标类，通常用于表示该操作。它必须是一个 `string`。
    - `bgColor` 是背景颜色，用于装饰操作。可以是以下之一 `bootstrap contextual color
      <https://getbootstrap.com/docs/3.3/components/#available-variations>`_ (`danger`、`info`、`secondary`、`success` 或 `warning`)。

    这些值必须定义以确定滑动器的行为和视觉效果。

示例：扩展现有组件
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

在以下示例中，你可以使用 `xpath` 将现有元素包装在 ActionSwiper 组件中。这里，在邮件中添加了一个滑动器，用于标记消息为已读。

.. code-block:: xml

  <xpath expr="//*[hasclass('o_Message')]" position="after">
    <ActionSwiper
      onRightSwipe="messaging.device.isMobile and messageView.message.isNeedaction ?
        {
          action: () => messageView.message.markAsRead(),
          icon: 'fa-check-circle',
          bgColor: 'bg-success',
        } : undefined"
    />
  </xpath>
  <xpath expr="//ActionSwiper" position="inside">
    <xpath expr="//*[hasclass('o_Message')]" position="move"/>
  </xpath>

.. _frontend/owl/checkbox:

CheckBox
--------

位置
~~~~~~~~

`@web/core/checkbox/checkbox`

描述
~~~~~~~~~~~

这是一个简单的复选框组件，旁边带有一个标签。复选框与标签链接：每当单击标签时，复选框会切换状态。

.. code-block:: xml

  <CheckBox value="boolean" disabled="boolean" t-on-change="onValueChange">
    一些文本
  </CheckBox>

属性
~~~~~

.. list-table::
    :widths: 20 20 60
    :header-rows: 1

    * - 名称
      - 类型
      - 描述
    * - `value`
      - `boolean`
      - 如果为 true，则复选框被选中，否则未选中
    * - `disabled`
      - `boolean`
      - 如果为 true，则复选框禁用，否则启用

.. _frontend/owl/colorlist:

ColorList
---------

位置
~~~~~~~~

`@web/core/colorlist/colorlist`

描述
~~~~~~~~~~~

ColorList 允许你从预定义的列表中选择颜色。默认情况下，组件显示当前选择的颜色，直到出现 `canToggle` 属性，该属性允许展开列表。不同的属性可以改变其行为，例如总是展开列表，或者在点击时将其作为切换器，一直到选择颜色为止。

属性
~~~~~

.. list-table::
    :widths: 20 20 60
    :header-rows: 1

    * - 名称
      - 类型
      - 描述
    * - `canToggle`
      - `boolean`
      - 可选，是否可以通过点击展开颜色列表
    * - `colors`
      - `array`
      - 显示在组件中的颜色列表。每个颜色都有唯一的 `id`
    * - `forceExpanded`
      - `boolean`
      - 可选。如果为 true，列表始终处于展开状态
    * - `isExpanded`
      - `boolean`
      - 可选。如果为 true，列表默认展开
    * - `onColorSelected`
      - `function`
      - 选择颜色后执行的回调
    * - `selectedColor`
      - `number`
      - 可选。已选择的颜色 `id`

颜色 `id` 如下：

.. list-table::
    :header-rows: 1

    * - Id
      - 颜色
    * - `0`
      - `无颜色`
    * - `1`
      - `红色`
    * - `2`
      - `橙色`
    * - `3`
      - `黄色`
    * - `4`
      - `浅蓝色`
    * - `5`
      - `深紫色`
    * - `6`
      - `鲑鱼粉`
    * - `7`
      - `中蓝色`
    * - `8`
      - `深蓝色`
    * - `9`
      - `紫红色`
    * - `12`
      - `绿色`
    * - `11`
      - `紫色`

.. _frontend/owl/dropdown:

Dropdown
--------

位置
~~~~~~~~

`@web/core/dropdown/dropdown` 和 `@web/core/dropdown/dropdown_item`

描述
~~~~~~~~~~~

下拉菜单组件比想象的复杂。它们需要提供许多功能，例如：

- 单击切换项目列表
- 直接的兄弟下拉菜单：当一个打开时，悬停切换其他菜单
- 单击外部区域关闭
- 选择项目后可选关闭项目列表
- 选择项目时调用函数
- 支持子下拉菜单，层级不限
- 自行设计样式
- 可配置快捷键打开/关闭下拉菜单或选择项目
- 键盘导航（方向键、Tab 键、Shift+Tab、Home、End、Enter 和 Esc）
- 页面滚动或调整大小时重新定位
- 自动选择打开方向（支持从右到左语言的自动处理）。

为了彻底解决这些问题，Odoo 框架提供了一套组件：`Dropdown`（实际下拉菜单）和 `DropdownItem`（每个列表项）。

.. code-block:: xml

  <Dropdown>
    <t t-set-slot="toggler">
      <!-- "toggler" 插槽内容渲染在按钮内部 -->
      点击我以切换下拉菜单！
    </t>
    <!-- "default" 插槽内容渲染在 div 中 -->
    <DropdownItem onSelected="selectItem1">菜单项 1</DropdownItem>
    <DropdownItem onSelected="selectItem2">菜单项 2</DropdownItem>
  </Dropdown>

属性
~~~~~

一个 `<Dropdown/>` 组件实际上是一个 `<div class="dropdown"/>`，旁边有一个 `<button class="dropdown-toggle"/>` 按钮。
按钮负责决定菜单 `<div class="dropdown-menu"/>` 是否存在于 DOM 中。

.. list-table::
   :widths: 20 20 60
   :header-rows: 1

   * - Dropdown
     - 类型
     - 描述
   * - `startOpen`
     - `boolean`
     - 初始下拉菜单打开状态（默认为 `false`）
   * - `menuClass`
     - `string`
     - 应用于下拉菜单 `<div class="dropdown-menu"/>` 的附加 CSS 类
   * - `togglerClass`
     - `string`
     - 应用于切换器 `<button class="dropdown-toggle"/>` 的附加 CSS 类
   * - `hotkey`
     - `string`
     - 用于通过键盘切换打开的快捷键
   * - `tooltip`
     - `string`
     - 在切换器上添加工具提示
   * - `beforeOpen`
     - `function`
     - 打开前执行的钩子函数。可以是异步的。
   * - `manualOnly`
     - `boolean`
     - 如果为 true，则仅在单击按钮时切换下拉菜单（默认为 `false`）
   * - `disabled`
     - `boolean`
     - 禁用（如果为 true）下拉按钮（默认为 `false`）
   * - `title`
     - `string`
     - `<button class="dropdown-toggle"/>` 的 title 属性内容（默认：无）
   * - `position`
     - `string`
     - 定义菜单打开方向。自动应用 RTL 方向。应该是有效的 :ref:`usePosition <frontend/hooks/useposition>` 钩子位置。（默认：`bottom-start`）
   * - `toggler`
     - `"parent"` 或 `undefined`
     - 设置为 `"parent"` 时，`<button class="dropdown-toggle"/>` 不会渲染（因此忽略 `toggler` 插槽），切换功能由父节点处理（例如用于透视单元格）。默认：`undefined`

一个 `<DropdownItem/>` 组件实际上是一个 `<span class="dropdown-item"/>`。当选择 `<DropdownItem/>` 时，它会调用其 `onSelected` 属性。如果此属性是方法，确保它已绑定（如果该方法需要使用 `this` 值）。

.. list-table::
   :widths: 20 20 60
   :header-rows: 1

   * - DropdownItem
     - 类型
     - 描述
   * - `onSelected`
     - `Function`
     - 选择下拉菜单项时调用的函数
   * - `parentClosingMode`
     - `none` | `closest` | `all`
     - 选择项目时，控制关闭哪个父下拉菜单：无、最近或全部（默认 = `all`）
   * - `hotkey`
     - `string`
     - 可选的快捷键，用于选择项目
   * - `href`
     - `string`
     - 如果提供，`DropdownItem` 将变为 `<a href="value" class="dropdown-item"/>` 而不是 `<span class="dropdown-item"/>`。（默认：未提供）
   * - `title`
     - `string`
     - 可选的 title 属性，传递给 DropdownItem 的根节点。（默认：未提供）
   * - `dataset`
     - `Object`
     - 可选对象，包含应添加到根元素 dataset 的值。此方法可用于使元素在程序化查找（例如测试或引导）中更易识别。

技术说明
~~~~~~~~~~~~~~~

渲染的 DOM 结构如下：

.. code-block:: html

   <div class="dropdown">
       <button class="dropdown-toggle">点击我！</button>
       <!-- 下面的 <div/> 根据上面的按钮状态决定是否出现在 DOM 中 -->
       <div class="dropdown-menu">
           <span class="dropdown-item">菜单项 1</span>
           <span class="dropdown-item">菜单项 2</span>
       </div>
   </div>

要正确使用 `<Dropdown/>` 组件，你需要填充两个
`OWL 插槽 <https://github.com/odoo/owl/blob/master/doc/reference/slots.md>`_：

- `toggler` 插槽：包含下拉菜单的 *toggler* 元素，渲染在 `button` 内部（除非 `toggler` 属性设置为 `parent`），
- `default` 插槽：包含下拉菜单项的元素，渲染在 `<div class="dropdown-menu"/>` 内。虽然并非强制，但通常至少在 `menu` 插槽中会有一个 `DropdownItem`。

当多个下拉菜单共享 DOM 中的同一父元素时，它们会相互通知其状态变化。
这意味着当其中一个下拉菜单打开时，其他下拉菜单会在鼠标悬停时自动打开，无需单击。

示例：直接的兄弟下拉菜单
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

当单击一个下拉菜单切换器（**File** 、**Edit** 或 **About**）时，其他菜单将会在悬停时自动打开。

.. code-block:: xml

  <div>
    <Dropdown>
      <t t-set-slot="toggler">文件</t>
      <DropdownItem onSelected="() => this.onItemSelected('file-open')">打开</DropdownItem>
      <DropdownItem onSelected="() => this.onItemSelected('file-new-document')">新建文档</DropdownItem>
      <DropdownItem onSelected="() => this.onItemSelected('file-new-spreadsheet')">新建表格</DropdownItem>
    </Dropdown>
    <Dropdown>
      <t t-set-slot="toggler">编辑</t>
      <DropdownItem onSelected="() => this.onItemSelected('edit-undo')">撤销</DropdownItem>
      <DropdownItem onSelected="() => this.onItemSelected('edit-redo')">重做</DropdownItem>
      <DropdownItem onSelected="() => this.onItemSelected('edit-find')">查找</DropdownItem>
    </Dropdown>
    <Dropdown>
      <t t-set-slot="toggler">关于</t>
      <DropdownItem onSelected="() => this.onItemSelected('about-help')">帮助</DropdownItem>
      <DropdownItem onSelected="() => this.onItemSelected('about-update')">检查更新</DropdownItem>
    </Dropdown>
  </div>

示例：多层次下拉菜单（带 `t-call`）
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

此示例展示了如何创建一个 `文件` 下拉菜单，带有 `新建` 和 `另存为...` 子菜单项。

.. code-block:: xml

  <t t-name="addon.Dropdown.File">
    <Dropdown>
      <t t-set-slot="toggler">文件</t>
      <DropdownItem onSelected="() => this.onItemSelected('file-open')">打开</DropdownItem>
      <t t-call="addon.Dropdown.File.New"/>
      <DropdownItem onSelected="() => this.onItemSelected('file-save')">保存</DropdownItem>
      <t t-call="addon.Dropdown.File.Save.As"/>
    </Dropdown>
  </t>

  <t t-name="addon.Dropdown.File.New">
    <Dropdown>
      <t t-set-slot="toggler">新建</t>
      <DropdownItem onSelected="() => this.onItemSelected('file-new-document')">文档</DropdownItem>
      <DropdownItem onSelected="() => this.onItemSelected('file-new-spreadsheet')">表格</DropdownItem>
    </Dropdown>
  </t>

  <t t-name="addon.Dropdown.File.Save.As">
    <Dropdown>
      <t t-set-slot="toggler">另存为...</t>
      <DropdownItem onSelected="() => this.onItemSelected('file-save-as-csv')">CSV</DropdownItem>
      <DropdownItem onSelected="() => this.onItemSelected('file-save-as-pdf')">PDF</DropdownItem>
    </Dropdown>
  </t>

示例：多层次下拉菜单（嵌套）
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: xml

  <Dropdown>
    <t t-set-slot="toggler">文件</t>
    <DropdownItem onSelected="() => this.onItemSelected('file-open')">打开</DropdownItem>
    <Dropdown>
      <t t-set-slot="toggler">新建</t>
      <DropdownItem onSelected="() => this.onItemSelected('file-new-document')">文档</DropdownItem>
      <DropdownItem onSelected="() => this.onItemSelected('file-new-spreadsheet')">表格</DropdownItem>
    </Dropdown>
    <DropdownItem onSelected="() => this.onItemSelected('file-save')">保存</DropdownItem>
    <Dropdown>
      <t t-set-slot="toggler">另存为...</t>
      <DropdownItem onSelected="() => this.onItemSelected('file-save-as-csv')">CSV</DropdownItem>
      <DropdownItem onSelected="() => this.onItemSelected('file-save-as-pdf')">PDF</DropdownItem>
    </Dropdown>
  </Dropdown>

示例：递归多层次下拉菜单
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

在此示例中，我们递归调用一个模板以显示类似树结构的下拉菜单。

.. code-block:: xml

  <t t-name="addon.MainTemplate">
    <div>
      <t t-call="addon.RecursiveDropdown">
        <t t-set="name" t-value="'主菜单'" />
        <t t-set="items" t-value="state.menuItems" />
      </t>
    </div>
  </t>

  <t t-name="addon.RecursiveDropdown">
    <Dropdown>
      <t t-set-slot="toggler"><t t-esc="name"/></t>
        <t t-foreach="items" t-as="item" t-key="item.id">

          <!-- 如果此项目没有子项：将其设为 <DropdownItem/> -->
          <t t-if="!item.childrenTree.length">
            <DropdownItem onSelected="() => this.onItemSelected(item)" t-esc="item.name"/>
          </t>
          <!-- 否则：递归调用当前的下拉菜单模板。 -->
          <t t-else="" t-call="addon.RecursiveDropdown">
            <t t-set="name" t-value="item.name" />
            <t t-set="items" t-value="item.childrenTree" />
          </t>

        </t>
      </t>
    </Dropdown>
  </t>

.. _frontend/owl/notebook:

Notebook
--------

位置
~~~~~~~~

`@web/core/notebook/notebook`

描述
~~~~~~~~~~~

Notebook 用于在标签界面中显示多个页面。标签可以位于元素的顶部，水平显示，也可以在左侧垂直布局。

有两种方法可以定义要实例化的 Notebook 页面：一种是使用 `slot`，另一种是通过传递专用的 `props`。

可以通过 `isDisabled` 属性禁用某个页面，该属性可以直接设置在插槽节点上，或者在页面声明中禁用。如果使用 `pages` 作为 props 传递，则一旦禁用，相应的标签也会变灰并被设置为不可用。

属性
~~~~~

.. list-table::
    :widths: 20 20 60
    :header-rows: 1

    * - 名称
      - 类型
      - 描述
    * - `anchors`
      - `object`
      - 可选。允许锚点导航到不可见选项卡中的元素。
    * - `className`
      - `string`
      - 可选。设置在组件根部的类名。
    * - `defaultPage`
      - `string`
      - 可选。默认显示的页面 `id`。
    * - `icons`
      - `array`
      - 可选。选项卡中使用的图标列表。
    * - `orientation`
      - `string`
      - 可选。选项卡方向是 `horizontal`（水平）还是 `vertical`（垂直）。
    * - `onPageUpdate`
      - `function`
      - 可选。页面更改后执行的回调。
    * - `pages`
      - `array`
      - 可选。包含用于模板的 `page` 列表。

.. example::

   第一种方法是在组件的插槽中设置页面。

   .. code-block:: xml

    <Notebook orientation="'vertical'">
      <t t-set-slot="page_1" title="'页面 1'" isVisible="true">
        <h1>我的第一页</h1>
        <p>是时候构建 Owl 组件了。你读过文档了吗？</p>
      </t>
      <t t-set-slot="page_2" title="'第二页'" isVisible="true">
        <p>智者猫头鹰的无声飞行。穿越月光洒满的森林，指引我编程的道路</p>
      </t>
    </Notebook>

   另一种定义页面的方法是通过传递 props。如果某些页面具有相同的结构，此方法会很有用。首先为每个页面模板创建一个组件。

   .. code-block:: javascript

      import { Notebook } from "@web/core/notebook/notebook";

      class MyTemplateComponent extends owl.Component {
        static template = owl.tags.xml`
          <h1 t-esc="props.title" />
          <p t-esc="props.text" />
        `;
      }

      class MyComponent extends owl.Component {
        get pages() {
          return [
            {
              Component: MyTemplateComponent,
              title: "页面 1",
              props: {
                title: "我的第一页",
                text: "此页面不可见",
              },
            },
            {
              Component: MyTemplateComponent,
              id: "page_2",
              title: "页面 2",
              props: {
                title: "我的第二页",
                text: "你来对地方了！",
              },
            },
          ]
        }
      }
      MyComponent.template = owl.tags.xml`
        <Notebook defaultPage="'page_2'" pages="pages" />
      `;

  这两个示例如下所示：

  .. image:: owl_components/notebook.png
     :width: 400 px
     :alt: 垂直和水平布局的示例
     :align: center


.. _frontend/pager:

Pager
-----

位置
~~~~~~~~

`@web/core/pager/pager`

描述
~~~~~~~~~~~

Pager 是一个用于处理分页的小型组件。一个页面由 `offset` 和 `limit` 定义（页面的大小）。它显示当前页面和 `total` 元素的总数，例如“9-12 / 20”。在前面的示例中，`offset` 是 8，`limit` 是 4，`total` 是 20。它有两个按钮（“上一页”和“下一页”）用于在页面之间导航。

.. note::
    Pager 可以用于任何地方，但它的主要用途是在控制面板中。请参阅 :ref:`usePager <frontend/hooks/usepager>` 钩子以操作控制面板的分页器。

.. code-block:: xml

  <Pager offset="0" limit="80" total="50" onUpdate="doSomething" />

属性
~~~~~

.. list-table::
    :widths: 20 20 60
    :header-rows: 1

    * - 名称
      - 类型
      - 描述
    * - `offset`
      - `number`
      - 页面第一个元素的索引。它从 0 开始，但分页器显示 `offset + 1`。
    * - `limit`
      - `number`
      - 页面大小。`offset` 和 `limit` 的总和对应页面最后一个元素的索引。
    * - `total`
      - `number`
      - 页面可以到达的元素总数。
    * - `onUpdate`
      - `function`
      - 当分页器修改页面时调用的函数。该函数可以是异步的，在函数执行期间，分页器不可编辑。
    * - `isEditable`
      - `boolean`
      - 允许单击当前页面进行编辑（默认 `true`）。
    * - `withAccessKey`
      - `boolean`
      - 将快捷键 `p` 绑定到上一页按钮，将 `n` 绑定到下一页按钮（默认 `true`）。

.. _frontend/select_menu:

SelectMenu
----------

位置
~~~~~~~~

`@web/core/select_menu/select_menu`

描述
~~~~~~~~~~~

当你想要做的操作超过原生 `select` 元素的功能时，可以使用此组件。你可以自定义选项模板，允许在选项中进行搜索，或将它们分组到子部分中。

.. note::
    请优先使用原生 HTML `select` 元素，因为它默认提供无障碍功能，并且在移动设备上有更好的用户界面。该组件设计用于更复杂的用例，克服原生元素的限制。

属性
~~~~~

.. list-table::
    :widths: 20 20 60
    :header-rows: 1

    * - 名称
      - 类型
      - 描述
    * - `choices`
      - `array`
      - 可选。显示在下拉列表中的 `choice` 列表。
    * - `class`
      - `string`
      - 可选。设置在 SelectMenu 组件根部的类名。
    * - `groups`
      - `array`
      - 可选。包含下拉列表中显示的 `choices` 的 `group` 列表。
    * - `multiSelect`
      - `boolean`
      - 可选。启用多重选择。当启用多重选择时，所选值会显示为 :ref:`tag <frontend/tags_list>` 在 SelectMenu 输入框中。
    * - `togglerClass`
      - `string`
      - 可选。设置在切换按钮上的类名。
    * - `required`
      - `boolean`
      - 可选。是否可以取消选择所选值。
    * - `searchable`
      - `boolean`
      - 可选。下拉列表中是否显示搜索框。
    * - `searchPlaceholder`
      - `string`
      - 可选。显示在搜索框中的占位文本。
    * - `value`
      - `any`
      - 可选。当前选定的值。可以是任意类型。
    * - `onSelect`
      - `function`
      - 可选。选择选项时执行的回调。

`choice` 的结构如下：

    - `value` 是选项的实际值。它通常是一个技术字符串，但可以是任意类型。
    - `label` 是与选项关联的显示文本。通常是更友好的翻译 `string`。

`group` 的结构如下：

    - `choices` 是此组中要显示的 `choice` 列表。
    - `label` 是与组关联的显示文本。这是一个显示在组顶部的 `string`。

.. example::

   在以下示例中，SelectMenu 将显示四个选项。其中一个显示在选项的顶部，因为没有与其关联的组，而其他选项则按其组的标签分开显示。

   .. code-block:: javascript

      import { SelectMenu } from "@web/core/select_menu/select_menu";

      class MyComponent extends owl.Component {
        get choices() {
          return [
              {
                value: "value_1",
                label: "第一个值"
              }
          ]
        }
        get groups() {
          return [
            {
                label: "组 A",
                choices: [
                    {
                      value: "value_2",
                      label: "第二个值"
                    },
                    {
                      value: "value_3",
                      label: "第三个值"
                    }
                ]
            },
            {
                label: "组 B",
                choices: [
                    {
                      value: "value_4",
                      label: "第四个值"
                    }
                ]
            }
          ]
        }
      }
      MyComponent.template = owl.tags.xml`
        <SelectMenu
          choices="choices"
          groups="groups"
          value="'value_2'"
        />
      `;

   你还可以自定义切换按钮的外观，并为选项设置自定义模板，使用相应的组件 `slot`。

   .. code-block:: javascript

      MyComponent.template = owl.tags.xml`
        <SelectMenu
          choices="choices"
          groups="groups"
          value="'value_2'"
        >
          选择一个选项！
          <t t-set-slot="choice" t-slot-scope="choice">
            <span class="coolClass" t-esc="'👉 ' + choice.data.label + ' 👈'" />
          </t>
        </SelectMenu>
      `;

   .. image:: owl_components/select_menu.png
      :width: 400 px
      :alt: SelectMenu 使用和自定义示例
      :align: center

   当 SelectMenu 与多重选择一起使用时，`value` 属性必须是包含所选选项值的 `Array`。

   .. image:: owl_components/select_menu_multiSelect.png
      :width: 350 px
      :alt: 使用多重选择的 SelectMenu 示例
      :align: center

   对于更高级的用例，你可以自定义下拉菜单的底部区域，使用 `bottomArea` 插槽。在此，我们选择显示一个按钮，按钮的对应值设置为搜索输入中的值。

   .. code-block:: javascript

      MyComponent.template = owl.tags.xml`
        <SelectMenu
            choices="choices"
        >
            <span class="select_menu_test">选择一个选项</span>
            <t t-set-slot="bottomArea" t-slot-scope="select">
                <div t-if="select.data.searchValue">
                    <button class="btn text-primary" t-on-click="() => this.onCreate(select.data.searchValue)">
                        创建此项目 "<i t-esc="select.data.searchValue" />"
                    </button>
                </div>
            </t>
        </SelectMenu>
      `;

   .. image:: owl_components/select_menu_bottomArea.png
      :width: 400 px
      :alt: SelectMenu 的底部区域自定义示例
      :align: center

.. _frontend/tags_list:

TagsList
--------

位置
~~~~~~~~

`@web/core/tags_list/tags_list`

描述
~~~~~~~~~~~

此组件可以以圆形标签形式显示标签列表。标签可以简单地列出一些值，也可以是可编辑的，允许移除项目。
你可以使用 `itemsVisible` 属性限制显示的项目数量。如果列表长度超过此限制，则在最后一个标签旁边显示一个圆形标记，其中显示其他项目的数量。

属性
~~~~~

.. list-table::
    :widths: 20 20 60
    :header-rows: 1

    * - 名称
      - 类型
      - 描述
    * - `displayBadge`
      - `boolean`
      - 可选。是否将标签显示为徽章。
    * - `displayText`
      - `boolean`
      - 可选。是否显示标签文本。
    * - `itemsVisible`
      - `number`
      - 可选。列表中可见标签的限制数量。
    * - `tags`
      - `array`
      - 传递给组件的 `tag` 元素列表。

`tag` 的结构如下：

    - `colorIndex` 是可选的颜色 ID。
    - `icon` 是在显示文本之前显示的可选图标。
    - `id` 是标签的唯一标识符。
    - `img` 是显示在显示文本之前的可选图片，位于圆形中。
    - `onClick` 是可以传递给元素的可选回调。这允许父元素处理点击标签时的任何功能。
    - `onDelete` 是可以传递给元素的可选回调。这使得可以从标签列表中删除该项目，并且必须由父元素处理。
    - `text` 是与标签关联的显示 `string`。

.. example::

   在下一个示例中，使用 TagsList 组件显示多个标签。
   由开发者从父组件处理点击标签时会发生什么，或点击删除按钮时会发生什么。

   .. code-block:: javascript

      import { TagsList } from "@web/core/tags_list/tags_list";

      class Parent extends Component {
        setup() {
          this.tags = [{
              id: "tag1",
              text: "地球"
          }, {
              colorIndex: 1,
              id: "tag2",
              text: "风",
              onDelete: () => {...}
          }, {
              colorIndex: 2,
              id: "tag3",
              text: "火",
              onClick: () => {...},
              onDelete: () => {...}
          }];
        }
      }
      Parent.components = { TagsList };
      Parent.template = xml`<TagsList tags="tags" />`;

   根据传递给每个标签的属性，它们的外观和行为会有所不同。

   .. image:: owl_components/tags_list.png
      :width: 350 px
      :alt: 使用不同属性和选项的 TagsList 示例
      :align: center
