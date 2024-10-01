.. attribute:: context
   :noindex:

   在执行按钮调用时合并到视图上下文中的上下文，作为一个评估为字典的 Python 表达式。

   .. example::
      .. code-block:: xml

         <button name="button_confirm" type="object" context="{'BUSINESS_KEY': ANY}" string="LABEL"/>

   :requirement: 可选
   :type: :ref:`Python 表达式 <reference/view_architectures/python_expression>`
   :default: `{}`
