.. attribute:: widget
   :noindex:

   用于替代分配给字段类型的默认渲染方法和上下文（例如，:class:`~odoo.fields.Char`，:class:`~odoo.fields.Many2one`）。参见 :ref:`reference/js/widgets`。

   .. example::
      .. code-block:: xml

         <form>
             <field name="tag_ids" widget="many2many_tags"/>
         </form>
         <tree>
             <field name="sequence" widget="handle"/>
             <field name="level_progress" widget="progressbar"/>
         </tree>

   :requirement: 可选
   :type: str
   :default: `''`
