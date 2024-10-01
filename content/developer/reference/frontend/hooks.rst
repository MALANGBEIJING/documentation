.. _frontend/hooks:

=====
Hooks
=====

`Owl hooks <https://github.com/odoo/owl/blob/master/doc/reference/hooks.md>`_ 是一种
代码重构的方法，即使它依赖于某些组件生命周期。Owl 提供的大多数 hooks 与
组件的生命周期有关，但其中一些（例如 `useComponent <https://github.com/odoo/owl/blob/master/doc/reference/hooks.md#usecomponent>`_）提供了一种构建特定 hooks 的方法。

使用这些 hooks，可以构建许多自定义 hooks，帮助解决特定问题或简化一些常见任务。
本页面的其余部分记录了 Odoo Web 框架提供的 hooks 列表。

.. list-table::
   :widths: 30 70
   :header-rows: 1

   * - 名称
     - 简短描述
   * - :ref:`useAssets <frontend/hooks/useassets>`
     - 加载资产
   * - :ref:`useAutofocus <frontend/hooks/useAutofocus>`
     - 自动聚焦引用的元素
   * - :ref:`useBus <frontend/hooks/usebus>`
     - 订阅和取消订阅总线
   * - :ref:`usePager <frontend/hooks/usepager>`
     - 显示视图控制面板的分页器。
   * - :ref:`usePosition <frontend/hooks/useposition>`
     - 将元素相对于目标进行定位
   * - :ref:`useSpellCheck <frontend/hooks/useSpellCheck>`
     - 在输入框或文本区域获得焦点时激活拼写检查

.. _frontend/hooks/useassets:

useAssets
=========

位置
--------

`@web/core/assets`

描述
-----------

有关更多详细信息，请参见 :ref:`懒加载资产 <frontend/assets/lazy_loading>` 部分。

.. _frontend/hooks/useAutofocus:

useAutofocus
============

位置
--------

`@web/core/utils/hooks`

描述
-----------

在当前组件的 DOM 中引用 `t-ref="autofocus"` 的元素出现时自动聚焦，
并且如果它之前没有显示过。

.. code-block:: javascript

    import { useAutofocus } from "@web/core/utils/hooks";

    class Comp {
      setup() {
        this.inputRef = useAutofocus();
      }
      static template = "Comp";
    }

.. code-block:: xml

    <t t-name="Comp">
      <input t-ref="autofocus" type="text"/>
    </t>

API
---

.. js:function:: useAutofocus()

    :returns: 元素引用。

.. _frontend/hooks/usebus:

useBus
======

位置
--------

`@web/core/utils/hooks`

描述
-----------

向总线添加和清除事件监听器。此 hook 确保在组件卸载时正确清除监听器。

.. code-block:: javascript

    import { useBus } from "@web/core/utils/hooks";

    class MyComponent {
      setup() {
        useBus(this.env.bus, "some-event", event => {
          console.log(event);
        });
      }
    }

API
---

.. js:function:: useBus(bus, eventName, callback)

    :param EventBus bus: 目标事件总线
    :param string eventName: 我们想要监听的事件名称
    :param function callback: 监听器回调

.. _frontend/hooks/usepager:

usePager
========

位置
--------

`@web/search/pager_hook`

描述
-----------

显示视图控制面板的 :ref:`分页器 <frontend/pager>`。此 hook 正确设置 `env.config`
以提供分页器的属性。

.. code-block:: javascript

    import { usePager } from "@web/search/pager_hook";

    class CustomView {
      setup() {
        const state = owl.hooks.useState({
          offset: 0,
          limit: 80,
          total: 50,
        });
        usePager(() => {
          return {
            offset: this.state.offset,
            limit: this.state.limit,
            total: this.state.total,
            onUpdate: (newState) => {
              Object.assign(this.state, newState);
            },
          };
        });
      }
    }

API
---

.. js:function:: usePager(getPagerProps)

    :param function getPagerProps: 返回分页器属性的函数。

.. _frontend/hooks/useposition:

usePosition
===========

位置
--------

`@web/core/position_hook`

描述
-----------

帮助将 HTMLElement（`popper`）相对于另一个 HTMLElement（`reference`）进行定位。
此 hook 确保在窗口调整大小/滚动时更新定位。

.. code-block:: javascript

    import { usePosition } from "@web/core/position_hook";

    class MyPopover extends owl.Component {
      setup() {
        // 这里，参考是目标属性，它是一个 HTMLElement
        usePosition(this.props.target);
      }
    }
    MyPopover.template = owl.tags.xml`
      <div t-ref="popper">
        我通过一个神奇的 hook 进行定位！
      </div>
    `;

.. important::
   您应该使用 `t-ref 指令 <https://github.com/odoo/owl/blob/master/doc/reference/hooks.md#useref>`_ 指定您的 `popper` 元素。

API
---

.. js:function:: usePosition(reference[, options])

    :param reference: 参考的 HTMLElement 要进行定位
    :type reference: HTMLElement 或 ()=>HTMLElement
    :param Options options: 定位选项（见下表）

.. list-table::
   :widths: 20 20 60
   :header-rows: 1

   * - 选项
     - 类型
     - 描述
   * - `popper`
     - string
     - 这是要被定位的元素的 `useRef 参考 <https://github.com/odoo/owl/blob/master/doc/reference/hooks.md#useref>`_。
       默认值为 `"popper"`。
   * - `container`
     - HTMLElement
     - popper 不应溢出的容器。如果发生溢出，将尝试其他 popper 位置，直到找到一个不溢出的为止。
       （默认：`<html/>` 节点）
   * - `margin`
     - number
     - popper 和参考元素之间添加的边距（默认：`0`）
   * - `position`
     - Direction[-Variant]
     - 所需位置。它是由一个 `Direction` 和一个 `Variant` 用短横线分隔的字符串。
       `Direction` 可以是：`top`、`bottom`、`right`、`left`。
       `Variant` 可以是：`start`、`middle`、`end`、`fit`。
       变体可以省略（默认变体为 `middle`）。
       `fit` 变体意味着 popper 将具有相同的宽度或高度，具体取决于所选方向。
       有效位置的示例：`right-end`、`top-start`、`left-middle`、`left`、`bottom-fit`。 （默认位置：`bottom`）
   * - `onPositioned`
     - (el: HTMLElement, position: PositioningSolution) => void
     - 每次发生定位时调用的回调
       （例如，在组件挂载/补丁、文档滚动、窗口调整大小时...）。
       可用于根据当前位置进行动态样式调整。
       `PositioningSolution` 是一个具有以下类型的对象：
       `{ direction: Direction, variant: Variant, top: number, left: number }`。

.. example::

   .. code-block:: javascript

      import { usePosition } from "@web/core/position_hook";

      class DropMenu extends owl.Component {
        setup() {
          const toggler = owl.useRef("toggler");
          usePosition(
            () => toggler.el,
            {
              popper: "menu",
              position: "right-start",
              onPositioned: (el, { direction, variant }) => {
                el.classList.add(`dm-${direction}`); // -> "dm-top" "dm-right" "dm-bottom" "dm-left"
                el.style.backgroundColor = variant === "middle" ? "red" : "blue";
              },
            },
          );
        }
      }
      DropMenu.template = owl.tags.xml`
        <button t-ref="toggler">切换菜单</button>
        <div t-ref="menu">
          <t t-slot="default">
            这是菜单的默认内容。
          </t>
        </div>
      `;

.. _frontend/hooks/useSpellCheck:

useSpellCheck
=============

位置
--------

`@web/core/utils/hooks`

描述
-----------

在当前组件的输入框或文本区域上，通过 `t-ref="spellcheck"` 在获得焦点时激活拼写检查状态。
然后在失去焦点时移除该状态以及红色轮廓，从而提高内容的可读性。

该 hook 还可以用于任何具有 `contenteditable` 属性的 HTML 元素。要完全禁用可能由 hook 启用的元素上的拼写检查，请在元素上显式设置 `spellcheck` 属性为 `false`。

.. example::

   在以下示例中，拼写检查将在第一个输入框、文本区域和
   `contenteditable="true"` 的 div 上启用。

   .. code-block:: javascript

       import { useSpellCheck } from "@web/core/utils/hooks";

       class Comp {
         setup() {
           this.simpleRef = useSpellCheck();
           this.customRef = useSpellCheck({ refName: "custom" });
           this.nodeRef = useSpellCheck({ refName: "container" });
         }
         static template = "Comp";
       }

   .. code-block:: xml

       <t t-name="Comp">
         <input t-ref="spellcheck" type="text"/>
         <textarea t-ref="custom"/>
         <div t-ref="container">
           <input type="text" spellcheck="false"/>
           <div contenteditable="true"/>
         </div>
       </t>

API
---

.. js:function:: useSpellCheck([options])

    :param Options options: 拼写检查选项（见下表）

.. list-table::
   :widths: 20 20 60
   :header-rows: 1

   * - 选项
     - 类型
     - 描述
   * - `refName`
     - string
     - 这是要启用拼写检查的元素的 `useRef 参考 <https://github.com/odoo/owl/blob/master/doc/reference/hooks.md#useref>`_。
