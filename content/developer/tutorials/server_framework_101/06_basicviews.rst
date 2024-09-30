======================
第6章：基本视图
======================

我们在 :doc:`上一章 <05_firstui>` 中看到，Odoo 能够为给定模型生成默认视图。在实践中，默认视图对于业务应用程序 **从不** 是可接受的。相反，我们应该至少以逻辑方式组织各种字段。

视图在 XML 文件中定义，并与操作和菜单相关联。它们是 ``ir.ui.view`` 模型的实例。

在我们的房地产模块中，我们需要以逻辑方式组织字段：

- 在列表（树）视图中，我们希望显示的不仅仅是名称。
- 在表单视图中，字段应该被分组。
- 在搜索视图中，我们必须能够搜索的不仅仅是名称。具体来说，我们希望对“可用”物业进行过滤，并提供按邮政编码分组的快捷方式。

列表
====

**参考**：有关此主题的文档可以在 :ref:`reference/view_architectures/list` 中找到。

.. 注意::

    **目标**：在本节结束时，列表视图应如下所示：

    .. image:: 06_basicviews/list.png
      :align: center
      :alt: 列表视图

列表视图，也称为树视图，以表格形式显示记录。

它们的根元素是 ``<tree>``。该视图的最基本版本简单列出要在表中显示的所有字段（每个字段都是一列）：

.. code-block:: xml

    <tree string="测试">
        <field name="name"/>
        <field name="last_seen"/>
    </tree>

一个简单的示例可以在
`这里 <https://github.com/odoo/odoo/blob/6da14a3aadeb3efc40f145f6c11fc33314b2f15e/addons/crm/views/crm_lost_reason_views.xml#L46-L54>`__ 找到。

.. 练习:: 添加自定义列表视图。

    在适当的 XML 文件中为 ``estate.property`` 模型定义一个列表视图。请检查本节的 **目标** 以获取要显示的字段。

    提示：

    - 不要添加您可以在上面的示例中找到的 ``editable="bottom"`` 属性。我们稍后会回来讨论这个。
    - 某些字段标签可能需要调整以匹配参考。

与往常一样，您需要重新启动服务器（不要忘记 ``-u`` 选项）并刷新浏览器以查看结果。

.. 警告::

    您可能会在本章中使用一些复制粘贴，因此请始终确保 ``id`` 对每个视图保持唯一！

表单
====

**参考**：有关此主题的文档可以在 :ref:`reference/view_architectures/form` 中找到。

.. 注意::

    **目标**：在本节结束时，表单视图应如下所示：

    .. image:: 06_basicviews/form.png
      :align: center
      :alt: 表单视图

表单用于创建和编辑单个记录。

它们的根元素是 ``<form>``。它们由高级结构元素（组和选项卡）和交互元素（按钮和字段）组成：

.. code-block:: xml

    <form string="测试">
        <sheet>
            <group>
                <group>
                    <field name="name"/>
                </group>
                <group>
                    <field name="last_seen"/>
                </group>
            </group>
            <notebook>
                <page string="描述">
                    <field name="description"/>
                </page>
            </notebook>
        </sheet>
    </form>

可以使用常规 HTML 标签，例如 ``div`` 和 ``h1``, 以及 ``class`` 属性（Odoo 提供一些内置类）来微调外观。

一个简单的示例可以在
`这里 <https://github.com/odoo/odoo/blob/6da14a3aadeb3efc40f145f6c11fc33314b2f15e/addons/crm/views/crm_lost_reason_views.xml#L16-L44>`__ 找到。

.. 练习:: 添加自定义表单视图。

    在适当的 XML 文件中为 ``estate.property`` 模型定义一个表单视图。请检查本节的 **目标** 以获取页面的预期最终设计。

这可能需要一些试错才能获得预期的结果 ;-) 建议您逐一添加字段和标签，以帮助理解其工作原理。

为了避免每次修改视图时都重新启动服务器，使用 ``--dev xml`` 参数启动服务器可能很方便：

.. code-block:: console

    $ ./odoo-bin --addons-path=addons,../enterprise/,../tutorials/ -d rd-demo -u estate --dev xml

此参数允许您仅刷新页面以查看视图修改。

搜索
======

**参考**：有关此主题的文档可以在 :ref:`reference/view_architectures/search` 中找到。

.. 注意::

    **目标**：在本节结束时，搜索视图应如下所示：

    .. image:: 06_basicviews/search_01.png
      :align: center
      :alt: 搜索字段

    .. image:: 06_basicviews/search_02.png
      :align: center
      :alt: 过滤器

    .. image:: 06_basicviews/search_03.png
      :align: center
      :alt: 按组

搜索视图与列表视图和表单视图略有不同，因为它们不显示 *内容*。尽管它们适用于特定模型，但它们用于过滤其他视图的内容（通常是聚合视图，例如 :ref:`reference/view_architectures/list`）。除了用例的不同之外，它们的定义方式相同。

它们的根元素是 ``<search>``。该视图的最基本版本简单列出希望快捷访问的所有字段：

.. code-block:: xml

    <search string="测试">
        <field name="name"/>
        <field name="last_seen"/>
    </search>

Odoo 生成的默认搜索视图提供了按 ``name`` 过滤的快捷方式。通常在自定义搜索视图中添加用户可能会过滤的字段是很常见的。

.. 练习:: 添加自定义搜索视图。

    在适当的 XML 文件中为 ``estate.property`` 模型定义一个搜索视图。请查看本节 **目标** 中的第一张图像以获取字段列表。

重新启动服务器后，应该可以根据给定字段进行过滤。

搜索视图还可以包含 ``<filter>`` 元素，作为预定义搜索的切换。过滤器必须具有以下属性之一：

- ``domain``：将给定域添加到当前搜索中
- ``context``：向当前搜索添加一些上下文；使用键 ``group_by`` 按给定字段名对结果进行分组

一个简单的示例可以在
`这里 <https://github.com/odoo/odoo/blob/715a24333bf000d5d98b9ede5155d3af32de067c/addons/delivery/views/delivery_view.xml#L30-L44>`__ 找到。

在继续进行练习之前，有必要介绍“域”的概念。

域
-------

**参考**：有关此主题的文档可以在 :ref:`reference/orm/domains` 中找到。

在 Odoo 中，域编码条件以记录的形式：域是一组条件的列表，用于选择模型记录的子集。每个条件是一个三元组，包括 *字段名称*、*操作符* 和 *值*。如果指定字段满足应用于值的操作符的条件，则记录满足条件。

例如，在 *产品* 模型中使用以下域选择所有单位价格大于 *1000* 的 *服务*：

.. code-block:: python

    [('product_type', '=', 'service'), ('unit_price', '>', 1000)]

默认情况下，条件通过隐式 AND 组合，这意味着 *每个* 条件都需要满足才能匹配一个域。可以使用逻辑操作符 ``&``（AND）、``|``（OR）和 ``!``（NOT）明确组合条件。它们在前缀位置使用（操作符插入在其参数之前而不是之间）。例如，要选择“是服务 *或* 单位价格 *不* 在 1000 和 2000 之间的产品”：

.. code-block:: python

    ['|',
        ('product_type', '=', 'service'),
        '!', '&',
            ('unit_price', '>=', 1000),
            ('unit_price', '<', 2000)]

.. 注意:: XML 不允许在 XML 元素中使用 ``<`` 和 ``&``。为避免解析错误，应使用实体引用： ``&lt;`` 表示 ``<`` 和 ``&amp;`` 表示 ``&``。其他实体引用（``&gt;``、``&apos;`` 和 ``&quot;``）是可选的。

    .. 示例::
        .. code-block:: xml

            <filter name="negative" domain="[('test_val', '&lt;', 0)]"/>

.. 练习:: 添加过滤器和分组。

    应在先前创建的搜索视图中添加以下内容：

    - 显示可用物业的过滤器，即状态应为 '新建' 或 '收到报价'。
    - 按邮政编码分组结果的能力。

看起来不错吗？此时我们已经能够创建模型并设计符合业务逻辑的用户界面。然而，仍然缺少一个关键组件：模型之间的 :doc:`链接 <07_relations>`。
