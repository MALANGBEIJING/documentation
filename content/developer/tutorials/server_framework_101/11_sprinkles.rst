=============================
第11章：添加一些装饰
=============================

我们的房地产模块现在从商业角度看是合理的。我们创建了 :doc:`特定视图 <06_basicviews>`，添加了多个 :doc:`操作按钮 <09_actions>` 和 :doc:`约束 <10_constraints>`。然而，我们的用户界面仍然有些粗糙。我们希望为列表视图添加一些颜色，并有条件地隐藏某些字段和按钮。例如，当物业已售或已取消时，“已售”和“取消”按钮应消失，因为此时不再允许更改状态。

本章涵盖了在视图中可以做的非常小的子集。请随时查看参考文档以获取更全面的概述。

**参考**：有关本章的文档可以在 :doc:`../../reference/user_interface/view_records` 和 :doc:`../../reference/user_interface/view_architectures` 中找到。

内联视图
============

.. 注意::

    **目标**：在本节结束时，应在物业类型视图中添加特定的物业列表：

    .. image:: 11_sprinkles/inline_view.png
        :align: center
        :alt: 内联列表视图

在房地产模块中，我们为物业添加了报价列表。我们只需添加字段 ``offer_ids``：

.. code-block:: xml

    <field name="offer_ids"/>

该字段使用 ``estate.property.offer`` 的特定视图。在某些情况下，我们希望定义一个特定的列表视图，仅在表单视图的上下文中使用。例如，我们希望显示与物业类型链接的物业列表。然而，我们只希望显示三个字段以便于理解：名称、预期价格和状态。

为此，我们可以定义 *内联* 列表视图。内联列表视图直接在表单视图内定义。例如：

.. code-block:: python

    from odoo import fields, models

    class TestModel(models.Model):
        _name = "test_model"
        _description = "测试模型"

        description = fields.Char()
        line_ids = fields.One2many("test_model_line", "model_id")


    class TestModelLine(models.Model):
        _name = "test_model_line"
        _description = "测试模型行"

        model_id = fields.Many2one("test_model")
        field_1 = fields.Char()
        field_2 = fields.Char()
        field_3 = fields.Char()

.. code-block:: xml

    <form>
        <field name="description"/>
        <field name="line_ids">
            <tree>
                <field name="field_1"/>
                <field name="field_2"/>
            </tree>
        </field>
    </form>

在 `test_model` 的表单视图中，我们为 `test_model_line` 定义了一个特定的列表视图，包含字段 ``field_1`` 和 ``field_2``。

可以在
`这里 <https://github.com/odoo/odoo/blob/0e12fa135882cd5095dbf15fe2f64231c6a84336/addons/event/views/event_tag_views.xml#L27-L33>`__ 找到示例。

.. 练习:: 添加内联列表视图。

    - 向 ``estate.property.type`` 模型添加 ``One2many`` 字段 ``property_ids``。
    - 按照本节 **目标** 中的描述将该字段添加到 ``estate.property.type`` 的表单视图中。

小部件
=======

**参考**：有关本节的文档可以在 :ref:`reference/js/widgets` 中找到。

.. 注意::

    **目标**：在本节结束时，物业的状态应使用特定的小部件显示：

    .. image:: 11_sprinkles/widget.png
        :align: center
        :alt: 状态栏小部件

    显示四个状态：新建、收到报价、报价接受和已售。

每当我们向模型添加字段时，我们（几乎）从未担心这些字段在用户界面中的外观。例如，为 ``Date`` 字段提供了日期选择器， ``One2many`` 字段自动显示为列表。Odoo 根据字段类型选择合适的“小部件”。

然而，在某些情况下，我们希望对字段进行特定的表示，这可以通过 ``widget`` 属性来实现。我们在使用 ``widget="many2many_tags"`` 属性时已经使用过它。如果我们没有使用它，则该字段将显示为列表。

每种字段类型都有一组可以用来微调其显示的小部件。有些小部件还接受额外选项。详尽的列表可以在 :ref:`reference/js/widgets` 中找到。

.. 练习:: 使用状态栏小部件。

    使用 ``statusbar`` 小部件来显示 ``estate.property`` 的 ``state``，如本节 **目标** 中所述。

    提示：可以在
    `这里 <https://github.com/odoo/odoo/blob/0e12fa135882cd5095dbf15fe2f64231c6a84336/addons/account/views/account_bank_statement_views.xml#L136>`__ 找到一个简单示例。

.. 警告:: 在视图中多次使用同一字段

    仅将字段 **添加一次** 到列表或表单视图中。多次添加不被支持。

列表排序
==========

**参考**：有关本节的文档可以在 :ref:`reference/orm/models` 中找到。

.. 注意::

    **目标**：在本节结束时，所有列表应默认以确定性顺序显示。物业类型可以手动排序。

在之前的练习中，我们创建了多个列表视图。然而，在任何时候我们都没有指定记录应按默认顺序列出。这对许多业务案例来说是非常重要的。例如，在我们的房地产模块中，我们希望将最高的报价显示在列表顶部。

模型
-----

Odoo 提供了几种设置默认排序的方法。最常见的方法是在模型中直接定义 ``_order`` 属性。通过这种方式，检索到的记录将遵循一个确定的顺序，这在所有视图中都将保持一致，包括在编程搜索记录时。默认情况下没有指定顺序，因此记录将根据 PostgreSQL 以非确定性顺序检索。

``_order`` 属性采用一个包含将用于排序的字段列表的字符串。它将转换为 SQL 中的 order_by_ 子句。例如：

.. code-block:: python

    from odoo import fields, models

    class TestModel(models.Model):
        _name = "test_model"
        _description = "测试模型"
        _order = "id desc"

        description = fields.Char()

我们的记录按降序 ``id`` 排序，这意味着最高的在前。

.. 练习:: 添加模型排序。

    在相应模型中定义以下排序：

    =================================== ===================================
    模型                               排序
    =================================== ===================================
    ``estate.property``                 降序 ID
    ``estate.property.offer``           降序价格
    ``estate.property.tag``             名称
    ``estate.property.type``            名称
    =================================== ===================================

视图
----

排序可以在模型级别进行。这有一个优势，即在检索记录列表的地方始终保持一致的顺序。然而，也可以通过在视图中直接定义 ``default_order`` 属性来定义特定的顺序
（`示例 <https://github.com/odoo/odoo/blob/892dd6860733c46caf379fd36f57219082331b66/addons/crm/report/crm_activity_report_views.xml#L30>`__）。

手动
------

模型和视图排序都允许在排序记录时灵活性，但还有一种情况需要覆盖：手动排序。用户可能希望根据业务逻辑对记录进行排序。例如，在我们的房地产模块中，我们希望手动排序物业类型。确实，有用的类型出现在列表顶部。如果我们的房地产代理主要出售房屋，那么将“房屋”放在“公寓”之前会更方便。

为此，使用 ``sequence`` 字段结合 ``handle`` 小部件。显然， ``sequence`` 字段必须是 ``_order`` 属性中的第一个字段。

.. 练习:: 添加手动排序。

    - 添加以下字段：

    =================================== ======================= =======================
    模型                               字段                   类型
    =================================== ======================= =======================
    ``estate.property.type``            Sequence                Integer
    =================================== ======================= =======================

    - 将序列添加到 ``estate.property.type`` 列表视图中，使用正确的小部件。

    提示：可以在 `模型 <https://github.com/odoo/odoo/blob/892dd6860733c46caf379fd36f57219082331b66/addons/crm/models/crm_stage.py#L36>`__ 和 `视图 <https://github.com/odoo/odoo/blob/892dd6860733c46caf379fd36f57219082331b66/addons/crm/views/crm_stage_views.xml#L23>`__ 中找到示例。
属性和选项
======================

详细说明视图外观的所有可用功能将是不可行的。因此，我们将重点介绍最常用的功能。

表单
----

.. 注意::

    **目标**：在本节结束时，物业表单视图将具有：

    - 条件显示的按钮和字段
    - 标签颜色

    .. image:: 11_sprinkles/form.gif
      :align: center
      :alt: 带装饰的表单视图


在我们的房地产模块中，我们希望修改某些字段的行为。例如，我们不希望能够在表单视图中创建或编辑物业类型。相反，我们希望在其适当的菜单中处理这些类型。我们还希望给标签赋予颜色。为了添加这些行为自定义，我们可以向多个字段小部件添加 ``options`` 属性。

.. 练习:: 添加小部件选项。

    - 为 ``property_type_id`` 字段添加适当的选项，以防止从物业表单视图中创建和编辑物业类型。有关更多信息，请查看 :ref:`Many2one 小部件文档 <reference/js/widgets>`。

    - 添加以下字段：

    =================================== ======================= =======================
    模型                               字段                   类型
    =================================== ======================= =======================
    ``estate.property.tag``             Color                   Integer
    =================================== ======================= =======================

    然后为 ``tag_ids`` 字段添加适当的选项，以在标签上添加颜色选择器。有关更多信息，请查看 :ref:`FieldMany2ManyTags 小部件文档 <reference/js/widgets>`。

在 :doc:`05_firstui` 中，我们看到保留字段用于特定行为。例如， ``active`` 字段用于自动过滤掉非活动记录。我们还添加了 ``state`` 作为保留字段。现在是时候使用它了！可以将 ``state`` 字段与视图中的 ``invisible`` 属性结合使用，以条件显示按钮。

.. 练习:: 添加按钮的条件显示。

    使用 ``invisible`` 属性来条件显示表头按钮，如本节 **目标** 中所示（注意当状态被修改时“已售”和“取消”按钮的变化）。

    提示：可以在 Odoo XML 文件中搜索 ``invisible=`` 以找到一些示例。

更一般来说，可以根据其他字段的值使字段 ``invisible``、 ``readonly`` 或 ``required``。请注意， ``invisible`` 也可以应用于视图的其他元素，例如 ``button`` 或 ``group``。

`invisible`、 `readonly` 和 `required` 可以具有任何 Python 表达式作为值。该表达式给出了属性适用的条件。例如：

.. code-block:: xml

    <form>
        <field name="description" invisible="not is_partner"/>
        <field name="is_partner" invisible="True"/>
    </form>

这意味着当 ``is_partner`` 为 ``False`` 时， ``description`` 字段是不可见的。重要的是要注意，在 ``invisible`` 中使用的字段 **必须** 在视图中存在。如果不应显示给用户，则可以使用 ``invisible`` 属性将其隐藏。

.. 练习:: 使用 ``invisible``。

    - 当没有花园时，在 ``estate.property`` 表单视图中使花园面积和朝向不可见。
    - 一旦报价状态设置，'接受' 和 '拒绝' 按钮应不可见。
    - 不允许在物业状态为 '报价接受'、'已售' 或 '已取消' 时添加报价。为此，使用 ``readonly`` 属性。

.. 警告::

    在视图中使用（条件） ``readonly`` 属性可以防止数据输入错误，但请记住，它不提供任何安全级别！没有进行服务器端检查，因此始终可以通过 RPC 调用写入字段。

列表
----

.. 注意::

    **目标**：在本节结束时，物业和报价列表视图应具有颜色装饰。此外，报价和标签将在列表中直接可编辑，且可用日期默认隐藏。

    .. image:: 11_sprinkles/decoration.png
      :align: center
      :alt: 带装饰和可选字段的列表视图

    .. image:: 11_sprinkles/editable_list.gif
      :align: center
      :alt: 可编辑列表

当模型只有少数字段时，通过列表视图直接编辑记录可能很有用，而无需打开表单视图。在房地产示例中，添加报价或创建新标签时无需打开表单视图。这可以通过 ``editable`` 属性实现。

.. 练习:: 使列表视图可编辑。

    使 ``estate.property.offer`` 和 ``estate.property.tag`` 列表视图可编辑。

另一方面，当模型有很多字段时，添加太多字段到列表视图并使其变得不清晰可能会很诱人。另一种方法是添加字段，但使它们可选地隐藏。这可以通过 ``optional`` 属性实现。

.. 练习:: 使字段可选。

    在 ``estate.property`` 列表视图中使 ``date_availability`` 字段可选，默认隐藏。

最后，颜色代码对于视觉上突出记录很有用。例如，在房地产模块中，我们希望以红色显示拒绝的报价，以绿色显示接受的报价。这可以通过 ``decoration-{$name}`` 属性实现（请参见 :ref:`reference/js/widgets` 获取完整列表）：

.. code-block:: xml

    <tree decoration-success="is_partner==True">
        <field name="name"/>
        <field name="is_partner" invisible="1"/>
    </tree>

当 ``is_partner`` 为 ``True`` 时，记录将以绿色显示。

.. 练习:: 添加一些装饰。

    在 ``estate.property`` 列表视图中：

    - 收到报价的物业为绿色
    - 接受报价的物业为绿色且加粗
    - 已售物业为淡色

    在 ``estate.property.offer`` 列表视图中：

    - 拒绝的报价为红色
    - 接受的报价为绿色
    - 状态不应再可见

    提示：

    - 请记住，**所有** 在属性中使用的字段必须在视图中！
    - 如果您想测试“报价收到”和“报价接受”状态的颜色，请在表单视图中添加该字段并手动更改（我们将在稍后实现业务逻辑）。

搜索
------

**参考**：有关本节的文档可以在 :ref:`reference/view_architectures/search` 和 :ref:`reference/view_architectures/search/defaults` 中找到。

.. 注意::

    **目标**：在本节结束时，可用的物业将默认过滤，并且搜索居住面积将返回面积大于给定数值的结果。

    .. image:: 11_sprinkles/search.gif
      :align: center
      :alt: 默认过滤器和域

最后但同样重要的是，我们希望在搜索时进行一些调整。首先，我们希望在访问物业时默认应用“可用”过滤器。要实现这一点，我们需要使用 ``search_default_{$name}`` 操作上下文，其中 ``{$name}`` 是过滤器名称。这意味着我们可以在操作级别定义将默认激活的过滤器。

这是一个
`操作的示例 <https://github.com/odoo/odoo/blob/6decc32a889b46947db6dd4d42ef995935894a2a/addons/crm/report/crm_opportunity_report_views.xml#L115>`__
及其
`相应的过滤器 <https://github.com/odoo/odoo/blob/6decc32a889b46947db6dd4d42ef995935894a2a/addons/crm/report/crm_opportunity_report_views.xml#L68>`__。

.. 练习:: 添加默认过滤器。

    在 ``estate.property`` 操作中使“可用”过滤器默认选中。

我们模块中的另一个有用改进是能够通过居住面积高效搜索。实际上，用户希望搜索“至少”给定面积的物业。期望用户找到确切居住面积的物业是不现实的。虽然始终可以进行自定义搜索，但这并不方便。

搜索视图的 ``<field>`` 元素可以具有 ``filter_domain``，该域会覆盖在给定字段上生成的搜索域。在给定的域中， ``self`` 代表用户输入的值。在下面的示例中，它用于同时在 ``name`` 和 ``description`` 字段上进行搜索。

.. code-block:: xml

    <search string="Test">
        <field name="description" string="名称和描述"
               filter_domain="['|', ('name', 'ilike', self), ('description', 'ilike', self)]"/>
    </search>

.. 练习:: 更改居住面积搜索。

    为居住面积添加 ``filter_domain``，以包含面积等于或大于给定值的物业。
统计按钮
============

.. 注意::

    **目标**：在本节结束时，物业类型表单视图上将有一个统计按钮，点击后会显示与该类型的物业相关的所有报价列表。

    .. image:: 11_sprinkles/stat_button.gif
      :align: center
      :alt: 统计按钮

如果您已经在 Odoo 中使用了一些功能模块，您可能已经遇到过“统计按钮”。这些按钮显示在表单视图的右上角，提供快速访问链接文档的功能。在我们的房地产模块中，我们希望能够快速链接到与给定物业类型相关的报价，如本节 **目标** 所示。

在本教程的这一点上，我们已经看到了实现这一点的大部分概念。然而，并没有单一的解决方案，如果您不知道从哪里开始，它可能仍然令人困惑。我们将在练习中描述一个逐步的解决方案。查找 ``oe_stat_button`` 的示例在 Odoo 代码库中也总是有用的。

以下练习可能比之前的练习稍微困难一些，因为它假设您能够自行搜索源代码中的示例。如果您卡住了，附近可能有人可以帮助您 ;-)

该练习介绍了 :ref:`reference/fields/related` 的概念。理解它的最简单方法是将其视为计算字段的特定情况。以下 ``description`` 字段的定义：

.. code-block:: python

        ...

        partner_id = fields.Many2one("res.partner", string="合作伙伴")
        description = fields.Char(related="partner_id.name")

等效于：

.. code-block:: python

        ...

        partner_id = fields.Many2one("res.partner", string="合作伙伴")
        description = fields.Char(compute="_compute_description")

        @api.depends("partner_id.name")
        def _compute_description(self):
            for record in self:
                record.description = record.partner_id.name

每当合作伙伴名称更改时，描述也会随之修改。

.. 练习:: 向物业类型添加统计按钮。

    - 将字段 ``property_type_id`` 添加到 ``estate.property.offer``。我们可以将其定义为对 ``property_id.property_type_id`` 的相关字段并设置为存储。

    借助此字段，报价在创建时将与物业类型链接。您可以将该字段添加到报价的列表视图中，以确保其正常工作。

    - 将字段 ``offer_ids`` 添加到 ``estate.property.type``，它是前一步中定义的字段的 One2many 反向。

    - 向 ``estate.property.type`` 添加字段 ``offer_count``。它是一个计算字段，计算与给定物业类型相关的报价数量（使用 ``offer_ids`` 来实现）。

    在这一点上，您拥有所有必要的信息以了解与物业类型链接的报价数量。如果不确定，请将 ``offer_ids`` 和 ``offer_count`` 直接添加到视图中。下一步是在点击统计按钮时显示列表。

    - 在 ``estate.property.type`` 上创建一个统计按钮，指向 ``estate.property.offer`` 动作。这意味着您应该使用 ``type="action"`` 属性（如果需要复习，请回到 :doc:`09_actions` 的末尾）。

    此时，点击统计按钮应显示所有报价。我们仍需过滤报价。

    - 在 ``estate.property.offer`` 动作上，添加一个域，定义 ``property_type_id`` 等于 ``active_id``（= 当前记录， `这里有一个示例 <https://github.com/odoo/odoo/blob/df37ce50e847e3489eb43d1ef6fc1bac6d6af333/addons/event/views/event_views.xml#L162>`__）。

看起来不错吗？如果没有，不用担心， :doc:`下一章 <12_inheritance>` 不需要统计按钮 ;-)
