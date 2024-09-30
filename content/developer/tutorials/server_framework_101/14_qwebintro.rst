QWeb 简介
===================================

到目前为止，我们房地产模块的界面设计相对有限。构建列表视图是简单的，因为只需要字段列表。表单视图也是如此：尽管使用了几个标签，如 ``<group>`` 或 ``<page>``, 但在设计方面几乎没有什么可做的。

然而，如果我们想为我们的应用程序赋予独特的外观，就有必要更进一步，能够设计新的视图。此外，PDF 报告或网站页面等其他功能需要另一种工具，以便以更大的灵活性创建：模板引擎。

您可能已经熟悉现有的引擎，如 Jinja（Python）、ERB（Ruby）或 Twig（PHP）。Odoo 附带了自己的内置引擎：:ref:`reference/qweb`。QWeb 是 Odoo 使用的主要模板引擎。它是一个 XML 模板引擎，主要用于生成 HTML 片段和页面。

您可能已经在 Odoo 中遇到了 `看板`_，在该看板中，记录以卡片状结构显示。我们将为我们的房地产模块构建这样的视图。

具体示例：看板视图
===============================

**参考**：与此主题相关的文档可以在 :ref:`reference/view_architectures/kanban` 找到。

.. 注意::

    **目标**：在本节结束时，应创建物业的看板视图：

    .. image:: 14_qwebintro/kanban.png
        :align: center
        :alt: 看板视图

在我们的房地产应用程序中，我们希望添加一个看板视图来显示我们的物业。看板视图是标准的 Odoo 视图（与表单和列表视图类似），但它们的结构更加灵活。实际上，每个卡片的结构是表单元素（包括基本的 HTML）和 QWeb 的混合。看板视图的定义与列表和表单视图的定义类似，唯一的区别是它们的根元素是 ``<kanban>``。在其最简单的形式下，看板视图看起来像这样：

.. code-block:: xml

    <kanban>
        <templates>
            <t t-name="kanban-box">
                <div class="oe_kanban_global_click">
                    <field name="name"/>
                </div>
            </t>
        </templates>
    </kanban>

让我们来逐步分析这个示例：

- ``<templates>``：定义了一系列 :ref:`reference/qweb` 模板。看板视图 *必须* 至少定义一个根模板 ``kanban-box``，该模板将为每条记录渲染一次。
- ``<t t-name="kanban-box">``： ``<t>`` 是 QWeb 指令的占位符元素。在这种情况下，它用于将模板的 ``name`` 设置为 ``kanban-box``。
- ``<div class="oe_kanban_global_click">``： ``oe_kanban_global_click`` 使得 ``<div>`` 可点击，以打开记录。
- ``<field name="name"/>``：这将把 ``name`` 字段添加到视图中。

.. 练习:: 创建最小的看板视图。

    使用提供的简单示例，为物业创建一个最小的看板视图。要显示的唯一字段是 ``name``。

    提示：您必须在相应的 ``ir.actions.act_window`` 的 ``view_mode`` 中添加 ``kanban``。

一旦看板视图工作正常，我们就可以开始改进它。如果我们想有条件地显示一个元素，我们可以使用 ``t-if`` 指令（参见 :ref:`reference/qweb/conditionals`）。

.. code-block:: xml

    <kanban>
        <field name="state"/>
        <templates>
            <t t-name="kanban-box">
                <div class="oe_kanban_global_click">
                    <field name="name"/>
                    <div t-if="record.state.raw_value == 'new'">
                        这是新的！
                    </div>
                </div>
            </t>
        </templates>
    </kanban>

我们添加了一些内容：

- ``t-if``：如果条件为真，则渲染 ``<div>`` 元素。
- ``record``：一个对象，具有所有请求字段作为其属性。每个字段有两个属性 ``value`` 和 ``raw_value``。前者根据当前用户参数进行格式化，后者是直接来自 :meth:`~odoo.models.Model.read` 的值。

在上面的示例中，字段 ``name`` 是在 ``<templates>`` 元素中添加的，但 ``state`` 在外面。当我们需要某个字段的值，但不想在视图中显示它时，可以将其添加到 ``<templates>`` 元素之外。

.. 练习:: 改进看板视图。

    将以下字段添加到看板视图：预期价格、最佳价格、销售价格和标签。请注意：最佳价格仅在收到报价时显示，而销售价格仅在接受报价时显示。

    请参考本节的 **目标** 以获得视觉示例。

让我们为我们的视图提供最终的润色：物业必须默认按类型分组。您可能想查看 :ref:`reference/view_architectures/kanban` 中描述的各种选项。

.. 练习:: 添加默认分组。

    使用适当的属性默认按类型对物业进行分组。您还必须防止拖放。

    请参考本节的 **目标** 以获得视觉示例。

看板视图是一个典型的示例，说明从现有视图开始并进行微调，而不是从头开始，始终是个好主意。可用的选项和类有很多，所以... 阅读和学习吧！

.. _templating: https://en.wikipedia.org/wiki/Template_processor
.. _kanban board: https://en.wikipedia.org/wiki/Kanban_board
