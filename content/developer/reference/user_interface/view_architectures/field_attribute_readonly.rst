.. attribute:: readonly
   :noindex:

   字段是否可以被用户修改（`False`）或为只读（`True`），作为一个Python表达式，评估为布尔值。

   .. example::
      .. code-block:: xml

         <field name="fname_a" readonly="True"/>
         <field name="fname_b" readonly="name_a in [fname_b, parent.fname_d]"/>

   :requirement: 可选
   :type: :ref:`Python 表达式 <reference/view_architectures/python_expression>`
   :default: `False`
