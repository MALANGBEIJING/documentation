=============
修改代码
=============

有时，我们需要自定义用户界面的工作方式。许多常见的需求可以通过一些支持的API来实现。例如，所有注册表都是很好的扩展点：字段注册表允许添加/删除特定的字段组件，主组件注册表允许添加应始终显示的组件。

然而，有些情况是这些方法无法满足的。在这种情况下，我们可能需要直接修改对象或类。为此，Odoo 提供了实用函数 `patch`。它主要用于覆盖/更新无法控制的其他组件/代码段的行为。

描述
===========

`patch` 函数位于 `@web/core/utils/patch`：

.. js:function:: patch(objToPatch, extension)

    :param object objToPatch: 需要修改的对象
    :param object extension: 一个对象，映射每个键到扩展
    :returns: 一个用于移除补丁的函数

    `patch` 函数会直接修改 `objToPatch` 对象（或类），并应用 `extension` 对象中描述的所有键/值。返回一个解除补丁的函数，以便在必要时移除补丁。

    大多数补丁操作通过使用原生的 `super` 关键字来访问父类的值（请参见下面的示例）。

修改简单对象
========================

以下是一个如何修改对象的简单示例：

.. code-block:: javascript

  import { patch } from "@web/core/utils/patch";

  const object = {
    field: "一个字段",
    fn() {
      // 执行某些操作
    },
  };

  patch(object, {
    fn() {
      // 执行其他操作
    },
  });

当修改函数时，我们通常希望能够访问 "父" 函数。为此，我们可以简单地使用原生的 ``super`` 关键字：

.. code-block:: javascript

  patch(object, {
    fn() {
      super.fn(...arguments);
      // 执行其他操作
    },
  });

.. warning::

    ``super`` 只能在方法中使用，不能在函数中使用。这意味着以下 JavaScript 构造是无效的。

    .. code-block:: javascript

      const obj = {
        a: function () {
          // 抛出错误: "Uncaught SyntaxError: 'super' 关键字在此处意外出现"
          super.a();
        },
        b: () => {
          // 抛出错误: "Uncaught SyntaxError: 'super' 关键字在此处意外出现"
          super.b();
        },
      };

Getters 和 Setters 也受到支持：

.. code-block:: javascript

    patch(object, {
      get number() {
        return super.number / 2;
      },
      set number(value) {
        super.number = value;
      },
    });

.. _frontend/patching_class:

修改 JavaScript 类
===========================

`patch` 函数设计为适用于任何对象：对象或 ES6 类。

但是，由于 JavaScript 类使用原型继承，当需要修改类中的标准方法时，实际上需要修改 `prototype`：

.. code-block:: javascript

  class MyClass {
    static myStaticFn() {...}
    myPrototypeFn() {...}
  }

  // 这将修改静态属性!!!
  patch(MyClass, {
    myStaticFn() {...},
  });

  // 这是常见情况：修改类方法
  patch(MyClass.prototype, {
    myPrototypeFn() {...},
  });

此外，JavaScript 以特殊的方式处理构造函数，使其无法被修改。唯一的解决方法是调用原始构造函数中的某个方法，并修改该方法：

.. code-block:: javascript

  class MyClass {
    constructor() {
      this.setup();
    }
    setup() {
      this.number = 1;
    }
  }

  patch(MyClass.prototype, {
    setup() {
      super.setup(...arguments);
      this.doubleNumber = this.number * 2;
    },
  });

.. warning::

    无法直接修改类的 `constructor`！

修改组件
====================

组件是通过 JavaScript 类定义的，因此上述所有信息依然适用。出于这些原因，Owl 组件应该使用 `setup` 方法，这样它们也可以轻松地被修改（请参阅 :ref:`最佳实践 <frontend/owl/best_practices>` 章节）。

.. code-block:: javascript

  patch(MyComponent.prototype, {
    setup() {
      useMyHook();
    },
  });

移除补丁
================

`patch` 函数返回其相对的函数。这在测试时非常有用，当我们在测试开始时修改某些内容，并在测试结束时移除补丁。

.. code-block:: javascript

    const unpatch = patch(object, { ... });
    // 在此处进行测试
    unpatch();

对多个对象应用相同的补丁
===========================================

有时，我们可能希望对多个对象应用相同的补丁，但由于 `super` 关键字的工作方式，`extension` 只能用于一次补丁，无法复制/克隆（参见 `super 关键字的文档 <https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/super#description>`_）。可以使用一个函数来返回用于补丁的对象，从而使其唯一。

.. code-block:: javascript

    const obj1 = {
      method() {
        doSomething();
      },
    };

    const obj2 = {
      method() {
        doThings();
      },
    };

    function createExtensionObj() {
      return {
        method() {
          super.method();
          doCommonThings();
        },
      };
    }

    patch(obj1, createExtensionObj());
    patch(obj2, createExtensionObj());

.. warning::

  如果一个 `extension` 基于另一个 `extension`，那么这两个扩展应该分别应用。不要复制/克隆扩展。

  .. code-block:: javascript

      const object = {
        method1() {
          doSomething();
        },
        method2() {
          doAnotherThing();
        },
      };

      const ext1 = {
        method1() {
          super.method1();
          doThings();
        },
      };

      const invalid_ext2 = {
        ...ext1, // 这将无法工作：super 将无法在来自 ext1 的方法中引用正确的对象
        method2() {
          super.method2();
          doOtherThings();
        },
      };

      patch(object, invalid_ext2);
      object.method1(); // 抛出错误: Uncaught TypeError: (intermediate value).method1 is not a function

      const valid_ext2 = {
        method2() {
          super.method2();
          doOtherThings();
        },
      };

      patch(object, ext1); // 首先应用基础扩展补丁
      patch(object, valid_ext2); // 然后应用新扩展补丁
      object.method1(); // 正常工作
