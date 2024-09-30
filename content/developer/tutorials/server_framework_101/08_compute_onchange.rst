========================================
第8章：计算字段和 onchange
========================================

模型之间的 :doc:`关系 <07_relations>` 是任何 Odoo 模块的关键组成部分。它们对于任何业务案例的建模都是必要的。然而，我们可能希望在给定模型中的字段之间建立链接。有时一个字段的值是从其他字段的值中确定的，有时我们希望帮助用户进行数据输入。

这些情况通过计算字段和 onchange 概念得到支持。虽然本章在技术上并不复杂，但这两个概念的语义非常重要。这也是我们第一次编写 Python 逻辑。到目前为止，我们只编写了类定义和字段声明。

计算字段
===============

**参考**：有关此主题的文档可以在 :ref:`reference/fields/compute` 中找到。

.. 注意::

    **目标**：在本节结束时：

    - 在物业模型中，总面积和最佳报价应被计算：

    .. image:: 08_compute_onchange/compute.gif
        :align: center
        :alt: 计算字段

    - 在物业报价模型中，有效日期应被计算并可以更新：

    .. image:: 08_compute_onchange/compute_inverse.gif
        :align: center
        :alt: 带反向的计算字段

在我们的房地产模块中，我们定义了居住面积和花园面积。因此，自然地定义总面积为这两个字段的和。我们将使用计算字段的概念来实现，即给定字段的值将从其他字段的值中计算得出。

到目前为止，字段一直直接存储在数据库中并直接从数据库中检索。字段也可以是 *计算的*。在这种情况下，字段的值不是从数据库中检索，而是通过调用模型的方法动态计算。

要创建计算字段，创建一个字段并将其属性 :attr:`~odoo.fields.Field.compute` 设置为方法的名称。计算方法应为 ``self`` 中的每条记录设置计算字段的值。

按照约定，:attr:`~odoo.fields.Field.compute` 方法是私有的，这意味着它们不能从表示层调用，仅能从业务层调用（见 :ref:`tutorials/server_framework_101/01_architecture`）。私有方法的名称以下划线 ``_`` 开头。

依赖关系
------------

计算字段的值通常依赖于计算记录中其他字段的值。ORM 期望开发人员通过装饰器 :func:`~odoo.api.depends` 指定这些依赖关系。给定的依赖关系由 ORM 用于在其某些依赖项被修改时触发字段的重新计算：

.. code-block:: python

    from odoo import api, fields, models

    class TestComputed(models.Model):
        _name = "test.computed"

        total = fields.Float(compute="_compute_total")
        amount = fields.Float()

        @api.depends("amount")
        def _compute_total(self):
            for record in self:
                record.total = 2.0 * record.amount

.. 注意:: ``self`` 是一个集合。
    :class: aphorism

    对象 ``self`` 是一个 *记录集*，即有序的记录集合。它支持对集合的标准 Python 操作，例如 ``len(self)`` 和 ``iter(self)``, 以及额外的集合操作，例如 ``recs1 | recs2``。

    在 ``self`` 上迭代会逐一返回记录，每条记录本身是大小为 1 的集合。您可以通过使用点符号访问/赋值单个记录的字段，例如 ``record.name``。

Odoo 中有许多计算字段的示例。
`这里 <https://github.com/odoo/odoo/blob/713dd3777ca0ce9d121d5162a3d63de3237509f4/addons/account/models/account_move.py#L3420-L3423>`__ 是一个简单的示例。

.. 练习:: 计算总面积。

    - 向 ``estate.property`` 添加 ``total_area`` 字段。它定义为 ``living_area`` 和 ``garden_area`` 的和。

    - 按照本节 **目标** 中的第一张图像在表单视图中添加该字段。

对于关系字段，可以通过字段使用路径作为依赖关系：

.. code-block::

    description = fields.Char(compute="_compute_description")
    partner_id = fields.Many2one("res.partner")

    @api.depends("partner_id.name")
    def _compute_description(self):
        for record in self:
            record.description = "测试合作伙伴 %s" % record.partner_id.name

这个例子是用 :class:`~odoo.fields.Many2one` 给出的，但对于 :class:`~odoo.fields.Many2many` 或 :class:`~odoo.fields.One2many` 也是有效的。可以在
`这里 <https://github.com/odoo/odoo/blob/713dd3777ca0ce9d121d5162a3d63de3237509f4/addons/account/models/account_reconcile_model.py#L248-L251>`__ 找到一个示例。

让我们在我们的模块中尝试以下练习！

.. 练习:: 计算最佳报价。

    - 向 ``estate.property`` 添加 ``best_price`` 字段。它定义为报价中 ``price`` 的最高（即最大）值。

    - 按照本节 **目标** 中的第一张图像将该字段添加到表单视图。

    提示：您可能想尝试使用 :meth:`~odoo.models.BaseModel.mapped` 方法。查看
    `这里 <https://github.com/odoo/odoo/blob/f011c9aacf3a3010c436d4e4f408cd9ae265de1b/addons/account/models/account_payment.py#L686>`__ 的简单示例。

反向函数
----------------

您可能注意到计算字段默认是只读的。这是预期的，因为用户不应设置一个值。

在某些情况下，能够直接设置一个值可能很有用。在我们的房地产示例中，我们可以定义报价的有效期限并设置有效日期。我们希望能够设置有效期或日期，二者互相影响。

为此，Odoo 提供了使用 ``inverse`` 函数的能力：

.. code-block:: python

    from odoo import api, fields, models

    class TestComputed(models.Model):
        _name = "test.computed"

        total = fields.Float(compute="_compute_total", inverse="_inverse_total")
        amount = fields.Float()

        @api.depends("amount")
        def _compute_total(self):
            for record in self:
                record.total = 2.0 * record.amount

        def _inverse_total(self):
            for record in self:
                record.amount = record.total / 2.0

可以在
`这里 <https://github.com/odoo/odoo/blob/2ccf0bd0dcb2e232ee894f07f24fdc26c51835f7/addons/crm/models/crm_lead.py#L308-L317>`__ 找到一个示例。

计算方法设置字段，而反向方法设置字段的依赖项。

请注意，``inverse`` 方法在保存记录时调用，而 ``compute`` 方法在每次其依赖项发生变化时调用。

.. 练习:: 计算报价的有效日期。

    - 向 ``estate.property.offer`` 模型添加以下字段：

    ========================= ========================= =========================
    字段                     类型                      默认
    ========================= ========================= =========================
    validity                  Integer                   7
    date_deadline             Date
    ========================= ========================= =========================

    其中 ``date_deadline`` 是一个计算字段，定义为报价中的 ``create_date`` 和 ``validity`` 两个字段的和。定义一个适当的反向函数，以便用户可以设置日期或有效期。

    提示：``create_date`` 仅在创建记录时填写，因此您需要一个后备措施以防在创建时崩溃。

    - 按照本节 **目标** 中的第二张图像在表单视图和列表视图中添加字段。

附加信息
----------------------

计算字段默认 **不存储** 在数据库中。因此，除非定义了 ``search`` 方法，否则 **无法** 在计算字段上进行搜索。这个主题超出了本次培训的范围，因此我们将不予讨论。可以在
`这里 <https://github.com/odoo/odoo/blob/f011c9aacf3a3010c436d4e4f408cd9ae265de1b/addons/event/models/event_event.py#L188>`__ 找到一个示例。

另一种解决方案是使用 ``store=True`` 属性存储字段。虽然这通常很方便，但请注意可能增加到模型的计算负载。让我们重用我们的示例：

.. code-block:: python

    description = fields.Char(compute="_compute_description", store=True)
    partner_id = fields.Many2one("res.partner")

    @api.depends("partner_id.name")
    def _compute_description(self):
        for record in self:
            record.description = "测试合作伙伴 %s" % record.partner_id.name

每次合作伙伴的 ``name`` 发生变化时， ``description`` 会自动为 **所有引用它的记录** 重新计算！当需要重新计算数百万条记录时，这可能会迅速变得不可承受。

还值得注意的是，计算字段可以依赖于另一个计算字段。ORM 足够智能，可以按正确的顺序正确重新计算所有依赖项……但有时可能会导致性能下降。

在定义计算字段时，始终必须考虑性能。计算字段的复杂程度越高（例如，依赖项较多或计算字段依赖于其他计算字段），计算所需的时间就越长。始终花一些时间提前评估计算字段的成本。大多数情况下，只有当您的代码达到生产服务器时，您才会意识到它会减缓整个过程。这可不太好 :-(

Onchanges
=========

**参考**：有关此主题的文档可以在 :func:`~odoo.api.onchange` 中找到：

.. 注意::

    **目标**：在本节结束时，启用花园时将设置默认区域为 10，并将朝向设置为北。

    .. image:: 08_compute_onchange/onchange.gif
        :align: center
        :alt: onchange

在我们的房地产模块中，我们还希望帮助用户进行数据输入。当设置“花园”字段时，我们希望为花园面积和朝向提供默认值。此外，当“花园”字段未设置时，我们希望花园面积重置为零，并删除朝向。在这种情况下，给定字段的值修改其他字段的值。

“onchange”机制为客户端界面提供了一种更新表单的方法，而无需将任何内容保存到数据库中，每当用户填写字段值时。要实现这一点，我们定义一个方法，其中 ``self`` 表示表单视图中的记录，并用 :func:`~odoo.api.onchange` 装饰以指定触发它的字段。您对 ``self`` 的任何更改都将反映在表单中：

.. code-block:: python

    from odoo import api, fields, models

    class TestOnchange(models.Model):
        _name = "test.onchange"

        name = fields.Char(string="名称")
        description = fields.Char(string="描述")
        partner_id = fields.Many2one("res.partner", string="合作伙伴")

        @api.onchange("partner_id")
        def _onchange_partner_id(self):
            self.name = "文档为 %s" % (self.partner_id.name)
            self.description = "默认描述为 %s" % (self.partner_id.name)

在这个例子中，更改合作伙伴也会更改名称和描述值。用户可以选择是否在之后更改名称和描述值。还要注意，我们没有在 ``self`` 上循环，这是因为该方法仅在表单视图中触发，在该视图中 ``self`` 始终是单个记录。

.. 练习:: 设置花园面积和朝向的值。

    在 ``estate.property`` 模型中创建一个 ``onchange`` 方法，以便在花园设置为 True 时设置花园面积（10）和朝向（北）的值。当未设置时，清除字段。

附加信息
----------------------

onchange 方法还可以返回非阻塞的警告消息
（`示例 <https://github.com/odoo/odoo/blob/cd9af815ba591935cda367d33a1d090f248dd18d/addons/payment_authorize/models/payment.py#L34-L36>`__）。

如何使用它们？
================

计算字段和 onchange 的使用没有严格的规则。

在许多情况下，计算字段和 onchange 都可以用于实现相同的结果。始终优先使用计算字段，因为它们也会在表单视图的上下文之外触发。绝对不要使用 onchange 向模型添加业务逻辑。这是一个 **非常糟糕** 的主意，因为 onchange 在以编程方式创建记录时不会自动触发；它们仅在表单视图中触发。

计算字段和 onchange 的常见陷阱是尝试通过添加过多逻辑来“过于聪明”。这可能会导致与预期结果相反的效果：最终用户会因为所有的自动化而感到困惑。

计算字段通常更易于调试：这样的字段是由给定方法设置的，因此很容易跟踪何时设置该值。而 onchange 则可能令人困惑：很难知道 onchange 的范围。由于多个 onchange 方法可能设置相同的字段，因此很容易变得难以追踪值的来源。

使用存储的计算字段时，请密切关注依赖关系。当计算字段依赖于其他计算字段时，更改一个值可能会触发大量的重新计算。这会导致性能下降。

在 :doc:`下一章 <09_actions>` 中，我们将看到如何在点击按钮时触发一些业务逻辑。
