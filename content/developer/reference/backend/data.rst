.. _reference/data:

==========
数据文件
==========

Odoo 是高度数据驱动的，模块定义的很大一部分就是定义其管理的各种记录：UI（菜单和视图）、安全性（访问权限和记录规则）、报表和普通数据都通过记录定义。

结构
=========

在 Odoo 中定义数据的主要方式是通过 XML 数据文件：XML 数据文件的基本结构如下：

* 根元素 ``odoo`` 中的任意数量的操作元素

.. code-block:: xml

    <?xml version="1.0" encoding="UTF-8"?>
    <!-- 数据文件的根元素 -->
    <odoo>
        <operation/>
        ...
    </odoo>

数据文件按顺序执行，操作只能引用之前定义的操作结果。

.. note::

    如果预期数据文件的内容只应用一次，可以指定 Odoo 标志 ``noupdate`` 设置为 1。如果文件中的部分数据预期只应用一次，可以将该部分放入 <data noupdate="1"> 域中。

    .. code-block:: xml

        <odoo>
            <data noupdate="1">
                <!-- 仅在安装模块时加载（odoo-bin -i module） -->
                <operation/>
            </data>

            <!-- 在安装和更新时重新加载（odoo-bin -i/-u） -->
            <operation/>
        </odoo>

核心操作
===============

.. _reference/data/record:

``record`` 
----------

``record`` 适当地定义或更新数据库记录，它具有以下属性：

``model`` (必需)
    要创建（或更新）的模型名称。
``id`` 
    此记录的 :term:`外部标识符`。强烈建议提供一个。

    * 对于记录创建，允许后续定义修改或引用此记录。
    * 对于记录修改，指定要修改的记录。
``context`` 
    创建记录时使用的上下文。
``forcecreate`` 
    在更新模式下，如果记录不存在，是否应创建记录。

    需要一个 :term:`外部 id`，默认为  ``True`` 。

 ``field``  
---------

每个记录可以由  ``field``  标签组成，定义创建记录时要设置的值。没有  ``field``  的 ``record`` 将使用所有默认值（创建）或不执行任何操作（更新）。

 ``field``  具有一个必需的 ``name`` 属性，即要设置的字段名称，并且可以通过各种方法定义值：

无
    如果未为字段提供值，将隐式在字段上设置 ``False``。可以用于清除字段或避免使用字段的默认值。
``search`` 
    对于 :ref:`关系字段 <reference/fields/relational>`，应为该字段模型的 :ref:`域 <reference/orm/domains>`。

    将评估域，使用该域搜索字段的模型，并将搜索结果设置为字段的值。如果字段是 :class:`~odoo.fields.Many2one`，将仅使用第一个结果。
``ref`` 
    如果提供了 ``ref`` 属性，其值必须是有效的 :term:`外部 id`，它将被查找并设置为字段的值。

    主要用于 :class:`~odoo.fields.Many2one` 和 :class:`~odoo.fields.Reference` 字段。
``type`` 
    如果提供了 ``type`` 属性，则用于解释和转换字段的内容。字段的内容可以通过 ``file`` 属性提供，或通过节点的正文提供。

    可用的类型包括：

    ``xml``, ``html`` 
        提取  ``field``  的子元素作为单个文档，评估任何通过 ``%(external_id)s`` 指定的 :term:`外部 id`。可以使用 ``%%`` 输出实际的 *%* 符号。
    ``file``
        确保字段内容是当前模型中的有效文件路径，将 ``{module},{path}`` 对作为字段值保存。
    ``char``
        直接将字段内容设置为字段值，不进行任何修改。
    ``base64``
        对字段内容进行 base64 编码，与 ``file`` 属性结合使用时非常有用，例如将图像数据加载到附件中。
    ``int``
        将字段内容转换为整数并设置为字段值。
    ``float``
        将字段内容转换为浮点数并设置为字段值。
    ``list``, ``tuple`` 
        应包含任意数量的 ``value`` 元素，这些元素具有与  ``field``  相同的属性，每个元素解析为生成的元组或列表中的一个项目，生成的集合将设置为字段的值。
``eval`` 
    对于不适合上述方法的情况，``eval`` 属性直接评估其提供的任何 Python 表达式，并将结果设置为字段的值。

    评估上下文包含各种模块（ ``time`` 、 ``datetime`` 、``timedelta`` 、 ``relativedelta`` ），用于解析 :term:`外部标识符` 的函数（ ``ref`` ）以及当前字段的模型对象（如果适用）（ ``obj`` ）。

``delete`` 
----------

``delete`` 标签可以删除先前定义的任意数量的记录。它具有以下属性：

``model`` (必需)
    要删除记录的模型。
``id`` 
    要删除的记录的 :term:`外部 id`。
``search`` 
    一个 :ref:`域 <reference/orm/domains>`，用于查找要删除的模型记录。

``id`` 和 ``search`` 是互斥的。

``function`` 
------------

``function`` 标签调用模型上的方法，并提供相应的参数。它有两个必需的参数 ``model`` 和 ``name`` ，分别指定模型和要调用的方法名称。

参数可以通过 ``eval`` 提供（应评估为调用方法的参数序列）或通过 ``value`` 元素提供（参见 ``list`` 值）。

.. code-block:: xml

    <odoo>
        <data noupdate="1">
            <record id="partner_1" model="res.partner">
                <field name="name">Odude</field>
            </record>

            <function model="res.partner" name="send_inscription_notice"
                eval="[[ref('partner_1'), ref('partner_2')]]"/>

            <function model="res.users" name="send_vip_inscription_notice">
                <function eval="[[('vip','=',True)]]" model="res.partner" name="search"/>
            </function>
        </data>

        <record id="model_form_view" model="ir.ui.view">
            ...
        </record>
    </odoo>

.. ignored assert

.. _reference/data/shortcuts:

快捷方式
=========

由于 Odoo 的某些重要结构模型比较复杂，数据文件提供了使用 :ref:`记录标签 <reference/data/record>` 定义它们的简化方式：

``menuitem`` 
------------

定义一个带有多个默认值和回退的 ``ir.ui.menu`` 记录：

``parent`` 
    * 如果设置了 ``parent`` 属性，它应该是其他菜单项的 :term:`外部 id`，用于作为新菜单项的父级。
    * 如果未提供 ``parent``，则尝试将 ``name`` 属性解释为以 ``/`` 分隔的菜单名称序列，并在菜单层次结构中找到位置。在此解释中，会自动创建中间菜单。
    * 否则，菜单被定义为“顶级”菜单项（*不是*没有父级的菜单）。
``name`` 
    如果未指定 ``name`` 属性，尝试从关联的操作中获取菜单名称（如果有）。否则使用记录的 ``id``。
``groups`` 
    ``groups`` 属性被解释为 ``res.groups`` 模型的 :term:`外部标识符` 的逗号分隔序列。如果 :term:`外部标识符` 前缀带有减号（``-``），则将该组从菜单的组中*移除*。
``action`` 
    如果指定，``action`` 属性应该是打开菜单时要执行的操作的 :term:`外部 id`。
``id`` 
    菜单项的 :term:`外部 id`。

.. _reference/data/template:

``template`` 
------------

创建一个 :ref:`QWeb 视图 <reference/view_architectures/qweb>`，只需要视图的 ``arch`` 部分，并允许一些*可选*属性：

``id`` 
    视图的 :term:`外部标识符`。
``name`` , ``inherit_id`` , ``priority`` 
    与 ``ir.ui.view`` 上的相应字段相同（注意：``inherit_id`` 应为 :term:`外部标识符`）。
``primary`` 
    如果设置为  ``True``  并与 ``inherit_id`` 结合使用，将视图定义为主视图。
``groups`` 
    组的 :term:`外部标识符` 的逗号分隔列表。
``page`` 
    如果设置为 ``"True"``，则模板为网站页面（可链接、可删除）。
``optional`` 
    ``enabled`` 或 ``disabled``，视图是否可以在网站界面中禁用及其默认状态。如果未设置，视图始终启用。

.. _reference/data/csvdatafiles:

CSV 数据文件
==============

XML 数据文件灵活且自描述，但在批量创建大量简单记录时非常冗长。

在这种情况下，数据文件也可以使用 csv_，这通常用于 :ref:`访问权限 <reference/security/acl>`：

* 文件名为 :file:`{model_name}.csv`。
* 第一行列出要写入的字段，特殊字段 ``id`` 用于 :term:`外部标识符`（用于创建或更新）。
* 之后的每一行都会创建一个新记录。

以下是定义国家/地区状态的数据文件 ``res.country.state.csv`` 的前几行：

.. literalinclude:: data/res.country.state.csv
    :language: text

以更可读的格式呈现：

.. csv-table::
    :file: data/res.country.state.csv
    :header-rows: 1
    :class: table-striped table-hover table-sm

对于每一行（记录）：

* 第一列是要创建或更新的记录的 :term:`外部 id`。
* 第二列是要链接的国家/地区对象的 :term:`外部 id`（必须预先定义国家/地区对象）。
* 第三列是 ``res.country.state`` 的 ``name`` 字段。
* 第四列是 ``res.country.state`` 的 ``code`` 字段。

.. _base64: https://tools.ietf.org/html/rfc3548.html#section-3
.. _csv: https://en.wikipedia.org/wiki/Comma-separated_values
