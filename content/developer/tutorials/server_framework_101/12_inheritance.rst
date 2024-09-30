继承
=======================

Odoo 的一个强大方面是其模块化。一个模块专注于特定的业务需求，但模块之间也可以相互作用。这对于扩展现有模块的功能非常有用。例如，在我们的房地产场景中，我们希望在常规用户视图中直接显示销售人员的物业列表。

但是在深入了解特定的 Odoo 模块继承之前，让我们先看看如何更改标准的 CRUD（创建、读取、更新或删除）方法的行为。

Python 继承
==================

.. 注意::

    **目标**：在本节结束时：

    - 不应删除状态既不是“新建”也不是“已取消”的物业。

    .. image:: 12_inheritance/unlink.gif
        :align: center
        :alt: 解除链接

    - 创建报价时，物业状态应更改为“报价收到”
    - 不应以低于现有报价的价格创建报价

    .. image:: 12_inheritance/create.gif
        :align: center
        :alt: 创建

在我们的房地产模块中，我们从未需要开发任何特定功能来执行标准的 CRUD 操作。Odoo 框架提供了执行这些操作所需的必要工具。实际上，由于经典的 Python 继承，这些操作已经包含在我们的模型中::

    from odoo import fields, models

    class TestModel(models.Model):
        _name = "test_model"
        _description = "测试模型"

        ...

我们的 ``class TestModel`` 继承自 :class:`~odoo.models.Model`，它提供了 :meth:`~odoo.models.Model.create`、 :meth:`~odoo.models.Model.read`、 :meth:`~odoo.models.Model.write` 和 :meth:`~odoo.models.Model.unlink` 方法。

这些方法（以及在 :class:`~odoo.models.Model` 中定义的任何其他方法）可以被扩展以添加特定的业务逻辑::

    from odoo import fields, models

    class TestModel(models.Model):
        _name = "test_model"
        _description = "测试模型"

        ...

        @api.model
        def create(self, vals):
            # 执行一些业务逻辑，修改 vals...
            ...
            # 然后调用 super 执行父方法
            return super().create(vals)

装饰器 :func:`~odoo.api.model` 对 :meth:`~odoo.models.Model.create` 方法是必要的，因为在创建的上下文中，记录集 ``self`` 的内容并不相关，但在其他 CRUD 方法中则不是必需的。

同样重要的是，即使我们可以直接重写 :meth:`~odoo.models.Model.unlink` 方法，您几乎总是希望使用装饰器 :func:`~odoo.api.ondelete` 编写一个新方法。带有此装饰器的方法将在 :meth:`~odoo.models.Model.unlink` 时被调用，避免在直接重写 :meth:`~odoo.models.Model.unlink` 时可能发生的一些问题。

在 Python 3 中， ``super()`` 相当于 ``super(TestModel, self)``。后者在您需要使用修改后的记录集调用父方法时可能是必需的。

.. 危险::

    - **始终** 调用 ``super()`` 以避免打断流程。只有在非常特定的情况下，您才不想调用它。
    - 确保 **始终** 返回与父方法一致的数据。例如，如果父方法返回一个 ``dict()``，则您的重写也必须返回一个 ``dict()``。

.. 练习:: 向 CRUD 方法添加业务逻辑。

    - 如果物业的状态不是 'New' 或 'Canceled'，则防止其被删除。

    提示：创建一个带有 :func:`~odoo.api.ondelete` 装饰器的新方法，并记住 ``self`` 可以是包含多个记录的记录集。

    - 在创建报价时，将物业状态设置为 'Offer Received'。如果用户尝试以低于现有报价的价格创建报价，则也抛出错误。

    提示： ``property_id`` 字段在 ``vals`` 中可用，但它是一个 ``int``。要实例化一个 ``estate.property`` 对象，请使用 ``self.env[model_name].browse(value)``（ `示例 <https://github.com/odoo/odoo/blob/136e4f66cd5cafe7df450514937c7218c7216c93/addons/gamification/models/badge.py#L57>`__）。

模型继承
=================

**参考**：有关此主题的文档可以在 :ref:`reference/orm/inheritance` 中找到。

在我们的房地产模块中，我们希望在设置 / 用户与公司 / 用户表单视图中直接显示与销售人员相关的物业列表。为此，我们需要向 ``res.users`` 模型添加一个字段，并调整其视图以显示该字段。

Odoo 提供了两种*继承*机制，以模块化的方式扩展现有模型。

第一个继承机制允许模块通过以下方式修改定义在另一个模块中的模型的行为：

- 向模型添加字段，
- 重写模型中的字段定义，
- 向模型添加约束，
- 向模型添加方法，
- 重写模型中的现有方法。

第二个继承机制（委托）允许每个模型的记录与父模型的记录链接，并提供对该父记录字段的透明访问。

.. image:: 12_inheritance/inheritance_methods.png
    :align: center
    :alt: 继承方法

在 Odoo 中，第一个机制无疑是最常用的。在我们的案例中，我们希望向现有模型添加一个字段，这意味着我们将使用第一个机制。例如::

    from odoo import fields, models

    class InheritedModel(models.Model):
        _inherit = "inherited.model"

        new_field = fields.Char(string="新字段")

添加到模型的两个字段的实用示例可以在
`这里 <https://github.com/odoo/odoo/blob/60e9410e9aa3be4a9db50f6f7534ba31fea3bc29/addons/account_fleet/models/account_move.py#L39-L47>`__ 找到。

根据约定，每个继承模型在其自己的 Python 文件中定义。在我们的示例中，它将是 ``models/inherited_model.py``。

.. 练习:: 向用户添加字段。

    - 向 ``res.users`` 添加以下字段：

    ===================== ================================================================
    字段                 类型
    ===================== ================================================================
    property_ids          One2many 反向引用 ``estate.property`` 中的销售人员字段
    ===================== ================================================================

    - 向字段添加域，以便仅列出可用的物业。

在下一节中，让我们将字段添加到视图中，并检查一切是否正常工作！

视图继承
================

**参考**：有关此主题的文档可以在 :ref:`reference/view_records/inheritance` 中找到。

.. 注意::

    **目标**：在本节结束时，链接到销售人员的可用物业列表应显示在其用户表单视图中。

    .. image:: 12_inheritance/users.png
        :align: center
        :alt: 用户

Odoo 提供视图继承，而不是直接修改现有视图（通过重写它们）。子“扩展”视图应用于根视图的顶部。这些扩展可以同时添加和删除其父视图的内容。

扩展视图通过 ``inherit_id`` 字段引用其父视图。它的 ``arch`` 字段包含多个 ``xpath`` 元素，这些元素选择并更改其父视图的内容：

.. code-block:: xml

    <record id="inherited_model_view_form" model="ir.ui.view">
        <field name="name">inherited.model.form.inherit.test</field>
        <field name="model">inherited.model</field>
        <field name="inherit_id" ref="inherited.inherited_model_view_form"/>
        <field name="arch" type="xml">
            <!-- 找到字段描述并在后面添加字段
                 new_field -->
            <xpath expr="//field[@name='description']" position="after">
              <field name="new_field"/>
            </xpath>
        </field>
    </record>

``expr``
    一个 XPath_ 表达式，用于选择父视图中的单个元素。
    如果它不匹配任何元素或匹配多个元素，则引发错误。
``position``
    应用到匹配元素的操作：

    ``inside``
        将 ``xpath`` 的主体附加到匹配元素的末尾。
    ``replace``
        用 ``xpath`` 的主体替换匹配的元素，替换新主体中所有 ``$0`` 节点的出现。
    ``before``
        将 ``xpath`` 的主体作为兄弟插入到匹配元素之前。
    ``after``
        将 ``xpath`` 的主体作为兄弟插入到匹配元素之后。
    ``attributes``
        使用 ``xpath`` 的主体中的特殊 ``attribute`` 元素更改匹配元素的属性。

当匹配单个元素时，可以直接在要找到的元素上设置 ``position`` 属性。下面两个继承具有相同的结果。

.. code-block:: xml

    <xpath expr="//field[@name='description']" position="after">
        <field name="idea_ids" />
    </xpath>

    <field name="description" position="after">
        <field name="idea_ids" />
    </field>

视图继承扩展的示例可以在
`这里 <https://github.com/odoo/odoo/blob/691d1f087040f1ec7066e485d19ce3662dfc6501/addons/account_fleet/views/account_move_views.xml#L3-L17>`__ 找到。

.. 练习:: 向用户视图添加字段。

    在新的 notebook 页面中将 ``property_ids`` 字段添加到 ``base.view_users_form``。

    提示：有关用户视图继承的示例可以在
    `这里 <https://github.com/odoo/odoo/blob/691d1f087040f1ec7066e485d19ce3662dfc6501/addons/gamification/views/res_users_views.xml#L5-L14>`__ 找到。

继承在 Odoo 中广泛使用，因为它的模块化概念。请随时阅读相关文档以获取更多信息！

在 :doc:`下一章 <13_other_module>` 中，我们将学习如何与其他模块进行交互。

.. _XPath: https://w3.org/TR/xpath
