测试 Odoo
============

有很多方法可以测试应用程序。在 Odoo 中，我们有三种测试类型：

- Python 单元测试（参见 `测试 Python 代码`_）：用于测试模型业务逻辑
- JS 单元测试（参见 `测试 JS 代码`_）：用于在隔离状态下测试 JavaScript 代码
- Tours（参见 `集成测试`_）：模拟真实场景，确保 Python 和 JavaScript 部分正常交互。

.. _testing/python:

测试 Python 代码
===================

Odoo 提供对使用 `Python 的 unittest 库 <https://docs.python.org/3/library/unittest.html>`_ 进行模块测试的支持。

要编写测试，只需在模块中定义一个 ``tests`` 子包，它将自动检查测试模块。测试模块的名称应以 ``test_`` 开头，并应从 ``tests/__init__.py`` 导入，例如：

.. code-block:: text

    your_module
    ├── ...
    ├── tests
    |   ├── __init__.py
    |   ├── test_bar.py
    |   └── test_foo.py

并且 ``__init__.py`` 包含::

    from . import test_foo, test_bar

.. warning::

    未从 ``tests/__init__.py`` 导入的测试模块将不会被运行。

测试运行器将简单地运行任何测试用例，如官方 `unittest 文档`_ 所描述，但 Odoo 提供了一些与测试 Odoo 内容（主要是模块）相关的实用程序和助手。

.. autoclass:: odoo.tests.TransactionCase
    :members: browse_ref, ref

.. autoclass:: odoo.tests.SingleTransactionCase
    :members: browse_ref, ref

.. autoclass:: odoo.tests.HttpCase
    :members: browse_ref, ref, url_open, browser_js

.. autofunction:: odoo.tests.tagged

默认情况下，测试在对应模块安装后自动运行。测试用例还可以配置为在所有模块安装后运行，而不是在模块安装后立即运行：

  # coding: utf-8
  from odoo.tests import HttpCase, tagged

  # 此测试仅在所有模块安装后执行。
  @tagged('-at_install', 'post_install')
  class WebsiteVisitorTests(HttpCase):
    def test_create_visitor_on_tracked_page(self):
        Page = self.env['website.page']

最常见的情况是使用 :class:`~odoo.tests.TransactionCase` 并在每个方法中测试模型的属性：

    class TestModelA(TransactionCase):
        def test_some_action(self):
            record = self.env['model.a'].create({'field': 'value'})
            record.some_action()
            self.assertEqual(
                record.field,
                expected_field_value)

        # 其他测试...

.. note::

    测试方法必须以 ``test_`` 开头。

.. autoclass:: odoo.tests.Form
    :members:

.. autoclass:: odoo.tests.M2MProxy
    :members: add, remove, clear

.. autoclass:: odoo.tests.O2MProxy
    :members: new, edit, remove

运行测试
-------------

如果启动 Odoo 服务器时启用了 :option:`--test-enable <odoo-bin --test-enable>`，测试将在安装或更新模块时自动运行。

.. _unittest 文档: https://docs.python.org/3/library/unittest.html

.. _developer/reference/testing/selection:

测试选择
--------------

在 Odoo 中，Python 测试可以标记，以便在运行测试时便于选择。

:class:`odoo.tests.BaseCase` 的子类（通常通过 :class:`~odoo.tests.TransactionCase` 或 :class:`~odoo.tests.HttpCase`）默认会自动标记为 ``standard`` 和 ``at_install``。

调用
~~~~~~~~~~

:option:`--test-tags <odoo-bin --test-tags>` 可用于选择/过滤要在命令行上运行的测试。这意味着 :option:`--test-enable <odoo-bin --test-enable>` 是隐含的，因此在使用 :option:`--test-tags <odoo-bin --test-tags>` 时不必指定 :option:`--test-enable <odoo-bin --test-enable>`。

此选项默认值为 ``+standard``，这意味着标记为 ``standard`` 的测试（显式或隐式）将在启动 Odoo 时默认运行，并使用 :option:`--test-enable <odoo-bin --test-enable>`。

在编写测试时，可以在 **测试类** 上使用 :func:`~odoo.tests.tagged` 装饰器来添加或移除标签。

装饰器的参数是标签名称，作为字符串。

.. danger:: :func:`~odoo.tests.tagged` 是一个类装饰器，对函数或方法没有影响。

标签可以用负号（``-``）前缀，以 *移除* 标签，而不是添加或选择它们，例如，如果您不希望测试默认执行，可以移除 ``standard`` 标签：

.. code-block:: python

    from odoo.tests import TransactionCase, tagged

    @tagged('-standard', 'nice')
    class NiceTest(TransactionCase):
        ...

此测试将不会被默认选择，要运行它，必须显式选择相关标签：

.. code-block:: console

    $ odoo-bin --test-tags nice

请注意，仅会执行标记为 ``nice`` 的测试。要运行 *both* ``nice`` 和 ``standard`` 测试，请在 :option:`--test-tags <odoo-bin --test-tags>` 中提供多个值：在命令行上，值是 *累加的*（选择具有 *任何* 指定标签的所有测试）。

.. code-block:: console

    $ odoo-bin --test-tags nice,standard

配置开关参数也接受 ``+`` 和 ``-`` 前缀。``+`` 前缀是隐含的，因此完全可选。``-``（负号）前缀用于取消选择带有前缀标签的测试，即使这些测试被其他指定标签选择，例如，如果有 ``standard`` 测试，同时标记为 ``slow``，您可以运行所有标准测试 *除了* 慢速测试：

.. code-block:: console

    $ odoo-bin --test-tags 'standard,-slow'

当您编写一个不继承 :class:`~odoo.tests.BaseCase` 的测试时，该测试将没有默认标签，您必须显式添加它们才能将测试包括在默认测试套件中。这在使用简单的 ``unittest.TestCase`` 时是一个常见问题，因为它们不会被运行：

.. code-block:: python

    import unittest
    from odoo.tests import tagged

    @tagged('standard', 'at_install')
    class SmallTest(unittest.TestCase):
        ...

除了标签，您还可以指定要测试的特定模块、类或函数。 :option:`--test-tags <odoo-bin --test-tags>` 接受的格式的完整语法是：

.. code-block:: text

    [-][tag][/module][:class][.method]

因此，如果您想测试 `stock_account` 模块，可以使用：

    .. code-block:: console

        $ odoo-bin --test-tags /stock_account

如果您想测试具有唯一名称的特定函数，可以直接指定：

    .. code-block:: console

        $ odoo-bin --test-tags .test_supplier_invoice_forwarded_by_internal_user_without_supplier

这等同于

    .. code-block:: console

        $ odoo-bin --test-tags /account:TestAccountIncomingSupplierInvoice.test_supplier_invoice_forwarded_by_internal_user_without_supplier

如果测试的名称不明确。多个模块、类和函数可以同时用逗号分隔，如同常规标签一样。

.. _reference/testing/tags:
特殊标签
~~~~~~~~~~~~

- ``standard``: 所有继承自 :class:`~odoo.tests.BaseCase` 的 Odoo 测试都隐式标记为 standard。 :option:`--test-tags <odoo-bin --test-tags>` 默认也为 ``standard``。

  这意味着未标记的测试将在启用测试时默认执行。
- ``at_install``: 表示测试将在模块安装后立即执行，并且在其他模块安装之前执行。这是一个默认的隐式标签。
- ``post_install``: 表示测试将在所有模块安装后执行。这通常是您希望用于 HttpCase 测试的情况。

  请注意，这与 ``at_install`` *不是互斥* 的，但是通常情况下，您不希望同时使用这两者，因此 ``post_install`` 通常与 ``-at_install`` 配对使用时标记测试类。

示例
~~~~~~~~

.. important::

    测试仅会在已安装的模块中执行。如果您从干净的数据库开始，您需要至少安装一次模块，并使用 :option:`-i <odoo-bin -i>` 开关。之后就不再需要，除非您需要升级模块，此时可以使用 :option:`-u <odoo-bin -u>`。为了简洁起见，下面的示例中未指定这些开关。

仅运行销售模块中的测试：

.. code-block:: console

    $ odoo-bin --test-tags /sale

运行销售模块中的测试，但不包括标记为慢的测试：

.. code-block:: console

    $ odoo-bin --test-tags '/sale,-slow'

仅运行库存模块中的测试或标记为慢的测试：

.. code-block:: console

    $ odoo-bin --test-tags '-standard, slow, /stock'

.. note:: ``-standard`` 是隐式的（不需要），并且为了清晰起见而存在。

测试 JS 代码
===============

测试复杂系统是防止回归和保证某些基本功能正常工作的重要保障。由于 Odoo 的 JavaScript 代码库并不简单，因此有必要对其进行测试。在本节中，我们将讨论在隔离状态下测试 JS 代码的实践：这些测试保持在浏览器中，不应到达服务器。

.. _reference/testing/qunit:

Qunit 测试套件
----------------

Odoo 框架使用 QUnit_ 库测试框架作为测试运行器。QUnit 定义了 *测试* 和 *模块*（一组相关测试）的概念，并为我们提供了一个基于 Web 的界面来执行测试。

例如，这里是一个 pyUtils 测试的示例：

.. code-block:: javascript

    QUnit.module('py_utils');

    QUnit.test('simple arithmetic', function (assert) {
        assert.expect(2);

        var result = pyUtils.py_eval("1 + 2");
        assert.strictEqual(result, 3, "should properly evaluate sum");
        result = pyUtils.py_eval("42 % 5");
        assert.strictEqual(result, 2, "should properly evaluate modulo operator");
    });

运行测试套件的主要方法是让 Odoo 服务器运行，然后在 Web 浏览器中导航到 ``/web/tests``。然后，测试套件将由 Web 浏览器的 JavaScript 引擎执行。

.. image:: testing/tests.png
    :align: center

Web UI 有许多有用的功能：可以仅运行某些子模块，或过滤匹配某个字符串的测试。它可以显示每个断言，无论是失败还是通过，可以重新运行特定测试等。

.. warning::

    当测试套件正在运行时，请确保：

    - 浏览器窗口处于聚焦状态，
    - 没有缩放。它需要保持在 100% 的缩放级别。

    如果不是这样，某些测试将失败，且没有适当的解释。

测试基础设施
----------------------

以下是测试基础设施中最重要部分的高级概述：

- 有一个名为 `web.qunit_suite`_ 的资产包。此包包含主要代码（公共资产 + 后端资产）、一些库、QUnit 测试运行器和下面列出的测试包。

- 一个名为 `web.tests_assets`_ 的包包括测试套件所需的大部分资产和工具：自定义 QUnit 断言、测试助手、延迟加载的资产等。

- 另一个资产包 `web.qunit_suite_tests`_ 包含所有测试脚本。这通常是添加测试文件到套件的地方。

- 在 Web 中有一个 `controller`_，映射到路由 */web/tests*。该控制器仅渲染 *web.qunit_suite* 模板。

- 要执行测试，只需将浏览器指向路由 */web/tests*。在这种情况下，浏览器将下载所有资产，QUnit 将接管。

- 在 `qunit_config.js`_ 中有一些代码，它在控制台中记录测试通过或失败时的一些信息。

- 我们希望运行机器人也能运行这些测试，因此在 `test_js.py`_ 中有一个测试，它简单地生成一个浏览器并指向 *web/tests* URL。请注意，browser_js 方法生成的是 Chrome 无头实例。
模块化和测试
----------------------

由于 Odoo 的设计方式，任何插件都可以修改系统其他部分的行为。例如，*voip* 插件可以修改 *FieldPhone* 小部件以使用额外功能。从测试系统的角度来看，这并不是一个好的做法，因为这意味着插件的测试将在安装 voip 插件时失败（请注意，runbot 在所有插件安装时运行测试）。

同时，我们的测试系统也很出色，因为它可以检测到其他模块何时破坏某些核心功能。对此问题没有完整的解决方案。现在，我们逐个案例解决此问题。

通常，修改其他行为并不是一个好主意。就我们的 voip 示例而言，当然更干净的做法是添加一个新的 *FieldVOIPPhone* 小部件，并修改需要它的几个视图。这样，*FieldPhone* 小部件就不会受到影响，两个小部件都可以进行测试。

添加新测试用例
----------------------

假设我们正在维护一个插件 *my_addon*，并且我们想要为某些 JavaScript 代码（例如位于 *my_addon.utils* 中的实用函数 myFunction）添加测试。添加新测试用例的过程如下：

1. 创建一个新文件 *my_addon/static/tests/utils_tests.js*。该文件包含添加 QUnit 模块 *my_addon > utils* 的基本代码。

    .. code-block:: javascript

        odoo.define('my_addon.utils_tests', function (require) {
        "use strict";

        var utils = require('my_addon.utils');

        QUnit.module('my_addon', {}, function () {

            QUnit.module('utils');

        });
        });

2. 在 *my_addon/assets.xml* 中，将文件添加到主测试资产中：

    .. code-block:: xml

        <?xml version="1.0" encoding="utf-8"?>
        <odoo>
            <template id="qunit_suite_tests" name="my addon tests" inherit_id="web.qunit_suite_tests">
                <xpath expr="//script[last()]" position="after">
                    <script type="text/javascript" src="/my_addon/static/tests/utils_tests.js"/>
                </xpath>
            </template>
        </odoo>

3. 重启服务器并更新 *my_addon*，或从界面执行此操作（以确保加载新的测试文件）。

4. 在 *utils* 子测试套件的定义后添加测试用例：

    .. code-block:: javascript

        QUnit.test("some test case that we want to test", function (assert) {
            assert.expect(1);

            var result = utils.myFunction(someArgument);
            assert.strictEqual(result, expectedResult);
        });

5. 访问 */web/tests/* 确保测试被执行。

辅助函数和专业断言
---------------------------

没有帮助，测试 Odoo 的某些部分是相当困难的。特别是，视图比较棘手，因为它们与服务器通信，并且可能执行许多 RPC，这需要被模拟。这就是我们开发一些专业辅助函数的原因，这些函数位于 `test_utils.js`_。

- 模拟测试函数：这些函数帮助设置测试环境。最重要的用例是模拟 Odoo 服务器给出的答案。这些函数使用 `mock server`_。这是一个 JavaScript 类，模拟对最常见模型方法的答案：read、search_read、nameget 等。

- DOM 辅助工具：用于模拟某些特定目标上的事件/操作。例如，testUtils.dom.click 在目标上执行点击。请注意，这比手动执行更安全，因为它还检查目标是否存在且可见。

- 创建辅助工具：它们可能是 `test_utils.js`_ 导出的最重要的函数。这些辅助工具对于创建一个小部件、带有模拟环境以及尽可能多地模拟真实条件的细节非常有用。最重要的当然是 `createView`_。

- `qunit 断言`_: QUnit 可以通过专业断言进行扩展。对于 Odoo，我们经常测试一些 DOM 属性。这就是我们制作一些断言来帮助实现这一目标的原因。例如，*containsOnce* 断言接受一个小部件/jQuery/HtmlElement 和一个选择器，然后检查目标是否准确包含 CSS 选择器的匹配项。

例如，使用这些辅助工具，简单表单测试的示例可能如下所示：

.. code-block:: javascript

    QUnit.test('simple group rendering', function (assert) {
        assert.expect(1);

        var form = testUtils.createView({
            View: FormView,
            model: 'partner',
            data: this.data,
            arch: '<form string="Partners">' +
                    '<group>' +
                        '<field name="foo"/>' +
                    '</group>' +
                '</form>',
            res_id: 1,
        });

        assert.containsOnce(form, 'table.o_inner_group');

        form.destroy();
    });

注意使用了 testUtils.createView 辅助工具和 containsOnce 断言。此外，表单控制器在测试结束时已被正确销毁。
最佳实践
--------------

没有特定顺序：

- 所有测试文件应添加到 *some_addon/static/tests/* 中。
- 对于错误修复，确保在没有错误修复的情况下测试失败，并在有修复的情况下测试通过。这确保它确实有效。
- 尽量减少测试所需的代码量。
- 通常，两个小测试比一个大测试更好。较小的测试更容易理解和修复。
- 始终在测试后进行清理。例如，如果你的测试实例化了一个小部件，它应该在结束时销毁。
- 不必实现完整和完整的代码覆盖率。但添加一些测试会有很大帮助：这可以确保你的代码不会完全损坏，并且每当修复错误时，向现有测试套件中添加测试会容易得多。
- 如果你想检查一些负断言（例如，某个 HtmlElement 不具有特定的 css 类），那么尝试在同一测试中添加正断言（例如，通过执行一个改变状态的操作）。这将有助于避免测试在未来变得无效（例如，如果 css 类被更改）。

提示
----

- 仅运行一个测试：你可以（临时地！）将 *QUnit.test(...)*
  定义更改为 *QUnit.only(...)*。这对于确保 QUnit 仅运行这个特定测试非常有用。
- 调试标志：大多数创建实用函数都有调试模式（通过 debug: true 参数激活）。在这种情况下，目标小部件将放置在 DOM 中，而不是隐藏的 qunit 特定工具中，并且会记录更多信息。例如，所有模拟的网络通信将在控制台中可用。
- 在处理失败的测试时，通常会添加调试标志，然后注释掉测试的结尾（尤其是 destroy 调用）。这样，可以直接查看小部件的状态，甚至更好，可以通过单击/与其交互来操控小部件。

集成测试
===================

单独测试 Python 代码和 JS 代码非常有用，但这并不能证明 Web 客户端和服务器能够协同工作。为了做到这一点，我们可以编写另一种类型的测试：游览。游览是一些有趣业务流程的迷你场景。它描述了应遵循的步骤顺序。测试运行程序将创建一个 PhantomJs 浏览器，指向正确的 URL，并根据场景模拟点击和输入。

编写测试游览
-------------------

结构
~~~~~~~~~

要为 `your_module` 编写测试游览，请首先创建所需的文件：

.. code-block:: text

    your_module
    ├── ...
    ├── static
    |   └── tests
    |       └── tours
    |           └── your_tour.js
    ├── tests
    |   ├── __init__.py
    |   └── test_calling_the_tour.py
    └── __manifest__.py

然后，你可以：

- 更新 :file:`__manifest__.py` 以将 :file:`your_tour.js` 添加到资产中。

  .. code-block:: python

     'assets': {
         'web.assets_tests': [
             'your_module/static/tests/tours/your_tour.js',
         ],
     },

- 更新 :file:`tests` 文件夹中的 :file:`__init__.py` 以导入 :file:`test_calling_the_tour`。

.. seealso::
   - :ref:`Assets Bundle <reference/assets_bundle>`
   - :ref:`testing/python`

JavaScript
~~~~~~~~~~

#. 通过注册设置你的游览。

   .. code-block:: javascript

      /** @odoo-module **/
      import tour from 'web_tour.tour';
      tour.register('rental_product_configurator_tour', {
          url: '/web',  // 在这里，你可以指定任何其他起始 URL
          test: true,
      }, [
          // 你的步骤序列
      ]);

#. 添加你想要的任何步骤。

每个步骤至少包含一个触发器。你可以使用预定义步骤
<{GITHUB_PATH}/addons/web_tour/static/src/tour_service/tour_utils.js#L426} 或者编写自己的个性化步骤。

以下是一些步骤示例：

.. example::

   .. code-block:: javascript

      // 第一步
      tour.stepUtils.showAppsMenuItem(),
      // 第二步
      {
          trigger: '.o_app[data-menu-xmlid="your_module.maybe_your_module_menu_root"]',
          edition: 'community'  // 可选
      }, {
          // 第三步
      },

.. example::

   .. code-block:: javascript

      {
          trigger: '.js_product:has(strong:contains(Chair floor protection)) .js_add',
          extra_trigger: '.oe_advanced_configurator_modal',  // 确保我们在向导中
      },

.. example::

   .. code-block:: javascript

      {
          trigger: 'a:contains("Add a product")',
          // 额外触发器以确保在尝试添加另一行之前添加一行
          extra_trigger: '.o_field_many2one[name="product_template_id"] .o_external_button',
      },

以下是你个性化步骤的一些可能参数：

- **trigger**：要在其上“运行”某个操作的选择器/元素。游览将在元素存在并可见之前等待该元素，然后在其上“运行”该操作。
- **extra_trigger**：用于步骤“运行”的可选辅助条件。将像 **trigger** 元素一样等待，但操作不会在额外触发器上运行。

  适合用作前置条件，或两个不同且无关的条件。
- **run**：要在 *trigger* 元素上执行的操作。

  默认情况下，尝试将 **trigger** 的内容设置为 ``Text``（如果是 ``input``），否则单击它。

  该操作也可以是：

  - 一个函数，同步，在触发器的 ``Tip`` 上执行，作为上下文（``this``）并传入操作助手。
  - 要在触发器元素上运行的操作助手之一的名称：

    .. rst-class:: o-definition-list

    ``click``
        单击元素，执行所有相关的中间事件。
    :samp:`text {content}`
        单击（聚焦）元素，然后将 ``content`` 设置为元素的值（如果是输入），选项（如果是选择），或内容。
    ``dblclick``、``tripleclick``
        与 ``click`` 相同，但重复多次。
    ``clicknoleave``
        默认情况下，``click``（及其变体）将触发触发器元素的“退出”事件（mouseout、mouseleave）。此助手将抑制这些事件（注意：对其他元素的进一步单击不会隐式触发这些事件）。
    ``text_blur``
        类似于 ``text``，但跟随 ``focusout`` 和 ``blur`` 事件。
    :samp:`drag_and_drop {target}`
       模拟将 **trigger** 元素拖动到 ``target``。
- **edition**：可选，

  - 如果不指定版本，步骤将在社区版和企业版中都是活动的。
  - 有时，企业版或社区版的步骤将有所不同。你可以为企业版和社区版编写两个步骤。
  - 通常，你希望为使用主菜单的步骤指定版本，因为主菜单在社区版和企业版中不同。
- **position**：可选，“top”、“right”、“bottom”或“left”。运行交互式游览时，工具提示相对于 **target** 的位置。
- **content**：可选但建议，交互式游览中的工具提示内容，也记录到控制台，因此非常有用以跟踪和调试自动化游览。
- **auto**：游览管理器在交互式时是否应等待用户执行操作，默认为 ``false``。
- **in_modal**：如果设置，**trigger** 元素只会在顶部模态窗口中搜索，默认为 ``false``。
- **timeout**：直到步骤可以 ``run`` 的等待时间，以毫秒为单位，默认为 10000（10 秒）。

.. important::

   游览的最后一步（们）应始终将客户端返回到“稳定”状态（例如，没有正在进行的编辑）并确保所有副作用（网络请求）已完成，以避免竞争条件或在清理过程中出现错误。

.. seealso::
   - `jQuery 文档关于 find <https://api.jquery.com/find/>`_
Python
~~~~~~

要从 Python 测试启动游览，请使类继承自 :class:`~odoo.tests.HTTPCase`，并调用 `start_tour`：

.. code-block:: python

   def test_your_test(self):
       # 可选的设置
       self.start_tour("/web", 'your_module.your_tour_name', login="admin")
       # 可选的验证

调试技巧
--------------

观察浏览器中的游览
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

有两种方法，各有利弊：

``watch=True``
**************

当通过测试套件在本地运行游览时，可以将 ``watch=True`` 参数添加到 ``browser_js`` 或 ``start_tour`` 调用中：

    self.start_tour("/web", code, watch=True)

这将自动打开一个 Chrome 窗口，其中运行游览。

**优点**
  - 如果游览具有 Python 设置/周围代码或多个步骤，则始终有效
  - 完全自动运行（只需选择启动游览的测试）
  - 事务性（*应该*始终可以多次运行）

**缺点**
  - 仅在本地有效
  - 仅在测试/游览可以正确本地运行时有效

通过浏览器运行
***************

游览也可以通过浏览器 UI 启动，方法是在 JavaScript 控制台中调用

.. code-block:: javascript

    odoo.startTour(tour_name);

或者通过在 URL 中设置 ``?debug=tests`` 启用 :ref:`测试模式 <frontend/framework/tests_debug_mode>`，然后在调试菜单中选择 **Start Tour** 并选择一个游览：

.. image:: testing/tours.png
   :align: center

**优点**
  - 更容易运行
  - 可以在生产或测试站点上使用，而不仅仅是本地实例
  - 允许在“入职”模式下运行（手动步骤）

**缺点**
  - 对于涉及 Python 设置的测试游览更难使用
  - 可能由于游览副作用无法多次工作

.. tip::

   使用此方法可以观察或与需要 Python 设置的游览进行交互：

   - 在相关游览开始之前添加 *python* 断点
     （``start_tour`` 或 ``browser_js`` 调用）
   - 当命中断点时，在浏览器中打开实例
   - 运行游览

   此时，Python 设置将对浏览器可见，并且游览将能够运行。

   如果你还想让测试在游览的副作用之后继续，你可能需要注释掉 ``start_tour`` 或 ``browser_js`` 调用。

在 browser_js 测试期间的屏幕截图和屏幕录像
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

当从命令行运行使用 ``HttpCase.browser_js`` 的测试时，Chrome 浏览器以无头模式使用。默认情况下，如果测试失败，将在失败时拍摄 PNG 屏幕截图并写入

.. code-block:: console

  '/tmp/odoo_tests/{db_name}/screenshots/'

自 Odoo 13.0 以来，新增了两个命令行参数来控制此行为：
:option:`--screenshots <odoo-bin --screenshots>` 和 :option:`--screencasts <odoo-bin --screencasts>`

检查 / 调试步骤
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

在尝试修复 / 调试游览时，屏幕截图（在失败时）不一定足够。在这种情况下，查看每个步骤发生的事情可能会很有用。

当处于“入职”模式时，这非常简单（因为它们主要由用户显式驱动），但在运行“测试”游览时或通过测试套件运行游览时就更复杂。在这种情况下，有两个主要技巧：

- 有一个步骤带有 ``run() { debugger; }`` 动作。

  这可以添加到现有步骤中，或者可以是一个新的专用步骤。一旦步骤的 **trigger** 匹配，执行将停止所有 JavaScript 执行。

  **优点**
    - 非常简单
    - 游览在你恢复执行后会重新启动

  **缺点**
    - 页面交互受限，因为所有 JavaScript 被阻止
    - 调试游览管理器内部并不是很有用

- 添加一个带有触发器的步骤，该触发器永远不会成功，并且 ``timeout`` 非常长。

  浏览器将等待 **trigger** 直到 ``timeout`` 之前才会失败游览，这允许在开发人员准备恢复之前检查和与页面交互，通过手动启用 **trigger**（使用无意义的类是有用的，因为可以通过将该类添加到页面的任何可见元素来触发它）。

  **优点**
    - 允许与页面交互
    - 容易应用于超时的步骤（只需添加一个长 ``timeout`` 然后查看周围）
    - 没有对这个情况无用的调试器 UI

  **缺点**
    - 更加手动，特别是在恢复时
Performance Testing
===================

查询计数
------------

测试性能的一个方法是测量数据库查询。手动测试可以使用 `--log-sql` CLI 参数。如果您想为某个操作建立最大查询数量，可以使用集成在 Odoo 测试类中的 :meth:`~odoo.tests.BaseCase.assertQueryCount` 方法。

.. code-block:: python

    with self.assertQueryCount(11):
        do_something()

.. _qunit: https://qunitjs.com/
.. _qunit_config.js: https://github.com/odoo/odoo/blob/51ee0c3cb59810449a60dae0b086b49b1ed6f946/addons/web/static/tests/helpers/qunit_config.js#L49
.. _web.tests_assets: https://github.com/odoo/odoo/blob/51ee0c3cb59810449a60dae0b086b49b1ed6f946/addons/web/views/webclient_templates.xml#L594
.. _web.qunit_suite: https://github.com/odoo/odoo/blob/51ee0c3cb59810449a60dae0b086b49b1ed6f946/addons/web/views/webclient_templates.xml#L660
.. _web.qunit_suite_tests: https://github.com/odoo/odoo/blob/51ee0c3cb59810449a60dae0b086b49b1ed6f946/addons/web/views/webclient_templates.xml#L680
.. _controller: https://github.com/odoo/odoo/blob/51ee0c3cb59810449a60dae0b086b49b1ed6f946/addons/web/controllers/main.py#L637
.. _test_js.py: https://github.com/odoo/odoo/blob/51ee0c3cb59810449a60dae0b086b49b1ed6f946/addons/web/tests/test_js.py#L13
.. _test_utils.js: https://github.com/odoo/odoo/blob/51ee0c3cb59810449a60dae0b086b49b1ed6f946/addons/web/static/tests/helpers/test_utils.js
.. _mock server: https://github.com/odoo/odoo/blob/51ee0c3cb59810449a60dae0b086b49b1ed6f946/addons/web/static/tests/helpers/mock_server.js
.. _qunit assertions: https://github.com/odoo/odoo/blob/51ee0c3cb59810449a60dae0b086b49b1ed6f946/addons/web/static/tests/helpers/qunit_asserts.js
.. _createView: https://github.com/odoo/odoo/blob/51ee0c3cb59810449a60dae0b086b49b1ed6f946/addons/web/static/tests/helpers/test_utils_create.js#L267
