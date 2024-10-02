:show-content:

.. _reference/orm:

=======
ORM API
=======

.. toctree::
   :titlesonly:

   orm/changelog

.. automodule:: odoo.models

.. _reference/orm/models:
.. _reference/orm/model:

模型
====

模型字段在模型本身中定义为属性::

    from odoo import models, fields
    class AModel(models.Model):
        _name = 'a.model.name'

        field1 = fields.Char()

.. warning:: 这意味着你不能定义同名字段和方法，后者会静默地覆盖前者。

默认情况下，字段的标签（用户可见的名称）是字段名称的大写版本，可以通过 ``string`` 参数覆盖此默认行为。::

        field2 = fields.Integer(string="字段标签")

有关字段类型和参数的列表，请参阅 :ref:`字段参考
<reference/fields>`。

默认值可以作为字段的参数定义，可以是一个值::

    name = fields.Char(default="一个值")

也可以是一个返回默认值的函数::

    def _default_name(self):
        return self.get_value()

    name = fields.Char(default=lambda self: self._default_name())

.. rubric:: API

.. autoclass:: odoo.models.BaseModel()

    .. autoattribute:: _auto
    .. attribute:: _log_access

        是否自动生成并更新ORM的
        :ref:`reference/fields/automatic/log_access`。

        默认值为 :attr:`~._auto` 的设置值。

    .. autoattribute:: _table
    .. autoattribute:: _sql_constraints

    .. autoattribute:: _register
    .. autoattribute:: _abstract
    .. autoattribute:: _transient

    .. autoattribute:: _name
    .. autoattribute:: _description

    .. autoattribute:: _inherit
    .. autoattribute:: _inherits

    .. autoattribute:: _rec_name
    .. autoattribute:: _order

    .. autoattribute:: _check_company_auto

    .. autoattribute:: _parent_name
    .. autoattribute:: _parent_store

    .. autoattribute:: _fold_name

抽象模型
--------

.. autoclass:: odoo.models.AbstractModel

模型
----

.. autoclass:: odoo.models.Model

   .. autoattribute:: _auto
   .. autoattribute:: _abstract

临时模型
--------

.. autoclass:: odoo.models.TransientModel
   :members: _transient_vacuum

   .. autoattribute:: _transient_max_count
   .. autoattribute:: _transient_max_hours

.. _reference/fields:
.. _reference/orm/fields:

字段
====

.. currentmodule:: odoo.fields

.. autoclass:: Field()

.. _reference/fields/basic:

基础字段
--------

.. autoclass:: Boolean()

.. autoclass:: Char()

.. autoclass:: Float()

.. autoclass:: Integer()

.. _reference/fields/advanced:

高级字段
--------

.. autoclass:: Binary()

.. autoclass:: Html()

.. autoclass:: Image()

.. autoclass:: Monetary()

.. autoclass:: Selection()

.. autoclass:: Text()

.. _reference/fields/date:

日期（时间）字段
~~~~~~~~~~~~~

:class:`日期 <odoo.fields.Date>` 和 :class:`日期时间 <odoo.fields.Datetime>` 是任何商业应用中的重要字段。
使用不当会导致隐蔽但令人痛苦的错误，本节旨在为Odoo开发者提供必要的知识，避免错误使用这些字段。

为Date/Datetime字段分配值时，有以下选项是有效的：

* `date` 或 `datetime` 对象。
* 具有正确服务器格式的字符串：

  * 对于 :class:`~odoo.fields.Date` 字段为 ``YYYY-MM-DD``，
  * 对于 :class:`~odoo.fields.Datetime` 字段为 ``YYYY-MM-DD HH:MM:SS``。

* `False` 或 `None`。

日期和日期时间字段类有一些帮助方法，用于尝试转换为兼容的类型：

* :func:`~odoo.fields.Date.to_date` 会转换为 :class:`datetime.date`
* :func:`~odoo.fields.Datetime.to_datetime` 会转换为 :class:`datetime.datetime`。

.. example::

    解析来自外部来源的日期/日期时间::

        fields.Date.to_date(self._context.get('date_from'))

日期/日期时间比较的最佳实践：

* 日期字段 **只能** 与日期对象比较。
* 日期时间字段 **只能** 与日期时间对象比较。

.. warning:: 可以比较表示日期和日期时间的字符串，但是结果可能不是预期的，
             因为日期时间字符串始终大于日期字符串，因此 **强烈** 不推荐这种做法。

与日期和日期时间进行的常见操作（如加法、减法或获取某一时期的开始/结束）可以通过
:class:`~odoo.fields.Date` 和 :class:`~odoo.fields.Datetime` 执行。
这些帮助方法也可以通过导入 `odoo.tools.date_utils` 使用。

.. note:: 时区

    日期时间字段在数据库中以 `没有时区的时间戳` 列形式存储，并且以UTC时区存储。这是设计使然，因为这样使得Odoo数据库不依赖于托管服务器系统的时区。时区转换完全由客户端处理。

.. autoclass:: Date()
    :members: today, context_today, to_date, to_string, start_of, end_of, add, subtract

.. autoclass:: Datetime()
    :members: now, today, context_timestamp, to_datetime, to_string, start_of, end_of, add, subtract

.. _reference/fields/relational:

关系字段
~~~~~~~~~

.. autoclass:: Many2one()

.. autoclass:: One2many()

.. autoclass:: Many2many()

.. autoclass:: Command()
   :members:
   :undoc-members:
   :member-order: bysource

伪关系字段
~~~~~~~~~

.. autoclass:: Reference()

.. autoclass:: Many2oneReference()

.. _reference/fields/compute:

计算字段
~~~~~~~~

字段可以通过 ``compute`` 参数进行计算（而不是直接从数据库读取）。它 **必须为字段赋值**。如果它使用了其他 *字段* 的值，则应该使用 :func:`~odoo.api.depends` 来指定这些字段。::

    from odoo import api
    total = fields.Float(compute='_compute_total')

    @api.depends('value', 'tax')
    def _compute_total(self):
        for record in self:
            record.total = record.value + record.value * record.tax

* 使用子字段时，依赖项可以是点路径::

    @api.depends('line_ids.value')
    def _compute_total(self):
        for record in self:
            record.total = sum(line.value for line in record.line_ids)

* 默认情况下，计算字段不会存储，它们是在被请求时计算并返回的。设置 ``store=True`` 将会把它们存储到数据库中，并自动启用搜索功能。
* 也可以通过设置 ``search`` 参数启用对计算字段的搜索。该值是返回
  :ref:`reference/orm/domains` 的方法名称。::

    upper_name = field.Char(compute='_compute_upper', search='_search_upper')

    def _search_upper(self, operator, value):
        if operator == 'like':
            operator = 'ilike'
        return [('name', operator, value)]

  搜索方法在处理域时被调用，并在执行实际的模型搜索之前运行。它必须返回与条件等效的域： ``field operator value``。

.. TODO and/or 通过为搜索域设置 store=True？

* 计算字段默认是只读的。要允许 *设置* 计算字段的值，请使用 ``inverse`` 参数。它是一个函数名称，该函数反转计算并设置相关字段。::

    document = fields.Char(compute='_get_document', inverse='_set_document')

    def _get_document(self):
        for record in self:
            with open(record.get_document_path) as f:
                record.document = f.read()
    def _set_document(self):
        for record in self:
            if not record.document: continue
            with open(record.get_document_path()) as f:
                f.write(record.document)

* 可以同时通过同一个方法计算多个字段，只需在所有字段上使用相同的方法并同时设置它们的值。::

    discount_value = fields.Float(compute='_apply_discount')
    total = fields.Float(compute='_apply_discount')

    @api.depends('value', 'discount')
    def _apply_discount(self):
        for record in self:
            # 从折扣百分比计算实际折扣
            discount = record.value * record.discount
            record.discount_value = discount
            record.total = record.value - discount

.. warning::

    虽然可以为多个字段使用相同的计算方法，但不建议为反向方法做同样的处理。

    在执行反向计算时，**所有** 使用该反向方法的字段都是受保护的，这意味着它们不能被计算，即使它们的值不在缓存中。

    如果这些字段中的任何一个被访问并且它的值不在缓存中，ORM 将简单地返回这些字段的默认值 `False`。
    这意味着除了触发反向方法的字段之外，反向字段的值可能不正确，并且这可能会破坏反向方法的预期行为。

.. _reference/fields/related:

相关字段
~~~~~~~~~

计算字段的一个特例是 *相关*（代理）字段，它提供当前记录的子字段的值。它们通过设置 ``related`` 参数来定义，并且像常规计算字段一样可以被存储。::

    nickname = fields.Char(related='user_id.partner_id.name', store=True)

相关字段的值是通过一系列关系字段进行传递并在达到的模型上读取字段值。完整的字段序列由 ``related`` 属性指定。

如果没有重新定义某些字段属性，则这些属性会自动从源字段复制： ``string`` ， ``help`` ， ``required`` （仅在序列中的所有字段都是必需时）， ``groups`` ， ``digits`` ， ``size`` ， ``translate`` ， ``sanitize`` ， ``selection`` ， ``comodel_name`` ， ``domain`` ，``context`` 。所有无语义的属性都从源字段复制。

默认情况下，相关字段是：

* 不存储
* 不复制
* 只读
* 在超级用户模式下计算

添加属性 ``store=True`` 以使其像计算字段一样存储。相关字段在它们的依赖项被修改时会自动重新计算。

.. tip::

    如果你不希望相关字段在任何依赖项更改时重新计算，可以指定精确的字段依赖项。::

        nickname = fields.Char(
            related='partner_id.name', store=True,
            depends=['partner_id'])
        # 只有当 partner_id 被修改时才会重新计算昵称，而不是当 partner 的 name 被修改时。

.. warning::

    你不能在 ``related`` 字段依赖中串联 :class:`~odoo.fields.Many2many` 或 :class:`~odoo.fields.One2many` 字段。

    ``related`` 可以用来引用另一个模型上的 :class:`~odoo.fields.One2many` 或 :class:`~odoo.fields.Many2many` 字段，前提是通过当前模型上的 ``Many2one`` 关系进行操作。
    ``One2many`` 和 ``Many2many`` 不受支持，结果不会正确聚合。::

      m2o_id = fields.Many2one()
      m2m_ids = fields.Many2many()
      o2m_ids = fields.One2many()

      # 支持
      d_ids = fields.Many2many(related="m2o_id.m2m_ids")
      e_ids = fields.One2many(related="m2o_id.o2m_ids")

      # 不支持：请使用自定义的 Many2many 计算字段
      f_ids = fields.Many2many(related="m2m_ids.m2m_ids")
      g_ids = fields.One2many(related="o2m_ids.o2m_ids")

.. currentmodule:: odoo.models

.. _reference/fields/automatic:

自动字段
--------

.. attribute:: Model.id

    标识符 :class:`字段 <odoo.fields.Field>`

    如果当前记录集的长度为1，返回它唯一的记录的id。

    否则抛出错误。

.. attribute:: Model.display_name

    默认在Web客户端中显示的字段名称 :class:`字段 <odoo.fields.Char>`

    默认情况下，它等于 :attr:`~odoo.models.BaseModel._rec_name` 值字段，但可以通过重写 :attr:`~odoo.models.BaseModel._compute_display_name` 来自定义行为。

.. _reference/fields/automatic/log_access:

访问日志字段
~~~~~~~~~~~~

如果启用了 :attr:`~odoo.models.BaseModel._log_access`，则会自动设置和更新这些字段。为了避免在不必要的表上创建或更新这些字段，可以禁用它。

默认情况下，:attr:`~odoo.models.BaseModel._log_access` 设置为与 :attr:`~odoo.models.BaseModel._auto` 相同的值。

.. attribute:: Model.create_date

    记录创建的时间，:class:`~odoo.fields.Datetime`

.. attribute:: Model.create_uid

    创建记录的用户，:class:`~odoo.fields.Many2one` 到 ``res.users``。

.. attribute:: Model.write_date

    记录最后一次更新的时间，:class:`~odoo.fields.Datetime`

.. attribute:: Model.write_uid

    最后一次更新记录的用户，:class:`~odoo.fields.Many2one` 到 ``res.users``。

.. warning:: :attr:`~odoo.models.BaseModel._log_access` *必须* 在 :class:`~odoo.models.TransientModel` 上启用。

.. _reference/orm/fields/reserved:

保留字段名称
------------

一些字段名称被保留用于特定的预定义行为，超出了自动字段的范畴。当需要相关行为时，应在模型上定义这些字段：

.. attribute:: Model.name

   默认值为 :attr:`~odoo.models.BaseModel._rec_name`，用于在需要代表“命名”的上下文中显示记录。

   :class:`~odoo.fields.Char`

.. attribute:: Model.active

  切换记录的全局可见性，如果 ``active`` 设置为 ``False``，则该记录在大多数搜索和列表中不可见。

  :class:`~odoo.fields.Boolean`

  特殊方法：

  .. automethod:: Model.toggle_active
  .. automethod:: Model.action_archive
  .. automethod:: Model.action_unarchive

.. attribute:: Model.state

  对象的生命周期阶段，使用在 :class:`fields <odoo.fields.Field>` 的 ``states`` 属性上。

  :class:`~odoo.fields.Selection`

.. attribute:: Model.parent_id

  :attr:`~._parent_name` 的默认值，用于将记录组织为树形结构，并在域中启用 ``child_of`` 和 ``parent_of`` 操作符。

  :class:`~odoo.fields.Many2one`

.. attribute:: Model.parent_path

  当 :attr:`~._parent_store` 设置为True时，用户存储反映树形结构的值，并优化搜索域中的 ``child_of`` 和 ``parent_of`` 操作符。
  必须声明为 ``index=True`` 以确保正确操作。

  :class:`~odoo.fields.Char`

.. attribute:: Model.company_id

  Odoo多公司行为的主要字段名称。

  用于 `:meth:~odoo.models._check_company` 检查多公司一致性。
  定义记录是由哪些公司共享（没有值），还是仅由特定公司的用户访问。

  :class:`~odoo.fields.Many2one`
  :type: :class:`~odoo.addons.base.models.res_company`
记录集
======

与模型和记录的交互是通过记录集完成的，记录集是同一模型的记录的有序集合。

.. warning:: 与名称的含义相反，当前记录集可能包含重复项。未来这一点可能会改变。

定义在模型上的方法是在记录集上执行的，并且它们的 ``self`` 是一个记录集::

    class AModel(models.Model):
        _name = 'a.model'
        def a_method(self):
            # self 可以是从0条记录到数据库中的所有记录
            self.do_operation()

迭代记录集将生成新的 *单条记录* 集（“单例”），类似于在 Python 字符串上迭代时生成单个字符的字符串::

        def do_operation(self):
            print(self) # => a.model(1, 2, 3, 4, 5)
            for record in self:
                print(record) # => a.model(1), 然后是 a.model(2), 然后是 a.model(3), ...

字段访问
--------

记录集提供了“活动记录”接口：模型字段可以直接从记录中作为属性进行读取和写入。

.. note::

    当访问多个记录集中的非关系字段时，请使用 :meth:`~odoo.models.BaseModel.mapped`::

        total_qty = sum(self.mapped('qty'))

字段值也可以像字典项一样访问，这比 ``getattr()`` 更优雅且更安全，尤其是对于动态字段名称。
设置字段值会触发数据库更新::

    >>> record.name
    示例名称
    >>> record.company_id.name
    公司名称
    >>> record.name = "Bob"
    >>> field = "name"
    >>> record[field]
    Bob

.. warning::

    尝试在多个记录上读取非关系字段会引发错误。

访问关系字段 (:class:`~odoo.fields.Many2one`,
:class:`~odoo.fields.One2many`, :class:`~odoo.fields.Many2many`)
*总是* 返回一个记录集，如果字段未设置，则返回空集。

记录缓存和预取
-------------

Odoo 为记录的字段维护缓存，因此并非每次字段访问都会发出数据库请求，这对性能来说将是灾难性的。以下示例仅在第一次语句时查询数据库::

    record.name             # 第一次访问从数据库读取值
    record.name             # 第二次访问从缓存中获取值

为了避免每次读取一个字段，Odoo *预取* 记录和字段，并使用一些启发式方法以获得良好的性能。一旦必须在某条记录上读取字段，ORM 实际上会读取一个较大的记录集上的该字段，并将返回的值存储在缓存中以供以后使用。预取的记录集通常是通过迭代获取记录时的记录集。
此外，所有简单存储的字段（布尔型、整数型、浮点型、字符型、文本型、日期、日期时间、选择、Many2one）会一起获取；它们对应模型表中的列，并通过同一个查询高效地获取。

考虑以下示例，其中 ``partners`` 是一个包含1000条记录的记录集。没有预取的话，这个循环将向数据库发出2000个查询。有了预取，只需发出一个查询::

    for partner in partners:
        print partner.name          # 第一次迭代预取所有 'partners' 的 'name' 和 'lang'
        print partner.lang

预取也适用于 *次级记录*：当读取关系字段时，它们的值（记录）会被订阅以供将来预取。访问这些次级记录之一会预取同一模型的所有次级记录。这使得以下示例仅生成两个查询，一个用于合作伙伴，一个用于国家::

    countries = set()
    for partner in partners:
        country = partner.country_id        # 第一次迭代预取所有合作伙伴
        countries.add(country.name)         # 第一次迭代预取所有国家

.. seealso::
    :meth:`~odoo.models.Model.search_fetch` 和
    :meth:`~odoo.models.Model.fetch` 方法可以用于填充记录缓存，尤其是在预取机制效果不佳的情况下。

.. _reference/api/decorators:

方法装饰器
==========

.. automodule:: odoo.api
    :members: depends, depends_context, constrains, onchange, returns, autovacuum, model, model_create_multi, ondelete

.. .. currentmodule:: odoo.api

.. .. autodata:: model
.. .. autodata:: depends
.. .. autodata:: constrains
.. .. autodata:: onchange
.. .. autodata:: returns
.. .. autodata:: autovacuum

.. todo:: 使用 sphinx 2.0 : autodecorator

.. todo:: 在视图参考中添加
  * 可以通过在视图中添加 ``on_change="0"`` 来禁止触发器::

    <field name="name" on_change="0"/>

  在用户编辑字段时，即使有依赖的计算字段或明确的onchange，也不会触发界面更新。

.. _reference/orm/environment:

环境
====

.. currentmodule:: odoo.api

.. autoclass:: Environment

.. code-block:: bash

    >>> records.env
    <Environment 对象 ...>
    >>> records.env.uid
    3
    >>> records.env.user
    res.user(3)
    >>> records.env.cr
    <Cursor 对象 ...>

当从其他记录集创建记录集时，环境会被继承。环境可用于获取其他模型中的空记录集，并查询该模型：

.. code-block:: bash

    >>> self.env['res.partner']
    res.partner()
    >>> self.env['res.partner'].search([('is_company', '=', True), ('customer', '=', True)])
    res.partner(7, 18, 12, 14, 17, 19, 8, 31, 26, 16, 13, 20, 30, 22, 29, 15, 23, 28, 74)

一些懒加载属性可以访问环境（上下文）数据：

.. autoattribute:: Environment.lang
.. autoattribute:: Environment.user
.. autoattribute:: Environment.company
.. autoattribute:: Environment.companies

有用的环境方法
----------------

.. automethod:: Environment.ref
.. automethod:: Environment.is_superuser
.. automethod:: Environment.is_admin
.. automethod:: Environment.is_system

更改环境
--------

.. currentmodule:: odoo.models

.. automethod:: Model.with_context

.. automethod:: Model.with_user

.. automethod:: Model.with_company

.. automethod:: Model.with_env

.. automethod:: Model.sudo

.. _reference/orm/sql:
SQL 执行
-------------

环境中的 :attr:`~odoo.api.Environment.cr` 属性是当前数据库事务的游标，允许直接执行 SQL 查询，
无论是因为查询难以通过 ORM 表达（例如复杂的连接），还是出于性能原因::

    self.env.cr.execute("some_sql", params)

.. warning::
    执行原始 SQL 绕过了 ORM，因此也绕过了 Odoo 的安全规则。
    请确保在使用用户输入时对查询进行清理，并且除非确实需要使用 SQL 查询，否则请优先使用 ORM 工具。

构建 SQL 查询的推荐方式是使用封装对象

.. autoclass:: odoo.tools.SQL

    .. automethod:: SQL.join
    .. automethod:: SQL.identifier

需要了解的重要一点是，模型并不会立即执行数据库更新。实际上，为了提高性能，框架会延迟字段的重新计算，
而某些数据库更新也会被延迟。因此，在查询数据库之前，必须确保数据库包含查询所需的相关数据。
这一操作被称为 *flush*，它会执行预期的数据库更新。

.. example::

    .. code-block:: python

        # 确保 'partner_id' 在数据库中是最新的
        self.env['model'].flush_model(['partner_id'])

        self.env.cr.execute(SQL("SELECT id FROM model WHERE partner_id IN %s", ids))
        ids = [row[0] for row in self.env.cr.fetchall()]

在每次 SQL 查询之前，必须 flush 该查询所需的数据。flush 有三个级别，每个级别都有其对应的 API。
可以 flush 所有内容、模型的所有记录或特定的记录。由于延迟更新通常可以提高性能，我们建议在 flush 时尽可能 *具体*。

.. automethod:: odoo.api.Environment.flush_all

.. automethod:: Model.flush_model

.. automethod:: Model.flush_recordset

因为模型使用相同的游标，且 :class:`~odoo.api.Environment` 持有多个缓存，
当在原始 SQL 中 *更改* 数据库时，必须使缓存失效，否则进一步使用模型可能会变得不一致。
在 SQL 中使用 ``CREATE`` 、 ``UPDATE`` 或 ``DELETE`` 时，必须清除缓存，但不需要清除 ``SELECT``（它只是读取数据库）。

.. example::

    .. code-block:: python

        # 确保 'state' 在数据库中是最新的
        self.env['model'].flush_model(['state'])

        self.env.cr.execute("UPDATE model SET state=%s WHERE state=%s", ['new', 'old'])

        # 从缓存中使 'state' 失效
        self.env['model'].invalidate_model(['state'])

和 flush 一样，可以使整个缓存失效、模型所有记录的缓存失效，或者特定记录的缓存失效。
甚至可以使某些记录或模型的所有记录上的特定字段失效。由于缓存通常提高了性能，我们建议在使缓存失效时尽量 *具体*。

.. automethod:: odoo.api.Environment.invalidate_all

.. automethod:: Model.invalidate_model

.. automethod:: Model.invalidate_recordset

上面的方法可以保持缓存与数据库的一致性。但是，如果计算字段的依赖关系在数据库中发生了变化，
必须通知模型，以便重新计算这些字段。框架唯一需要知道的是 *哪些* 字段在 *哪些* 记录上发生了变化。

.. example::

    .. code-block:: python

        # 确保 'state' 在数据库中是最新的
        self.env['model'].flush_model(['state'])

        # 使用 RETURNING 子句来获取哪些行发生了变化
        self.env.cr.execute("UPDATE model SET state=%s WHERE state=%s RETURNING id", ['new', 'old'])
        ids = [row[0] for row in self.env.cr.fetchall()]

        # 使缓存失效，并通知框架更新
        records = self.env['model'].browse(ids)
        records.invalidate_recordset(['state'])
        records.modified(['state'])

必须找出哪些记录被修改了。可能有多种方式可以做到这一点，可能涉及额外的 SQL 查询。在上述示例中，我们利用 PostgreSQL 的 ``RETURNING`` 子句，
无需额外的查询即可检索到这些信息。在通过使缓存失效保持缓存一致后，调用修改记录的 ``modified`` 方法，并指定已更新的字段。

.. automethod:: Model.modified


.. _reference/orm/models/crud:

常用 ORM 方法
==================

.. currentmodule:: odoo.models

创建/更新
-------------

.. todo:: api.model_create_multi 信息

.. automethod:: Model.create

.. automethod:: Model.copy

.. automethod:: Model.default_get

.. automethod:: Model.name_create

.. automethod:: Model.write

搜索/读取
-----------

.. automethod:: Model.browse

.. automethod:: Model.search

.. automethod:: Model.search_count

.. automethod:: Model.search_fetch

.. automethod:: Model.name_search

.. automethod:: Model.fetch

.. automethod:: Model.read

.. automethod:: Model._read_group

.. automethod:: Model.read_group

字段
~~~~~~

.. automethod:: Model.fields_get

.. _reference/orm/domains:
搜索域
~~~~~~~~~~~~~~

搜索域是一个条件列表，每个条件是一个三元组（可以是 ``list`` 或 ``tuple`` ），格式为 ``(字段名, 操作符, 值)``，具体如下：

* ``字段名`` ( ``str`` )
    当前模型中的字段名，或通过类 :class:`~odoo.fields.Many2one` 进行关系字段的点表示法遍历，例如 ``'street'`` 或 ``'partner_id.country'``。

* ``操作符`` ( ``str`` )
    用于将 ``字段名`` 与 ``值`` 进行比较的操作符。有效的操作符有：

    ``=``
        等于
    ``!=``
        不等于
    ``>``
        大于
    ``>=``
        大于或等于
    ``<``
        小于
    ``<=``
        小于或等于
    ``=?``
        未设置或等于（如果 ``值`` 为 ``None`` 或 ``False`` 则返回 true，否则行为与 ``=`` 相同）
    ``=like``
        将 ``字段名`` 与 ``值`` 模式进行匹配。模式中的下划线 ``_`` 匹配任意单个字符，百分号 ``%`` 匹配任意数量字符（包括零个）。
    ``like``
        将 ``字段名`` 与 ``%值%`` 模式进行匹配。类似于 ``=like``，但在匹配前会自动为 ``值`` 两侧添加百分号。
    ``not like``
        不与 ``%值%`` 模式匹配。
    ``ilike``
        忽略大小写的 ``like`` 匹配。
    ``not ilike``
        忽略大小写的 ``not like`` 匹配。
    ``=ilike``
        忽略大小写的 ``=like`` 匹配。
    ``in``
        等于 ``值`` 列表中的任意一个元素，``值`` 应该是一个项目列表。
    ``not in``
        不等于 ``值`` 列表中的任何一个元素。
    ``child_of``
        是 ``值`` 记录的子记录（后代），``值`` 可以是一个项目或项目列表。根据模型的语义（即遵循 :attr:`~odoo.models.Model._parent_name` 字段定义的关系）进行操作。
    ``parent_of``
        是 ``值`` 记录的父记录（祖先），``值`` 可以是一个项目或项目列表。根据模型的语义（即遵循 :attr:`~odoo.models.Model._parent_name` 字段定义的关系）进行操作。
    ``any``
        如果在通过 ``字段名`` 进行关系字段遍历时（:class:`~odoo.fields.Many2one`、:class:`~odoo.fields.One2many` 或 :class:`~odoo.fields.Many2many`）有任何记录满足提供的域 ``值``，则匹配。
    ``not any``
        如果在通过 ``字段名`` 进行关系字段遍历时（:class:`~odoo.fields.Many2one`、:class:`~odoo.fields.One2many` 或 :class:`~odoo.fields.Many2many`）没有记录满足提供的域 ``值``，则匹配。

* ``值``
    变量类型，必须可以与命名字段通过 ``操作符`` 进行比较。

域条件可以使用逻辑操作符以 *前缀* 形式组合：

``'&'`` 
    逻辑 *AND*，默认操作，用于组合一个接一个的条件。二元操作符（使用接下来的 2 个条件或组合）。
``'|'`` 
    逻辑 *OR*，二元操作符。
``'!'`` 
    逻辑 *NOT*，一元操作符。

    .. note:: 通常用于否定组合条件。
        个人条件通常有负形式（例如 ``=`` -> ``!=`` ， ``<`` -> ``>=``），这比否定正条件更简单。

.. example::

    要搜索名称为 *ABC* 的合作伙伴，并且电话或手机号码包含 *7620*：

        [('name', '=', 'ABC'),
         '|', ('phone','ilike','7620'), ('mobile', 'ilike', '7620')]

    要搜索至少有一行包含缺货产品的待开票销售订单：

        [('invoice_status', '=', 'to invoice'),
         ('order_line', 'any', [('product_id.qty_available', '<=', 0)])]

删除记录
------

.. automethod:: Model.unlink

.. _reference/orm/records/info:

记录（集）信息
-----------------------

.. autoattribute:: Model.ids

.. attribute:: env

    返回给定记录集的环境。

    :type: :class:`~odoo.api.Environment`

.. todo:: 环境文档

.. automethod:: Model.exists

.. automethod:: Model.ensure_one

.. automethod:: Model.get_metadata

.. _reference/orm/records/operations:

操作
----------

记录集是不可变的，但可以使用各种集合操作来组合相同模型的集合，返回新的记录集。

.. 集合操作保持顺序，但可能引入重复项。

* ``record in set`` 返回 ``record``（必须是 1 个元素的记录集）是否存在于 ``set`` 中。 ``record not in set`` 是相反的操作。
* ``set1 <= set2`` 和 ``set1 < set2`` 返回 ``set1`` 是否是 ``set2`` 的子集（分别为严格子集）。
* ``set1 >= set2`` 和 ``set1 > set2`` 返回 ``set1`` 是否是 ``set2`` 的超集（分别为严格超集）。
* ``set1 | set2`` 返回两个记录集的并集，一个包含两个源中所有记录的新记录集。
* ``set1 & set2`` 返回两个记录集的交集，一个只包含两个源中都存在的记录的新记录集。
* ``set1 - set2`` 返回一个新记录集，包含 ``set1`` 中不在 ``set2`` 中的记录。

记录集是可迭代的，因此可以使用通常的 Python 工具进行转换（例如 :func:`python:map`、:func:`python:sorted`、:func:`~python:itertools.ifilter` 等），
但是这些操作返回的要么是 :class:`python:list`，要么是 :term:`python:iterator`，从而失去了调用它们结果的方法，或使用集合操作的能力。

因此，记录集提供了以下操作，返回的仍然是记录集（如果可能的话）：
过滤
~~~~~~

.. automethod:: Model.filtered

.. automethod:: Model.filtered_domain

映射
~~~~

.. automethod:: Model.mapped

.. note::

    自从 Odoo 13 版本起，多关系字段的访问已被支持，并且行为类似于映射调用：

    .. code-block:: python3

        records.partner_id  # 等价于 records.mapped('partner_id')
        records.partner_id.bank_ids  # 等价于 records.mapped('partner_id.bank_ids')
        records.partner_id.mapped('name')  # 等价于 records.mapped('partner_id.name')

排序
~~~~

.. automethod:: Model.sorted

分组
~~~~

.. automethod:: Model.grouped

.. _reference/orm/inheritance:

继承与扩展
=========================

Odoo 提供了三种不同的机制来以模块化的方式扩展模型：

* 从现有模型创建新模型，向副本中添加新信息，同时保持原始模块不变
* 就地扩展其他模块中定义的模型，替换之前的版本
* 将模型的一些字段委托给它包含的记录

.. image:: orm/inheritance_methods.png
    :align: center

经典继承
---------------------

当同时使用 :attr:`~odoo.models.Model._inherit` 和 :attr:`~odoo.models.Model._name` 属性时，Odoo 会使用现有模型（通过 :attr:`~odoo.models.Model._inherit` 提供）作为基础创建一个新模型。新模型继承了基础模型的所有字段、方法和元信息（默认值等）。

.. code-block:: python

    class Inheritance0(models.Model):
        _name = 'inheritance.0'
        _description = 'Inheritance Zero'

        name = fields.Char()

        def call(self):
            return self.check("model 0")

        def check(self, s):
            return "This is {} record {}".format(s, self.name)

    class Inheritance1(models.Model):
        _name = 'inheritance.1'
        _inherit = 'inheritance.0'
        _description = 'Inheritance One'

        def call(self):
            return self.check("model 1")

然后使用它们：

    a = env['inheritance.0'].create({'name': 'A'})
    b = env['inheritance.1'].create({'name': 'B'})

    a.call()
    b.call()

结果为：

    "This is model 0 record A"
    "This is model 1 record B"

第二个模型继承了第一个模型的 ``check`` 方法及其 ``name`` 字段，但重写了 ``call`` 方法，就像标准的 :ref:`Python 继承 <python:tut-inheritance>` 一样。

扩展
---------

当使用 :attr:`~odoo.models.Model._inherit` 而不定义 :attr:`~odoo.models.Model._name` 时，新模型将替换现有模型，实际上在原地扩展了它。这种方式常用于向现有模型（在其他模块中创建的）添加新字段或方法，或自定义或重新配置它们（例如更改它们的默认排序顺序）：

.. code-block:: python

    class Extension0(models.Model):
        _name = 'extension.0'
        _description = 'Extension zero'

        name = fields.Char(default="A")

    class Extension1(models.Model):
        _inherit = 'extension.0'

        description = fields.Char(default="Extended")

.. code-block:: python3

    record = env['extension.0'].create({})
    record.read()[0]

结果为：

    {'name': "A", 'description': "Extended"}

.. note::

    它还将返回各种 :ref:`自动字段 <reference/fields/automatic>`，除非这些字段已被禁用。
委托
----------

第三种继承机制提供了更多的灵活性（可以在运行时修改），但功能较弱：通过使用 :attr:`~odoo.models.Model._inherits`，模型将任何在当前模型中找不到的字段查找委托给“子”模型。委托是通过父模型上自动设置的 :class:`~odoo.fields.Reference` 字段来实现的。

主要区别在于语义。当使用委托时，模型是 **拥有一个** 而不是 **是一个**，将关系从继承转变为组合：

.. code-block:: python

    class Screen(models.Model):
        _name = 'delegation.screen'
        _description = 'Screen'

        size = fields.Float(string='Screen Size in inches')

    class Keyboard(models.Model):
        _name = 'delegation.keyboard'
        _description = 'Keyboard'

        layout = fields.Char(string='Layout')

    class Laptop(models.Model):
        _name = 'delegation.laptop'
        _description = 'Laptop'

        _inherits = {
            'delegation.screen': 'screen_id',
            'delegation.keyboard': 'keyboard_id',
        }

        name = fields.Char(string='Name')
        maker = fields.Char(string='Maker')

        # 一个 Laptop 拥有一个屏幕
        screen_id = fields.Many2one('delegation.screen', required=True, ondelete="cascade")
        # 一个 Laptop 拥有一个键盘
        keyboard_id = fields.Many2one('delegation.keyboard', required=True, ondelete="cascade")

.. code-block:: python3

    record = env['delegation.laptop'].create({
        'screen_id': env['delegation.screen'].create({'size': 13.0}).id,
        'keyboard_id': env['delegation.keyboard'].create({'layout': 'QWERTY'}).id,
    })
    record.size
    record.layout

结果为：

    13.0
    'QWERTY'

可以直接写入委托字段的值：

    record.write({'size': 14.0})

.. warning:: 使用委托继承时，方法不会被继承，只有字段会被继承。

.. warning::

    * `_inherits` 实现得不太完善，尽量避免使用；
    * 链式 `_inherits` 基本上没有实现，我们无法保证其最终行为。


字段增量定义
-----------------------------

字段作为类属性在模型类上定义。如果扩展了模型，也可以通过在子类上重新定义具有相同名称和类型的字段来扩展字段定义。在这种情况下，字段的属性将从父类继承，并被子类中给定的属性覆盖。

例如，下面的第二个类仅在字段 ``state`` 上添加了提示信息：

.. code-block:: python

    class First(models.Model):
        _name = 'foo'
        state = fields.Selection([...], required=True)

    class Second(models.Model):
        _inherit = 'foo'
        state = fields.Selection(help="提示信息")

.. _reference/exceptions:

错误管理
================

.. automodule:: odoo.exceptions
    :members: AccessDenied, AccessError, CacheMiss, MissingError, RedirectWarning, UserError, ValidationError
