与其他模块交互
=======================================

在 :doc:`上一章 <12_inheritance>` 中，我们使用继承来修改模块的行为。在我们的房地产场景中，我们希望更进一步，能够为客户生成发票。Odoo 提供了一个发票模块，因此从我们的房地产模块直接创建发票是个不错的主意，也就是说，一旦物业设置为“已售”，将在发票应用程序中创建发票。

具体示例：账单移动
==============================

.. 注意::

    **目标**：在本节结束时：

    - 应创建一个新的模块 ``estate_account``
    - 当物业被出售时，应为买方开具发票

    .. image:: 13_other_module/create_inv.gif
        :align: center
        :alt: 发票创建

每当我们与另一个模块交互时，我们需要记住模块化。如果我们打算将我们的应用程序出售给房地产机构，有些机构可能希望使用发票功能，而其他机构可能不希望使用。

链接模块
-----------

此类用例的常见方法是创建一个“链接”模块。在我们的案例中，该模块将依赖于 ``estate`` 和 ``account``，并将包括房地产物业的发票创建逻辑。这样，房地产和会计模块可以独立安装。当同时安装这两个模块时，链接模块提供新功能。

.. 练习:: 创建链接模块。

    创建 ``estate_account`` 模块，该模块依赖于 ``estate`` 和 ``account`` 模块。
    目前，它将是一个空壳。

    提示：您已经在 :doc:`教程开始 <02_newapp>` 时做过这件事。这个过程非常相似。

当 ``estate_account`` 模块出现在列表中时，请安装它！您会注意到发票应用程序也已安装。这是预期的，因为您的模块依赖于它。如果您卸载发票应用程序，则该模块也将被卸载。

发票创建
----------------

现在是生成发票的时候了。我们想要向 ``estate.property`` 模型添加功能，也就是说，我们希望为物业出售时添加一些额外逻辑。这听起来很熟悉吗？如果没有，建议您返回 :doc:`上一章 <12_inheritance>`，因为您可能错过了某些内容 ;-)

第一步，我们需要扩展按下物业的 :doc:`'已售' 按钮 <09_actions>` 时调用的操作。为此，我们需要在 `estate_account` 模块中为 ``estate.property`` 模型创建一个 :doc:`模型继承 <12_inheritance>`。目前，重写的操作将简单地返回 ``super`` 调用。也许一个示例会使事情更清楚::

    from odoo import models

    class InheritedModel(models.Model):
        _inherit = "inherited.model"

        def inherited_action(self):
            return super().inherited_action()

一个实用的示例可以在
`这里 <https://github.com/odoo/odoo/blob/f1f48cdaab3dd7847e8546ad9887f24a9e2ed4c1/addons/event_sale/models/account_move.py#L7-L16>`__ 找到。

.. 练习:: 添加发票创建的第一步。

    - 在 ``estate_account`` 模块的正确文件夹中创建一个 ``estate_property.py`` 文件。
    - ``_inherit`` ``estate.property`` 模型。
    - 重写 ``action_sold`` 方法（您可能将其命名为其他名称）以返回 ``super`` 调用。

    提示：为确保其工作正常，请在重写的方法中添加 ``print`` 或调试断点。

是否有效？如果没有，检查所有 Python 文件是否正确导入。

如果重写有效，我们可以继续创建发票。不幸的是，没有简单的方法知道如何在 Odoo 中创建任何给定对象。大多数情况下，需要查看其模型以查找所需字段并提供适当的值。

一个很好的学习方法是查看其他模块如何做您想要做的事情。例如，销售的基本流程之一是从销售订单创建发票。这看起来是一个良好的起点，因为它确切地执行我们想要做的事情。花一些时间阅读和理解 ` _create_invoices <https://github.com/odoo/odoo/blob/f1f48cdaab3dd7847e8546ad9887f24a9e2ed4c1/addons/sale/models/sale.py#L610-L717>`__ 方法。当您阅读完毕时，不要因为这个简单的任务看起来复杂而感到沮丧，我们可以继续教程。

要创建发票，我们需要以下信息：

- 一个 ``partner_id``：客户
- 一个 ``move_type``：它有多个 `可能的值 <https://github.com/odoo/odoo/blob/f1f48cdaab3dd7847e8546ad9887f24a9e2ed4c1/addons/account/models/account_move.py#L138-L147>`__
- 一个 ``journal_id``：会计日记账

这些信息足以创建一个空发票。

.. 练习:: 添加发票创建的第二步。

    在 ``action_sold`` 方法的重写中创建一个空的 ``account.move``：

    - ``partner_id`` 来自当前的 ``estate.property``
    - ``move_type`` 应对应于“客户发票”

    提示：

    - 要创建对象，请使用 ``self.env[model_name].create(values)``，其中 ``values`` 是一个 ``dict``。
    - ``create`` 方法不接受记录集作为字段值。

当物业设置为“已售”时，您现在应该在发票 / 客户 / 发票中看到创建的新客户发票。

显然，到目前为止，我们没有任何发票行。要创建发票行，我们需要以下信息：

- ``name``：行的描述
- ``quantity``：数量
- ``price_unit``：单价

此外，发票行需要链接到发票。链接行到发票的最简单和最有效的方法是在创建发票时包含所有行。为此，``invoice_line_ids`` 字段包含在 ``account.move`` 的创建中，这是一个 :class:`~odoo.fields.One2many`。One2many 和 Many2many 使用特殊的“命令”，这些命令通过 :class:`~odoo.fields.Command` 命名空间使其人性化。该命名空间表示在一组记录上执行的三元组命令。最初，这个三元组是执行这些命令的唯一选项，但现在标准是使用命名空间。格式是将它们放在一个按顺序执行的列表中。以下是创建 ``test_model`` 时包括 One2many 字段 ``line_ids`` 的简单示例::

    from odoo import Command

    def inherited_action(self):
        self.env["test_model"].create(
            {
                "name": "测试",
                "line_ids": [
                    Command.create({
                        "field_1": "value_1",
                        "field_2": "value_2",
                    })
                ],
            }
        )
        return super().inherited_action()

.. 练习:: 添加发票创建的第三步。

    在 ``account.move`` 的创建过程中添加两个发票行。每个出售的物业将根据以下条件开具发票：

    - 销售价格的 6%
    - 额外的 100.00 管理费用

    提示：按照上述示例，在创建时添加 ``invoice_line_ids``。
    对于每一行，我们需要一个 ``name``、 ``quantity`` 和 ``price_unit``。

本章可能是迄今为止最困难的章节，但它最接近实际 Odoo 开发。在 :doc:`下一章 <14_qwebintro>` 中，我们将介绍 Odoo 中使用的模板机制。
