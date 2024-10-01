.. attribute:: required
   :noindex:

   字段是否可以留空（`False`）或必须设置（`True`），作为一个Python表达式，评估为布尔值。

   .. example::
      .. code-block:: xml

         <field name="fname_a" required="True"/>
         <field name="fname_b" required="fname_c != 3"/>

   :requirement: 可选
   :type: :ref:`Python 表达式 <reference/view_architectures/python_expression>`
   :default: `False`
