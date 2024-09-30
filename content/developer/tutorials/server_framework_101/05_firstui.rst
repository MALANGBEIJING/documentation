========================================
第5章：最终，进行一些用户界面操作
========================================

现在我们已经创建了新的 :doc:`模型 <03_basicmodel>` 及其相应的 :doc:`访问权限 <04_securityintro>`，是时候与用户界面进行交互了。

在本章结束时，我们将创建几个菜单，以便访问默认的列表和表单视图。

数据文件（XML）
================

**参考**：有关此主题的文档可以在 :ref:`reference/data` 中找到。

在 :doc:`04_securityintro` 中，我们通过 CSV 文件添加了数据。当要加载的数据格式简单时，CSV 格式很方便。当格式更复杂（例如，加载视图结构或电子邮件模板）时，我们使用 XML 格式。例如，这个
`帮助字段 <https://github.com/odoo/odoo/blob/09c59012bf80d2ccbafe21c39e604d6cfda72924/addons/crm/views/crm_lost_reason_views.xml#L61-L69>`__
包含 HTML 标签。虽然通过 CSV 文件加载这样的数据是可能的，但使用 XML 文件更为方便。

XML 文件必须添加到与 CSV 文件相同的文件夹，并在 ``__manifest__.py`` 中以类似方式定义。当模块安装或更新时，数据文件的内容也会按顺序加载，因此对 CSV 文件所做的所有说明也适用于 XML 文件。
当数据与视图相关联时，我们将其添加到 ``views`` 文件夹中。

在本章中，我们将通过 XML 文件加载第一个操作和菜单。操作和菜单是数据库中的标准记录。

.. 注意::

    当性能重要时，CSV 格式优于 XML 格式。在 Odoo 中，加载 CSV 文件比加载 XML 文件更快。

在 Odoo 中，用户界面（操作、菜单和视图）主要通过创建和组合在 XML 文件中定义的记录来定义。一个常见的模式是菜单 > 操作 > 视图。
用户通过几个菜单级别导航以访问记录；最深的级别是触发打开记录列表的操作。

操作
=======

**参考**：有关此主题的文档可以在 :doc:`../../reference/backend/actions` 中找到。

.. 注意::

    **目标**：在本节结束时，系统中应该加载一个操作。我们在用户界面中尚看不到任何内容，但日志中应该加载该文件：

    .. code-block:: text

        INFO rd-demo odoo.modules.loading: loading estate/views/estate_property_views.xml

操作可以通过三种方式触发：

1. 通过单击菜单项（链接到特定操作）
2. 通过单击视图中的按钮（如果这些按钮与操作连接）
3. 作为对象上的上下文操作

本章只涵盖第一种情况。第二种情况将在 :doc:`后面的章节 <09_actions>` 中讨论，而最后一种是高级主题的重点。在我们的房地产示例中，我们希望将一个菜单链接到 ``estate.property`` 模型，以便我们能够创建新记录。操作可以视为菜单与模型之间的链接。

我们的 `test_model` 的基本操作如下：

.. code-block:: xml

    <record id="test_model_action" model="ir.actions.act_window">
        <field name="name">测试操作</field>
        <field name="res_model">test_model</field>
        <field name="view_mode">tree,form</field>
    </record>

- ``id`` 是一个 :term:`外部标识符`。它可以用于引用记录（而不需要知道其数据库内标识符）。
- ``model`` 的固定值为 ``ir.actions.act_window`` (:ref:`reference/actions/window`)。
- ``name`` 是操作的名称。
- ``res_model`` 是该操作适用的模型。
- ``view_mode`` 是可用的视图；在此情况下，它们是列表（树）和表单视图。稍后我们会看到 :doc:`<14_qwebintro>` 可以有其他视图模式。

Odoo 中随处可见示例，但
`这个 <https://github.com/odoo/odoo/blob/09c59012bf80d2ccbafe21c39e604d6cfda72924/addons/crm/views/crm_lost_reason_views.xml#L57-L70>`__
是一个简单操作的良好示例。注意 XML 数据文件的结构，因为您将在以下练习中需要它。

.. 练习:: 添加操作。

    在适当的文件夹中创建 ``estate_property_views.xml`` 文件，并在 ``__manifest__.py`` 文件中定义它。

    为模型 ``estate.property`` 创建一个操作。

重新启动服务器，您应该在日志中看到文件已加载。

菜单
=====

**参考**：有关此主题的文档可以在 :ref:`reference/data/shortcuts` 中找到。

.. 注意::

    **目标**：在本节结束时，应该创建三个菜单，并显示默认视图：

    .. image:: 05_firstui/estate_menu_root.png
      :align: center
      :alt: 根菜单

    .. image:: 05_firstui/estate_menu_action.png
      :align: center
      :alt: 一级和操作菜单

    .. image:: 05_firstui/estate_form_default.png
      :align: center
      :alt: 默认表单视图

为了减少声明菜单（``ir.ui.menu``）并将其连接到相应操作的复杂性，我们可以使用 ``<menuitem>`` 快捷方式。

我们的 ``test_model_action`` 的基本菜单是：

.. code-block:: xml

    <menuitem id="test_model_menu_action" action="test_model_action"/>

菜单 ``test_model_menu_action`` 链接到操作 ``test_model_action``，而该操作又链接到模型 `test_model`。正如之前提到的，操作可以视为菜单与模型之间的链接。

然而，菜单总是遵循一种结构，实际上有三个级别的菜单：

1. 根菜单，在应用切换器中显示（Odoo Community 应用切换器是下拉菜单）
2. 一级菜单，在顶部栏中显示
3. 操作菜单

   .. image:: 05_firstui/menu_01.png
      :align: center
      :alt: 根菜单

   .. image:: 05_firstui/menu_02.png
      :align: center
      :alt: 一级和操作菜单

定义结构的最简单方法是在 XML 文件中创建它。我们的 ``test_model_action`` 的基本结构如下：

.. code-block:: xml

    <menuitem id="test_menu_root" name="测试">
        <menuitem id="test_first_level_menu" name="第一层">
            <menuitem id="test_model_menu_action" action="test_model_action"/>
        </menuitem>
    </menuitem>

第三个菜单的名称来自 ``action`` 的名称。

.. 练习:: 添加菜单。

    在适当的文件夹中创建 ``estate_menus.xml`` 文件，并在 ``__manifest__.py`` 文件中定义它。请记住数据文件的顺序加载 ;-)

    为在前一练习中创建的 ``estate.property`` 操作创建三个级别的菜单。请参阅本节的 **目标** 以获取预期结果。

重新启动服务器并 **刷新浏览器**\ [#refresh]_. 您现在应该能看到菜单，甚至可以创建您的第一个房地产物业广告！

字段、属性和视图
===========================

.. 注意::

    **目标**：在本节结束时，销售价格应为只读，卧室数量和可用日期应具有默认值。此外，销售价格和可用日期值在记录被复制时不会被复制。

    .. image:: 05_firstui/attribute_and_default.gif
      :align: center
      :alt: 模型与视图之间的交互

    保留字段 ``active`` 和 ``state`` 被添加到 ``estate.property`` 模型中。

到目前为止，我们只使用了通用视图来处理我们的房地产物业广告，但在大多数情况下，我们希望细化视图。Odoo 中可以进行许多细化，但通常第一步是确保：

- 一些字段具有默认值
- 一些字段为只读
- 一些字段在复制记录时不被复制

在我们的房地产业务案例中，我们希望：

- 销售价格应为只读（稍后会自动填写）
- 可用日期和销售价格在复制记录时不应被复制
- 默认卧室数量应为 2
- 默认可用日期应为 3 个月后

一些新属性
-------------------

在继续进行视图设计之前，让我们回顾一下我们的模型定义。我们看到一些属性，如 ``required=True``，会影响数据库中的表模式。其他属性将影响视图或提供默认值。

.. 练习:: 向字段添加新属性。

  找到适当的属性（见 :class:`~odoo.fields.Field`）以：

  - 设置销售价格为只读
  - 防止复制可用日期和销售价格值

重新启动服务器并刷新浏览器。您将无法设置任何销售价格。复制记录时，可用日期应为空。

默认值
--------------

任何字段都可以设置默认值。在字段定义中，添加选项 ``default=X``，其中 ``X`` 是 Python 文字值（布尔值、整数、浮点数、字符串）或一个函数，接受模型并返回一个值：

.. code-block:: python

    name = fields.Char(default="未知")
    last_seen = fields.Datetime("最后查看", default=fields.Datetime.now)

``name`` 字段将默认具有值 '未知'，而 ``last_seen`` 字段将设置为当前时间。

.. 练习:: 设置默认值。

    添加适当的默认属性，使得：

    - 默认卧室数量为 2
    - 默认可用日期为 3 个月后

    提示：这可能对您有所帮助： :meth:`~odoo.fields.Date.today`

检查默认值是否按预期设置。

保留字段
---------------

**参考**：有关此主题的文档可以在 :ref:`reference/orm/fields/reserved` 中找到。

一些字段名称保留用于预定义行为。当相关行为被期望时，应在模型上定义这些字段。

.. 练习:: 添加活动字段。

    向 ``estate.property`` 模型添加 ``active`` 字段。

重新启动服务器，创建新物业，然后返回列表视图... 该物业将不再列出！ ``active`` 是具有特定行为的保留字段示例：当记录 ``active=False`` 时，它会自动从任何搜索中删除。要显示创建的物业，您需要特意搜索非活动记录。

.. image:: 05_firstui/inactive.gif
  :align: center
  :alt: 非活动记录

.. 练习:: 为活动字段设置默认值。

    为 ``active`` 字段设置适当的默认值，以便其不再消失。

注意，默认的 ``active=False`` 值已分配给所有现有记录。

.. 练习:: 添加状态字段。

    向 ``estate.property`` 模型添加一个 ``state`` 字段。五个可能的值：新建、收到报价、接受报价、已售出和已取消。它必须是必需的，不应被复制，并且其默认值应设置为 '新建'。

    确保使用正确的类型！

``state`` 将在稍后用于多个用户界面增强。

现在，我们能够通过默认视图与用户界面进行交互，下一步显而易见：我们希望定义 :doc:`我们自己的视图 <06_basicviews>`。

.. [#refresh] 需要刷新，因为 web 客户端出于性能原因保持各种菜单和视图的缓存。
