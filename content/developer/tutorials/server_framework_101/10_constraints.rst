=======================
第10章：约束
=======================

在 :doc:`上一章 <09_actions>` 中，我们介绍了向模型添加一些业务逻辑的能力。现在我们可以将按钮链接到业务代码，但我们如何防止用户输入不正确的数据呢？例如，在我们的房地产模块中，没有任何机制阻止用户设置负的预期价格。

Odoo 提供了两种设置自动验证不变式的方法： :func:`Python 约束 <odoo.api.constrains>` 和 :attr:`SQL 约束 <odoo.models.Model._sql_constraints>`。

SQL
===

**参考**：有关此主题的文档可以在 :ref:`reference/orm/models` 中找到，以及 `PostgreSQL 的文档`_。

.. 注意::

    **目标**：在本节结束时：

    - 金额应为（严格）正数

    .. image:: 10_constraints/sql_01.gif
        :align: center
        :alt: 金额约束

    - 物业类型和标签应具有唯一名称

    .. image:: 10_constraints/sql_02.gif
        :align: center
        :alt: 名称约束

SQL 约束通过模型属性 :attr:`~odoo.models.Model._sql_constraints` 定义。此属性分配一个三元组列表，包含字符串 ``(name, sql_definition, message)``，其中 ``name`` 是有效的 SQL 约束名称， ``sql_definition`` 是一个 table_constraint_ 表达式，而 ``message`` 是错误消息。

您可以找到一个简单示例
`这里 <https://github.com/odoo/odoo/blob/24b0b6f07f65b6151d1d06150e376320a44fd20a/addons/analytic/models/analytic_account.py#L20-L23>`__。

.. 练习:: 添加 SQL 约束。

    将以下约束添加到相应模型中：

    - 物业预期价格必须严格为正数
    - 物业销售价格必须为正数
    - 报价价格必须严格为正数
    - 物业标签名称和物业类型名称必须唯一

    提示：在 Odoo 代码库中搜索 ``unique`` 关键字以查找唯一名称的示例。

使用 ``-u estate`` 选项重新启动服务器以查看结果。请注意，您可能有数据会阻止 SQL 约束的设置。可能会弹出类似以下的错误消息：

.. code-block:: text

    ERROR rd-demo odoo.schema: Table 'estate_property_offer': unable to add constraint 'estate_property_offer_check_price' as CHECK(price > 0)

例如，如果某些报价的价格为零，则无法应用约束。您可以删除有问题的数据以应用新约束。

Python
======

**参考**：有关此主题的文档可以在 :func:`~odoo.api.constrains` 中找到。

.. 注意::

    **目标**：在本节结束时，不可能接受低于预期价格 90% 的报价。

    .. image:: 10_constraints/python.gif
        :align: center
        :alt: Python 约束

SQL 约束是确保数据一致性的有效方式。然而，有时可能需要更复杂的检查，这需要 Python 代码。在这种情况下，我们需要一个 Python 约束。

Python 约束定义为带有 :func:`~odoo.api.constrains` 装饰器的方法，并在记录集上调用。装饰器指定了哪些字段参与约束。当任何这些字段被修改时，约束会自动进行评估。方法应该在不满足其不变式时引发异常：

.. code-block:: python

    from odoo.exceptions import ValidationError

    ...

    @api.constrains('date_end')
    def _check_date_end(self):
        for record in self:
            if record.date_end < fields.Date.today():
                raise ValidationError("结束日期不能设置在过去")
        # 所有记录都通过测试，不返回任何内容

可以在
`这里 <https://github.com/odoo/odoo/blob/274dd3bf503e1b612179db92e410b336bfaecfb4/addons/stock/models/stock_quant.py#L239-L244>`__ 找到一个简单的示例。

.. 练习:: 添加 Python 约束。

    添加一个约束，以确保销售价格不能低于预期价格的 90%。

    提示：销售价格在报价被验证之前为零。您需要微调检查以考虑到这一点。

    .. 警告::

        在处理浮点数时，始终使用 :meth:`~odoo.tools.float_utils.float_compare` 和 :meth:`~odoo.tools.float_utils.float_is_zero` 方法来自 `odoo.tools.float_utils` ！

    确保在每次更改销售价格或预期价格时触发约束！

SQL 约束通常比 Python 约束更有效。当性能很重要时，始终优先使用 SQL 约束。

我们的房地产模块开始变得不错。我们添加了一些业务逻辑，并且现在确保数据一致性。然而，用户界面仍然有些粗糙。让我们看看如何在 :doc:`下一章 <11_sprinkles>` 中改善它。

.. _PostgreSQL 的文档：
.. _table_constraint:
    https://www.postgresql.org/docs/12/ddl-constraints.html
