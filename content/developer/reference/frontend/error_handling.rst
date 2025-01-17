==============
错误处理
==============

在编程中，错误处理是一个复杂的话题，存在许多陷阱，当您在框架的约束条件下编写代码时，这可能变得更加棘手，因为您处理错误的方式需要与框架调度错误的方式相匹配，反之亦然。

本文概述了Odoo JavaScript框架和Owl如何处理错误，并提供了一些建议，帮助您以避免常见问题的方式与这些系统接口。

JavaScript中的错误
====================

在深入了解Odoo中的错误处理以及如何自定义错误处理行为之前，确保我们在"错误"的确切定义上达成共识，以及JavaScript中错误处理的一些特殊情况。

`Error` 类
-----------------

当我们谈论错误处理时，首先想到的可能是内置的 `Error` 类，或扩展它的类。在本文的其余部分，当我们提到一个 `Error` 类的实例时，我们将用斜体字使用术语 *Error object*。

任何东西都可以被抛出
----------------------

在JavaScript中，您可以抛出任何值。抛出 *Error objects* 是一种惯例，但也可以抛出任何其他对象，甚至是原始值。尽管我们不建议您抛出非 *Error object* 的任何东西，但Odoo JavaScript框架需要能够处理这些场景，这将帮助您理解我们所做的一些设计决策。

当实例化 *Error object* 时，浏览器会收集关于“调用堆栈”当前状态的信息（无论是适当的调用堆栈，还是异步函数和承诺继续的重建调用堆栈）。这些信息被称为“堆栈跟踪”，对于调试非常有用。Odoo框架在错误对话框中显示此堆栈跟踪（如果可用）。

当抛出一个不是 *Error object* 的值时，浏览器仍然会收集有关当前调用堆栈的信息，但这些信息在JavaScript中不可用：它仅在未处理错误时可在开发者工具控制台中获取。

抛出 *Error objects* 使我们能够显示更详细的信息，用户可以在需要时复制/粘贴以进行错误报告，但它也使错误处理更加稳健，因为它允许我们在处理错误时根据其类进行过滤。不幸的是，JavaScript没有在catch子句中按错误类过滤的语法支持，但您可以相对容易地自己做到这一点：

.. code-block:: javascript

  try {
    doStuff();
  } catch (e) {
    if (!(e instanceof MyErrorClass)) {
      throw e; // 捕获到无法处理的错误，重新抛出
    }
    // 处理 MyErrorClass
  }

承诺拒绝是错误
-----------------------------

在承诺（Promise）采用的早期阶段，承诺通常被视为存储结果和“错误”的不相交联合，使用承诺拒绝作为信号表示软失败的情况相当普遍。尽管乍一看似乎是个好主意，但浏览器和JavaScript运行时已经开始几乎在所有方面将拒绝的承诺视为抛出的错误：

- 在异步函数中抛出具有与返回被拒绝的承诺相同的效果，即抛出的值作为其拒绝原因。
- 在异步函数中的catch块中捕获在相应的try块中等待的拒绝承诺。
- 运行时收集有关拒绝承诺的堆栈信息。
- 未同步捕获的拒绝承诺会在全局/window对象上分发事件，如果未对事件调用preventDefault，则浏览器会记录错误，Standalone运行时（如Node）将终止进程。
- 调试器功能“在异常上暂停”在拒绝承诺时暂停。

出于这些原因，Odoo框架将拒绝的承诺与抛出的错误完全一样对待。不要在您不会抛出错误的地方创建拒绝的承诺，并始终将 *Error objects* 作为其拒绝原因来拒绝承诺。

`error` 事件不是错误
-----------------------------

除了 `window` 上的 `error` 事件外，其他对象（如 `<media>`、`<audio>`、`<img>`、`<script>` 和 `<link>` 元素或 XMLHttpRequest 对象）上的 `error` 事件都不是错误。就本文而言，“错误”专指抛出的值和被拒绝的承诺。如果您需要处理这些元素上的错误或希望将它们视为错误，则需要显式添加事件侦听器以处理该事件：

.. code-block:: javascript

  const scriptEl = document.createElement("script");
  scriptEl.src = "https://example.com/third_party_script.js";
  return new Promise((resolve, reject) => {
    scriptEl.addEventListener("error", reject);
    scriptEl.addEventListener("load", resolve);
    document.head.append(scriptEl);
  });

Odoo JS框架内错误的生命周期
================================================

抛出的错误会展开其调用堆栈以查找可以处理它们的catch子句。错误的处理方式取决于在展开调用堆栈时遇到的代码。尽管几乎可以从无限数量的地方抛出错误，但只有少数几条路径可以进入JS框架的错误处理代码。

在模块的顶层抛出错误
----------------------------------------------

当加载JS模块时，该模块顶层的代码会被执行并可能抛出。尽管框架可能会用对话框报告这些错误，但模块加载是JavaScript框架的关键时刻，因此某些抛出错误的模块可能会完全阻止框架代码启动，因此此阶段的任何错误报告都是“尽力而为”。然而，在模块加载期间抛出的错误至少应该在浏览器控制台中记录错误消息。由于这种类型的错误是关键的，因此应用程序没有办法恢复，您应该以一种不可能在定义期间抛出模块的方式编写代码。在此阶段发生的任何错误处理和报告的目标纯粹是为了帮助您，开发人员，修复抛出错误的代码，我们提供的没有机制来自定义这些错误的处理方式。

错误服务
-----------------

当抛出一个错误但从未捕获时，运行时会在全局对象 (`window`) 上调度一个事件。事件的类型取决于错误是同步抛出还是异步抛出：同步抛出的错误调度 `error` 事件，而从异步上下文中抛出的错误以及被拒绝的承诺则调度 `unhandledrejection` 事件。

JS框架包含一个专门处理这些事件的服务：错误服务。当接收到这些事件之一时，错误服务首先创建一个新的 *Error object*，用于包装抛出的错误；这是因为任何值都可以被抛出，承诺可以用任何值拒绝，包括 `undefined` 或 `null`，这意味着它不保证包含任何信息，或者我们可以在该值上存储任何信息。包装的 *Error object* 用于收集有关抛出值的一些信息，以便可以在需要显示有关任何类型的错误的信息的框架代码中统一使用。

错误服务在这个包装的 *Error object* 上存储了抛出错误的完整堆栈跟踪，并且当调试模式为 `assets` 时，使用源映射将有关包含每个堆栈帧的函数的源文件的信息添加到该堆栈跟踪中。函数在捆绑资产中的位置被保留，因为在某些场景中这可能是有用的。当错误具有 `cause` 时，此过程还会展开 `cause` 链，以构建完整的复合堆栈跟踪。虽然 *Error objects* 上的 `cause` 字段是标准的，但某些主要浏览器仍然不显示错误链的完整堆栈跟踪。因此，我们手动添加此信息。特别是在Owl钩子内抛出错误时，这一点尤为有用，稍后会详细介绍。

一旦包装的错误包含所有所需的信息，我们就开始实际处理错误的过程。为此，错误服务依次调用在 `error_handlers` 注册表中注册的所有函数，直到其中一个函数返回真值，表示该错误已被处理。在此之后，如果没有对错误事件调用 `preventDefault`，并且错误服务能够在包装的错误对象上添加堆栈跟踪，则错误服务会对错误事件调用 `preventDefault`，并在控制台中记录堆栈跟踪。这是因为，如前所述，某些浏览器无法正确显示错误链，而事件的默认行为是浏览器记录错误，因此我们简单地覆盖该行为，以记录更完整的堆栈跟踪。如果错误服务无法收集有关抛出错误的堆栈跟踪信息，则我们不会调用 `preventDefault`。这可能发生在抛出非错误值时：字符串、未定义或其他随机对象。在这些情况下，浏览器会记录错误堆栈跟踪本身，因为它具有该信息，但不会将其暴露给JS代码。

`error_handlers` 注册表
-----------------------------

`error_handlers` 注册表是扩展JS框架处理“通用”错误的主要方式。在此上下文中，通用错误是指可以在许多地方发生但应该统一处理的错误。一些示例：

- UserError：当用户尝试执行Python代码认为因业务原因无效的操作时，Python代码会引发UserError，rpc函数在JavaScript中抛出相应的错误。这种情况可能在任何RPC中发生，我们不希望开发人员在所有这些地方都显式处理这种错误，我们希望在所有地方发生相同的行为：停止当前执行的代码（通过throw实现），并显示一条向用户解释出错的对话框。
- AccessError：与用户错误的推理相同：它可以在任何地方发生，应该以相同的方式显示，无论发生在哪里。
- LostConnection：推理相同。

在Owl组件中抛出错误
-------------------------------------

注册或修改Owl组件是扩展Web客户端功能的主要方式。因此，抛出的绝大多数错误都是以某种方式从Owl组件抛出的。有几种可能的场景：

- 在组件的设置或渲染期间抛出
- 在生命周期钩子内抛出
- 从事件处理程序抛出

从事件处理程序或直接或间接由事件处理程序调用的函数或方法中抛出错误意味着Owl的代码或JS框架的代码不在调用堆栈中。如果您不捕获错误，它会直接落入错误服务中。

当在组件的设置或渲染期间抛出错误时，Owl会捕获该错误并向上遍历组件层次结构，允许注册了 `onError` 钩子的组件尝试处理该错误。如果没有任何组件处理错误，Owl会销毁应用程序，因为它很可能处于损坏状态。

.. seealso::
  `Owl文档中的错误处理 <https://github.com/odoo/owl/blob/master/doc/reference/error_handling.md>`_

在Odoo内部，有些地方我们不希望在发生错误的情况下整个应用程序崩溃，因此框架在一些地方使用 `onError` 钩子。操作服务将操作和视图包装在一个处理错误的组件中。如果客户端操作或视图在渲染期间抛出错误，它会尝试返回到上一个操作。错误被分派到错误服务，以便无论如何都可以显示错误对话框。在框架调用“用户”代码的绝大多数地方使用类似的策略：我们通常停止显示故障组件并显示错误对话框。

在钩子回调函数内抛出错误时，Owl创建一个新的 *Error object*，其中包含有关注册钩子的位置的信息，并将其原因设置为原始抛出的值。这是因为原始错误的堆栈跟踪不包含有关哪个组件注册了该钩子以及在哪里的信息，它仅包含调用了该钩子的内容的信息。由于钩子是由Owl代码调用的，因此大多数这些信息通常对开发人员不太有用，但知道钩子注册的位置和由哪个组件注册是非常有用的。

当阅读提到“OwlError：以下错误发生在 <hookName>”的错误时，请确保阅读复合堆栈跟踪的两个部分：

.. code-block::
  :emphasize-lines: 4,12

  Error: The following error occurred in onMounted: "My error"
    at wrapError
    at onMounted
    at MyComponent.setup
    at new ComponentNode
    at Root.template
    at MountFiber._render
    at MountFiber.render
    at ComponentNode.initiateRender

  Caused by: Error: My error
    at ParentComponent.someMethod
    at MountFiber.complete
    at Scheduler.processFiber
    at Scheduler.processTasks

第一条突出显示的行告诉您注册了 `onMounted` 钩子的组件，而第二条突出显示的行则告诉您哪个函数抛出了错误。在这种情况下，子组件正在调用作为道具从其父组件接收的函数，而该函数是父组件的一个方法。这两条信息都可能有用，因为该方法可能是由子组件错误调用的（或在不应该调用的生命周期点调用），但也可能是父组件的方法包含错误。

将错误标记为已处理
-------------------------

在前面的部分中，我们讨论了注册错误处理程序的两种方式：一种是将它们添加到 `error_handlers` 注册表中，另一种是使用Owl中的 `onError` 钩子。在这两种情况下，处理程序必须决定是否将错误标记为已处理。

`onError`
~~~~~~~~~

在Owl中使用 `onError` 注册的处理程序将被视为已处理，除非您重新抛出它。您在 `onError` 中所做的任何事情都可能导致用户界面与应用程序状态不同步，因为错误阻止了Owl完成某些工作。如果您无法处理错误，则应重新抛出它，让其余代码处理它。

如果您不重新抛出错误，则需要更改某些状态，以便应用程序可以以无错误的方式重新渲染。此时，如果您不重新抛出错误，则不会报告该错误。在某些情况下，这种情况是可以接受的，但在大多数情况下，您应该做的是在一个单独的调用堆栈中调度此错误。最简单的方法是创建一个被拒绝的Promise，并将错误作为其拒绝原因：

.. code-block:: javascript

    import { Component, onError } from "@odoo/owl";
    class MyComponent extends Component {
      setup() {
        onError((error) => {
          // 此方法的实现留给读者
          this.removeErroringSubcomponent();
          Promise.reject(error); // 创建一个被拒绝的Promise，而不将其传递到任何地方
        });
      }
    }

这会导致浏览器在window上调度一个 `unhandledrejection` 事件，从而导致JS框架的错误处理启动，在大多数情况下通过打开一个包含错误信息的对话框来处理错误。这是操作服务和对话服务在停止呈现损坏的操作或对话框时使用的策略，同时仍然报告错误。

`error_handlers` 注册表中的处理程序
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

添加到 `error_handlers` 注册表的处理程序可以以两种方式将错误标记为已处理，含义不同。

第一种方法是处理程序可以返回真值，这意味着处理程序已处理该错误并采取了一些措施，因为它收到的错误与它能够处理的错误类型相匹配。这通常意味着它已打开一个对话框或通知以警告用户有关错误。这阻止错误服务调用具有较高序列号的后续处理程序。

另一种方法是对错误事件调用 `preventDefault`：这有不同的含义。在决定能够处理收到的错误后，处理程序需要决定该错误是否是正常操作期间允许发生的错误，如果是，则应调用 `preventDefault`。这通常适用于业务错误，例如访问错误或验证错误：用户可以与其他用户共享对其没有访问权限的资源的链接，用户可以尝试保存处于无效状态的记录。

在不调用 `preventDefault` 的情况下，该错误被视为意外，在测试期间发生此类事件会导致测试失败，因为它通常表明代码存在缺陷。

避免尽可能抛出错误
=========================================

如上所述，有许多原因可以避免抛出错误。

错误是昂贵的
--------------------

由于错误需要展开调用堆栈并收集信息，因此抛出错误的速度较慢。此外，JavaScript运行时通常会优化代码，假设异常是罕见的，因此在发生异常时，通常会将代码编译为假设它不抛出，并在发生时回退到较慢的代码路径。

抛出错误会使调试变得更加困难
--------------------------------------

JavaScript调试器（例如，Chrome和Firefox开发者工具中包含的调试器）具有允许您在抛出异常时暂停执行的功能。您还可以选择只在捕获的异常上暂停，还是在捕获和未捕获的异常上都暂停。

当您在Owl或JavaScript框架调用的代码内部抛出错误（例如，在字段、视图、操作、组件等中）时，由于它们管理资源，它们需要捕获错误并检查它们，以决定错误是否是关键的，应用程序是否应崩溃，还是错误是预期的，应该以特定的方式处理。

因此，几乎所有在JavaScript代码中抛出的错误都会在某个时刻被捕获，尽管它们可能会在无法处理的情况下被重新抛出，但这意味着在Odoo中使用“在未捕获异常处暂停”功能实际上是无用的，因为它总是会在JavaScript框架代码内暂停，而不是在原始抛出错误的代码附近。

但是，“在捕获异常处暂停”功能仍然非常有用，因为它在每个抛出语句和被拒绝的承诺上暂停执行。这使得开发人员可以在发生异常情况时停止并检查执行上下文。

然而，这仅在假设异常很少抛出时才成立。如果异常经常抛出，页面中的任何操作都可能导致调试器停止执行，开发人员可能需要在许多“例行”异常中逐步查找，才能找到他们实际感兴趣的异常场景。在某些情况下，由于在调试器中单击播放按钮会将焦点移出页面，可能会使感兴趣的抛出场景无法访问，而必须使用键盘快捷键恢复执行，这会导致开发人员体验不佳。

抛出会中断代码的正常流
-------------------------------------------

抛出错误时，看似应该始终执行的代码可能会被跳过，这可能导致许多微妙的错误和内存泄漏。以下是一个简单的示例：

.. code-block:: javascript

    eventTarget.addEventListener("event", handler);
    someFunction();
    eventTarget.removeEventListener("event", handler);

在此代码块中，我们向事件目标添加一个事件侦听器，然后调用一个可能在该目标上分发事件的函数。调用该函数后，我们删除事件侦听器。

如果 `someFunction` 抛出，事件侦听器将永远不会被移除。这意味着与该事件侦听器相关的内存实际上泄漏了，并且在事件目标被释放之前将永远不会被释放。

除了内存泄漏之外，仍然存在事件侦听器，这意味着它可能会在调用 `someFunction` 以外的原因分发的事件中被调用。这是一个错误。

为了避免这种情况，需要将调用包装在 `try` 块中，并将清理放在 `finally` 块中：

.. code-block:: javascript

    eventTarget.addEventListener("event", handler);
    try {
      someFunction();
    } finally {
      eventTarget.removeEventListener("event", handler);
    }

虽然这样避免了上述问题，但这不仅需要更多的代码，还需要知道该函数可能抛出。如果要避免在可能抛出的地方包装整个代码块在 `try/finally` 块中，这将是不可管理的。

捕获错误
===============

有时，您需要调用已知会抛出错误的代码，并希望处理其中的一些错误。需要牢记两个重要事项：

- 重新抛出不是您预期的错误类型的错误。这通常通过 `instanceof` 检查完成。
- 将try块保持尽可能小。这可以避免捕获不是您尝试捕获的错误。通常，try块应该仅包含 *一个* 语句。

.. code-block:: javascript

    let someVal;
    try {
      someVal = someFunction();
      // 不要在此处开始使用 someVal。
    } catch (e) {
      if (!(e instanceof MyError)) {
        throw e;
      }
      someVal = null;
    }
    // 在此处开始使用 someVal

虽然这在try/catch中是简单的，但在使用 `Promise.catch` 时，实际上更容易意外地将更大部分的代码包装在捕获块中：

.. code-block:: javascript

    someFunction().then((someVal) => {
      // 使用 someVal
    }).catch((e) => {
      if (!(e instanceof MyError)) {
        throw e;
      }
      return null;
    });

在此示例中，catch块实际上捕获整个then块中的错误，这不是我们想要的。在此特定示例中，由于我们根据错误类型进行了适当的过滤，因此我们没有吞下错误，但您可以看到，如果我们预期一个单一的错误类型，而决定不进行 `instanceof` 检查，这可能更容易。请注意，与前面的示例不同，此处的null并未通过使用 `someVal` 的代码路径传递。因此，为了避免这种情况，catch子句通常应尽可能接近可能抛出承诺的地方，并且应始终根据错误类型进行过滤。

无错误控制流
=======================

由于上述原因，您应该避免抛出错误来处理常规事务，尤其是控制流。如果某个函数预计无法完成其工作，应该在不抛出异常的情况下传达该失败。考虑以下示例代码：

.. code-block:: javascript

    let someVal;
    try {
      someVal = someFunction();
    } catch (e) {
      if (!(e instanceof MyError)) {
        throw e;
      }
      someVal = null;
    }

上述代码存在许多问题。首先，由于我们希望变量 `someVal` 在 `try/catch` 块后可访问，因此需要在该块之前声明它，并且它不能是 `const`，因为它需要在初始化后赋值。这使得稍后代码的可读性受到影响，因为您现在需要留意此变量可能在代码中的其他地方被重新赋值。

其次，当我们捕获错误时，必须检查该错误是否确实是我们预期捕获的错误类型，如果不是，则重新抛出错误。如果我们不这样做，可能会吞下未被处理的错误，无法正确报告它们，例如，我们可能会捕获并吞下一个TypeError，如果底层代码尝试访问 `null` 或 `undefined` 的属性。

最后，这不仅非常冗长，而且容易出错：如果您忘记添加 `try/catch`，则可能最终会得到回溯。如果您添加了 `try/catch` 块但忘记重新抛出意外错误，则可能吞下无关的错误。如果您希望避免重新分配变量，则可能会将使用变量的整个块移到 `try` 块内。这样做，您可能会将要捕获的错误与意外的错误一起捕获，甚至可能导致嵌套的 `try/catch` 块。最后，这使得识别哪个行实际上期望抛出错误变得更加困难。

以下部分概述了一些可替代的方式，而不是抛出错误。

返回 `null` 或 `undefined`
----------------------------

如果函数返回原始值或对象，您通常可以使用 `null` 或 `undefined` 来指示无法完成其预期工作。这在大多数情况下是足够的。代码看起来像这样：

.. code-block:: javascript

    const someVal = someFunction();
    // 进一步
    if (someVal !== null) { /* 做点什么 */ }

如您所见，这简单得多。

返回一个对象或数组
-------------------------

在某些情况下，`null` 或 `undefined` 的值是预期的返回值。在这些情况下，您可以返回一个包装对象或一个包含返回值或错误的两个元素的数组：

.. code-block:: javascript

    const { val: someVal, err } = someFunction();
    if (err) {
      return;
    }
    // 用于已知有效的someVal做一些事情

或者使用数组：

.. code-block:: javascript

    const [err, someVal] = someFunction();
    if (err) {
      return;
    }
    // 用于已知有效的someVal做一些事情

.. note::

    在使用两个元素的数组时，建议让错误成为第一个元素，以便在解构时更难被忽略。您需要显式添加占位符或逗号以跳过错误，而如果错误是第二个元素，则很容易只解构第一个元素并错误地忽略错误。

何时抛出错误
====================

前面的部分给出了许多避免抛出错误的良好理由，那么在什么情况下抛出错误是最佳选择？

- 在许多地方都可以发生的通用错误，但应该在所有地方以相同方式处理；例如，访问错误可以发生在几乎任何RPC上，我们总是希望显示有关用户为何无法访问的信息。
- 某些预条件必须始终满足某些操作；例如，无法呈现视图，因为域无效。这类错误通常不打算在任何地方捕获，表明代码不正确或数据已损坏。抛出强制框架退出并阻止在损坏状态下操作。
- 当递归遍历某个深层数据结构时，抛出错误比在许多调用级别手动测试错误并向前传递它们更符合人体工学，尽管这种情况在实践中非常罕见，并且需要权衡与本文提到的所有缺点之间的关系。
