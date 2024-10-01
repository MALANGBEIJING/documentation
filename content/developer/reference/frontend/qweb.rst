.. highlight:: xml

.. _reference/qweb:

==============
QWeb 模板
==============

QWeb 是 Odoo 使用的主要模板引擎\ [#othertemplates]_。它是一个 XML 模板引擎\ [#genshif]_，主要用于生成 HTML_ 片段和页面。

模板指令通过以 ``t-`` 为前缀的 XML 属性指定，例如用于 :ref:`reference/qweb/conditionals` 的 ``t-if``，元素和其他属性将直接渲染。

为了避免元素渲染，还可以使用占位符元素 ``<t>``，它只执行其指令但不生成任何输出::

    <t t-if="condition">
        <p>测试</p>
    </t>

将渲染为::

    <p>测试</p>

如果 ``condition`` 为 true，但是::

    <div t-if="condition">
        <p>测试</p>
    </div>

将渲染为::

    <div>
        <p>测试</p>
    </div>

.. _reference/qweb/output:

数据输出
===========

QWeb 的输出指令 ``out`` 会自动对其输入进行 HTML 转义，以限制显示用户提供的内容时的 XSS_ 风险。

``out`` 接受一个表达式，计算并将结果注入到文档中::

    <p><t t-out="value"/></p>

如果将 ``value`` 设置为 ``42``，渲染结果为::

    <p>42</p>

参见 :ref:`reference/qweb/advanced-output`，了解更多高级主题（例如注入原始 HTML 等）。

.. _reference/qweb/conditionals:

条件
============

QWeb 提供了一个条件指令 ``if``，它计算作为属性值提供的表达式::

    <div>
        <t t-if="condition">
            <p>ok</p>
        </t>
    </div>

如果条件为 true，则元素会被渲染::

    <div>
        <p>ok</p>
    </div>

如果条件为 false，则它会从结果中删除::

    <div>
    </div>

条件渲染适用于承载指令的元素，承载元素不一定是 ``<t>``::

    <div>
        <p t-if="condition">ok</p>
    </div>

将产生与前面的示例相同的结果。

还提供了额外的条件分支指令 ``t-elif`` 和 ``t-else``::

    <div>
        <p t-if="user.birthday == today()">生日快乐！</p>
        <p t-elif="user.login == 'root'">欢迎，主人！</p>
        <p t-else="">欢迎！</p>
    </div>


.. _reference/qweb/loops:

循环
=====

QWeb 提供了一个迭代指令 ``foreach``，该指令接受一个返回集合的表达式，以及第二个参数 ``t-as``，用于指定当前迭代项的名称::

    <t t-foreach="[1, 2, 3]" t-as="i">
        <p><t t-out="i"/></p>
    </t>

渲染结果为::

    <p>1</p>
    <p>2</p>
    <p>3</p>

与条件一样，``foreach`` 适用于承载指令属性的元素，

::

    <p t-foreach="[1, 2, 3]" t-as="i">
        <t t-out="i"/>
    </p>

与前面的示例等效。

``foreach`` 可以迭代数组（当前项为当前值）或映射（当前项为当前键）。迭代整数（相当于迭代从 0（含）到给定整数（不含）的数组）仍然受支持，但已弃用。

除了通过 ``t-as`` 传递的名称，``foreach`` 还提供了一些其他变量用于获取各种数据点：

.. warning:: ``$as`` 将被 ``t-as`` 传递的名称替换

.. rst-class:: o-definition-list

:samp:`{$as}_all`（已弃用）
    正在迭代的对象

    .. note:: 该变量仅在 JavaScript QWeb 中可用，Python 不支持。

:samp:`{$as}_value`
    当前迭代的值，对于列表和整数与 ``$as`` 相同，但对于映射，它提供值（而 ``$as`` 提供键）
:samp:`{$as}_index`
    当前迭代的索引（第一个迭代项的索引为 0）
:samp:`{$as}_size`
    如果可用，则表示集合的大小
:samp:`{$as}_first`
    当前项是否是迭代的第一个项（等效于 :samp:`{$as}_index == 0`）
:samp:`{$as}_last`
    当前项是否是迭代的最后一项（等效于 :samp:`{$as}_index + 1 == {$as}_size`），需要迭代对象的大小可用
:samp:`{$as}_parity`（已弃用）
    当前迭代轮次的奇偶性，值为 ``"even"`` 或 ``"odd"``
:samp:`{$as}_even`（已弃用）
    一个布尔标志，指示当前迭代轮次是否为偶数
:samp:`{$as}_odd`（已弃用）
    一个布尔标志，指示当前迭代轮次是否为奇数

这些由 ``foreach`` 提供的额外变量以及在 ``foreach`` 中创建的所有新变量仅在 ``foreach`` 的范围内可用。如果该变量在 ``foreach`` 的上下文之外存在，则迭代结束时，该变量的值会复制到全局上下文。

::

    <t t-set="existing_variable" t-value="False"/>
    <!-- existing_variable 现在为 False -->

    <p t-foreach="[1, 2, 3]" t-as="i">
        <t t-set="existing_variable" t-value="True"/>
        <t t-set="new_variable" t-value="True"/>
        <!-- existing_variable 和 new_variable 现在为 True -->
    </p>

    <!-- existing_variable 始终为 True -->
    <!-- new_variable 未定义 -->

.. _reference/qweb/attributes:

属性
==========

QWeb 可以动态计算属性并将计算结果设置到输出节点。这是通过 ``t-att``（属性）指令完成的，该指令有三种不同的形式：

.. rst-class:: o-definition-list

:samp:`t-att-{$name}`
    创建一个名为 ``$name`` 的属性，评估属性值并将结果设置为属性的值::

        <div t-att-a="42"/>

    渲染结果为::

        <div a="42"></div>
:samp:`t-attf-{$name}`
    与上面的相同，但参数是一个 :term:`格式化字符串`，而不仅仅是一个表达式，通常用于混合文字和非文字字符串（例如类）::

        <t t-foreach="[1, 2, 3]" t-as="item">
            <li t-attf-class="row {{ (item_index % 2 === 0) ? 'even' : 'odd' }}">
                <t t-out="item"/>
            </li>
        </t>

    渲染结果为::

        <li class="row even">1</li>
        <li class="row odd">2</li>
        <li class="row even">3</li>

    .. tip::
       格式化字符串有两种等效的语法：``"plain_text {{code}}"``（即 jinja 样式）和 ``"plain_text #{code}"``（即 ruby 样式）。

:samp:`t-att=mapping`
    如果参数是一个映射，则每个（键，值）对都会生成一个新属性及其值::

        <div t-att="{'a': 1, 'b': 2}"/>

    渲染结果为::

        <div a="1" b="2"></div>
:samp:`t-att=pair`
    如果参数是一个对（元组或包含两个元素的数组），则对的第一个元素为属性名称，第二个元素为属性值::

        <div t-att="['a', 'b']"/>

    渲染结果为::

        <div a="b"></div>

.. _reference/qweb/set:
==================
设置变量
==================

QWeb 允许在模板中创建变量，以便缓存计算结果（多次使用它），或者为数据赋予更清晰的名称等。

这可以通过 ``set`` 指令来实现，该指令接受要创建的变量的名称。要设置的值可以通过以下两种方式提供：

* 使用 ``t-value`` 属性包含一个表达式，并将其计算结果设置为变量的值::

    <t t-set="foo" t-value="2 + 1"/>
    <t t-out="foo"/>

  将输出 ``3``。
* 如果没有 ``t-value`` 属性，则节点的主体将被渲染并设置为变量的值::

    <t t-set="foo">
        <li>ok</li>
    </t>
    <t t-out="foo"/>

.. _reference/qweb/call:

.. _reference/qweb/sub-templates:

调用子模板
=====================

QWeb 模板可以用于顶层渲染，但也可以在另一个模板中调用（以避免重复或为模板的部分命名），这可以通过 ``t-call`` 指令来实现::

    <t t-call="other-template"/>

这会调用指定的模板，并使用父模板的执行上下文，如果 ``other-template`` 定义为::

    <p><t t-value="var"/></p>

上面的调用将渲染为 ``<p/>``（无内容），但::

    <t t-set="var" t-value="1"/>
    <t t-call="other-template"/>

将渲染为 ``<p>1</p>``。

然而，这有一个问题，设置在 ``t-call`` 外部的变量仍然是可见的。替代方法是在调用子模板之前，设置在 ``call`` 指令主体中的内容将被评估，并可以修改局部上下文::

    <t t-call="other-template">
        <t t-set="var" t-value="1"/>
    </t>
    <!-- "var" 在这里不存在 -->

``call`` 指令的主体可以是任意复杂的（不仅仅是 ``set`` 指令），并且其渲染形式将在被调用的模板中作为魔法变量 ``0`` 可用::

    <div>
        此模板被调用，内容为:
        <t t-out="0"/>
    </div>

通过如下调用::

    <t t-call="other-template">
        <em>content</em>
    </t>

将渲染为::

    <div>
        此模板被调用，内容为:
        <em>content</em>
    </div>

.. _reference/qweb/advanced-output:

高级输出
===============

默认情况下，``out`` 会对需要转义的内容进行 HTML 转义，以防止 XSS_ 风险。

不需要转义的内容将以原始形式注入文档，可能成为文档实际标记的一部分。

唯一跨平台的“安全”内容是 :ref:`t-call <reference/qweb/call>` 或 :ref:`t-set <reference/qweb/set>` 使用“主体”（而非 ``t-value`` 或 ``t-valuef``）的输出。

Python
------

通常情况下，您不需要过多关心：适合的 API 应自动生成“安全”内容，系统应能透明地工作。

但在某些情况下，您可能希望更明确一些，以下 API 输出的安全内容在注入模板时默认不会再次被转义：

* :class:`HTML 字段 <odoo.fields.Html>`。
* :func:`~odoo.tools.misc.html_escape` 和 :func:`markupsafe.escape`（它们是别名，不会发生二次转义的风险）。
* :func:`~odoo.tools.mail.html_sanitize`。
* :class:`markupsafe.Markup`。

  .. warning:: :class:`markupsafe.Markup` 是一个不安全的 API，它是一种 *声明*，表明您希望内容是标记安全的，但无法验证，因此应谨慎使用。
* :func:`~odoo.tools.pycompat.to_text` 不会将内容标记为安全，但也不会从安全内容中删除该信息。

强制双重转义
-----------------------

如果内容被标记为安全，但出于某种原因仍需转义（例如打印 HTML 字段的标记），则可以将其转换为普通字符串以“去除”安全标记，例如在 Python 中使用 `str(content)`，在 JavaScript 中使用 `String(content)`。

.. note::

    由于 :class:`~markupsafe.Markup` 是比 :js:class:`Markup` 更丰富的类型，因此某些操作会从 :js:class:`Markup` 中移除安全信息，但不会从 :class:`~markupsafe.Markup` 中移除。例如，在 Python 中进行字符串拼接（``'' + content``）时，结果会是一个 :class:`~markupsafe.Markup`，其他操作数会正确转义；而在 JavaScript 中，结果会是一个 :js:class:`String`，其他操作数不会在拼接之前进行转义。

已弃用的输出指令
----------------------------

.. rst-class:: o-definition-list

``esc``
    是 ``out`` 的别名，最初会对输入进行 HTML 转义。尚未正式弃用，其与 ``out`` 唯一的区别在于 ``esc`` 的定义不太明确/不正确。
``raw``
    是 ``out`` 的一个版本，它 *从不* 转义其内容。无论内容是否安全，都会按原样输出。

    .. deprecated:: 15.0

        使用带有 :class:`markupsafe.Markup` 值的 ``out`` 代替。

        ``t-raw`` 已被弃用，因为随着生成内容的代码不断演变，很难追踪其将用于标记，从而导致更复杂的审核和更危险的漏洞。

Python
======

专用指令
--------------------

资源包
~~~~~~~~~~~~~

.. todo:: 请某人编写此部分，因为我不清楚它们的工作原理

“智能记录”字段格式化
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

``t-field`` 指令只能在访问“智能”记录（通过 ``browse`` 方法的结果）时使用。它能够根据字段类型自动格式化，并集成在网站的富文本编辑中。

``t-options`` 可用于自定义字段，最常见的选项是 ``widget``，其他选项取决于字段或小部件。

调试
---------

.. rst-class:: o-definition-list

``t-debug``
    没有值时，调用 :func:`breakpoint` 内置函数，通常会启动调试器（默认为 :mod:`pdb`）。

    其行为可以通过 :envvar:`PYTHONBREAKPOINT` 或 :func:`sys.breakpointhook` 进行配置。

渲染缓存：
----------------

``t-cache="key_cache"`` 标记模板的一部分以在渲染时缓存。每个子指令仅在第一次渲染时调用。这意味着在这些子指令渲染过程中执行的 SQL 查询也仅执行一次。

``t-nocache="documentation"`` 标记模板的一部分，每次都重新渲染。内容只能使用根值。

为什么以及何时使用 ``t-cache``？
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

此指令用于加快渲染速度，通过缓存部分最终文档，从而可能节省数据库查询。然而，应谨慎使用 ``t-cache``，因为它不可避免地会使模板复杂化（例如，理解 ``t-set`` 可能变得更加困难）。

然而，为了实际节省数据库查询，可能需要使用延迟评估的值来渲染模板。如果这些延迟值被用于缓存的部分，并且该部分在缓存中可用，则这些值将不会被评估。

``t-cache`` 指令对于依赖于有限数据量的模板部分非常有用。我们建议使用分析器（通过启用“**添加 qweb 指令上下文**”选项）来分析模板的渲染。在控制器中传递延迟值以渲染模板时，您可以显示使用这些值的指令并触发查询。

使用此类缓存的一个问题是如何确保不同用户能够一致地渲染缓存部分（即，不同用户应以相同方式渲染缓存部分）。另一个潜在问题是在需要时使缓存项失效。为此，应该明智地选择 **key expression**（缓存键表达式）。例如，使用记录集的 ``write_date`` 可以使缓存键过时，而无需显式丢弃缓存。

还需要注意的是，``t-cache`` 部分中的值是作用域化的。这意味着如果在模板的某一部分有 ``t-set`` 指令，则后续渲染的内容可能与没有 ``t-cache`` 指令时不同。

如果在 ``t-cache`` 内还有另一个 ``t-cache``，该怎么办？
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

这些部分会被缓存，每个部分只包含其渲染结果的字符串。因此，内部的 ``t-cache`` 可能会更少地被读取，其缓存键可能不一定会被使用。如果必须如此，那么可能需要在同一个节点或父节点上添加 ``t-nocache``。

``t-nocache`` 的用途是什么？
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

如果您想缓存带有 ``t-cache`` 的模板的一部分，但有一小部分需要保持动态，并在缓存时重新评估。然而，``t-nocache`` 部分将无法访问模板中的 ``t-set`` 值，只有控制器提供的值可以在此处访问。例如，菜单会被缓存，因为它一直相同并且渲染需要时间（使用开发工具的性能选项与 qweb 上下文一起调查）。然而，在菜单中，我们希望电子商务购物车始终是最新的，因此会使用 ``t-nocache`` 保持这一部分动态。

``t-cache`` 的基本原理
~~~~~~~~~~~~~~~~~~~~~~~

``t-cache`` 指令允许您存储模板的渲染结果。**key expression**（例如 42: ``t-cache="42"``）将作为一个 Python 表达式进行评估，并用于生成 **缓存键**。因此，对于相同的模板部分，可以有不同的 **缓存值**（已缓存的渲染部分）。如果 **key expression** 是一个元组或列表，则在生成 **缓存键** 时将进行搜索。如果 **key expression** 返回一个或多个记录集，则将使用模型、ID 及其对应的 write_date 来生成 **缓存键**。特殊情况：如果 **key expression** 返回 Falsy 值，则内容将不会被缓存。

示例::

    <div t-cache="record,bool(condition)">
        <span t-if="condition" t-field="record.partner_id.name">
        <span t-else="" t-field="record.partner_id" t-options-widget="contact">
    </div>

在这种情况下，可能会有对应于每个已返回记录的值（字符串），条件为 true 以及 false 的缓存值。而如果某个模块修改了记录，``write_date`` 被修改，缓存值将被丢弃。
``t-cache`` 和作用域值 (``t-set``，``t-foreach``...)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

在 ``t-cache`` 中的值具有作用域，这会导致行为的变化，特别是在父节点上是否存在 ``t-cache``。请记住，Odoo 使用了大量模板，``t-call`` 和视图继承。添加 ``t-cache`` 因此可能会修改您在编辑时看不到的模板的渲染。
（``t-foreach`` 就像每次迭代的 ``t-set``）

示例::

    <div>
        <t t-set="a" t-value="1"/>
        <inside>
            <t t-set="a" t-value="2"/>
            <t t-out="a"/>
        </inside>
        <outside t-out="a"/>

        <t t-set="b" t-value="1"/>
        <inside t-cache="True">
            <t t-set="b" t-value="2"/>
            <t t-out="b"/>
        </inside>
        <outside t-out="b"/>
    </div>

将渲染为::

    <div>
        <inside>2</inside>
        <outside>2</outside>

        <inside>2</inside>
        <outside>1</outside>
    </div>

``t-nocache`` 的基本原理
~~~~~~~~~~~~~~~~~~~~~~~~~

带有 ``t-nocache`` 属性的节点中的模板部分不会被缓存。因此，该内容是**动态的**，并且每次都会重新渲染。然而，可用的值仅限于控制器提供的值（在调用 ``_render`` 方法时）。

示例::

    <section>
        <article t-cache="record">
            <title><t t-out="record.name"/> <i t-nocache="">(views: <t t-out="counter"/>)</i></title>
            <content t-out="record.description"/>
        </article>
    </section>

将渲染为（counter = 1）::

    <section>
        <article>
            <title>记录名称 <i>(浏览量: 1)</i></title>
            <content>记录描述</content>
        </article>
    </section>

在这里，包含容器的 ``<i>`` 标签始终会被渲染，而其他部分作为缓存中的单一字符串。

``t-nocache`` 和作用域根值 (``t-set``，``t-foreach``...)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

``t-nocache`` 标签中的内容可以用于文档和解释为何添加此指令。
这些值在 ``t-nocache`` 中具有作用域，这些值仅限于根值（由控制器提供的值和/或在调用 ``ir.qweb`` 的 ``_render`` 方法时）。可以在模板部分中使用 ``t-set``，但不会在其他地方可用。

示例::

    <section>
        <t t-set="counter" t-value="counter * 10"/>
        <header t-nocache="">
            <t t-set="counter" t-value="counter + 5"/>
            (浏览量: <t t-out="counter"/>)
        </header>
        <article t-cache="record">
            <title><t t-out="record.name"/> <i t-nocache="">(浏览量: <t t-out="counter"/>)</i></title>
            <content t-out="record.description"/>
        </article>
        <footer>(浏览量: <t t-out="counter"/>)</footer>
    </section>

将渲染为（counter = 1）::

    <section>
        <header>
            (浏览量: 6)
        </header>
        <article>
            <title>记录名称 <i>(浏览量: 1)</i></title>
            <content>记录描述</content>
        </article>
        <footer>(浏览量: 10)</footer>
    </section>

在这里，包含容器的 ``<i>`` 标签始终会被渲染，而其他部分作为缓存中的单一字符串。``t-set`` 设置的计数器值在 ``t-nocache`` 之外不会被更新。

``t-nocache-*`` 将一些原始值添加到缓存中
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

为了能够使用模板中生成的值，可以将其缓存。该指令使用 ``t-nocache-*="expr"`` 的形式，其中 ``*`` 是选定值的名称，``expr`` 是 Python 表达式，因此结果将被缓存。缓存的值必须是原始类型。

示例::

    <section t-cache="records">
        <article t-foreach="records" t-as="record">
            <header>
                <title t-field="record.get_method_title()"/>
            </header>
            <footer t-nocache="此部分具有动态计数器，必须始终渲染。"
                    t-nocache-cached_value="record.get_base_counter()">
                <span t-out="counter + cached_value"/>
            </footer>
        </article>
    </section>

``cached_value`` 值与 ``t-cache="records"`` 缓存模板部分一起缓存，并且每次都添加到作用域根值中。

辅助工具
-------

基于请求的辅助工具
~~~~~~~~~~~~~

在大多数 Python 端的 QWeb 用例中，通常是在控制器中（并且在 HTTP 请求期间），此时存储在数据库中的模板（如 :ref:`views <reference/view_architectures/qweb>`）可以通过调用 :meth:`odoo.http.HttpRequest.render` 轻松渲染：

.. code-block:: python

    response = http.request.render('my-template', {
        'context_value': 42
    })

这将自动创建一个 :class:`~odoo.http.Response` 对象，您可以返回它，也可以根据需要进一步自定义。
基于视图的渲染
~~~~~~~~~~

相比于前面提到的辅助工具，``_render`` 方法位于 ``ir.qweb`` 上，提供了更深入的控制，和公共模块方法 ``render``（不使用数据库）一样：

.. py:method:: _render(id[, values])

    通过数据库 ID 或者 :term:`外部 ID` 渲染 QWeb 视图/模板。模板会自动从 ``ir.qweb`` 记录中加载。

    ``_prepare_environment`` 方法设置了一些渲染上下文中的默认值。``http_routing`` 和 ``website`` 插件也提供了它们所需的默认值。
    您可以使用 ``minimal_qcontext=False`` 选项，避免使用这些默认值，就像公共方法 ``render`` 一样：

    .. rst-class:: o-definition-list

    ``request``
        当前的 :class:`~odoo.http.Request` 对象（如果有）
    ``debug``
        当前请求是否处于 ``debug`` 模式（如果有）
    :func:`quote_plus <werkzeug.urls.url_quote_plus>`
        URL 编码实用工具函数
    :mod:`json`
        对应的标准库模块
    :mod:`time`
        对应的标准库模块
    :mod:`datetime`
        对应的标准库模块
    `relativedelta <https://labix.org/python-dateutil#head-ba5ffd4df8111d1b83fc194b97ebecf837add454>`_
        见此模块
    ``keep_query``
        ``keep_query`` 辅助函数

    :param values: 要传递给 QWeb 渲染的上下文值
    :param str engine: 用于渲染的 Odoo 模型的名称，可以用来本地扩展或定制 QWeb（通过创建一个基于 ``ir.qweb`` 的 "新" QWeb，并进行修改）

.. py:method:: render(template_name, values, load, **options)

    :func:`load(ref)`
        返回 etree 对象，ref

JavaScript
==========

独占指令
--------

定义模板
~~~~~~~~~~

``t-name`` 指令只能位于模板文件的顶层（即文档根的直接子节点）::

    <templates>
        <t t-name="template-name">
            <!-- 模板代码 -->
        </t>
    </templates>

它不接受其他参数，可以和 ``<t>`` 元素或任何其他元素一起使用。对于 ``<t>`` 元素，它应该有一个子节点。

模板名称是一个任意字符串，虽然当多个模板相关（例如，调用子模板）时，通常使用点分隔的名称来指示层次关系。

模板继承
~~~~~~~~~~

模板继承用于：

- 就地修改现有模板，例如，向由其他模块创建的模板添加信息。
- 从给定的父模板创建新模板。

模板继承通过两个指令完成：
- ``t-inherit``：被继承模板的名称，
- ``t-inherit-mode``：继承的行为，可以设置为 ``primary``，以从父模板创建新的子模板，或者设置为 ``extension``，以在原处修改父模板。

可选的 ``t-name`` 指令也可以指定。如果在 primary 模式下使用，它将是新创建的模板的名称，否则它将作为注释添加到转换后的模板中，以帮助追踪继承链。

对于继承本身，使用 xpath 指令进行更改。请参阅 XPATH_ 文档以获取可用指令的完整集合。

Primary 继承（子模板）::

    <t t-name="child.template" t-inherit="base.template" t-inherit-mode="primary">
        <xpath expr="//ul" position="inside">
            <li>新元素</li>
        </xpath>
    </t>

Extension 继承（就地转换）::

    <t t-inherit="base.template" t-inherit-mode="extension">
        <xpath expr="//tr[1]" position="after">
            <tr><td>新单元格</td></tr>
        </xpath>
    </t>

旧继承机制（已弃用）
~~~~~~~~~~~~~~~~~~~~~~

模板继承通过 ``t-extend`` 指令完成，它接受要修改的模板名称作为参数。

当与 ``t-name`` 结合使用时，``t-extend`` 指令将作为 primary 继承运行，而单独使用时则作为 extension 继承运行。

在两种情况下，修改都是通过任何数量的 ``t-jquery`` 子指令完成的::

    <t t-extend="base.template">
        <t t-jquery="ul" t-operation="append">
            <li>新元素</li>
        </t>
    </t>

``t-jquery`` 指令接受一个 `CSS 选择器`_。此选择器用于扩展模板上的上下文节点，指定的 ``t-operation`` 将应用于这些节点：

.. rst-class:: o-definition-list

``append``
    节点的内容被追加到上下文节点的末尾（上下文节点的最后一个子节点之后）
``prepend``
    节点的内容被预置到上下文节点中（在上下文节点的第一个子节点之前插入）
``before``
    节点的内容被插入到上下文节点之前
``after``
    节点的内容被插入到上下文节点之后
``inner``
    节点的内容替换上下文节点的子节点
``replace``
    节点的内容用来替换上下文节点本身
``attributes``
    节点的内容应该是任何数量的 ``attribute`` 元素，每个元素都有一个 ``name`` 属性和一些文本内容，上下文节点的指定属性将被设置为该值（如果已存在则替换，如果不存在则添加）
``no operation``
    如果没有指定 ``t-operation``，则模板内容将被解释为 JavaScript 代码，并在上下文节点上作为 ``this`` 执行。

.. warning:: 尽管比其他操作更强大，但这种模式也更难调试和维护，因此建议尽量避免使用。


调试
-----

JavaScript 的 QWeb 实现提供了几个调试钩子：

.. rst-class:: o-definition-list

``t-log``
    接受一个表达式参数，在渲染过程中计算该表达式，并将其结果通过 ``console.log`` 打印出来::

        <t t-set="foo" t-value="42"/>
        <t t-log="foo"/>

    将在控制台打印出 ``42``。
``t-debug``
    在模板渲染期间触发一个调试器断点::

        <t t-if="a_test">
            <t t-debug=""/>
        </t>

    如果调试激活，执行将停止（确切的条件取决于浏览器及其开发工具）。
``t-js``
    节点的内容是 JavaScript 代码，在模板渲染期间执行。接受一个 ``context`` 参数，该参数是渲染上下文将在 ``t-js`` 内容中可用的名称::

        <t t-set="foo" t-value="42"/>
        <t t-js="ctx">
            console.log("Foo is", ctx.foo);
        </t>

助手
------

.. js:attribute:: core.qweb

    （core 是 ``web.core`` 模块）一个 :js:class:`QWeb2.Engine` 实例，加载了所有模块定义的模板文件，并引用了标准的助手对象 ``_``（underscore）、``_t``（翻译函数）和 JSON_。

    :js:func:`core.qweb.render <QWeb2.Engine.render>` 可用于轻松渲染基本模块模板。

.. _reference/qweb/api:

API
---

.. js:class:: QWeb2.Engine

    QWeb 的“渲染器”，负责处理 QWeb 的大部分逻辑（加载、解析、编译和渲染模板）。

    Odoo Web 在 core 模块中为用户实例化了一个引擎，并将其导出为 ``core.qweb``。此外，它还会将各个模块的所有模板文件加载到该 QWeb 实例中。

    :js:class:`QWeb2.Engine` 也充当“模板命名空间”。

    .. js:function:: QWeb2.Engine.render(template[, context])

        渲染先前加载的模板为字符串，使用 ``context``（如果提供）来查找模板渲染期间访问的变量（例如要显示的字符串）。

        :param String template: 要渲染的模板名称
        :param Object context: 用于模板渲染的基本命名空间
        :returns: 字符串

    该引擎还提供了其他一些可能有用的方法（例如，如果您需要一个单独的模板命名空间，如在 Odoo Web 中，Kanban 视图有自己的 :js:class:`QWeb2.Engine` 实例，以避免与其他模块的模板冲突）：

    .. js:function:: QWeb2.Engine.add_template(templates)

        将一个模板文件（模板集合）加载到 QWeb 实例中。模板可以是以下形式：

        XML 字符串
            QWeb 将尝试将其解析为 XML 文档并加载。

        URL
            QWeb 将尝试下载 URL 内容，然后加载生成的 XML 字符串。

        ``Document`` 或 ``Node``
            QWeb 将遍历文档的第一级（提供的根节点的子节点），并加载任何命名模板或模板覆盖。

        :type templates: String | Document | Node

    :js:class:`QWeb2.Engine` 还公开了用于自定义行为的各种属性：

    .. js:attribute:: QWeb2.Engine.prefix

        解析时用于识别指令的前缀。默认值为 ``t``。

    .. js:attribute:: QWeb2.Engine.debug

        布尔标志，将引擎置于“调试模式”。在正常情况下，QWeb 会截取模板执行期间引发的任何错误。在调试模式下，它会让所有异常继续传播，而不做拦截。

    .. js:attribute:: QWeb2.Engine.jQuery

        用于模板继承处理的 jQuery 实例。默认值为 ``window.jQuery``。

    .. js:attribute:: QWeb2.Engine.preprocess_node

        一个 ``Function``。如果存在，会在将每个 DOM 节点编译为模板代码之前调用。在 Odoo Web 中，这用于自动翻译模板中的文本内容和某些属性。默认值为 ``null``。

.. [#genshif] 它与 Genshi_ 类似，尽管它没有使用（并且不支持） `XML 命名空间`_

.. [#othertemplates] 尽管 Odoo 还使用了一些其他模板引擎，主要出于历史原因或因为它们更适合特定用例。Odoo 9.0 仍然依赖于 Jinja_ 和 Mako_。

.. _templating:
    https://en.wikipedia.org/wiki/Template_processor

.. _Jinja: http://jinja.pocoo.org
.. _Mako: https://www.makotemplates.org
.. _Genshi: https://genshi.edgewall.org
.. _XML 命名空间: https://en.wikipedia.org/wiki/XML_namespace
.. _HTML: https://en.wikipedia.org/wiki/HTML
.. _XSS: https://en.wikipedia.org/wiki/Cross-site_scripting
.. _JSON: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON
.. _CSS 选择器: https://api.jquery.com/category/selectors/
.. _XPATH: https://developer.mozilla.org/en-US/docs/Web/XPath
