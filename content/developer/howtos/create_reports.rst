=========================
创建自定义报表
=========================

SQL 视图是一种创建自定义报表的技术，用于显示现有模型的字段和视图无法显示的数据。换句话说，这种技术可以避免为了数据分析目的而不必要地创建和计算额外字段。

创建模型
==============

SQL 视图的创建方式与标准模型相似：

.. code-block:: python

  from odoo import fields, models


  class ModuleReport(models.Model):
      _name = 'module.report'
      _description = "模块报表"
      _rec_name = 'module_field'
      _auto = False

其中属性：

- `_auto = False` 表示我们不希望将模型存储在数据库中
- `_rec_name` 指定模型的哪个字段代表记录的名称（即在打开记录的表单视图时导航面包屑中使用的名称）

字段的定义方式与标准模型相同，唯一的区别是每个字段都标记为 `readonly=True`。

.. note::
   别忘了将您的新模型添加到安全文件中。

填充模型
==================

填充 SQL 视图表有两种方法：

- 重写 `BaseModel.init()` 方法，
- 设置 `_table_query` 属性。

无论采用哪种方法，SQL 查询将被执行以填充模型。因此，任何 SQL 命令都可以用于收集和/或计算所需数据。请记住，您绕过了 ORM（即，如果还没有，建议阅读 :ref:`reference/security`）。`SELECT` 返回的列将填充模型的字段，因此请确保列名与字段名匹配，或者使用与字段名匹配的别名。

.. tabs::

   .. tab:: 重写 `BaseModel.init()`

      在大多数情况下，重写 `BaseModel.init()` 方法是更标准和更好的选择。它需要导入 `tools`，通常写成如下形式：

      .. code-block:: python

         def init(self):
             tools.drop_view_if_exists(self.env.cr, self._table)
             self.env.cr.execute("""CREATE or REPLACE VIEW %s as (
                                    SELECT
                                       %s
                                    FROM
                                       %s
                 )""" % (self._table, self._select(), self._from()))

      `tools.drop_view_if_exists` 确保在执行 SQL 查询时不会创建冲突的视图。通常将查询的不同部分分离开来，以便更容易扩展模型。具体如何在方法之间拆分查询并没有标准化，但至少 `_select` 和 `_from` 方法是常见的，当然，这些方法都将返回字符串。

      .. seealso::
         `示例：使用 BaseModel.init() 重写的 SQL 视图
         <{GITHUB_PATH}/addons/project/report/project_report.py>`_

   .. tab:: 使用 `_table_query`

      ``_table_query`` 属性用于当视图依赖于上下文时。通常写成如下形式：

      .. code-block:: python

          @property
          def _table_query(self):
              return 'SELECT %s FROM %s' % (self._select(), self._from())

      并遵循与 `BaseModel.init()` 相同的 `_select` 和 `_from` 方法标准。

      一个使用该属性而不是重写 `BaseModel.init()` 的示例是在多公司和多货币环境中，当用户在公司之间切换时，涉及货币相关金额的转换需要使用货币汇率。

      .. seealso::
         `示例：使用 _table_query 的 SQL 视图
         <{GITHUB_PATH}/addons/account/report/account_invoice_report.py>`_

使用模型
=============

视图和菜单项的创建与使用方式与任何其他 Odoo 模型相同。您现在可以开始使用您的 SQL 视图了。祝您玩得开心！

额外提示
==========

.. tip::
   SQL 视图中的一个常见错误是由于表连接（JOIN）导致某些数据重复，从而导致字段的 `group_operator` 和/或数据透视视图的计数错误。最好使用足够的数据来测试您的 SQL 视图，以确保生成的字段值符合您的预期。

.. tip::
   如果您有一个不希望作为度量字段的字段（例如，在数据透视表或图表视图中），将 `store=False` 添加到该字段中，它就不会显示出来。
