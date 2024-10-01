===========
性能分析
===========

.. _performance/profiling:

性能分析
=========

.. currentmodule:: odoo.tools.profiler

性能分析是关于分析程序的执行并测量汇总数据。这些数据可以是每个函数的耗时，执行的 SQL 查询等。

虽然性能分析本身并不能提升程序的性能，但它可以非常有助于发现性能问题并识别导致问题的程序部分。

Odoo 提供了一个集成的性能分析工具，该工具允许在执行过程中记录所有执行的查询和堆栈跟踪。它可以用于分析用户会话的一组请求，或特定代码部分的执行。性能分析结果可以通过集成的 `speedscope <https://github.com/jlfwong/speedscope>`_ 可视化工具以火焰图的形式查看，也可以保存为 JSON 文件或存储在数据库中以供自定义工具分析。

.. _performance/profiling/enable:

启用性能分析器
-------------------

可以通过用户界面启用性能分析器，这是一种最简单的方法，但只能对 Web 请求进行分析，也可以从 Python 代码启用，允许分析任何代码部分，包括测试代码。

.. tabs::

   .. tab:: 从用户界面启用

      #. :ref:`启用开发者模式 <developer-mode>`。
      #. 在开始分析会话之前，必须在数据库上全局启用分析器。可以通过以下两种方式完成：

         - 打开 :ref:`开发者模式工具 <developer-mode/tools>`，然后切换 :guilabel:`启用分析` 按钮。一个向导会建议一组过期时间选项，选择 :guilabel:`启用分析` 按钮来全局启用分析器。

           .. image:: performance/enable_profiling_wizard.png

         - 前往 :guilabel:`设置 --> 常规设置 --> 性能`，并在 :guilabel:`启用分析到` 字段中设置所需的时间。

      #. 在数据库上启用分析器后，用户可以在他们的会话中启用它。要启用，需在 :ref:`开发者模式工具 <developer-mode/tools>` 中再次切换 :guilabel:`启用分析` 按钮。默认情况下，推荐启用 :guilabel:`记录 SQL` 和 :guilabel:`记录跟踪` 选项。有关不同选项的更多信息，请参阅 :ref:`performance/profiling/collectors`。

         .. image:: performance/profiling_debug_menu.png

      启用分析器后，所有向服务器发送的请求都会被分析，并保存为 `ir.profile` 记录。这些记录按从启用到禁用分析器的当前会话分组。

      .. note::
         Odoo Online 数据库无法进行性能分析。

   .. tab:: 从 Python 代码启用

      手动启动分析器可以方便地分析特定方法或代码部分。这段代码可以是测试、计算方法或整个加载过程等。

      要从 Python 代码启动分析器，请将其作为上下文管理器调用。您可以通过参数指定 *要记录的内容*。对于测试类，提供了一个快捷方式：:code:`self.profile()`。有关 `collectors` 参数的更多信息，请参阅 :ref:`performance/profiling/collectors`。

      .. example::

         .. code-block:: python

            with Profiler():
                do_stuff()

      .. example::

         .. code-block:: python

            with Profiler(collectors=['sql', PeriodicCollector(interval=0.1)]):
                do_stuff()

      .. example::

         .. code-block:: python

            with self.profile():
                with self.assertQueryCount(__system__=1211):
                    do_stuff()

         .. note::
            分析器在 `assertQueryCount` 之外调用，以捕获退出上下文管理器时的查询（例如，flush 操作）。

      .. autoclass:: Profiler()
         :special-members: __init__

      启用分析器后，所有测试方法的执行都会被分析，并保存为 `ir.profile` 记录。这些记录按单个分析会话分组。当使用 :code:`@warmup` 和 :code:`@users` 装饰器时尤其有用。

      .. tip::
         当一个方法被多次调用时，分析结果可能难以分析，因为所有调用都会在堆栈跟踪中分组在一起。可以添加一个 **执行上下文** 作为上下文管理器，将结果拆分为多个帧。

         .. example::

            .. code-block:: python

               for index in range(max_index):
                   with ExecutionContext(current_index=index):  # 在 speedscope 结果中识别每次调用。
                       do_stuff()

.. _performance/profiling/analyse:

分析结果
-------------------

要浏览性能分析结果，请确保 :ref:`分析器在数据库上全局启用 <performance/profiling/enable>`，然后打开 :ref:`开发者模式工具 <developer-mode/tools>`，点击性能分析部分右上角的按钮。一个 `ir.profile` 记录的列表视图将打开，按分析会话分组。

.. image:: performance/profiling_web.png
   :align: center

每个记录都有一个可点击的链接，点击后将在新标签中打开 speedscope 结果。

.. image:: performance/flamegraph_example.png
   :align: center

Speedscope 不在此文档的讨论范围内，但有很多工具可以尝试：搜索、相似帧的高亮显示、帧的缩放、时间轴、左重、夹层视图等。

根据激活的分析选项，Odoo 会生成不同的视图模式，您可以从顶部菜单访问这些模式。

.. image:: performance/speedscope_modes.png
   :align: center

- :guilabel:`Combined` 视图显示合并的所有 SQL 查询和跟踪。
- :guilabel:`Combined no context` 视图显示相同结果，但忽略保存的执行上下文 <performance/profiling/enable>`。
- :guilabel:`sql (no gap)` 视图显示所有 SQL 查询，仿佛它们一个接一个执行，没有任何 Python 逻辑。这对于优化 SQL 非常有用。
- :guilabel:`sql (density)` 视图仅显示所有 SQL 查询，并在它们之间留下间隙。这有助于判断 SQL 还是 Python 代码是问题所在，并识别出许多小查询可能可以批量处理的区域。
- :guilabel:`frames` 视图显示仅来自 :ref:`定期收集器 <performance/profiling/collectors/periodic>` 的结果。

.. important::
   尽管分析器设计得尽可能轻量化，但它仍可能影响性能，尤其是在使用 :ref:`同步收集器 <performance/profiling/collectors/sync>` 时。在分析 speedscope 结果时请牢记这一点。

.. _performance/profiling/collectors:

收集器
----------

分析器负责 *何时* 进行分析，而收集器负责 *分析什么*。

每个收集器专注于以自己的格式和方式收集分析数据。可以通过开发者模式工具中的专用切换按钮，或通过 Python 代码中的键或类单独启用每个收集器。

Odoo 目前有四个可用的收集器：

.. list-table::
   :header-rows: 1

   * - 名称
     - 切换按钮
     - Python 键
     - Python 类
   * - :ref:`SQL 收集器 <performance/profiling/collectors/sql>`
     - :guilabel:`记录 SQL`
     - `sql`
     - `SqlCollector`
   * - :ref:`定期收集器 <performance/profiling/collectors/periodic>`
     - :guilabel:`记录跟踪`
     - `traces_async`
     - `PeriodicCollector`
   * - :ref:`QWeb 收集器 <performance/profiling/collectors/qweb>`
     - :guilabel:`记录 qweb`
     - `qweb`
     - `QwebCollector`
   * - :ref:`同步收集器 <performance/profiling/collectors/sync>`
     - 无
     - `traces_sync`
     - `SyncCollector`

默认情况下，无论是通过用户界面还是 Python 代码启用分析器，都会启用 SQL 和定期收集器。

.. _performance/profiling/collectors/sql:
SQL 收集器
~~~~~~~~~~~~~

SQL 收集器会保存当前线程（针对所有游标）中向数据库发出的所有 SQL 查询，以及堆栈跟踪。每次查询时，收集器的开销都会增加到被分析的线程中，这意味着在执行大量小查询时可能会影响执行时间和其他分析器的表现。

它在调试查询计数时非常有用，或者可以将信息添加到合并的 speedscope 视图中的 :ref:`定期收集器 <performance/profiling/collectors/periodic>` 中。

.. autoclass:: SQLCollector

.. _performance/profiling/collectors/periodic:

定期收集器
~~~~~~~~~~~~~~~~~~

该收集器在单独的线程中运行，并在每个时间间隔保存被分析线程的堆栈跟踪。可以通过用户界面中的 :guilabel:`间隔` 选项或 Python 代码中的 `interval` 参数来定义时间间隔（默认值为 10 毫秒）。

.. warning::
   如果时间间隔设置得太低，分析长时间请求时会导致内存问题。如果时间间隔设置得太高，短时间函数执行的信息可能会丢失。

由于它在独立线程中运行，对执行时间的影响非常小，因此它是分析性能的最佳方式之一。

.. autoclass:: PeriodicCollector

.. _performance/profiling/collectors/qweb:

QWeb 收集器
~~~~~~~~~~~~~~

该收集器保存所有指令的 Python 执行时间和查询。与 :ref:`SQL 收集器 <performance/profiling/collectors/sql>` 类似，在执行大量小指令时开销可能较大。与其他收集器相比，收集的数据不同，并且可以通过 `ir.profile` 表单视图中的自定义小部件进行分析。

它主要用于优化视图。

.. autoclass:: QwebCollector

.. _performance/profiling/collectors/sync:

同步收集器
~~~~~~~~~~~~~~

该收集器会为每个函数的调用和返回保存堆栈，并在同一个线程中运行，因此对性能影响很大。

它在调试和理解复杂流程时非常有用，可以跟踪代码的执行流程。然而，由于开销较大，不推荐用于性能分析。

.. autoclass:: SyncCollector

.. _performance/profiling/pitfalls:

性能陷阱
--------------------

- 注意随机性。多次执行可能会导致不同的结果。例如，在执行期间垃圾回收器被触发。
- 小心阻塞调用。在某些情况下，外部 `c_call` 可能需要一些时间才能释放 GIL，从而导致 :ref:`定期收集器 <performance/profiling/collectors/periodic>` 出现意外的长帧。分析器应该能够检测到这一点并发出警告。如果需要，可以在此类调用之前手动触发分析器。
- 注意缓存。分析之前确保 `view`/`assets`/... 已在缓存中，否则可能导致不同的结果。
- 注意分析器的开销。执行大量小查询时，:ref:`SQL 收集器 <performance/profiling/collectors/sql>` 的开销可能很大。性能分析可以帮助发现问题，但在测量代码变更的实际影响时，您可能需要禁用分析器。
- 分析结果可能会占用大量内存。在某些情况下（例如，分析安装过程或长时间请求），可能会达到内存限制，尤其是在渲染 speedscope 结果时，这可能导致 HTTP 500 错误。在这种情况下，您可能需要使用更高的内存限制启动服务器：`--limit-memory-hard $((8*1024**3))`。

.. _reference/performance/populate:

数据库填充
===================

Odoo CLI 提供了一个 :ref:`数据库填充 <reference/cmdline/populate>` 功能，通过 CLI 命令 :command:`odoo-bin populate` 实现。

该功能可以根据需要填充数据库中的测试数据，代替手动或程序化地指定测试数据。这可以用于检测测试流程中的各种错误或性能问题。

.. _reference/performance/populate/methods:

要填充给定的模型，可以定义以下方法和属性。

.. currentmodule:: odoo.models

.. autoattribute:: Model._populate_sizes
.. autoattribute:: Model._populate_dependencies
.. automethod:: Model._populate
.. automethod:: Model._populate_factories

.. note::
   您必须在模型上至少定义 :meth:`~odoo.models.Model._populate` 或
   :meth:`~odoo.models.Model._populate_factories` 以启用数据库填充功能。

.. example::
   .. code-block:: python

       from odoo.tools import populate

       class CustomModel(models.Model)
           _inherit = "custom.some_model"
           _populate_sizes = {"small": 100, "medium": 2000, "large": 10000}
           _populate_dependencies = ["custom.some_other_model"]

           def _populate_factories(self):
               # 可以从注册表中访问先前填充的模型的记录 ID
               some_other_ids = self.env.registry.populated_models["custom.some_other_model"]

               def get_some_field(values=None, random=None, **kwargs):
                   """ 根据其他字段的值选择 some_field 的值。

                   :param dict values:
                   :param random: 已种子的 :class:`random.Random` 对象
                   """
                   field_1 = values['field_1']
                   if field_1 in [value2, value3]:
                       return random.choice(some_field_values)
                   return False

               return [
                   ("field_1", populate.randomize([value1, value2, value3])),
                   ("field_2", populate.randomize([value_a, value_b], [0.5, 0.5])),
                   ("some_other_id", populate.randomize(some_other_ids)),
                   ("some_field", populate.compute(get_some_field, seed="some_field")),
                   ('active', populate.cartesian([True, False])),
               ]

           def _populate(self, size):
               records = super()._populate(size)

               # 如果您想更新生成的记录
               # 例如设置父子关系
               records.do_something()

               return records

填充工具
----------------

提供了多种填充工具，便于轻松创建所需的数据生成器。

.. automodule:: odoo.tools.populate
   :members: cartesian, compute, constant, iterate, randint, randomize

.. _performance/good_practices:

良好实践
==============

.. _performance/good_practices/batch:

批处理操作
----------------

在处理记录集时，几乎总是批量操作更好。

.. example::
   不要在遍历记录集时调用执行 SQL 查询的方法，因为它会对记录集中的每条记录执行查询。

   .. rst-class:: bad-example
   .. code-block:: python

      def _compute_count(self):
          for record in self:
              domain = [('related_id', '=', record.id)]
              record.count = other_model.search_count(domain)

   取而代之，可以使用 `_read_group` 代替 `search_count`，以对整个记录集执行一次 SQL 查询。

   .. rst-class:: good-example
   .. code-block:: python

      def _compute_count(self):
          domain = [('related_id', 'in', self.ids)]
          counts_data = other_model._read_group(domain, ['related_id'], ['__count'])
          mapped_data = dict(counts_data)
          for record in self:
              record.count = mapped_data.get(record, 0)

   .. note::
      此示例并非在所有情况下都是最佳或正确的。它只是 `search_count` 的替代方案。另一种解决方案可以是预取并计数逆向的 `One2many` 字段。
.. example::
   不要一个接一个地创建记录。

   .. rst-class:: bad-example
   .. code-block:: python

      for name in ['foo', 'bar']:
          model.create({'name': name})

   取而代之，先积累创建的数据，然后在批量上调用 `create` 方法。这样做通常不会有太大影响，并且有助于框架优化字段计算。

   .. rst-class:: good-example
   .. code-block:: python

      create_values = []
      for name in ['foo', 'bar']:
          create_values.append({'name': name})
      records = model.create(create_values)

.. example::
   不要在循环中浏览单个记录时，未预取记录集的字段。

   .. rst-class:: bad-example
   .. code-block:: python

      for record_id in record_ids:
          model.browse(record_id)
          record.foo  # 每个记录都会执行一个查询。

   取而代之，先浏览整个记录集。

   .. rst-class:: good-example
   .. code-block:: python

      records = model.browse(record_ids)
      for record in records:
          record.foo  # 整个记录集只执行一次查询。

   我们可以通过读取 `prefetch_ids` 字段来验证记录是否已批量预取，其中包括每个记录的 id。

   如果需要，可以使用 `with_prefetch` 方法来禁用批量预取：

   .. code-block:: python

      for values in values_list:
          message = self.browse(values['id']).with_prefetch(self.ids)

.. _performance/good_practices/algorithmic_complexity:

降低算法复杂度
---------------------------------

算法复杂度衡量了一个算法相对于输入大小 `n` 完成所需的时间。当复杂度很高时，随着输入的增大，执行时间会迅速增长。在某些情况下，通过正确准备输入的数据，可以降低算法复杂度。

.. example::
   对于某个给定问题，我们考虑一个用两个嵌套循环编写的简单算法，其复杂度为 O(n²)。

   .. rst-class:: bad-example
   .. code-block:: python

      for record in self:
          for result in results:
              if results['id'] == record.id:
                  record.foo = results['foo']
                  break

   假设所有结果都有不同的 id，我们可以准备数据以降低复杂度。

   .. rst-class:: good-example
   .. code-block:: python

      mapped_result = {result['id']: result['foo'] for result in results}
      for record in self:
          record.foo = mapped_result.get(record.id)

.. example::
   选择错误的数据结构来存储输入可能会导致二次复杂度。

   .. rst-class:: bad-example
   .. code-block:: python

      invalid_ids = self.search(domain).ids
      for record in self:
          if record.id in invalid_ids:
              ...

   如果 `invalid_ids` 是类列表的数据结构，算法的复杂度可能是二次的。

   取而代之，最好使用集合操作，例如将 `invalid_ids` 转换为集合。

   .. rst-class:: good-example
   .. code-block:: python

      invalid_ids = set(invalid_ids)
      for record in self:
          if record.id in invalid_ids:
              ...

   根据输入的不同，记录集操作也可以使用。

   .. rst-class:: good-example
   .. code-block:: python

      invalid_ids = self.search(domain)
      for record in self - invalid_ids:
          ...

.. _performance/good_practices/index:
使用索引
-----------

数据库索引可以加快搜索操作，无论是通过代码搜索还是通过用户界面进行搜索。

.. code-block:: python

   name = fields.Char(string="Name", index=True)

.. warning::
   注意不要为每个字段创建索引，因为索引会消耗空间，并在执行 `INSERT`、`UPDATE` 和 `DELETE` 时对性能产生影响。
