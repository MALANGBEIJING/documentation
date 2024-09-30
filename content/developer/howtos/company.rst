.. _reference/howtos/company:

========================
多公司指南
========================

.. warning::

    本教程需要具备良好的 Odoo 知识。
    如果需要，请先参考 :doc:`../tutorials/server_framework_101` 教程。

从版本 13.0 开始，用户可以同时登录多个公司。这允许用户访问多个公司的信息，也可以在多公司环境中创建/编辑记录。

如果管理不当，可能会导致许多多公司行为不一致。例如，一个同时登录公司 A 和公司 B 的用户可以在公司 A 中创建销售订单并向其中添加属于公司 B 的产品。只有当用户退出公司 B 时，销售订单才会发生访问错误。

为了正确管理多公司行为，Odoo 的 ORM 提供了多种功能：

- :ref:`公司相关字段 <howto/company/company_dependent>`
- :ref:`多公司一致性 <howto/company/check_company>`
- :ref:`默认公司 <howto/company/default_company>`
- :ref:`视图 <howto/company/views>`
- :ref:`安全规则 <howto/company/security>`

.. _howto/company/company_dependent:

公司相关字段
------------------------

当一个记录可以在多个公司之间共享时，我们必须预期，视哪个公司设置了值，给定字段会有不同的值。

为了让同一记录的字段支持多个值，必须将该字段定义为 `company_dependent=True`。

.. code-block:: python

   from odoo import api, fields, models

   class Record(models.Model):
       _name = 'record.public'

       info = fields.Text()
       company_info = fields.Text(company_dependent=True)
       display_info = fields.Text(string='信息', compute='_compute_display_info')

       @api.depends_context('company')
       def _compute_display_info(self):
           for record in self:
               record.display_info = record.info + record.company_info

.. note:: `_compute_display_info` 方法使用了 `depends_context('company')` 装饰器
          (参见 :attr:`~odoo.api.depends_context`)，以确保根据当前公司 (`self.env.company`) 重新计算计算字段。

读取公司相关字段时，使用当前公司来检索其值。换句话说，如果用户登录到公司 A 和公司 B，且 A 是主公司，并为公司 B 创建记录，则公司相关字段的值将是公司 A 的值。

要读取由当前公司以外的公司设置的公司相关字段的值，我们需要确保使用的公司是正确的公司。这可以通过 :meth:`~odoo.models.Model.with_company` 来完成，它会更新当前公司。

.. code-block:: python

   # 作为主公司 (self.env.company) 访问
   val = record.company_dependent_field

   # 作为目标公司 (company_B) 访问
   val = record.with_company(company_B).company_dependent_field
   # record.with_company(company_B).env.company == company_B

.. warning::

    每当您在不同公司中计算/创建/...时，应该确保您在正确的公司执行这些操作。使用 `with_company` 并不会增加很大的开销，但可以避免日后产生问题。

    .. code-block:: python

       @api.onchange('field_name')
       def _onchange_field_name(self):
        self = self.with_company(self.company_id)
        ...

       @api.depends('field_2')
       def _compute_field_3(self):
        for record in self:
          record = record.with_company(record.company_id)
          ...

.. _howto/company/check_company:

多公司一致性
-------------------------

当通过 `company_id` 字段使记录可以在多个公司之间共享时，我们必须确保它不能通过关系字段链接到另一家公司。例如，我们不希望销售订单和其发票属于不同的公司。

为了确保多公司一致性，您必须：

* 将类属性 `_check_company_auto` 设置为 `True`。
* 如果模型具有 `company_id` 字段，则将关系字段定义为 `check_company=True`。

在每次 :meth:`~odoo.models.Model.create` 和 :meth:`~odoo.models.Model.write` 操作时，都会触发自动检查，以确保记录的多公司一致性。

.. code-block:: python

   from odoo import fields, models

   class Record(models.Model):
       _name = 'record.shareable'
       _check_company_auto = True

       company_id = fields.Many2one('res.company')
       other_record_id = fields.Many2one('other.record', check_company=True)

.. note:: `company_id` 字段不能定义为 `check_company=True`。

.. currentmodule:: odoo.models
.. automethod:: Model._check_company

.. warning:: `check_company` 功能执行严格检查！这意味着如果一条记录没有 `company_id`（即，该字段不是必填项），则它不能与已设置 `company_id` 的记录链接。

.. note::

    当字段没有定义 domain 且 `check_company` 设置为 `True` 时，系统将添加一个默认的 domain：`['|', '('company_id', '=', False), ('company_id', '=', company_id)]`

.. _howto/company/default_company:

默认公司
---------------

当 `company_id` 字段在模型中是必填项时，良好的做法是设置一个默认公司。这简化了用户的设置流程，或者当公司字段在视图中被隐藏时保证其有效性。事实上，如果用户没有访问多个公司的权限（即，用户没有 `base.group_multi_company` 组），公司字段通常是隐藏的。

.. code-block:: python

   from odoo import api, fields, models

   class Record(models.Model):
       _name = 'record.restricted'
       _check_company_auto = True

       company_id = fields.Many2one(
           'res.company', required=True, default=lambda self: self.env.company
       )
       other_record_id = fields.Many2one('other.record', check_company=True)


.. _howto/company/views:

视图
-----

如前述 :ref:`default_company <howto/company/default_company>` 所述，如果用户没有访问多个公司的权限，公司字段通常在视图中被隐藏。这是通过 `base.group_multi_company` 组来评估的。

.. code-block:: xml

   <record model="ir.ui.view" id="record_form_view">
       <field name="name">record.restricted.form</field>
       <field name="model">record.restricted</field>
       <field name="arch" type="xml">
           <form>
               <sheet>
                   <group>
                       <group>
                           <field name="company_id" groups="base.group_multi_company"/>
                           <field name="other_record_id"/>
                       </group>
                   </group>
               </sheet>
           </form>
       </field>
   </record>


.. _howto/company/security:

安全规则
--------------

在处理跨公司共享或限制在单个公司的记录时，我们必须确保用户无法访问其他公司所属的记录。

这是通过基于 `company_ids` 的安全规则实现的，`company_ids` 包含当前用户选中的公司（用户在多公司小部件中选中的公司）。

.. code-block:: xml

    <!-- 共享记录 -->
    <record model="ir.rule" id="record_shared_company_rule">
        <field name="name">共享记录：多公司</field>
        <field name="model_id" ref="model_record_shared"/>
        <field name="global" eval="True"/>
        <field name="domain_force">
            ['|', ('company_id', '=', False), ('company_id', 'in', company_ids)]
        </field>
    </record>

.. code-block:: xml

    <!-- 公司限制的记录 -->
    <record model="ir.rule" id="record_restricted_company_rule">
        <field name="name">限制记录：多公司</field>
        <field name="model_id" ref="model_record_restricted"/>
        <field name="global" eval="True"/>
        <field name="domain_force">
            [('company_id', 'in', company_ids)]
        </field>
    </record>

.. todo:: 检查公司相关字段上的 check_company 设置。
