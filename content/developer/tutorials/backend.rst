=================
构建模块
=================

.. danger::
   本教程已过时。我们推荐阅读 :doc:`server_framework_101` 作为替代。

.. warning::
   本教程要求 :doc:`已安装 Odoo </administration/on_premise>`

启动/停止 Odoo 服务器
==========================

Odoo 使用客户端/服务器架构，客户端通过 RPC 访问 Odoo 服务器，通常使用 Web 浏览器。

业务逻辑和扩展一般是在服务器端执行的，尽管支持客户端功能（如交互式地图等新数据表示）也可以添加到客户端。

要启动服务器，只需在 shell 中调用 :ref:`odoo-bin <reference/cmdline>` 命令，并根据需要添加文件的完整路径：

.. code:: bash

   odoo-bin

可以通过在终端中按两次 ``Ctrl-C`` 或通过终止对应的操作系统进程来停止服务器。

构建 Odoo 模块
====================

服务器和客户端扩展都打包为 *模块*，这些模块可以选择性地加载到 *数据库* 中。

Odoo 模块可以为 Odoo 系统添加全新的业务逻辑，或者修改和扩展现有的业务逻辑：例如，可以创建一个模块，为 Odoo 的通用会计支持添加您所在国家的会计规则，而下一个模块可以添加实时可视化公交车队的支持。

因此，Odoo 的一切都始于模块，也终于模块。

模块的组成
-----------------------

一个 Odoo 模块可以包含许多元素：

业务对象
    声明为 Python 类，这些资源根据其配置由 Odoo 自动持久化

:doc:`对象视图 <../reference/user_interface/view_architectures>`
    定义业务对象的 UI 显示

:ref:`数据文件 <reference/data>`
    声明模型元数据的 XML 或 CSV 文件：

    * :doc:`视图 <../reference/user_interface/view_architectures>` 或 :ref:`报告 <reference/reports>`
    * 配置数据（模块参数化，:ref:`安全规则 <reference/security>`）
    * 演示数据
    * 以及更多

:ref:`Web 控制器 <reference/controllers>`
    处理来自 Web 浏览器的请求

静态 Web 数据
    用于 Web 界面或网站的图像、CSS 或 JavaScript 文件

模块结构
----------------

每个模块是一个 *模块目录* 中的一个目录。可以通过使用 :option:`--addons-path <odoo-bin --addons-path>` 选项来指定模块目录。

.. tip::
   :class: aphorism

   大多数命令行选项也可以通过 :ref:`配置文件 <reference/cmdline/config>` 设置

Odoo 模块通过其 :ref:`manifest <reference/module/manifest>` 声明。

一个模块也是一个 `Python 包 <http://docs.python.org/2/tutorial/modules.html#packages>`_，带有 ``__init__.py`` 文件，包含模块中各 Python 文件的导入指令。

例如，如果模块有一个名为 ``mymodule.py`` 的文件，``__init__.py`` 可能包含以下内容：

::

    from . import mymodule

Odoo 提供了一种帮助设置新模块的机制，:ref:`odoo-bin <reference/cmdline/server>` 有一个子命令 :ref:`scaffold <reference/cmdline/scaffold>` 来创建一个空模块：

.. code-block:: console

   $ odoo-bin scaffold <module name> <where to put it>

该命令为您的模块创建一个子目录，并自动创建一堆模块的标准文件。大多数文件仅包含注释代码或 XML。本教程将逐步解释这些文件的使用。

.. exercise:: 模块创建

   使用上述命令行创建一个空模块 Open Academy，并将其安装到 Odoo 中。

对象关系映射（ORM）
-------------------------

Odoo 的关键组件之一是 :abbr:`ORM (对象关系映射)` 层。该层避免了手动编写大多数 :abbr:`SQL (结构化查询语言)`，并提供了扩展性和安全性服务\ [#rawsql]_。

业务对象声明为扩展 :class:`~odoo.models.Model` 的 Python 类，这些类将它们集成到自动持久化系统中。

模型可以通过在定义时设置多个属性进行配置。最重要的属性是 :attr:`~odoo.models.Model._name`，它是必需的，并为模型在 Odoo 系统中定义一个名称。以下是模型的最简定义：

::

    from odoo import models
    class MinimalModel(models.Model):
        _name = 'test.model'

模型字段
------------

字段用于定义模型可以存储的内容及其存储位置。字段作为模型类的属性定义：

::

    from odoo import models, fields

    class LessMinimalModel(models.Model):
        _name = 'test.model2'

        name = fields.Char()

常用属性
~~~~~~~~~~~~~~~~~

就像模型本身一样，其字段也可以通过传递配置属性进行配置：

::

    name = fields.Char(required=True)

一些属性适用于所有字段，以下是最常用的：

:attr:`~odoo.fields.Field.string` ( ``unicode``，默认：字段名称)
    在 UI 中显示的字段标签（对用户可见）。
:attr:`~odoo.fields.Field.required` ( ``bool`` ，默认： ``False``)
    如果为  ``True`` ，该字段不能为空，必须有一个默认值或在创建记录时始终提供一个值。
:attr:`~odoo.fields.Field.help` ( ``unicode`` ，默认： ``''``)
    提供给用户的长表单帮助提示。
:attr:`~odoo.fields.Field.index` ( ``bool`` ，默认： ``False`` )
    要求 Odoo 在列上创建 `数据库索引`_。

简单字段
~~~~~~~~~~~~~

字段大致分为两类：“简单”字段，它们是存储在模型表中的原子值，以及“关系”字段，它们链接记录（同一模型或不同模型）。

简单字段的示例包括 :class:`~odoo.fields.Boolean`、:class:`~odoo.fields.Date`、:class:`~odoo.fields.Char`。

保留字段
~~~~~~~~~~~~~~~

Odoo 在所有模型中创建了几个字段\ [#autofields]_。这些字段由系统管理，不应写入。如果有用或必要，可以读取它们：

:attr:`~odoo.fields.Model.id` (:class:`~odoo.fields.Id`)
    记录在其模型中的唯一标识符。
:attr:`~odoo.fields.Model.create_date` (:class:`~odoo.fields.Datetime`)
    记录的创建日期。
:attr:`~odoo.fields.Model.create_uid` (:class:`~odoo.fields.Many2one`)
    创建记录的用户。
:attr:`~odoo.fields.Model.write_date` (:class:`~odoo.fields.Datetime`)
    记录的最后修改日期。
:attr:`~odoo.fields.Model.write_uid` (:class:`~odoo.fields.Many2one`)
    最后修改记录的用户。
特殊字段
~~~~~~~~~~

默认情况下，Odoo 还要求所有模型中必须有一个 ``name`` 字段，以便实现各种显示和搜索功能。用于这些功能的字段可以通过设置 :attr:`~odoo.models.Model._rec_name` 进行覆盖。

.. exercise:: 定义一个模型

   在 *openacademy* 模块中定义一个新的数据模型 *Course*。课程应有一个标题和描述。课程必须有一个标题。

数据文件
----------

Odoo 是一个高度数据驱动的系统。虽然通过 Python_ 代码可以自定义行为，但模块的部分价值在于其加载时设置的数据。

.. tip:: 一些模块的存在仅仅是为了向 Odoo 添加数据。
   :class: aphorism

模块数据通过 :ref:`数据文件 <reference/data>` 声明，XML 文件中包含 ``<record>`` 元素。每个 ``<record>`` 元素都会创建或更新一个数据库记录。

.. code-block:: xml

   <odoo>

           <record model="{模型名称}" id="{记录标识符}">
               <field name="{字段名}">{字段值}</field>
           </record>

   </odoo>

* ``model`` 是记录所属的 Odoo 模型名称。
* ``id`` 是 :term:`外部标识符`，它允许在不知道数据库内部标识符的情况下引用该记录。
* ``<field>`` 元素的 ``name`` 属性是模型中的字段名称（如 ``description``），其内容是字段的值。

数据文件必须在清单文件中声明才能加载，可以在 ``'data'`` 列表中声明（始终加载）或在 ``'demo'`` 列表中声明（仅在演示模式下加载）。

.. exercise:: 定义演示数据

   创建演示数据，为 *Courses* 模型填充一些演示课程。

.. tip::
   数据文件的内容仅在模块安装或更新时加载。

   在进行一些更改后，不要忘记使用 :ref:`odoo-bin -u openacademy <reference/cmdline>` 将更改保存到您的数据库中。

.. _howtos/module/actions:

操作和菜单
-----------------

操作和菜单是数据库中的常规记录，通常通过数据文件声明。操作可以通过三种方式触发：

#. 点击菜单项（与特定操作关联）
#. 点击视图中的按钮（如果这些按钮与操作相关联）
#. 作为对象上的上下文操作

由于菜单声明相对复杂，存在一个 ``<menuitem>`` 快捷方式，可以更轻松地声明 ``ir.ui.menu`` 并将其连接到相应的操作。

.. code-block:: xml

   <record model="ir.actions.act_window" id="action_list_ideas">
       <field name="name">Ideas</field>
       <field name="res_model">idea.idea</field>
       <field name="view_mode">tree,form</field>
   </record>
   <menuitem id="menu_ideas" parent="menu_root" name="Ideas" sequence="10"
             action="action_list_ideas"/>

.. danger::
   :class: aphorism

   操作必须在其对应菜单之前声明。

   数据文件按顺序执行，操作的 ``id`` 必须在菜单创建前已存在于数据库中。

.. exercise:: 定义新的菜单项

   在 OpenAcademy 菜单项下定义新的菜单项以访问课程。用户应能够：

   - 显示所有课程的列表
   - 创建/修改课程

基础视图
===========

视图定义了模型记录的显示方式。每种类型的视图代表了一种可视化模式（例如记录列表、其聚合的图表等）。可以通过其类型来请求视图（例如*合作伙伴的列表*），也可以通过其 id 来专门请求视图。对于通用请求，优先级最低的视图将被使用（因此每种类型的最低优先级视图是该类型的默认视图）。

:ref:`视图继承 <reference/view_records/inheritance>` 允许修改已声明的视图（添加或删除内容）。

通用视图声明
------------------------

视图作为模型 ``ir.ui.view`` 的记录声明。视图类型由 ``arch`` 字段的根元素隐含：

.. code-block:: xml

   <record model="ir.ui.view" id="view_id">
       <field name="name">view.name</field>
       <field name="model">object_name</field>
       <field name="priority" eval="16"/>
       <field name="arch" type="xml">
           <!-- 视图内容: <form>, <tree>, <graph>, ... -->
       </field>
   </record>

.. danger:: 视图内容是 XML。
   :class: aphorism

   因此，``arch`` 字段必须声明为 ``type="xml"`` 才能正确解析。

列表视图
----------

列表视图，也称为树状视图，以表格形式显示记录。

其根元素是 ``<tree>``。最简单的树状视图形式只列出要在表中显示的所有字段（每个字段作为一列）：

.. code-block:: xml

    <tree string="Idea list">
        <field name="name"/>
        <field name="inventor_id"/>
    </tree>

.. _howtos/module/views/form:

表单视图
----------

表单用于创建和编辑单个记录。

其根元素是 ``<form>``。它由高层结构元素（组、标签页）和交互元素（按钮和字段）组成：

.. code-block:: xml

    <form string="Idea form">
        <group colspan="4">
            <group colspan="2" col="2">
                <separator string="General stuff" colspan="2"/>
                <field name="name"/>
                <field name="inventor_id"/>
            </group>

            <group colspan="2" col="2">
                <separator string="Dates" colspan="2"/>
                <field name="active"/>
                <field name="invent_date" readonly="1"/>
            </group>

            <notebook colspan="4">
                <page string="Description">
                    <field name="description" nolabel="1"/>
                </page>
            </notebook>

            <field name="state"/>
        </group>
    </form>

.. exercise:: 使用 XML 自定义表单视图

   为 Course 对象创建您自己的表单视图。应显示的数据为：课程名称和描述。

.. exercise:: 使用标签页

   在 Course 表单视图中，将描述字段放置在一个标签页下，以便以后更轻松地添加其他包含附加信息的标签页。

表单视图还可以使用纯 HTML 进行更灵活的布局：

.. code-block:: xml

   <form string="Idea Form">
       <header>
           <button string="Confirm" type="object" name="action_confirm"
                   invisible="state != 'draft'" class="oe_highlight" />
           <button string="Mark as done" type="object" name="action_done"
                   invisible="state != 'confirmed'" class="oe_highlight"/>
           <button string="Reset to draft" type="object" name="action_draft"
                   invisible="state not in ['confirmed', 'done']" />
           <field name="state" widget="statusbar"/>
       </header>
       <sheet>
           <div class="oe_title">
               <label for="name" class="oe_edit_only" string="Idea Name" />
               <h1><field name="name" /></h1>
           </div>
           <separator string="General" colspan="2" />
           <group colspan="2" col="2">
               <field name="description" placeholder="Idea description..." />
           </group>
       </sheet>
   </form>
搜索视图
------------

搜索视图自定义与列表视图（以及其他聚合视图）关联的搜索字段。其根元素是 ``<search>``，它们由定义哪些字段可供搜索的字段组成：

.. code-block:: xml

   <search>
       <field name="name"/>
       <field name="inventor_id"/>
   </search>

如果模型没有搜索视图，Odoo 会自动生成一个只允许基于 ``name`` 字段搜索的视图。

.. exercise:: 搜索课程

   允许根据课程的标题或描述进行搜索。

模型之间的关系
========================

一个模型的记录可以与另一个模型的记录相关联。例如，一个销售订单记录与包含客户数据的客户记录相关联；它还与其销售订单行记录相关联。

.. exercise:: 创建一个课程会话模型

   对于 Open Academy 模块，我们考虑一个用于 *会话* 的模型：会话是指在特定时间对特定听众教授的课程。

   创建一个 *会话* 模型。一个会话有名称、开始日期、持续时间和座位数量。添加一个操作和一个菜单项来显示它们。通过菜单项使新模型可见。

关系字段
-----------------

关系字段链接记录，链接的记录可以是同一模型（层级结构）的记录，也可以是不同模型的记录。

关系字段的类型有：

:class:`Many2one(other_model, ondelete='set null') <odoo.fields.Many2one>`
    一个简单的指向另一个对象的链接::

        print(foo.other_id.name)

    .. seealso:: `外键 <http://www.postgresql.org/docs/12/static/tutorial-fk.html>`_

:class:`One2many(other_model, related_field) <odoo.fields.One2many>`
    虚拟关系，是 :class:`~odoo.fields.Many2one` 的逆关系。
    :class:`~odoo.fields.One2many` 的行为类似于记录的容器，访问它会返回一个（可能为空的）记录集::

        for other in foo.other_ids:
            print(other.name)

    .. danger::

       由于 :class:`~odoo.fields.One2many` 是虚拟关系，必须在 :samp:`{other_model}` 中有一个 :class:`~odoo.fields.Many2one` 字段，并且其名称必须是 :samp:`{related_field}`。

:class:`Many2many(other_model) <odoo.fields.Many2many>`
    双向多重关系，一方的任意记录可以与另一方的任意数量记录相关联。其行为类似于记录的容器，访问它也会返回一个可能为空的记录集::

        for other in foo.other_ids:
            print(other.name)

.. exercise:: Many2one 关系

   使用 many2one，修改 *Course* 和 *Session* 模型以反映它们与其他模型的关系：

   - 课程有一个 *负责人*；该字段的值是内置模型 ``res.users`` 的一条记录。
   - 会话有一个 *讲师*；该字段的值是内置模型 ``res.partner`` 的一条记录。
   - 会话与一个 *课程* 相关联；该字段的值是模型 ``openacademy.course`` 的一条记录，并且是必需的。
   - 修改视图以适应这些变化。

.. exercise:: 反向 one2many 关系

   使用反向关系字段 one2many，修改模型以反映课程和会话之间的关系。

.. exercise:: 多个 many2many 关系

   使用 many2many 关系字段，修改 *Session* 模型，使每个会话与一组 *与会者* 相关联。与会者将由合作伙伴记录表示，因此我们将关联内置模型 ``res.partner``。相应地修改视图。
继承
===========

模型继承
-----------------

Odoo 提供了两种 *继承* 机制，以模块化的方式扩展现有模型。

第一种继承机制允许模块修改在另一个模块中定义的模型的行为：

- 向模型添加字段，
- 重写模型上的字段定义，
- 向模型添加约束，
- 向模型添加方法，
- 重写模型上的现有方法。

第二种继承机制（委托）允许将每个模型的记录链接到父模型中的记录，并提供对父记录字段的透明访问。

.. image:: ../reference/backend/orm/inheritance_methods.png
   :align: center

.. seealso::
   * :attr:`~odoo.models.Model._inherit`
   * :attr:`~odoo.models.Model._inherits`

视图继承
----------------

Odoo 提供了视图继承功能，而不是直接修改现有视图（通过覆盖它们）。通过视图继承，子“扩展”视图被应用在根视图之上，可以在其父视图中添加或删除内容。

扩展视图通过 ``inherit_id`` 字段引用其父视图，而它的 ``arch`` 字段则由任意数量的 ``xpath`` 元素组成，这些元素选择并修改父视图的内容：

.. code-block:: xml

   <!-- 改进的想法类别列表 -->
   <record id="idea_category_list2" model="ir.ui.view">
       <field name="name">id.category.list2</field>
       <field name="model">idea.category</field>
       <field name="inherit_id" ref="id_category_list"/>
       <field name="arch" type="xml">
           <!-- 找到字段描述并在其后添加 idea_ids 字段 -->
           <xpath expr="//field[@name='description']" position="after">
             <field name="idea_ids" string="想法数量"/>
           </xpath>
       </field>
   </record>

``expr`` 
    一个 XPath_ 表达式，用于在父视图中选择单个元素。如果没有匹配元素或匹配多个元素，将引发错误。
``position`` 
    应用于匹配元素的操作：

    ``inside``
        将 ``xpath`` 的内容追加到匹配元素的末尾
    ``replace``
        使用 ``xpath`` 的内容替换匹配的元素，替换新内容中的任何 ``$0`` 节点为原始元素
    ``before``
        将 ``xpath`` 的内容作为匹配元素的兄弟元素插入到它之前
    ``after``
        将 ``xpath`` 的内容作为匹配元素的兄弟元素插入到它之后
    ``attributes``
        使用 ``xpath`` 内容中的特殊 ``attribute`` 元素来修改匹配元素的属性

.. tip::
   当匹配单个元素时，可以直接在要找到的元素上设置 ``position`` 属性。以下两种继承方式将给出相同的结果。

    .. code-block:: xml

       <xpath expr="//field[@name='description']" position="after">
           <field name="idea_ids" />
       </xpath>

       <field name="description" position="after">
           <field name="idea_ids" />
       </field>


.. exercise:: 修改现有内容

   * 使用模型继承，修改现有的 *Partner* 模型，添加一个 ``instructor`` 布尔字段，以及一个对应于会话-合作伙伴关系的 many2many 字段。
   * 使用视图继承，在合作伙伴的表单视图中显示这些字段。
域 (Domains)
~~~~~~~~~~~~

在 Odoo 中，:ref:`reference/orm/domains` 是用于对记录进行条件编码的值。一个域是用于选择模型记录子集的条件列表。每个条件都是由字段名、操作符和值组成的三元组。

例如，当在 *产品* 模型上使用以下域时，将选择所有单价超过 *1000* 的 *服务*：

.. code-block:: python

    [('product_type', '=', 'service'), ('unit_price', '>', 1000)]

默认情况下，条件通过隐式的 AND 组合。可以使用逻辑操作符 ``&`` （AND）、 ``|`` （OR）和 ``!``（NOT）来显式组合条件。这些操作符使用前缀表示法（操作符插入其参数之前，而不是参数之间）。例如，选择 "服务 *或* 单价 *不* 在 1000 到 2000 之间的产品" 的域如下所示：

.. code-block:: python

    ['|',
        ('product_type', '=', 'service'),
        '!', '&',
            ('unit_price', '>=', 1000),
            ('unit_price', '<', 2000)]

可以在关联字段中添加 ``domain`` 参数，以限制在客户端界面中选择记录时可用的记录。

.. exercise:: 关联字段的域

   当选择 *Session* 的讲师时，只应显示那些设置了 ``instructor`` 为  ``True``  的讲师（合作伙伴）。

.. exercise:: 更复杂的域

   创建新的合作伙伴类别 *Teacher / Level 1* 和 *Teacher / Level 2*。一个课程的讲师可以是讲师或任何级别的教师。

计算字段和默认值
=====================

到目前为止，字段的值都是直接存储在数据库中并从数据库中检索出来的。字段也可以是 *计算字段*。在这种情况下，字段的值不是从数据库中检索的，而是通过调用模型的方法即时计算出来的。

要创建计算字段，可以创建一个字段，并将其属性 :attr:`~odoo.fields.Field.compute` 设置为方法的名称。计算方法应该简单地对 ``self`` 中的每条记录设置计算字段的值。

.. danger:: ``self`` 是一个集合
   :class: aphorism

   对象 ``self`` 是一个 *记录集*，即一个有序的记录集合。它支持标准的 Python 集合操作，如 ``len(self)`` 和 ``iter(self)``，以及额外的集合操作，如 ``recs1 + recs2`` 。

   迭代 ``self`` 会逐个返回记录，每条记录本身是一个大小为 1 的集合。你可以通过点符号来访问或分配单个记录上的字段，例如 ``record.name``。

.. code-block:: python

   import random
   from odoo import models, fields, api

   class ComputedModel(models.Model):
       _name = 'test.computed'

       name = fields.Char(compute='_compute_name')

       def _compute_name(self):
           for record in self:
               record.name = str(random.randint(1, 1e6))
依赖关系 (Dependencies)
-----------------------

计算字段的值通常依赖于计算记录上其他字段的值。ORM 期望开发者使用修饰器 :func:`~odoo.api.depends` 在计算方法中指定这些依赖关系。给定的依赖关系用于在某些依赖项被修改时，触发字段的重新计算：

.. code-block:: python

    from odoo import models, fields, api

    class ComputedModel(models.Model):
        _name = 'test.computed'

        name = fields.Char(compute='_compute_name')
        value = fields.Integer()

        @api.depends('value')
        def _compute_name(self):
            for record in self:
                record.name = "Record with value %s" % record.value

.. exercise:: 计算字段

   * 为 *Session* 模型添加已占座位的百分比字段
   * 在列表视图和表单视图中显示该字段
   * 将该字段显示为进度条

默认值 (Default values)
------------------------

任何字段都可以赋予一个默认值。在字段定义中，添加选项 ``default=X``，其中 ``X`` 可以是 Python 的字面值（布尔值、整数、浮点数、字符串），也可以是一个接受记录集并返回值的函数：

.. code-block:: python

    name = fields.Char(default="Unknown")
    user_id = fields.Many2one('res.users', default=lambda self: self.env.user)

.. note::
   对象 ``self.env`` 提供了访问请求参数和其他有用内容的途径：

    - ``self.env.cr`` 或 ``self._cr`` 是数据库 *cursor* 对象；用于查询数据库
    - ``self.env.uid`` 或 ``self._uid`` 是当前用户的数据库 ID
    - ``self.env.user`` 是当前用户的记录
    - ``self.env.context`` 或 ``self._context`` 是上下文字典
    - ``self.env.ref(xml_id)`` 返回与 XML ID 对应的记录
    - ``self.env[model_name]`` 返回给定模型的实例

.. exercise:: 活动对象 – 默认值

   * 将 ``start_date`` 字段的默认值定义为今天（请参考 :class:`~odoo.fields.Date`）。
   * 在 *Session* 类中添加字段 ``active``，并将会话默认设置为激活状态。

Onchange
========

"onchange" 机制为客户端界面提供了一种在用户为字段填写值后自动更新表单的方式，而不保存任何内容到数据库中。

例如，假设模型有三个字段 ``amount`` 、 ``unit_price`` 和 ``price``，你希望在任意一个字段修改时自动更新表单上的价格。为此，你可以定义一个方法，其中 ``self`` 代表表单视图中的记录，并使用 :func:`~odoo.api.onchange` 修饰该方法，指定要触发的字段。你对 ``self`` 所做的任何更改都会反映在表单中。

.. code-block:: xml

   <!-- 表单视图内容 -->
   <field name="amount"/>
   <field name="unit_price"/>
   <field name="price" readonly="1"/>

.. code-block:: python

   # onchange 处理器
   @api.onchange('amount', 'unit_price')
   def _onchange_price(self):
       # 设置自动更改的字段
       self.price = self.amount * self.unit_price
       # 可以选择返回警告和域
       return {
           'warning': {
               'title': "发生了一些错误",
               'message': "确实很糟糕",
           }
       }

对于计算字段，onchange 的行为是内置的。可以通过在 *Session* 表单中试验这一点来观察：更改座位数或参与者人数，``taken_seats`` 的进度条将自动更新。

.. exercise:: 警告

   添加一个显式的 onchange 来警告无效值，例如座位数为负数，或参与者人数超过座位数。
模型约束 (Model constraints)
============================

Odoo 提供了两种方式来设置自动验证的不变性约束：
:func:`Python 约束 <odoo.api.constrains>` 和 :attr:`SQL 约束 <odoo.models.Model._sql_constraints>`。

Python 约束是一个使用 :func:`~odoo.api.constrains` 装饰器的方法，该方法在记录集上被调用。装饰器指定了约束涉及的字段，以便当其中一个字段被修改时，约束会被自动评估。若不满足约束，方法应抛出异常：

.. code-block:: python

    from odoo.exceptions import ValidationError

    @api.constrains('age')
    def _check_something(self):
        for record in self:
            if record.age > 20:
                raise ValidationError("Your record is too old: %s" % record.age)
        # 所有记录通过测试，不返回任何内容

.. exercise:: 添加 Python 约束

   添加一个约束，检查讲师是否没有作为其自己课程的参与者。

SQL 约束通过模型属性 :attr:`~odoo.models.Model._sql_constraints` 定义。该属性赋值为字符串的三元组列表 ``(name, sql_definition, message)`` ，其中 ``name`` 是有效的 SQL 约束名称， ``sql_definition`` 是一个 table_constraint_ 表达式，``message`` 是错误信息。

.. exercise:: 添加 SQL 约束

   在 `PostgreSQL 文档`_ 的帮助下，添加以下约束：

   #. 检查课程描述和课程标题是否不同
   #. 使课程名称唯一

.. exercise:: 练习 6 - 添加复制选项

   由于我们为课程名称添加了唯一性约束，现在无法使用 "复制" 功能了 (:menuselection:`表单 --> 复制`)。

   重新实现你自己的 "copy" 方法，允许复制课程对象，同时将原名称更改为 "Copy of [原名称]"。

高级视图 (Advanced Views)
=========================

树视图 (Tree views)
-------------------

树视图可以采用额外的属性来进一步自定义其行为：

``decoration-{$name}`` 
    允许根据对应记录的属性更改行文本的样式。

    这些值是 Python 表达式。对于每条记录，表达式会在记录的属性作为上下文值时进行评估，如果结果为  ``True`` ，则应用对应的样式到该行。以下是一些可用的上下文值：

    * ``uid``：当前用户的 ID，
    * ``today``：当前本地日期，格式为 ``YYYY-MM-DD`` 的字符串，
    * ``now`` ：与 ``today`` 相同，外加当前时间。该值的格式为 ``YYYY-MM-DD hh:mm:ss``。

    ``{$name}`` 可以是 ``bf`` （加粗字体）、 ``it`` （斜体），或任何 `Bootstrap 语境颜色 <https://getbootstrap.com/docs/3.3/components/#available-variations>`_（ ``danger`` 、 ``info`` 、 ``muted`` 、 ``primary`` 、 ``success`` 或 ``warning``）。

    .. code-block:: xml

        <tree string="Idea Categories" decoration-info="state=='draft'"
            decoration-danger="state=='trashed'">
            <field name="name"/>
            <field name="state"/>
        </tree>

``editable`` 
    可以是 ``"top"`` 或 ``"bottom"``。使树视图在原地可编辑（无需通过表单视图），该值决定新行的出现位置。

.. exercise:: 列表着色

   修改 *Session* 的树视图，使得持续时间少于 5 天的课程以蓝色显示，超过 15 天的课程以红色显示。

日历视图 (Calendars)
====================

日历视图将记录显示为日历事件。它们的根元素是 ``<calendar>``，最常用的属性有：

``color`` 
    用于 *颜色分段* 的字段名称。颜色会自动分配给事件，但在同一颜色分段中的事件（在其 ``@color`` 字段具有相同值的记录）将获得相同颜色。
``date_start`` 
    记录的字段，保存事件的开始日期/时间。
``date_stop`` （可选）
    记录的字段，保存事件的结束日期/时间。
``string`` 
    记录的字段，用于定义每个日历事件的标签。

.. code-block:: xml

   <calendar string="Ideas" date_start="invent_date" color="inventor_id">
       <field name="name"/>
   </calendar>

.. exercise:: 日历视图

   为 *Session* 模型添加一个日历视图，使用户能够查看与 Open Academy 相关的事件。

搜索视图 (Search views)
=======================

搜索视图中的 ``<field>`` 元素可以具有 ``@filter_domain`` ，该属性会覆盖用于搜索给定字段生成的域。在给定的域中，``self`` 代表用户输入的值。在下面的示例中，它用于在 ``name`` 和 ``description`` 两个字段上进行搜索。

搜索视图还可以包含 ``<filter>`` 元素，这些元素作为预定义搜索的切换。过滤器必须具有以下属性之一：

``domain`` 
    将给定的域添加到当前搜索中。
``context`` 
    向当前搜索添加一些上下文；使用键 ``group_by`` 对结果进行分组。

.. code-block:: xml

   <search string="Ideas">
       <field name="name"/>
       <field name="description" string="Name and description"
              filter_domain="['|', ('name', 'ilike', self), ('description', 'ilike', self)]"/>
       <field name="inventor_id"/>
       <field name="country_id" widget="selection"/>

       <filter name="my_ideas" string="My Ideas"
               domain="[('inventor_id', '=', uid)]"/>
       <group string="Group By">
           <filter name="group_by_inventor" string="Inventor"
                   context="{'group_by': 'inventor_id'}"/>
       </group>
   </search>

要在动作中使用非默认搜索视图，应使用动作记录的 ``search_view_id`` 字段将其链接。

该动作还可以通过其 ``context`` 字段设置搜索字段的默认值：形式为 :samp:`search_default_{field_name}` 的上下文键将用提供的值初始化 *field_name*。搜索过滤器必须具有可选的 ``@name`` 属性，以便可以默认启用并表现为布尔值（它们只能被默认启用）。

.. exercise:: 搜索视图

   #. 在课程搜索视图中添加一个按钮，以过滤当前用户负责的课程。使其默认选中。
   #. 添加一个按钮，以按负责用户对课程进行分组。
甘特图 (Gantt)
===========

.. warning::
   甘特图视图需要 `web_gantt` 模块，该模块在 :ref:`企业版 <install/editions>` 中提供。

甘特图是用于展示项目规划和进展的横向条形图，其根元素是 ``<gantt>``。

.. code-block:: xml

   <gantt string="Ideas"
          date_start="invent_date"
          date_stop="date_finished"
          progress="progress"
          default_group_by="inventor_id" />

.. exercise:: 甘特图

   添加一个甘特图，允许用户查看与 Open Academy 模块相关的课程安排。课程应按讲师分组。

图表视图 (Graph views)
====================

图表视图允许对模型进行汇总概述和分析，其根元素是 ``<graph>``。

.. note::
   透视视图（元素 ``<pivot>``）是一个多维表，允许选择筛选器和维度，以获取正确的汇总数据集，然后转向更图形化的概述。透视视图与图表视图共享相同的内容定义。

图表视图有 4 种显示模式，默认模式通过 ``@type`` 属性进行选择。

条形图（默认）
    条形图，第一维用于定义水平轴上的组，其他维度在每组内定义汇总的条形。

    默认情况下，条形是并排显示的，可以通过在 ``<graph>`` 上使用 ``@stacked="True"`` 来堆叠。

线图
    二维线图。

饼图
    二维饼图。

图表视图包含 ``<field>``，其具有强制性的 ``@type`` 属性，取值如下：

``row`` （默认）
    该字段应默认进行汇总。

``measure`` 
    该字段应进行汇总，而不是分组。

.. code-block:: xml

   <graph string="Total idea score by Inventor">
       <field name="inventor_id"/>
       <field name="score" type="measure"/>
   </graph>

.. warning::
   图表视图对数据库值进行汇总，不适用于非存储的计算字段。

.. exercise:: 图表视图

   在 Session 对象中添加一个图表视图，以条形图的形式显示每门课程的参与人数。
看板 (Kanban)
==========

看板用于组织任务、生产过程等，其根元素是 ``<kanban>``。

看板视图展示了一组卡片，可能按列分组。每张卡片代表一条记录，每一列代表一个聚合字段的值。

例如，项目任务可以按阶段组织（每一列是一个阶段），或按负责人组织（每一列是一个用户）等。

看板视图定义每张卡片的结构，结合了表单元素（包括基本 HTML）和 :ref:`reference/qweb`。

.. exercise:: 看板视图

   添加一个看板视图，显示按课程分组的课程（因此列是课程）。

安全性 (Security)
================

访问控制机制必须配置，以实现一致的安全策略。

基于组的访问控制机制
-----------------------

组作为模型 ``res.groups`` 的常规记录创建，并通过菜单定义授予菜单访问权限。然而，即使没有菜单，对象仍可能间接可访问，因此必须为组定义实际的对象级权限（读取、写入、创建、删除）。这些通常通过 CSV 文件插入到模块中。还可以通过字段的 `groups` 属性限制对视图或对象中特定字段的访问。

访问权限
--------

访问权限作为模型 ``ir.model.access`` 的记录定义。每个访问权限与一个模型、一个组（或无组以实现全局访问）和一组权限（读取、写入、创建、删除）关联。这些访问权限通常通过以模型命名的 CSV 文件创建：``ir.model.access.csv`` 。

.. code-block:: text

   id,name,model_id/id,group_id/id,perm_read,perm_write,perm_create,perm_unlink
   access_idea_idea,idea.idea,model_idea_idea,base.group_user,1,1,1,0
   access_idea_vote,idea.vote,model_idea_vote,base.group_user,1,1,1,0

.. exercise:: 通过 Odoo 界面添加访问控制

   创建一个新用户 "John Smith"。然后创建一个组 "OpenAcademy / Session Read"，并为 *Session* 模型赋予读取权限。

.. exercise:: 通过数据文件在模块中添加访问控制

   使用数据文件，

   * 创建一个组 *OpenAcademy / Manager*，对所有 OpenAcademy 模型拥有完全访问权限。
   * 使 *Session* 和 *Course* 对所有用户可读。
记录规则 (Record Rules)
====================

记录规则限制对给定模型的一组记录的访问权限。规则是模型 ``ir.rule`` 的一条记录，关联到一个模型、多个组（多对多字段），以及适用的权限和一个域。该域指定了访问权限限制的记录。

以下是一个规则示例，该规则防止删除不在 ``cancel`` 状态的潜在客户。注意，字段 ``groups`` 的值必须遵循 ORM 中方法 :meth:`~odoo.models.Model.write` 的相同约定。

.. code-block:: xml

   <record id="delete_cancelled_only" model="ir.rule">
       <field name="name">Only cancelled leads may be deleted</field>
       <field name="model_id" ref="crm.model_crm_lead"/>
       <field name="groups" eval="[(4, ref('sales_team.group_sale_manager'))]"/>
       <field name="perm_read" eval="0"/>
       <field name="perm_write" eval="0"/>
       <field name="perm_create" eval="0"/>
       <field name="perm_unlink" eval="1" />
       <field name="domain_force">[('state','=','cancel')]</field>
   </record>

.. exercise:: 记录规则

   为模型 Course 和组 "OpenAcademy / Manager" 添加一个记录规则，限制对负责人的 ``write`` 和 ``unlink`` 访问。如果一门课程没有负责人，则该组的所有用户必须能够修改它。

向导 (Wizards)
==============

向导通过动态表单描述与用户的交互会话（或对话框）。向导只是扩展类 :class:`~odoo.models.TransientModel` 的模型，而不是 :class:`~odoo.models.Model`。类 :class:`~odoo.models.TransientModel` 扩展了 :class:`~odoo.models.Model`，并重用其现有机制，具有以下特性：

- 向导记录并不意味着持久化；它们会在一段时间后自动从数据库中删除。这就是它们被称为 *transient* 的原因。
- 向导记录可以通过关系字段（多对一或多对多）引用常规记录或向导记录，但常规记录 *不能* 通过多对一字段引用向导记录。

我们希望创建一个向导，允许用户为特定课程或一系列课程创建参与者。

.. exercise:: 定义向导

   创建一个向导模型，与 *Session* 模型建立多对一关系，并与 *Partner* 模型建立多对多关系。

启动向导
--------

向导只是带有 ``target`` 字段设置为 ``new`` 的 :ref:`window actions <howtos/module/actions>`，这会在单独的对话框中打开视图（通常是 :ref:`a form <howtos/module/views/form>`）。该操作可以通过菜单项触发，但通常是通过按钮触发。

启动向导的另一种方法是通过树视图或表单视图的 :menuselection:`Action` 菜单。这是通过操作的 ``binding_model_id`` 字段实现的。设置此字段将使操作在与之“绑定”的模型的视图中显示。

.. code:: xml

   <record id="launch_the_wizard" model="ir.actions.act_window">
       <field name="name">Launch the Wizard</field>
       <field name="res_model">wizard.model.name</field>
       <field name="view_mode">form</field>
       <field name="target">new</field>
       <field name="binding_model_id" ref="model_context_model_ref"/>
   </record>

.. tip::
   虽然向导使用常规视图和按钮，但通常在表单中单击任何按钮会首先保存表单，然后关闭对话框。由于这在向导中通常是不希望的，因此有一个特殊属性 ``special="cancel"`` 可用，该属性立即关闭向导而不保存表单。

.. exercise:: 启动向导

   #. 为向导定义一个表单视图。
   #. 在 *Session* 模型的上下文中添加启动它的操作。
   #. 在向导中为课程字段定义默认值；使用上下文参数 ``self._context`` 获取当前课程。

.. exercise:: 注册参与者

   向向导添加按钮，并实现相应的方法以将参与者添加到给定的课程中。

.. exercise:: 将参与者注册到多个课程

   修改向导模型，以便可以将参与者注册到多个课程中。
国际化 (Internationalization)
===========================

每个模块都可以在 i18n 目录中提供自己的翻译，文件命名为 LANG.po，其中 LANG 是语言的地区代码，或当语言和国家组合不同的时候（例如，pt.po 或 pt_BR.po）。Odoo 会自动为所有启用的语言加载翻译。开发人员在创建模块时始终使用英语，然后通过 Odoo 的 gettext POT 导出功能导出模块术语（:menuselection:`设置 --> 翻译 --> 导入/导出 --> 导出翻译`，不指定语言）来创建模块模板 POT 文件，然后推导出翻译的 PO 文件。许多 IDE 都有插件或模式用于编辑和合并 PO/POT 文件。

.. tip::
   Odoo 生成的可移植对象文件已发布在 `Transifex <https://www.transifex.com/odoo/public/>`_ 上，使软件翻译变得简单。

.. code-block:: text

  |- idea/ # 模块目录
     |- i18n/ # 翻译文件
        | - idea.pot # 翻译模板（从 Odoo 导出）
        | - fr.po # 法语翻译
        | - pt_BR.po # 巴西葡萄牙语翻译
        | (...)

.. tip::
   默认情况下，Odoo 的 POT 导出仅提取 XML 文件内或 Python 代码中的字段定义中的标签，但任何 Python 字符串都可以通过用函数 :func:`odoo._` 将其括起来进行翻译（例如 ``_("Label")``）。

.. exercise:: 翻译模块

   选择您的 Odoo 安装的第二种语言。使用 Odoo 提供的功能翻译您的模块。

报告 (Reporting)
================

打印报告
---------

Odoo 使用基于 :ref:`reference/qweb`、`Twitter Bootstrap`_ 和 Wkhtmltopdf_ 的报告引擎。

报告由两个元素组合而成：

* ``ir.actions.report``，配置报告的各种基本参数（默认类型，生成后是否应将报告保存到数据库等）

  .. code-block:: xml

     <record id="account_invoices" model="ir.actions.report">
         <field name="name">Invoices</field>
         <field name="model">account.invoice</field>
         <field name="report_type">qweb-pdf</field>
         <field name="report_name">account.report_invoice</field>
         <field name="report_file">account.report_invoice</field>
         <field name="attachment_use" eval="True"/>
         <field name="attachment">(object.state in ('open','paid')) and
             ('INV'+(object.number or '').replace('/','')+'.pdf')</field>
         <field name="binding_model_id" ref="model_account_invoice"/>
         <field name="binding_type">report</field>
     </record>

  .. tip::

     因为这主要是一个标准动作，与 :ref:`howto/module/wizard` 类似，通常将报告作为模型的树视图和/或表单视图中的 *上下文项* 添加，通过 ``binding_model_id`` 字段。

     在这里，我们还使用 ``binding_type`` 使得报告出现在 *报告* 上下文菜单中，而不是 *动作* 菜单中。虽然没有技术差异，但将元素放在正确的位置有助于用户。

* 一个标准的 :ref:`QWeb 视图 <reference/view_architectures/qweb>` 用于实际的报告：

  .. code-block:: xml

     <t t-call="web.html_container">
         <t t-foreach="docs" t-as="o">
             <t t-call="web.external_layout">
                 <div class="page">
                     <h2>Report title</h2>
                 </div>
             </t>
         </t>
     </t>

  标准渲染上下文提供了许多元素，其中最重要的是：

  ``docs``
      打印报告的记录
  ``user``
      打印报告的用户

由于报告是标准网页，因此它们通过 URL 可用，输出参数可以通过此 URL 操作，例如 *Invoice* 报告的 HTML 版本可以通过 
http://localhost:8069/report/html/account.report_invoice/1 访问（如果安装了 ``account``），PDF 版本可以通过 
http://localhost:8069/report/pdf/account.report_invoice/1 访问。

.. _reference/backend/reporting/printed-reports/pdf-without-styles:

.. danger::

   如果您的 PDF 报告缺少样式（即文本出现，但样式/布局与 HTML 版本不同），可能是您的 wkhtmltopdf_ 进程无法访问您的网络服务器以下载它们。

   如果您检查服务器日志并看到在生成 PDF 报告时 CSS 样式未被下载，那么大概率这是问题所在。

   wkhtmltopdf_ 进程将使用 ``web.base.url`` 系统参数作为所有链接文件的 *根路径*，但此参数会在管理员每次登录时自动更新。如果您的服务器位于某种代理后面，可能无法访问。您可以通过添加以下系统参数之一来修复此问题：

   - ``report.url``，指向从您的服务器可访问的 URL（可能是 ``http://localhost:8069`` 或类似地址）。这将仅用于此目的。

   - ``web.base.url.freeze``，设置为  ``True``  将停止对 ``web.base.url`` 的自动更新。

.. exercise:: 为 Session 模型创建报告

   对于每个会话，应该显示会话的名称、开始和结束时间，并列出会话的参与者。
仪表板 (Dashboards)
==================

.. exercise:: 定义一个仪表板

   定义一个仪表板，包含您创建的图形视图、会话日历视图和课程列表视图（可以切换到表单视图）。此仪表板应通过菜单项在菜单中可用，并在选择 OpenAcademy 主菜单时自动显示在网页客户端中。

.. [#autofields] 可以 :ref:`禁用某些字段的自动创建 <reference/fields/automatic/log_access>`。
.. [#rawsql] 可以编写原始 SQL 查询，但需要小心，因为这会绕过所有 Odoo 身份验证和安全机制。

.. _database index:
    https://use-the-index-luke.com/sql/preface
.. _POEdit: https://poedit.net
.. _PostgreSQL's documentation:
.. _table_constraint:
    https://www.postgresql.org/docs/12/static/ddl-constraints.html
.. _python: https://python.org
.. _XPath: https://w3.org/TR/xpath
.. _twitter bootstrap: https://getbootstrap.com
.. _wkhtmltopdf: https://wkhtmltopdf.org
