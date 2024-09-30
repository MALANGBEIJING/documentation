==================
定义模块数据
==================

.. important::
   本教程是 :doc:`server_framework_101` 教程的扩展。确保您已经完成了该教程，并使用您创建的 `estate` 模块作为本教程练习的基础。

数据类型
========

主数据
------

主数据通常是模块的技术或业务需求的一部分。换句话说，这类数据通常是模块正常运行所必需的。这些数据在安装模块时始终会被安装。

我们之前已经遇到过技术数据，因为我们已经定义了 :doc:`视图 <../reference/user_interface/view_records>` 和 :doc:`动作 <../reference/backend/actions>`。这些就是一种主数据。

除了技术数据外，还可以定义业务数据，例如国家、货币、计量单位，以及完整的国家本地化（法律报告、税收定义、会计科目表）等等……

演示数据
--------

除了主数据外，模块要正常工作所需的数据外，我们还希望有演示数据：

* 帮助销售代表快速演示。
* 为开发人员提供一组可用的数据来测试新功能，并查看这些新功能如何与他们自己可能未添加的数据结合使用。
* 测试数据是否正确加载，确保不会产生错误。
* 在创建新数据库时，快速设置大部分功能。

如果不明确表示不需要，启动服务器时会自动加载演示数据。这可以在数据库管理器中或通过命令行实现。

.. code-block:: console

  $ ./odoo-bin -h
  Usage: odoo-bin [options]

  Options:
  --version             显示程序的版本号并退出
  -h, --help            显示此帮助信息并退出

  常见选项:
    [...]
    --without-demo=WITHOUT_DEMO
                        禁用安装模块时加载演示数据
                        （以逗号分隔，使用 "all" 禁用所有模块的演示数据）。需要
                        -d 和 -i 参数。默认不禁用。
  [...]

  $ ./odoo-bin --addons-path=... -d db -i account --without-demo=all

数据声明
========

模块声明文件
------------

**参考**: 有关此主题的文档可以在 :ref:`模块声明文件 <reference/module/manifest>` 中找到。

数据可以通过 CSV 或 XML 格式声明。
每个包含数据的文件必须在模块声明文件中添加，才能被加载。

用于添加新数据的声明文件中的键为 ``data``（用于主数据）和 ``demo``（用于演示数据）。两者的值应为字符串列表，表示声明数据的文件的相对路径。

通常，演示数据放在 ``demo`` 文件夹中，视图和动作放在 ``views`` 文件夹中，安全相关数据放在 ``security`` 文件夹中，其他数据放在 ``data`` 文件夹中。

如果您的工作目录如下所示：

.. code-block:: bash

  estate
  ├── data
  │   └── master_data.xml
  ├── demo
  │   └── demo_data.xml
  ├── models
  │   ├── *.py
  │   └── __init__.py
  ├── security
  │   └── ir.model.access.csv
  ├── views
  │   └── estate_property_offer_views.xml
  ├── __init__.py
  └── __manifest__.py

您的模块声明文件应如下所示：

.. code-block:: python

  # -*- coding: utf-8 -*-

  {
      "name": "Real Estate",
      "depends": [
          ...
      ],
      "data": [
          "security/ir.model.access.csv",  # CSV 和 XML 文件在同一位置加载
          "views/estate_property_offer_views.xml",  # 视图也是数据
          "data/master_data.xml",  # 根据模型将数据分为多个文件
      ],
      "demo": [
          "demo/demo_data.xml",
      ],
      "application": True,
  }

CSV
---

**参考**: 有关此主题的文档可以在 :ref:`CSV 数据文件 <reference/data/csvdatafiles>` 中找到。

声明简单数据的最简单方法是使用 CSV 格式。然而，它的功能是有限的：对于长列表的简单模型使用 CSV，但对于其他情况，建议使用 XML。

.. code-block:: text

    id,field_a,field_b,related_id:id
    id1,valueA1,valueB1,module.relatedid
    id2,valueA2,valueB2,module.relatedid

.. 提示:: 您的 IDE 可能有语法高亮的扩展来显示 CSV 文件

  * `Atom <https://atom.io/packages/rainbow-csv>`__.
  * `PyCharm/IntelliJ <https://plugins.jetbrains.com/plugin/10037-csv-plugin>`__.
  * `Vim <https://github.com/mechatroner/rainbow_csv>`__.
  * `Visual Studio <https://marketplace.visualstudio.com/items?itemName=mechatroner.rainbow-csv>`__.

.. exercise:: 为 `estate` 模块添加一些标准的房产类型：住宅、商业、工业和土地。这些类型应始终安装。

XML
---

**参考**: 有关此主题的文档可以在 :ref:`数据文件 <reference/data>` 中找到。

当要创建的数据更加复杂时，使用 XML 会更有用，甚至是必要的。

.. code-block:: xml

    <odoo>
      <record id="id1" model="tutorial.example">
        <field name="field_a">valueA1</field>
        <field name="field_b">valueB1</field>
      </record>

      <record id="id2" model="tutorial.example">
        <field name="field_a">valueA2</field>
        <field name="field_b">valueB2</field>
      </record>
    </odoo>

.. exercise:: 为 `estate` 模块创建一些演示数据。

  ================== ==================== ======================
  字段               值                    值
  ================== ==================== ======================
  名称               大别墅                拖车房
  状态               新建                  已取消
  描述               一座漂亮的大别墅        拖车公园中的房子
  邮政编码           12345                 54321
  可用日期           2020-02-02            1970-01-01
  预期价格           1,600,000             100,000
  销售价格                                120,000
  卧室数             6                    1
  居住面积           100                  10
  立面数             4                    4
  车库               有                    无
  花园               有
  花园面积           100000
  花园朝向           南
  ================== ==================== ======================

数据扩展
~~~~~~~~

在核心培训中，我们在 :doc:`server_framework_101/12_inheritance` 章节中看到，我们可以继承（扩展）现有的视图。这是数据扩展的一个特殊情况：在模块中，任何数据都可以扩展。

当您在新模块中向现有模型添加新字段时，您可能希望在依赖的模块中填充这些字段的记录。这可以通过提供要扩展的记录的 `xml_id` 来完成。它不会替换记录，而是在这两条记录上设置 ``field_c`` 为给定的值。

.. code-block:: xml

    <odoo>
      <record id="id1" model="tutorial.example">
        <field name="field_c">valueC1</field>
      </record>

      <record id="id2" model="tutorial.example">
        <field name="field_c">valueC2</field>
      </record>
    </odoo>


``ref``
~~~~~~~

相关字段可以使用 ``ref`` 键进行设置。该键的值是您想要链接的记录的 ``xml_id``。请记住，``xml_id`` 由声明数据的模块名称、一个点和记录的 ``id`` 组成（如果是在声明该数据的模块中，直接使用 ``id`` 也可以）。

.. code-block:: xml

    <odoo>
      <record id="id1" model="tutorial.example">
        <field name="related_id" ref="module.relatedid"/>
      </record>
    </odoo>

.. exercise:: 为您创建的房产创建一些演示数据的报价。

  使用在 ``base`` 中定义的合作伙伴创建报价。

  ============== ========= ======= ========
  合作伙伴        房产        价格    有效期
  ============== ========= ======= ========
  Azure Interior 大别墅      10000   14
  Azure Interior 大别墅      1500000 14
  Deco Addict    大别墅      1500001 14
  ============== ========= ======= ========

.. exercise:: 确保您的两个演示房产的类型均设置为住宅。

``eval``
~~~~~~~~

要分配给字段的值不总是一个简单的字符串，您可能需要计算它。它还可以用于优化相关值的插入，或者由于约束，强制您批量添加相关值。参见::ref:`添加 X2many 字段 <tutorials/define_module_data/x2m>`。

.. code-block:: xml

    <odoo>
      <record id="id1" model="tutorial.example">
        <field name="year" eval="datetime.now().year+1"/>
      </record>
    </odoo>

.. exercise:: 您添加的报价的日期应始终相对于模块的安装日期。

``search``
~~~~~~~~~~

有时，您需要调用 ORM 执行 ``search`` 操作。这在 CSV 格式中是不可行的。

.. code-block:: xml

    <odoo>
      <record id="id1" model="account.move.line">
        <field name="account_id" search="[
          ('user_type_id', '=', ref('account.data_account_type_direct_costs')),
          ('company_id', '=', obj().env.company.id)]
        "/>
      </record>
    </odoo>

在此代码片段中，这是必需的，因为主数据取决于已安装的本地化设置。

``function``
~~~~~~~~~~~~

加载数据时，您可能还需要执行 Python 代码。

.. code-block:: xml

  <function model="tutorial.example" name="action_validate">
      <value eval="[ref('demo_invoice_1')]"/>
  </function>

.. exercise:: 使用 "接受报价" 按钮验证一个演示数据报价。拒绝其他报价。

.. _tutorials/define_module_data/x2m:

添加 X2many 字段
----------------

**参考**: 有关此主题的文档可以在 :class:`~odoo.fields.Command` 中找到。

如果您需要在 One2many 或 Many2many 字段中添加相关数据，可以使用 :class:`~odoo.fields.Command` 方法。

.. code-block:: xml

    <odoo>
      <record id="id1" model="tutorial.example">
        <field name="related_ids" eval="[
            Command.create({
                'name': '我的名字',
            }),
            Command.create({
                'name': '你的名字',
            }),
            Command.link(ref('model.xml_id')),
        ]"/>
      </record>
    </odoo>

.. exercise:: 创建一个新房产，但这次在与报价相关联的 One2many 字段中直接创建一些报价。

访问数据
========

.. warning:: 除了演示数据声明外，您不应在其他地方访问演示数据，即使是在测试中也不应。

有多种方式可以访问主数据或演示数据。

在 Python 代码中，您可以使用 ``env.ref(self, xml_id, raise_if_not_found=True)`` 方法。它返回与您指定的 ``xml_id`` 关联的记录集。

在 XML 中，您可以像这样使用 `ref` 键：

.. code-block:: xml

    <odoo>
      <record id="id1" model="tutorial.example">
        <field name="related_id" ref="module.relatedid"/>
      </record>
    </odoo>

它会调用 `ref` 方法，并将返回的记录的 id 存储在类型为 ``tutorial.example`` 的记录 ``id1`` 的 ``related_id`` 字段中。

在 CSV 中，列的标题必须以 ``:id`` 或 ``/id`` 作为后缀。

.. code-block:: text

  id,parent_id:id,name
  "child1","module.parent","名称1"
  "child2","module.parent","名称2"
  "child3","module.parent","名称3"

在 SQL 中更复杂，详见 :ref:`高级部分 <tutorials/define_module_data/xml_id>`。

.. warning:: 数据可以随时被用户删除。始终要编写防御性代码，将此考虑在内。

高级
====

.. _tutorials/define_module_data/xml_id:

什么是 XML id？
--------------

因为我们不希望在数据库的每个 SQL 表中都有一个 ``xml_id`` 列，所以我们需要一种机制来存储它。这是通过 ``ir.model.data`` 模型实现的。

该模型包含记录的名称（即 ``xml_id``）以及定义它的模块名称、定义它的模型和它的 id。

不更新
------

使用 ``noupdate`` 标志创建的记录在升级创建它的模块时不会更新，但如果尚不存在则会创建。

.. note:: ``odoo-bin -i module`` 将绕过此设置并始终加载数据。但通常不应在生产数据库上执行此操作。

.. code-block:: xml

    <odoo noupdate="1">
      <record id="id1" model="model">
        <field name="fieldA" eval="True"/>
      </record>
    </odoo>


使用 SQL 导入
-------------

在某些情况下，直接使用 SQL 导入是有意义的。然而，不推荐这样做，因为它绕过了 ORM 的所有功能、计算字段（包括元数据）和 Python 约束。

.. note:: 通常，使用原始 SQL 也会绕过 ACL，并增加注入风险。

  **参考**: :ref:`Odoo 中的安全性 <reference/security>`

* 在处理巨大的文件时，它可以帮助大幅加快导入时间 `<https://github.com/odoo/enterprise/blob/d46cceef8c594b9056d0115edb7169e207a5986f/product_unspsc/hooks.py#L19>`__。
* 对于更复杂的导入，比如 `翻译 <https://github.com/odoo/odoo/blob/e1f8d549895cd9c459e6350430f30d541d02838a/odoo/addons/base/models/ir_translation.py#L24>`__。
* 在初始化数据库时可能是必要的 `初始化数据库 <https://github.com/odoo/odoo/blob/e1f8d549895cd9c459e6350430f30d541d02838a/odoo/addons/base/data/base_data.sql>`__。
