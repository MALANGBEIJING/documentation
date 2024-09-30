==================================
第3章：模型和基本字段
==================================

在 :doc:`上一章 <02_newapp>` 结束时，我们能够创建一个 Odoo 模块。然而，此时它仍然是一个空壳，无法存储任何数据。在我们的房地产模块中，我们希望在数据库中存储与物业相关的信息（名称、描述、价格、居住面积等）。Odoo 框架提供了便于数据库交互的工具。

在继续练习之前，请确保 ``estate`` 模块已安装，即它必须在应用列表中显示为“已安装”。

.. 警告::

   不要使用可变全局变量。

   单个 Odoo 实例可以在同一个 Python 进程中并行运行多个数据库。不同的模块可能安装在每个数据库上，因此我们不能依赖于根据安装的模块而更新的全局变量。

对象关系映射
==========================

**参考**：有关此主题的文档可以在 :ref:`reference/orm/model` API 中找到。

.. 注意::

    **目标**：在本节结束时，应该创建表 ``estate_property``：

    .. code-block:: text

        $ psql -d rd-demo
        rd-demo=# SELECT COUNT(*) FROM estate_property;
        count
        -------
            0
        (1 row)

Odoo 的一个关键组成部分是 `ORM`_ 层。该层避免了手动编写大多数 `SQL`_，并提供了可扩展性和安全服务\ [#rawsql]_。

业务对象被声明为扩展 :class:`~odoo.models.Model` 的 Python 类，这将它们集成到自动持久性系统中。

通过在模型定义中设置属性，可以对模型进行配置。最重要的属性是 :attr:`~odoo.models.Model._name`，这是必需的，并定义了 Odoo 系统中模型的名称。以下是模型的最小定义：

.. code-block:: python

    from odoo import models

    class TestModel(models.Model):
        _name = "test_model"

这个定义足以让 ORM 生成一个名为 `test_model` 的数据库表。根据约定，所有模型都位于 `models` 目录中，每个模型都在自己的 Python 文件中定义。

看看 ``crm_recurring_plan`` 表是如何定义的，以及相应的 Python 文件是如何导入的：

1. 模型定义在文件 ``crm/models/crm_recurring_plan.py`` 中
   （参见 `这里 <https://github.com/odoo/odoo/blob/e80911aaead031e7523173789e946ac1fd27c7dc/addons/crm/models/crm_recurring_plan.py#L1-L9>`__）
2. 文件 ``crm_recurring_plan.py`` 在 ``crm/models/__init__.py`` 中导入
   （参见 `这里 <https://github.com/odoo/odoo/blob/e80911aaead031e7523173789e946ac1fd27c7dc/addons/crm/models/__init__.py#L15>`__）
3. 文件夹 ``models`` 在 ``crm/__init__.py`` 中导入
   （参见 `这里 <https://github.com/odoo/odoo/blob/e80911aaead031e7523173789e946ac1fd27c7dc/addons/crm/__init__.py#L5>`__）

.. 练习:: 定义房地产属性模型。

    根据 CRM 模块中给出的示例，为 ``estate_property`` 表创建适当的文件和文件夹。

    创建文件后，为 ``estate.property`` 模型添加最小定义。

任何对 Python 文件的修改都需要重新启动 Odoo 服务器。重新启动服务器时，我们将添加参数 ``-d`` 和 ``-u``：

.. code-block:: console

    $ ./odoo-bin --addons-path=addons,../enterprise/,../tutorials/ -d rd-demo -u estate

``-u estate`` 意味着我们希望升级 ``estate`` 模块，即 ORM 将应用数据库模式更改。在这种情况下，它创建一个新表。``-d rd-demo`` 意味着升级应在 ``rd-demo`` 数据库上执行。``-u`` 应始终与 ``-d`` 一起使用。

在启动过程中，您应该看到以下警告：

.. code-block:: text

    ...
    WARNING rd-demo odoo.models: 模型 estate.property 没有 _description
    ...
    WARNING rd-demo odoo.modules.loading: 模型 estate.property 没有访问规则，请考虑添加一个...
    ...

如果是这种情况，那么您应该没问题！为确保无误，请使用 ``psql`` 进行双重检查，如 **目标** 中所示。

.. 练习:: 添加描述。

    为您的模型添加 ``_description``，以消除其中一个警告。

模型字段
============================

**参考**：有关此主题的文档可以在 :ref:`reference/orm/fields` API 中找到。

字段用于定义模型可以存储什么以及它们存储的位置。字段作为模型类中的属性定义：

.. code-block:: python

    from odoo import fields, models

    class TestModel(models.Model):
        _name = "test_model"
        _description = "测试模型"

        name = fields.Char()

``name`` 字段是一个 :class:`~odoo.fields.Char`，将在 Python unicode ``str`` 和 SQL ``VARCHAR`` 中表示。

类型
-----

.. 注意::

    **目标**：在本节结束时，表 ``estate_property`` 应添加多个基本字段：

    .. code-block:: text

        $ psql -d rd-demo

        rd-demo=# \d estate_property;
                                                    Table "public.estate_property"
            Column       |            Type             | Collation | Nullable |                   Default
        --------------------+-----------------------------+-----------+----------+---------------------------------------------
        id                 | integer                     |           | not null | nextval('estate_property_id_seq'::regclass)
        create_uid         | integer                     |           |          |
        create_date        | timestamp without time zone |           |          |
        write_uid          | integer                     |           |          |
        write_date         | timestamp without time zone |           |          |
        name               | character varying           |           |          |
        description        | text                        |           |          |
        postcode           | character varying           |           |          |
        date_availability  | date                        |           |          |
        expected_price     | double precision            |           |          |
        selling_price      | double precision            |           |          |
        bedrooms           | integer                     |           |          |
        living_area        | integer                     |           |          |
        facades            | integer                     |           |          |
        garage             | boolean                     |           |          |
        garden             | boolean                     |           |          |
        garden_area        | integer                     |           |          |
        garden_orientation | character varying           |           |          |
        Indexes:
            "estate_property_pkey" PRIMARY KEY, btree (id)
        Foreign-key constraints:
            "estate_property_create_uid_fkey" FOREIGN KEY (create_uid) REFERENCES res_users(id) ON DELETE SET NULL
            "estate_property_write_uid_fkey" FOREIGN KEY (write_uid) REFERENCES res_users(id) ON DELETE SET NULL


字段分为两大类：'简单' 字段，它们是存储在模型表中的原子值，以及 '关系' 字段，它们链接记录（同一模型或不同模型）。

简单字段的示例包括 :class:`~odoo.fields.Boolean`、:class:`~odoo.fields.Float`、:class:`~odoo.fields.Char`、:class:`~odoo.fields.Text`、:class:`~odoo.fields.Date` 和 :class:`~odoo.fields.Selection`。

.. 练习:: 将基本字段添加到房地产属性表。

    将以下基本字段添加到表中：

    ========================= =========================
    字段                     类型
    ========================= =========================
    name                      Char
    description               Text
    postcode                  Char
    date_availability         Date
    expected_price            Float
    selling_price             Float
    bedrooms                  Integer
    living_area               Integer
    facades                   Integer
    garage                    Boolean
    garden                    Boolean
    garden_area               Integer
    garden_orientation        Selection
    ========================= =========================

    ``garden_orientation`` 字段必须有四个可能值：'North'、'South'、'East' 和 'West'。选择列表定义为一个元组列表，见 `这里 <https://github.com/odoo/odoo/blob/b0e0035b585f976e912e97e7f95f66b525bc8e43/addons/crm/report/crm_activity_report.py#L31-L34>`__ 的示例。

当字段添加到模型后，请使用 ``-u estate`` 重新启动服务器。

.. code-block:: console

    $ ./odoo-bin --addons-path=addons,../enterprise/,../tutorials/ -d rd-demo -u estate

连接到 ``psql`` 并检查 ``estate_property`` 表的结构。您会注意到几个额外的字段也已添加到表中。稍后我们会再次讨论它们。
公共属性
------------------

.. 注意::

    **目标**：在本节结束时，列 ``name`` 和 ``expected_price`` 应在表 ``estate_property`` 中设置为不可为空：

    .. code-block:: console

        rd-demo=# \d estate_property;
                                                    Table "public.estate_property"
            Column       |            Type             | Collation | Nullable |                   Default
        --------------------+-----------------------------+-----------+----------+---------------------------------------------
        ...
        name               | character varying           |           | not null |
        ...
        expected_price     | double precision            |           | not null |
        ...

与模型本身类似，字段可以通过作为参数传递配置属性进行配置：

.. code-block:: python

    name = fields.Char(required=True)

一些属性可用于所有字段，以下是最常见的属性：

:attr:`~odoo.fields.Field.string` (``str``, default: field's name)
    字段在用户界面中的标签（用户可见）。
:attr:`~odoo.fields.Field.required` (``bool``, default: ``False``)
    如果为 ``True``，则字段不能为空。它必须具有默认值，或者在创建记录时始终提供值。
:attr:`~odoo.fields.Field.help` (``str``, default: ``''``)
    为用户在用户界面中提供长形式的帮助工具提示。
:attr:`~odoo.fields.Field.index` (``bool``, default: ``False``)
    请求 Odoo 在该列上创建一个 `数据库索引`_。

.. 练习:: 为现有字段设置属性。

    添加以下属性：

    ========================= =========================
    字段                     属性
    ========================= =========================
    name                      required
    expected_price            required
    ========================= =========================

    重新启动服务器后，这两个字段应为不可为空。

自动字段
----------------

**参考**：有关此主题的文档可以在 :ref:`reference/fields/automatic` 中找到。

您可能注意到模型中有一些您未定义的字段。Odoo 在所有模型中创建了一些字段\ [#autofields]_. 这些字段由系统管理，不能写入，但在有用或必要时可以读取：

:attr:`~odoo.fields.Model.id` (:class:`~odoo.fields.Id`)
    模型记录的唯一标识符。
:attr:`~odoo.fields.Model.create_date` (:class:`~odoo.fields.Datetime`)
    记录的创建日期。
:attr:`~odoo.fields.Model.create_uid` (:class:`~odoo.fields.Many2one`)
    创建记录的用户。
:attr:`~odoo.fields.Model.write_date` (:class:`~odoo.fields.Datetime`)
    记录的最后修改日期。
:attr:`~odoo.fields.Model.write_uid` (:class:`~odoo.fields.Many2one`)
    最后修改记录的用户。

现在我们已经创建了第一个模型，让我们
:doc:`添加一些安全性 <04_securityintro>`！

.. [#autofields] 可以 :ref:`禁用某些字段的自动创建 <reference/fields/automatic/log_access>` 
.. [#rawsql] 编写原始 SQL 查询是可能的，但需要谨慎，因为这会绕过所有 Odoo 身份验证和安全机制。

.. _数据库索引:
    https://use-the-index-luke.com/sql/preface
.. _ORM:
    https://en.wikipedia.org/wiki/Object-relational_mapping
.. _SQL:
    https://en.wikipedia.org/wiki/SQL
