=======
操作
=======

操作定义了系统对用户操作的响应行为：登录、操作按钮、选择发票等。

操作可以存储在数据库中，也可以直接作为字典返回，例如在按钮方法中。所有操作共享两个必填属性：

``type``
    当前操作的类别，决定可以使用哪些字段以及如何解释操作。
``name``
    操作的简短用户可读描述，可能会显示在客户端界面中。

客户端可以通过 4 种形式获取操作：

*  ``False``
      如果当前有任何操作对话框打开，关闭它。
*  字符串
      如果与 :ref:`客户端操作 <reference/actions/client>` 匹配，则解释为客户端操作的标记，否则视为数字。
*  数字
      从数据库中读取相应的操作记录，可能是数据库标识符或 :term:`外部 id`。
*  字典
      解释为客户端操作描述符并执行。

.. _reference/bindings:

绑定
========

除了两个必填属性外，所有操作还共享一些*可选*属性，用于在任意模型的上下文菜单中呈现操作：

``binding_model_id``
    指定操作绑定到的模型。

    .. note:: 对于服务器操作，使用 ``model_id``。

``binding_type``
    指定绑定类型，主要决定操作将出现在哪个上下文菜单下。

    ``action`` (默认)
        指定操作将出现在绑定模型的 :menuselection:`操作` 上下文菜单中。
    ``report``
        指定操作将出现在绑定模型的 :menuselection:`打印` 上下文菜单中。

``binding_view_types``
    操作出现在上下文菜单中的视图类型的逗号分隔列表，通常是“列表”和/或“表单”。默认为 ``list,form``（同时包含列表和表单）。

.. _reference/actions/window:

窗口操作 (``ir.actions.act_window``)
==========================================

最常见的操作类型，用于通过 :doc:`视图 <../user_interface/view_records>` 显示模型：窗口操作定义了一组视图类型（以及可能的特定视图）用于模型（以及可能的特定记录）。

其字段包括：

``res_model``
    显示视图的模型。
``views``
    ``(view_id, view_type)`` 对的列表。每对的第二个元素是视图的类别（树、表单、图表等），第一个是可选的数据库 id（或 ``False``）。如果未提供 id，客户端应获取请求模型的默认视图类型。列表中的第一个视图类型是默认视图类型，操作执行时将默认打开。每种视图类型在列表中最多只能出现一次。
``res_id`` (可选)
    如果默认视图是 ``form``，指定要加载的记录（否则应创建新记录）。
``search_view_id`` (可选)
    ``(id, name)`` 对，``id`` 是要为该操作加载的特定搜索视图的数据库标识符。默认为获取模型的默认搜索视图。
``target`` (可选)
    指定视图应在主内容区域（``current``）、全屏模式（``fullscreen``）或对话框/弹出窗口（``new``）中打开。使用 ``main`` 而不是 ``current`` 以清除面包屑。默认为 ``current``。
``context`` (可选)
    传递给视图的附加上下文数据。
``domain`` (可选)
    隐式添加到所有视图搜索查询的过滤域。
``limit`` (可选)
    默认情况下显示的列表记录数。Web 客户端默认值为 80。

例如，打开带有列表和表单视图的客户（设置了 ``customer`` 标志的合作伙伴）::

    {
        "type": "ir.actions.act_window",
        "res_model": "res.partner",
        "views": [[False, "tree"], [False, "form"]],
        "domain": [["customer", "=", true]],
    }

或者在新对话框中打开特定产品的表单视图（单独获取）::

    {
        "type": "ir.actions.act_window",
        "res_model": "product.product",
        "views": [[False, "form"]],
        "res_id": a_product_id,
        "target": "new",
    }

数据库中的窗口操作具有一些不同的字段，客户端应忽略这些字段，主要用于构建 ``views`` 列表：

``view_mode`` (默认= ``tree,form`` )
    逗号分隔的视图类型列表，作为字符串（/!\\ 没有空格 /!\\）。这些类型都将出现在生成的 ``views`` 列表中（至少有一个 ``False`` 的 view_id）。
``view_ids``
    M2M\ [#notquitem2m]_ 到视图对象，定义 ``views`` 的初始内容。

    .. note:: 可以通过 ``ir.actions.act_window.view`` 干净地定义 Act_window 视图。

        如果您计划为模型提供多个视图，建议使用 ir.actions.act_window.view 而不是操作中的 ``view_ids``。

        .. code-block:: xml

            <record model="ir.actions.act_window.view" id="test_action_tree">
               <field name="sequence" eval="1"/>
               <field name="view_mode">tree</field>
               <field name="view_id" ref="view_test_tree"/>
               <field name="act_window_id" ref="test_action"/>
            </record>

``view_id``
    如果其类型是 ``view_mode`` 列表的一部分且尚未填充，特定视图将添加到 ``views`` 列表中。

这些字段主要用于 :ref:`参考/数据` 中定义操作时：

.. code-block:: xml

    <record model="ir.actions.act_window" id="test_action">
        <field name="name">一个测试操作</field>
        <field name="res_model">some.model</field>
        <field name="view_mode">graph</field>
        <field name="view_id" ref="my_specific_view"/>
    </record>

将使用 "my_specific_view" 视图，即使它不是模型的默认视图。

服务器端 ``views`` 序列的组成如下：

* 从 ``view_ids`` 中获取每个 ``(id, type)``（按 ``sequence`` 排序）。
* 如果定义了 ``view_id`` 且其类型尚未填充，则追加其 ``(id, type)``。
* 对于 ``view_mode`` 中未填充的每种类型，追加 ``(False, type)``。

.. todo::

    * ``usage``?
    * ``groups_id``?
    * ``filter``?

.. [#notquitem2m] 技术上并不是 M2M：添加了序列字段，并且可能仅由视图类型组成，没有视图 id。

.. _reference/actions/url:

URL 操作 (``ir.actions.act_url``)
====================================

允许通过 Odoo 操作打开 URL（网站/网页）。可以通过两个字段自定义：

``url``
    激活操作时要打开的地址。
``target`` (默认= ``new``)
    可用的值为：

    * ``new``：在新窗口/页面中打开 URL。
    * ``self``：在当前窗口/页面中打开 URL（替换实际内容）。
    * ``download``：重定向到下载 URL。

    示例：

::

    {
        "type": "ir.actions.act_url",
        "url": "https://odoo.com",
        "target": "self",
    }

这将用 Odoo 首页替换当前内容部分。

.. _reference/actions/server:

服务器操作 (``ir.actions.server``)
======================================

.. autoclass:: odoo.addons.base.models.ir_actions.IrActionsServer

允许从任何有效的操作位置触发复杂的服务器代码。对于客户端，只有两个字段是相关的：

``id``
    要运行的服务器操作的数据库标识符。
``context`` (可选)
    运行服务器操作时要使用的上下文数据。

数据库中的记录丰富得多，可以根据其 ``state`` 执行多种特定或通用操作。一些字段（及其相应的行为）在状态之间共享：

``model_id``
    与操作相关的 Odoo 模型。

``state``

* ``code``: 执行通过 ``code`` 参数提供的 Python 代码。

* ``object_create``: 根据 ``fields_lines`` 规范创建模型 ``crud_model_id`` 的新记录。

* ``object_write``: 根据 ``fields_lines`` 规范更新当前记录。

* ``multi``: 执行通过 ``child_ids`` 参数提供的多个操作。

状态字段
------------

根据其状态，行为通过不同的字段定义。每个字段的相关状态将在后面标明。

``code`` (代码)
  指定操作被调用时要执行的一段 Python 代码。

  .. code-block:: xml

      <record model="ir.actions.server" id="print_instance">
          <field name="name">Res Partner 服务器操作</field>
          <field name="model_id" ref="model_res_partner"/>
          <field name="state">code</field>
          <field name="code">
              raise Warning(record.name)
          </field>
      </record>

  .. note::

      代码段可以定义一个名为 ``action`` 的变量，该变量将作为下一个要执行的操作返回给客户端：

      .. code-block:: xml

          <record model="ir.actions.server" id="print_instance">
              <field name="name">Res Partner 服务器操作</field>
              <field name="model_id" ref="model_res_partner"/>
              <field name="state">code</field>
              <field name="code">
                  if record.some_condition():
                      action = {
                          "type": "ir.actions.act_window",
                          "view_mode": "form",
                          "res_model": record._name,
                          "res_id": record.id,
                      }
              </field>
          </record>

      如果满足某些条件，将要求客户端打开记录的表单。

  ..  这往往是从 :ref:`数据文件 <reference/data>` 创建的唯一操作类型，其他类型比 Python 代码更容易从 UI 定义，但不适合从 :ref:`数据文件 <reference/data>` 定义。

``crud_model_id`` (创建)(必填)
    要创建新记录的模型。
``link_field_id`` (创建)
    many2one 到 ``ir.model.fields``，指定应在其上设置新创建记录的当前记录的 m2o 字段（模型应匹配）。

``fields_lines`` (创建/更新)
    创建或复制记录时要覆盖的字段。 :class:`~odoo.fields.One2many`，包含以下字段：

    ``col1``
        在相关模型中设置的 ``ir.model.fields``（对于创建为 ``crud_model_id``，对于更新为 ``model_id``）。
    ``value``
        字段的值，通过 ``type`` 解释。
    ``type`` (值|引用|表达式)
        如果是 ``value``，则 ``value`` 字段被解释为文字值（可能被转换）；如果是 ``equation``，则 ``value`` 字段被解释为 Python 表达式并进行求值。

``child_ids`` (多)
    指定在 ``multi`` 状态下执行的多个子操作（``ir.actions.server``）。如果子操作本身返回操作，则最后一个操作将作为 multi 自身的下一个操作返回给客户端。

.. _reference/actions/server/context:

评估上下文
------------------

服务器操作的评估上下文中提供了一些关键字或与其相关：

* ``model`` 与操作相关的模型对象，链接到 ``model_id``。
* ``record``/``records`` 触发操作的记录/记录集，可能为空。
* ``env`` Odoo 环境。
* ``datetime``、``dateutil``、``time``、``timezone`` 对应的 Python 模块。
* ``log: log(message, level='info')`` 用于记录调试信息到 ir.logging 表的日志记录函数。
* ``Warning`` 用于构造 ``Warning`` 异常。

.. _reference/actions/report:

报告操作 (``ir.actions.report``)
======================================

触发报告打印。

如果您通过 `<record>` 而不是 `<report>` 标签定义报告，并希望操作显示在模型视图的打印菜单中，还需要指定 :ref:`reference/bindings` 中的 ``binding_model_id``。不必设置 ``binding_type`` 为 ``report``，因为 ``ir.actions.report`` 将隐式默认为此。

``name`` (必填)
    用作文件名，如果未指定 ``print_report_name``。否则，名称仅用于在列表中查找报告时提供助记符/描述。
``model`` (必填)
    报告所涉及的模型。
``report_type`` (默认=qweb-pdf)
    可以是 ``qweb-pdf``（用于 PDF 报告）或 ``qweb-html``（用于 HTML）。
``report_name`` (必填)
    用于呈现报告的 qweb 模板的名称 (:term:`外部 id`)。
``print_report_name``
    定义报告名称的 Python 表达式。
``groups_id``
    :class:`~odoo.fields.Many2many` 字段，表示允许查看/使用当前报告的组。
``multi``
    如果设置为 ``True``，则操作不会显示在表单视图中。
``paperformat_id``
    :class:`~odoo.fields.Many2one` 字段，表示您希望用于此报告的纸张格式（如果未指定，则使用公司格式）。
``attachment_use``
    如果设置为 ``True``，则报告只会在第一次请求时生成一次，然后从存储的报告中重新打印，而不是每次都重新生成。

    可用于只能生成一次的报告（例如出于法律原因）。
``attachment``
    定义报告名称的 Python 表达式；记录作为变量 ``object`` 可访问。

.. _reference/actions/client:

客户端操作 (``ir.actions.client``)
======================================

触发完全在客户端实现的操作。

``tag``
    操作的客户端标识符，客户端应该知道如何响应的任意字符串。
``params`` (可选)
    发送给客户端的附加数据的 Python 字典，与客户端操作标记一起发送。
``target`` (可选)
    客户端操作应在主内容区域（``current``）、全屏模式（``fullscreen``）还是在对话框/弹出窗口中打开（``new``）。使用 ``main`` 代替 ``current`` 以清除面包屑。默认为 ``current``。

::

    {
        "type": "ir.actions.client",
        "tag": "pos.ui"
    }

告知客户端启动销售点界面，服务器并不知道销售点界面是如何工作的。

.. seealso::
   - :ref:`教程：客户端操作 <howtos/web/client_actions>`。

.. _reference/actions/cron:

自动化操作 (``ir.cron``)
===============================

按预定频率自动触发的操作。

``name``
    自动化操作的名称（主要用于日志显示）。

``interval_number``
    两次操作执行之间的 *interval_type* 单位数。

``interval_type``
    频率间隔的单位（``分钟``、``小时``、``天``、``周``、``月``）。

``numbercall``
    此操作需要运行的次数。如果操作预期无限期运行，请设置为 ``-1``。

``doall``
    布尔值，指示在服务器重启时是否必须执行错过的操作。

``model_id``
    将调用此操作的模型。

``code``
    操作的代码内容。
    可以是对模型方法的简单调用：

    .. code-block:: python

        model.<method_name>()

``nextcall``
    此操作的下一次计划执行日期（日期/时间格式）。
