=============
视图记录
=============

视图定义了如何向最终用户显示记录。它们在 XML 中指定，并作为记录本身存储，这意味着它们可以独立于所代表的模型进行编辑。视图具有灵活性，允许高度自定义其控制的屏幕。存在各种 :ref:`视图类型 <reference/view_records/types>`。每种视图代表一种可视化模式：*表单*、*列表*、*看板* 等。

.. _reference/view_records/structure:

通用结构
=================

基本视图通常共享以下定义的通用最小结构。占位符以大写字母表示。

.. code-block:: xml

    <record id="ADDON.MODEL_view_TYPE" model="ir.ui.view">
      <field name="name">名称</field>
      <field name="model">模型</field>
      <field name="arch" type="xml">
        <VIEW_TYPE>
          <views/>
        </VIEW_TYPE>
      </field>
    </record>

.. _reference/view_records/types:

视图类型
==========

:ref:`表单 <reference/view_architectures/form>`
  显示和编辑单个记录的数据。
:ref:`列表 <reference/view_architectures/list>`
  查看和编辑多个记录。
:ref:`搜索 <reference/view_architectures/search>`
  应用过滤器并执行搜索。结果显示在当前列表、看板等视图中。
:ref:`看板 <reference/view_architectures/kanban>`
  将记录显示为“卡片”，可配置为小模板。
:ref:`Qweb <reference/view_architectures/qweb>`
  报告、网站等的模板。
:ref:`图形 <reference/view_architectures/graph>`
  可视化多条记录或记录组的聚合。
:ref:`透视表 <reference/view_architectures/pivot>`
  将聚合显示为 `透视表 <https://en.wikipedia.org/wiki/Pivot_table>`_。
:ref:`日历 <reference/view_architectures/calendar>`
  将记录显示为日历中的事件，按日、周、月或年显示。
:ref:`队列 <reference/view_architectures/cohort>` |企业版|
  显示和理解某些数据随时间变化的方式。
:ref:`甘特图 <reference/view_architectures/gantt>` |企业版|
  将记录显示为甘特图。
:ref:`网格 <reference/view_architectures/grid>` |企业版|
  以数字单元格的形式显示计算信息；几乎不可配置。
:ref:`地图 <reference/view_architectures/map>` |企业版|
  在地图上显示记录及其之间的路线。

.. |企业版| raw:: html

   <span class="badge" style="background-color:#714B67">企业特性</span>

.. _reference/view_records/fields:

字段
======

视图记录公开了许多字段。

.. autoclass:: odoo.addons.base.models.ir_ui_view.View()

   .. attribute:: name

      仅在寻找某种列表中的视图时作为记忆术/描述有用。
      大多数 Odoo 视图名称以插件的名称开头，并以讨论的视图类型结尾。

      :requirement: 可选
      :type: :class:`~odoo.fields.Char`

   .. attribute:: model

      与视图相关联的模型（如果适用）。

      :requirement: 强制
      :type: :class:`~odoo.fields.Char`

   .. attribute:: arch

      视图布局的描述，取决于 :doc:`视图类型 <view_architectures>`。

      :requirement: 可选
      :type: :class:`~odoo.fields.Text`

   .. attribute:: groups_id

      允许使用/访问当前视图的用户组。

      如果视图扩展了现有视图，则该扩展将仅对有权访问提供的 `groups_id` 的用户应用。

      :requirement: 可选
      :type: :class:`~odoo.fields.Many2many` -> :class:`~odoo.addons.base.models.res_users.Groups`

   .. attribute:: priority

      当通过指定 `model` 和 `type` 请求视图时，将返回优先级最低的匹配视图（即默认视图）。

      它还定义了在 :ref:`视图解析
      <reference/view_records/inheritance/resolution>` 期间视图应用的顺序。当通过 `id` 请求视图且其
      模式不是 `primary` 时，将匹配其 *最接近* 的父视图，该父视图的 `mode` 为 `primary`。

      :requirement: 可选
      :type: :class:`~odoo.fields.Integer`

   .. attribute:: inherit_id

      指向将应用 :ref:`继承
      <reference/view_records/inheritance>` 的父视图的引用。默认值为其值。使用 `ref` 属性指定父项：
      :code:`ref="ADDON.MODEL_parent_view_TYPE"`。

      如果继承的是同一模块的记录，则插件名称（点前的部分）不是必需的。

      有关更多信息，请参见 :ref:`reference/view_records/inheritance`。

      :requirement: 可选
      :type: :class:`~odoo.fields.Many2one`

   .. attribute:: mode

      仅在此视图继承自其他视图时适用（`inherit_id` 被设置）。

      .. attribute:: extension

         如果请求视图，则查找最接近的主视图（通过 `inherit_id`）。然后，将所有继承自它并具有此视图模型的视图应用。

      .. attribute:: primary

         完全解析最接近的主视图（即使它使用的模型与当前模型不同）。然后，将视图的 :ref:`继承规格
         <reference/view_records/inheritance/specs>` 应用，结果将用作该视图的实际 arch。

      想要在使用 `inherit_id` 时覆盖 `mode` 的情况是委托继承。在这种情况下，派生模型与其父模型分离，并且匹配的视图不会相互匹配。假设某个视图继承自与父模型相关联的视图，并希望自定义派生视图以显示派生模型中的数据，则需要将派生视图的 `mode` 设置为 `primary`，因为这是该派生模型的基本（可能是唯一）视图。否则， :ref:`视图匹配
      <reference/view_records/inheritance/resolution>` 规则将不适用。

      有关更多信息，请参见 :ref:`reference/view_records/inheritance`。

      :requirement: 可选
      :type: :class:`~odoo.fields.Selection`: `extension` / `primary`
      :default: `extension`

.. note::
   当前上下文和用户访问权限也可能会影响视图的功能。

.. _reference/view_records/inheritance:

继承
===========

继承
=====

继承允许自定义交付的视图。例如，它使得在模块安装时可以添加内容，或者根据操作提供不同的显示。

继承视图通常共享以下定义的通用结构。占位符以大写字母表示。这个合成视图将更新通过 XPath 定向的节点，以及通过其名称和属性定向的另一个节点。

.. code-block:: xml

   <record id="ADDON.MODEL_view_TYPE" model="ir.ui.view">
       <field name="model">模型</field>
       <field name="inherit_id" ref="视图引用"/>
       <field name="mode">模式</field>
       <field name="arch" type="xml">
           <xpath expr="XPATH" position="POSITION">
               <CONTENT/>
           </xpath>
           <NODE ATTRIBUTES="VALUES" position="POSITION">
               <CONTENT/>
           </NODE>
       </field>
   </record>

`inherit_id` 和 `mode` 字段决定了 :ref:`视图解析
<reference/view_records/inheritance/resolution>`。`xpath` 或 `NODE` 元素指示 :ref:`继承规格 <reference/view_records/inheritance/specs>`。`expr` 和 `position` 属性指定 :ref:`继承位置 <reference/view_records/inheritance/position>`。

.. _reference/view_records/inheritance/resolution:

视图解析
---------------

解析生成请求/匹配的 `primary` 视图的最终 `arch`，步骤如下：

#. 如果视图有父视图，则完全解析父视图，然后应用当前视图的继承规格；
#. 如果视图没有父视图，则其 `arch` 直接使用；
#. 查找当前视图的 `extension` 模式的子视图，并按深度优先应用它们的继承规格（先应用子视图，然后是其子视图，再是其兄弟视图）。

继承根据 `inherit_id` 字段应用。如果多个视图记录继承同一视图，则顺序由 `priority` 确定。

应用子视图的结果生成最终的 `arch`。

.. todo:: 关于 fields_view_get 的说明以及链接到 ORM ?

.. _reference/view_records/inheritance/specs:

继承规格
-----------------

继承规格是顺序应用的，包括：

#. 元素定位器，用于匹配父视图中的继承元素；
#. 子元素，用于修改继承的元素。

有三种类型的元素定位器：

- 带有 `expr` 属性的 `xpath` 元素。`expr` 是应用于当前 `arch` 的 `XPath <https://en.wikipedia.org/wiki/XPath>`_ 表达式\ [#hasclass]_，匹配找到的第一个节点；
- 带有 `name` 属性的 `field` 元素，匹配第一个同名字段。

  .. note::
     所有其他属性都将被忽略。

- 任何其他元素，匹配第一个同名且具有相同属性的元素。

  .. note::
     `position` 和 `version` 属性将被忽略。

.. [#hasclass] 为简化 QWeb 视图中的匹配，添加了扩展功能：
               `hasclass(*classes)` 匹配上下文节点是否具有所有指定的类。

.. example::
   .. code-block:: xml

      <xpath expr="page[@name='pg']/group[@name='gp']/field" position="inside">
          <field name="description"/>
      </xpath>

      <div name="name" position="replace">
          <field name="name2"/>
      </div>

.. _reference/view_records/inheritance/position:

继承位置
--------------------
继承规格
=========

继承规格接受一个可选的 `position` 属性，默认为 `inside`，用于指定如何修改匹配的节点。

.. attribute:: inside

   继承规格的内容将附加到匹配节点的后面。

   .. example::

      .. code-block:: xml

         <notebook position="inside">
             <page string="新功能">
                 ...
             </page>
         </notebook>

.. attribute:: after

   继承规格的内容将附加到匹配节点的父节点，位于匹配节点之后。

   .. example::

      .. code-block:: xml

         <xpath expr="//field[@name='x_field']" position="after">
             <field name="x_other_field"/>
         </xpath>

.. attribute:: before

   继承规格的内容将附加到匹配节点的父节点，位于匹配节点之前。

   .. example::

      .. code-block:: xml

         <field name="x_field" position="before">
             <field name="x_other_field"/>
         </field>

.. attribute:: replace

   继承规格的内容替换匹配节点。任何仅包含 `$0` 的文本节点将在规格内容中被匹配节点的副本替换，从而有效地包装匹配节点。

   .. example::

      .. code-block:: xml

         <xpath expr="//field[@name='x_field']" position="replace">
             <div class="wrapper">
                 $0
             </div>
         </xpath>

.. attribute:: attributes

   继承规格的内容应仅由 `attribute` 元素组成，每个元素都有一个 `name` 属性和一个可选的主体。

   - 如果 `attribute` 元素有主体，则在匹配节点中添加一个名为其 `name` 的新属性，值为 `attribute` 元素的文本。
   - 如果 `attribute` 元素没有主体，则从匹配节点中移除名为其 `name` 的属性。
   - 如果 `attribute` 元素有 `add` 属性、`remove` 属性或两者都有，则将匹配节点中名为 `name` 的属性的值重新计算，以考虑 `add` 和 `remove` 的值，以及一个可选的 `separator` 属性，默认为 `,`。`add` 包含其值，用 `separator` 分隔。`remove` 移除其值，用 `separator` 分隔。

   .. example::
      .. code-block:: xml

         <field name="x_field" position="attributes">
             <attribute name="invisible">True</attribute>
             <attribute name="class" add="mt-1 mb-1" remove="mt-2 mb-2" separator=" "/>
         </field>

.. attribute:: move

   在继承规格的内容上设置 `position="move"` 属性，以指定节点相对于继承规格的元素定位器如何移动，元素定位器上也必须设置 `position`，可用的值有 `inside`、`replace`、`after` 或 `before`。

   .. example::
      .. code-block:: xml

         <xpath expr="//@target" position="after">
             <xpath expr="//@node" position="move"/>
         </xpath>

         <field name="target_field" position="after">
             <field name="my_field" position="move"/>
         </field>

.. _reference/view_records/model_commons:

模型公共属性
=============

.. autoclass:: odoo.addons.base.models.ir_ui_view.View()
   :noindex:

   .. automethod:: Model.get_views
   .. automethod:: Model.get_view
