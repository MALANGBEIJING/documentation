.. attribute:: type
   :noindex:

   按钮的类型，指示其行为方式。可以有两种不同的值：

   .. attribute:: object
      :noindex:

      调用视图模型上的方法。按钮的 `name` 是调用的方法，与当前记录 ID 和当前 `context` 一起使用。

   .. attribute:: action
      :noindex:

      加载并执行 `ir.actions` 操作记录。按钮的 `name` 是要加载的操作的 XMLID。`context` 会扩展视图的模型（作为 `active_model`）和当前记录（作为 `active_id`）。

   .. example::
      .. code-block:: xml

         <button type="object" name="action_create_new" string="Create document"/>
         <button type="action" name="addon.action_create_view" string="Create and Edit"/>

   :requirement: 如果未设置 `special` 属性，则为必填项
   :type: str
