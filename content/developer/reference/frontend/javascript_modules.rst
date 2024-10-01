.. _frontend/js_modules:

==================
Javascript Modules
==================

Odoo 支持三种不同类型的 JavaScript 文件：

- :ref:`普通 JavaScript 文件 <frontend/modules/plain_js>`（无模块系统），
- :ref:`原生 JavaScript 模块 <frontend/modules/native_js>`，
- :ref:`Odoo 模块 <frontend/modules/odoo_module>`（使用自定义模块系统）。

正如 :ref:`资产管理页面 <reference/assets>` 中所述，所有 JavaScript 文件都会被捆绑在一起并提供给浏览器。
请注意，原生 JavaScript 文件由 Odoo 服务器处理并转换为 Odoo 自定义模块。

让我们简要说明每种类型的 JavaScript 文件的目的。普通 JavaScript 文件应仅保留用于外部库和一些小的特定低级用途。所有新的 JavaScript 文件都应在原生 JavaScript 模块系统中创建。自定义模块系统仅对旧的、尚未转换的文件有用。

.. _frontend/modules/plain_js:

Plain Javascript files
======================

普通 JavaScript 文件可以包含任意内容。建议在编写此类文件时使用 *iife* :dfn:`立即调用函数执行` 风格：

.. code-block:: javascript

  (function () {
    // 这里是一些代码
    let a = 1;
    console.log(a);
  })();

这样的文件的优点是避免将局部变量泄露到全局作用域。

显然，普通 JavaScript 文件没有模块系统的好处，因此需要小心捆绑中的顺序（因为浏览器将精确按照该顺序执行它们）。

.. note::
  在 Odoo 中，所有外部库都作为普通 JavaScript 文件加载。

.. _frontend/modules/native_js:

Native Javascript Modules
=========================

大多数新的 Odoo JavaScript 代码应使用原生 JavaScript 模块系统。这种方法更简单，并提供更好的开发者体验以及与 IDE 的更好集成。

有一个非常重要的点需要了解：Odoo 需要知道哪些文件应转换为 :ref:`Odoo 模块 <frontend/modules/odoo_module>`，而哪些文件不应转换。这是一个选择性系统：Odoo 将查看 JS 文件的第一行，并检查其是否包含字符串 *@odoo-module*。如果是，它将自动转换为 Odoo 模块。

例如，考虑以下模块，位于 :file:`web/static/src/file_a.js`：

.. code-block:: javascript

  /** @odoo-module **/
  import { someFunction } from './file_b';

  export function otherFunction(val) {
      return someFunction(val + 3);
  }

请注意第一行中的注释：它描述了此文件应被转换。任何没有此注释的文件将保持原样（这很可能是错误）。此文件将转换为一个 Odoo 模块，如下所示：

.. code-block:: javascript

   odoo.define('@web/file_a', function (require) {
   'use strict';
   let __exports = {};

   const { someFunction } = require("@web/file_b");

   __exports.otherFunction = function otherFunction(val) {
       return someFunction(val + 3);
   };

   return __exports;
   )};

因此，正如您所看到的，转换基本上是在顶部添加 `odoo.define`，并更新导入/导出语句。

另一个重要点是，翻译后的模块具有官方名称：*@web/file_a*。这是真正的模块名称。每个相对导入也将被转换。每个位于 Odoo 附加模块 :file:`some_addon/static/src/path/to/file.js` 中的文件将分配一个以附加模块名称为前缀的名称，如下所示：*@some_addon/path/to/file*。

相对导入可以正常工作，但仅当模块在同一 Odoo 附加模块中时。因此，假设我们有以下文件结构：

::

  addons/
      web/
          static/
              src/
                  file_a.js
                  file_b.js
      stock/
          static/
              src/
                  file_c.js

文件 :file:`file_b` 可以这样导入 :file:`file_a`：

.. code-block:: javascript

  /** @odoo-module **/
  import {something} from `./file_a`

但 :file:`file_c` 需要使用完整名称：

.. code-block:: javascript

  /** @odoo-module **/
  import {something} from `@web/file_a`


Aliased modules
---------------

由于 :ref:`Odoo 模块 <frontend/modules/odoo_module>` 遵循不同的模块命名模式，因此存在一个系统来平滑过渡到新系统。目前，如果一个文件被转换为模块（因此遵循新的命名约定），项目中尚未转换为 ES6 类似语法的其他文件将无法要求该模块。别名用于通过创建一个小代理函数将旧名称映射到新名称。然后可以通过其新旧名称调用模块。

要添加此类别名，文件顶部的注释标签应如下所示：

.. code-block:: javascript

  /** @odoo-module alias=web.someName**/
  import { someFunction } from './file_b';

  export default function otherFunction(val) {
      return someFunction(val + 3);
  }

然后，翻译后的模块还将创建一个与请求名称相对应的别名：

.. code-block:: javascript

  odoo.define(`web.someName`, function(require) {
      return require('@web/file_a')[Symbol.for("default")];
  });

别名的默认行为是重新导出模块的 ``default`` 值。这是因为“经典”模块通常导出一个单一值，直接使用，与默认导出语义大致相符。
然而，您也可以更直接地委托，并遵循别名模块的确切行为：

.. code-block:: javascript

  /** @odoo-module alias=web.someName default=0**/
  import { someFunction } from './file_b';

  export function otherFunction(val) {
      return someFunction(val + 3);
  }

在这种情况下，这将定义一个具有原始模块导出的确切值的别名：

.. code-block:: javascript

  odoo.define(`web.someName`, function(require) {
      return require('@web/file_a');
  });

.. note::
   只能通过此方法定义一个别名。如果您需要另一个别名，例如希望调用同一模块的三个名称，则必须手动添加代理。
   这不是最佳实践，除非没有其他选择，应该避免这样做。

Limitations
-----------

出于性能原因，Odoo 不使用完整的 JavaScript 解析器来转换原生模块。因此，存在一些限制，包括但不限于：

- `import` 或 `export` 关键字不能由非空格字符前导，
- 多行注释或字符串不能包含以 `import` 或 `export` 开头的行

  .. code-block:: javascript

    // 支持
    import X from "xxx";
    export X;
      export default X;
        import X from "xxx";

    /*
     * import X ...
     */

    /*
     * export X
     */


    // 不支持

    var a= 1;import X from "xxx";
    /*
      import X ...
    */

- 导出对象时，它不能包含注释

  .. code-block:: javascript

      // 支持
      export {
        a as b,
        c,
        d,
      }

      export {
        a
      } from "./file_a"


      // 不支持
      export {
        a as b, // 这是一个注释
        c,
        d,
      }

      export {
        a /* 这是一个注释 */
      } from "./file_a"

- Odoo 需要一种方法来确定模块是由路径描述（如 :file:`./views/form_view`）还是名称（如 `web.FormView`）。它必须使用启发式方法来做到这一点：如果名称中有 `/`，则被视为路径。这意味着 Odoo 不再真正支持带有 `/` 的模块名称。

由于“经典”模块未被弃用，目前没有计划删除它们，因此如果您遇到问题或受到原生模块限制的约束，您可以并且应该继续使用它们。这两种样式可以在同一 Odoo 附加模块中共存。

.. _frontend/modules/odoo_module:

Odoo Module System
==================

Odoo 定义了一个小模块系统（位于文件 :file:`addons/web/static/src/js/boot.js` 中，首先需要加载该文件）。Odoo 模块系统受 AMD 启发，工作原理是定义全局 odoo 对象上的 `define` 函数。然后，我们通过调用该函数定义每个 JavaScript 模块。在 Odoo 框架中，模块是将尽快执行的一段代码。它有一个名称和潜在的依赖项。当其依赖项加载时，模块也会被加载。模块的值就是定义该模块的函数的返回值。

例如，它可能如下所示：

.. code-block:: javascript

    // 在文件 a.js 中
    odoo.define('module.A', function (require) {
        "use strict";

        var A = ...;

        return A;
    });

    // 在文件 b.js 中
    odoo.define('module.B', function (require) {
        "use strict";

        var A = require('module.A');

        var B = ...; // 与 A 相关的某些内容

        return B;
    });

定义模块的另一种方法是在第二个参数中显式给出依赖项列表。

.. code-block:: javascript

    odoo.define('module.Something', ['module.A', 'module.B'], function (require) {
        "use strict";

        var A = require('module.A');
        var B = require('module.B');

        // 一些代码
    });


如果某些依赖项缺失/未就绪，则模块将简单地不被加载。几秒钟后，控制台将显示警告。

请注意，不支持循环依赖。这是有意义的，但这意味着需要小心。

Defining a module
-----------------

`odoo.define` 方法有三个参数：

- `moduleName`：JavaScript 模块的名称。它应该是唯一的字符串。
  约定是将 Odoo 附加模块的名称后跟特定描述。例如，`web.Widget` 描述在 `web` 附加模块中定义的模块，该模块导出一个 `Widget` 类（因为首字母大写）。

  如果名称不唯一，将抛出异常并在控制台中显示。

- `dependencies`：第二个参数是可选的。如果给出，它应该是字符串列表，每个字符串对应一个 JavaScript 模块。它描述了在执行模块之前需要加载的依赖项。如果没有显式给出依赖项，则模块系统将通过调用其函数的 toString，然后使用正则表达式查找所有 `require` 语句来提取它们。

  .. code-block:: javascript

     odoo.define('module.Something', ['web.ajax'], function (require) {
         "use strict";

         var ajax = require('web.ajax');

         // 一些代码
         return something;
     });

- 最后，最后一个参数是定义模块的函数。它的返回值就是模块的值，可以传递给其他要求它的模块。请注意，对于异步模块，有一个小的例外，见下节。

如果发生错误，将记录（调试模式下）在控制台中：

* `缺失依赖项`：
  这些模块未出现在页面中。可能是 JavaScript 文件不在页面中或模块名称错误
* `失败的模块`：
  检测到 JavaScript 错误
* `被拒绝的模块`：
  模块返回一个被拒绝的 Promise。它（以及其依赖的模块）未加载。
* `被拒绝的链接模块`：
  依赖于被拒绝模块的模块
* `未加载的模块`：
  依赖于缺失或失败的模块

Asynchronous modules
--------------------

可能发生某个模块需要在准备好之前执行一些工作。例如，它可以执行 rpc 来加载一些数据。在这种情况下，模块可以简单地返回一个承诺。模块系统将在注册模块之前等待承诺完成。

.. code-block:: javascript

    odoo.define('module.Something', function (require) {
        "use strict";

        var ajax = require('web.ajax');

        return ajax.rpc(...).then(function (result) {
            // 一些代码
            return something;
        });
    });
