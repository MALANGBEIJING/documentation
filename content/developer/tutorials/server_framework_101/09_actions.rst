==================================
第9章：准备好进行一些操作了吗？
==================================

到目前为止，我们主要通过声明字段和视图来构建我们的模块。我们刚刚在 :doc:`上一章 <08_compute_onchange>` 中引入了业务逻辑，这要归功于计算字段和 onchange。在任何实际的商业场景中，我们希望将一些业务逻辑与操作按钮链接起来。在我们的房地产示例中，我们希望能够：

- 取消或将物业标记为已售
- 接受或拒绝报价

有人可能会认为，我们已经可以通过手动更改状态来完成这些操作，但这并不方便。此外，我们想要添加一些额外的处理：当报价被接受时，我们希望为物业设置销售价格和买方。

对象类型
===========

**参考**：有关此主题的文档可以在 :doc:`../../reference/backend/actions` 和 :ref:`reference/exceptions` 中找到。

.. 注意::

    **目标**：在本节结束时：

    - 您应该能够取消或将物业标记为已售：

    .. image:: 09_actions/property.gif
        :align: center
        :alt: 取消并设置为已售

    已取消的物业不能被出售，已售的物业不能被取消。为了清晰起见，在视图中添加了 ``state`` 字段。

    - 您应该能够接受或拒绝报价：

    .. image:: 09_actions/offer_01.gif
        :align: center
        :alt: 接受或拒绝报价

    - 一旦报价被接受，销售价格和买方应被设置：

    .. image:: 09_actions/offer_02.gif
        :align: center
        :alt: 接受报价

在我们的房地产模块中，我们希望将业务逻辑与一些按钮链接起来。最常见的方法是：

- 在视图中添加一个按钮，例如在视图的 ``header`` 中：

.. code-block:: xml

    <form>
        <header>
            <button name="action_do_something" type="object" string="做某事"/>
        </header>
        <sheet>
            <field name="name"/>
        </sheet>
    </form>

- 并将此按钮链接到业务逻辑：

.. code-block:: python

    from odoo import fields, models

    class TestAction(models.Model):
        _name = "test.action"

        name = fields.Char()

        def action_do_something(self):
            for record in self:
                record.name = "某事"
            return True

通过将 ``type="object"`` 指定给我们的按钮，Odoo 框架将执行给定模型上的 ``name="action_do_something"`` 的 Python 方法。

需要注意的第一个重要细节是，我们的方法名称没有以下划线（``_``）开头。这使得我们的方法成为 **公共** 方法，可以直接从 Odoo 界面调用（通过 RPC 调用）。到目前为止，我们创建的所有方法（计算、onchange）都是内部调用，因此我们使用了以下划线开头的 **私有** 方法。除非需要从用户界面调用，否则您应该始终将方法定义为私有。

还要注意我们在 ``self`` 上循环。始终假设一个方法可以在多个记录上调用；这有利于可重用性。

最后，公共方法应始终返回某些内容，以便可以通过 XML-RPC 调用。当不确定时，只需 ``return True``。

在 Odoo 源代码中有数百个示例。一个示例是这个
`视图中的按钮 <https://github.com/odoo/odoo/blob/cd9af815ba591935cda367d33a1d090f248dd18d/addons/crm/views/crm_lead_views.xml#L9-L11>`__
及其
`相应的 Python 方法 <https://github.com/odoo/odoo/blob/cd9af815ba591935cda367d33a1d090f248dd18d/addons/crm/models/crm_lead.py#L746-L760>`__。

.. 练习:: 取消并将物业标记为已售。

    - 向 ``estate.property`` 模型添加按钮 '取消' 和 '已售'。已取消的物业不能被设置为已售，而已售的物业不能被取消。

      请参考 **目标** 的第一张图像以获取预期结果。

      提示：要引发错误，可以使用 :ref:`UserError<reference/exceptions>` 函数。Odoo 源代码中有许多示例 ;-)

    - 向 ``estate.property.offer`` 模型添加按钮 '接受' 和 '拒绝'。

      请参考 **目标** 的第二张图像以获取预期结果。

      提示：要使用图标作为按钮，请查看
      `这个示例 <https://github.com/odoo/odoo/blob/cd9af815ba591935cda367d33a1d090f248dd18d/addons/event/views/event_views.xml#L521>`__。

    - 当报价被接受时，为相应物业设置买方和销售价格。

      请参考 **目标** 的第三张图像以获取预期结果。

      注意：在现实生活中，对于给定的物业，只能接受一个报价！

操作类型
===========

在 :doc:`05_firstui` 中，我们创建了一个链接到菜单的操作。您可能会想知道是否可以将操作链接到按钮。好消息是，可以！一种方法是：

.. code-block:: xml

    <button type="action" name="%(test.test_model_action)d" string="我的操作"/>

我们使用 ``type="action"``，并在 ``name`` 中引用 :term:`外部标识符`。

在 :doc:`下一章 <10_constraints>` 中，我们将看到如何防止在 Odoo 中编码不正确的数据。
