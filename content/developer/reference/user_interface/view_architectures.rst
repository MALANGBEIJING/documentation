:custom-css: showcase_tables.css

==================
视图架构
==================

通用架构
====================

视图的架构由JavaScript框架解释的XML数据定义。

对于大多数视图，有一个 :file:`\*.rng` 文件定义属性和可能的架构。
某些视图则不受此类文件的约束，可能是因为它们接受HTML内容，或出于性能原因。

.. note::
   当前的上下文和用户访问权限可能会影响视图的能力。

.. seealso::
   :doc:`view_records`

.. _reference/view_architectures/python_expression:

Python 表达式
=================

在评估节点属性时，例如 `readonly` 修饰符，可以提供一个 **Python 表达式**，
该表达式将在可以访问以下变量的环境中执行：

- 当前视图中所有字段的名称，包含当前记录的值，`column_invisible` 在 :ref:`列表视图 <reference/view_architectures/list/field>` 中除外；
  关系字段以ID列表形式提供；
- 当前记录的ID；
- `parent`: 引用容器的记录；仅在 :ref:`关系字段 <studio/fields/relational-fields>` 的子视图中；
- `context (dict)`: 当前视图的上下文；
- `uid (int)`: 当前用户的ID；
- `today (str)`: 当前本地日期，格式为 `YYYY-MM-DD`；
- `now (str)`: 当前本地日期时间，格式为 `YYYY-MM-DD hh:mm:ss`。

.. example::
   .. code-block:: xml

      <field name="field_a" readonly="True"/>
      <field name="field_b" invisible="context.get('show_me') and field_a == 4"/>

.. example::
   .. code-block:: xml

      <field name="field_a"/>
      <field name="x2m">
          <!-- 子视图 -->
          <form>
              <field name="field_b" invisible="parent.field_a"/>
          </form>
      </field>

.. _reference/view_architectures/form:

表单
====

表单视图用于显示单个记录的数据。它们由常规HTML_和附加的语义和结构组件组成。

表单视图的根元素是 `form`。

.. code-block:: xml

   <form>
       ...
   </form>

.. _reference/view_architectures/form/root:

根属性
---------------

可选属性可以添加到根元素 `form` 以自定义视图。

.. include:: view_architectures/root_attribute_string.rst

.. include:: view_architectures/root_attribute_create.rst

.. include:: view_architectures/root_attribute_edit.rst

.. attribute:: duplicate
   :noindex:

   通过**操作**下拉菜单禁用/启用记录重复。

   :requirement: 可选
   :type: bool
   :default: `True`

.. include:: view_architectures/root_attribute_delete.rst

.. attribute:: js_class
   :noindex:

   Web客户端将实例化的JavaScript组件的名称，而不是表单视图。

   :requirement: 可选
   :type: str
   :default: `''`

.. attribute:: disable_autofocus
   :noindex:

   禁用自动聚焦于视图中的第一个字段。

   :requirement: 可选
   :type: bool
   :default: `False`

.. include:: view_architectures/root_attribute_banner_route.rst

.. _reference/view_architectures/form/semantic:

语义组件
-------------------

语义组件与Odoo系统相结合，并允许与之交互。

表单视图接受以下子语义组件：:ref:`field <reference/view_architectures/form/field>`，:ref:`label <reference/view_architectures/form/label>`，
:ref:`button <reference/view_architectures/form/button>`，:ref:`reference/view_architectures/form/chatter`和
:ref:`reference/view_architectures/form/attachment`。

占位符以大写字母表示。

.. _reference/view_architectures/form/field:

`field`: 显示字段值
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

`field` 元素呈现（并可能允许编辑）当前记录的单个字段。

在表单视图中多次使用相同字段是支持的，字段可以接收不同的 `invisible` 和 `readonly` 属性值。
这些字段的值可能相同，但可以以不同的方式显示。然而，当多个字段对 `required` 属性存在不同值时，行为并不保证。

.. code-block:: xml

   <form>
       <field name="FIELD_NAME"/>
   </form>

`field` 元素可以具有以下属性：

.. include:: view_architectures/field_attribute_name.rst

.. attribute:: id
   :noindex:

   节点ID。用于在视图中存在多个相同字段时（见 :ref:`reference/view_architectures/form/label`）。

   :requirement: 可选
   :type: str
   :default: 字段名称

.. include:: view_architectures/field_attribute_string.rst

.. attribute:: help
   :noindex:

   当悬停在字段或其标签上时显示的工具提示。

   :requirement: 可选
   :type: str
   :default: `''`

.. include:: view_architectures/field_attribute_widget.rst

.. attribute:: options
   :noindex:

   字段小部件（包括默认小部件）的配置选项，作为Python表达式评估为一个字典。

   对于关系字段，以下选项可用： `no_create`， `no_quick_create`， `no_open` 和 `no_create_edit`。

   .. example::
      .. code-block:: xml

         <field name="tag_ids" widget="many2many_tags" options="{'color_field': 'FIELD_NAME', 'no_quick_create': True}"/>

   :requirement: 可选
   :type: :ref:`Python expression <reference/view_architectures/python_expression>`
   :default: `{}`

.. include:: view_architectures/field_attribute_readonly.rst

.. include:: view_architectures/field_attribute_required.rst

.. include:: view_architectures/generic_attribute_invisible.rst

.. include:: view_architectures/generic_attribute_groups.rst

.. attribute:: domain
   :noindex:

   显示现有记录供选择时应用的过滤器，作为Python表达式评估为 :ref:`domain <reference/orm/domains>`。

   .. example::
      .. code-block:: xml

         <field name="fname" domain="[('fname_a', '=', parent.fname_b)]"/>

   :requirement: 可选
   :type: :ref:`Python expression <reference/view_architectures/python_expression>`
   :default: `[]`
   :scope: 关系字段

.. attribute:: context
   :noindex:

   .. todo:: 关于所有魔法上下文值的详细文档（TYPE_view_ref，group_by，
             search_default_FIELD...

   用于获取可能值和创建或搜索记录的上下文，作为Python表达式评估为一个字典。

   .. example::
      .. code-block:: xml

         <field name="fname" context="{
             'TYPE_view_ref': 'ADDON.MODEL_view_TYPE',
             'group_by': 'FIELD_NAME',
             'default_FIELD_NAME': ANY,
             'search_default_FIELD_NAME': True,
             'OTHER_BUSINESS_KEY': ANY,
           }"/>

   :requirement: 可选
   :type: :ref:`Python expression <reference/view_architectures/python_expression>`
   :default: `{}`
   :scope: 关系字段

.. attribute:: nolabel
   :noindex:

   是否隐藏字段标签。

   :requirement: 可选
   :type: bool
   :default: `False`
   :scope: 直接为 `group` 元素的子字段

.. attribute:: placeholder
   :noindex:

   在 *空* 字段上显示的帮助消息。它可以替代复杂表单中的字段标签。
   但是，它 *不应* 是数据示例，因为用户可能会将占位符文本与已填充字段混淆。

   :requirement: 可选
   :type: str
   :default: `''`

.. attribute:: mode
   :noindex:

   用于字段链接记录的显示模式（视图类型）的以逗号分隔的列表。允许的模式有： `tree`， `form`， `kanban` 和 `graph`。

   :requirement: 可选
   :type: str
   :default: `tree`
   :scope: :class:`~odoo.fields.One2many` 和 :class:`~odoo.fields.Many2many` 字段

.. include:: view_architectures/generic_attribute_class.rst

.. attribute:: filename
   :noindex:

   提供文件名称的相关字段的名称。

   :requirement: 可选
   :type: str
   :default: `''`
   :scope: :class:`~odoo.fields.Binary` 字段

.. attribute:: password
   :noindex:

   字段是否存储密码，因此其数据不应显示。

   :requirement: 可选
   :type: bool
   :default: `False`
   :scope: :class:`~odoo.fields.Char` 字段

.. attribute:: kanban_view_ref
   :noindex:

   应在移动环境中选择记录时使用的特定看板 :doc:`视图记录 <view_records>` 的XMLID。

   :requirement: 可选
   :type: str
   :default: `''`
   :scope: 关系字段

.. attribute:: default_focus
   :noindex:

   当视图打开时，字段是否获得焦点。它只能应用于视图中的一个字段。

   :requirement: 可选
   :type: bool
   :default: `False`

.. note::
   :ref:`关系字段 <studio/fields/relational-fields>` 节点可以包含特定的子视图。

   .. example::
      .. code-block:: xml

         <field name="children_ids">
            <tree>
               <field name="name"/>
            </tree>
            <form>
               <field name="id"/>
               <field name="name"/>
            </form>
         </field>

.. _reference/view_architectures/form/label:

`label`: 显示字段标签
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
When a :ref:`field <reference/view_architectures/form/field>` 组件不直接放置在 :ref:`group <reference/view_architectures/form/group>` 中时，或者当其 `nolabel` 属性被设置时，字段的标签不会自动显示在其值旁边。`label` 组件是手动显示字段标签的替代方式。

.. code-block:: xml

    <form>
        <div class="col col-md-auto">
            <label for="FIELD_NAME" string="LABEL"/>
            <div>
                <field name="FIELD_NAME" class="oe_inline"/>
            </div>
        </div>
    </form>

`label` 元素可以具有以下属性：

.. attribute:: for
   :noindex:

   与标签相关联的字段的引用。可以是字段的名称，或其ID（在 :ref:`field <reference/view_architectures/form/field>` 上设置的 `id` 属性）。

   当视图中有多个相同字段的出现时，且有多个与这些字段节点关联的 `label` 组件时，这些标签必须具有唯一的 `for` 属性；在这种情况下，引用相应字段节点的 `id` 属性。

   :requirement: 强制
   :type: str

.. attribute:: string
   :noindex:

   要显示的标签。

   :requirement: 可选
   :type: str
   :default: 来自模型中字段定义的字段标签

.. include:: view_architectures/generic_attribute_class.rst

.. include:: view_architectures/generic_attribute_invisible.rst

.. _reference/view_architectures/form/button:

`button`: 显示操作按钮
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: xml

   <form>
       <button type="object" name="ACTION" string="LABEL"/>
       <button type="object" name="ACTION" icon="FONT_AWESOME"/>
   </form>

`button` 元素可以具有以下属性：

.. include:: view_architectures/button_attribute_type.rst

.. include:: view_architectures/button_attribute_name.rst

.. include:: view_architectures/button_attribute_string.rst

.. include:: view_architectures/button_attribute_icon.rst

.. include:: view_architectures/button_attribute_help.rst

.. include:: view_architectures/button_attribute_context.rst

.. include:: view_architectures/generic_attribute_groups.rst

.. include:: view_architectures/generic_attribute_invisible.rst

.. include:: view_architectures/generic_attribute_class.rst

.. attribute:: special
   :noindex:

   对于在对话框中打开的表单视图，按钮的行为。可以具有两种不同的值：

   .. attribute:: save
      :noindex:

      保存记录并关闭对话框。

   .. attribute:: cancel
      :noindex:

      关闭对话框而不保存。

   .. example::
      .. code-block:: xml

         <button special="cancel" icon="fa-trash"/>

   :requirement: 可选
   :type: str
   :default: `''`

.. attribute:: confirm
   :noindex:

   在执行按钮操作之前显示的确认消息（并要求用户接受）。

   .. example::
      .. code-block:: xml

         <button name="action_destroye_gate" string="Send the goa'uld" type="object" confirm="Do you confirm the action?"/>

   :requirement: 可选
   :type: str
   :default: `''`

.. attribute:: data-hotkey
   :noindex:

   绑定到按钮的热键（`keyboard_shortcut`_，类似于 accesskey_）。当按下 `alt` 键与所选字符一起时，它会被启用，或者当 `shift+` 被添加到值时，与所选字符一起按下 `shift` 键。

   .. example::
      .. code-block:: xml

         <button type="object" name="action_confirm" string="Confirm" data-hotkey="c"/>
         <button type="object" name="action_tear" string="Tear the sheet" data-hotkey="shift+k"/>

   :requirement: 可选
   :type: str
   :default: `''`

.. _reference/view_architectures/form/chatter:

聊天小部件
~~~~~~~~~~~~~~

:ref:`聊天小部件 <reference/mixins/mail/chatter>` 是一种通信和日志工具，允许直接从记录（任务、订单、发票、事件、笔记等）中给同事和客户发送电子邮件。

它通过带有类 `oe_chatter` 的 `div` 元素添加，当模型继承 `mail.thread` 混合时。

.. example::
   .. code-block:: xml

      <form>
          <sheet>
              ...
          </sheet>
          <div class="oe_chatter">
              <field name="message_follower_ids"/>
              <field name="activity_ids"/>
              <field name="message_ids" options="OPTIONS"/>
          </div>
      </form>

.. _reference/view_architectures/form/attachment:

附件预览小部件
~~~~~~~~~~~~~~~~~~~~~~~~~~

附件预览小部件是通过带有类 `o_attachment_preview` 的 *空* `div` 元素添加的。

.. example::
   .. code-block:: xml

      <form>
          <sheet>
              ...
          </sheet>
          <div class="o_attachment_preview"/>
      </form>

.. _reference/view_architectures/form/structural:

结构组件
---------------------
结构组件提供结构或“视觉”特征，逻辑较少。它们用作表单视图中的元素或元素集合。

表单视图接受以下子结构组件：:ref:`group <reference/view_architectures/form/group>`、:ref:`sheet <reference/view_architectures/form/sheet>`、:ref:`notebook <reference/view_architectures/form/notebook>`、:ref:`newline <reference/view_architectures/form/newline>`、:ref:`separator <reference/view_architectures/form/separator>`、:ref:`header <reference/view_architectures/form/header>`、:ref:`footer <reference/view_architectures/form/footer>`、:ref:`reference/view_architectures/form/button_container` 和 :ref:`reference/view_architectures/form/title_container`。

占位符用大写字母表示。

.. _reference/view_architectures/form/group:

`group`: 定义列布局
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

`group` 元素用于在表单中定义列布局。默认情况下，组定义两个列，且组的直接子元素占用一个单列。

作为组直接子元素的 :ref:`field <reference/view_architectures/form/field>` 元素默认显示 `label`，并且标签和字段本身的 `colspan` 各为 `1`。

子元素水平排列（它们会尽量填充下一个列，然后再换行）。

.. code-block:: xml

   <form>
       <group>
           ...
       </group>
   </form>

`group` 元素可以具有以下属性：

.. attribute:: string
   :noindex:

   显示在组中的标题。

   :requirement: 可选
   :type: str
   :default: `''`

.. attribute:: col
   :noindex:

   `group` 中的列数。

   :requirement: 可选
   :type: int
   :default: `2`

.. attribute:: colspan
   :noindex:

   子元素占用的列数。

   :requirement: 可选
   :type: int
   :default: `1`

.. include:: view_architectures/generic_attribute_invisible.rst

.. admonition:: 可能的结构及其渲染表示

   .. list-table::
      :class: o-showcase-table

      * - .. image:: view_architectures/form_group.svg
             :align: center

      * - .. code-block:: xml

             <group>
                 <field name="a" string="custom"/>
                 <field name="b"/>
             </group>
             <group string="title 1">
                 <group string="title 2">
                     <field name="c"/>
                     <field name="d"/>
                 </group>
                 <group>
                     <field name="e"/>
                     <field name="f"/>
                     <field name="g"/>
                 </group>
             </group>
             <group col="12">
                 <group colspan="8">
                     <field name="h"/>
                 </group>
                 <group colspan="4">
                     <field name="i"/>
                 </group>
             </group>

.. _reference/view_architectures/form/sheet:

`sheet`: 使布局响应式
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

`sheet` 元素可以用作 :ref:`form <reference/view_architectures/form>` 根元素的直接子元素，以获得更窄且更响应式的表单布局（居中页面，边距...）。它通常包含 :ref:`group <reference/view_architectures/form/group>` 元素。

.. code-block:: xml

   <form>
       <sheet>
           ...
       </sheet>
   </form>

.. _reference/view_architectures/form/notebook:

`notebook` & `page`: 添加选项卡式部分
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
`notebook` 元素定义了一个选项卡式部分。每个选项卡通过 `page` 子元素定义。

`notebook` 元素不应放置在 `group` 元素内。

.. code-block:: xml

   <form>
       <notebook>
           <page string="LABEL">
               ...
           </page>
       </notebook>
   </form>

`page` 元素可以具有以下属性：

.. attribute:: string
   :noindex:

   选项卡的标题。

   :requirement: 可选
   :type: `str`
   :default: `''`

.. include:: view_architectures/generic_attribute_invisible.rst

.. admonition:: 可能的结构及其渲染表示

   .. list-table::
      :class: o-showcase-table

      * - .. image:: view_architectures/form_notebook.svg
             :align: center

      * - .. code-block:: xml

             <form>
                 <notebook>
                     <page string="Page1">
                         ...
                     </page>
                     <page string="Page2">
                         ...
                     </page>
                 </notebook>
             </form>

.. _reference/view_architectures/form/newline:

`newline`: 开始新的组行
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

`newline` 元素用于 :ref:`group <reference/view_architectures/form/group>` 元素内，以提前结束当前行并立即切换到新行，而无需填充之前的任何剩余列。

.. code-block:: xml

   <form>
       <group>
           ...
           <newline/>
           ...
       </group>
   </form>

.. admonition:: 可能的结构及其渲染表示

   .. list-table::
      :class: o-showcase-table

      * - .. image:: view_architectures/form_newline.svg
             :align: center

      * - .. code-block:: xml

             <form>
                 <group string="Title 1">
                     <group string="Title 1.1">...</group>
                     <newline/>
                     <group string="Title 1.2">...</group>
                     <group string="Title 1.3">...</group>
                 </group>
             </form>

.. _reference/view_architectures/form/separator:

`separator`: 添加水平间距
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

`separator` 元素在组内的元素之间添加垂直间距。

.. code-block:: xml

   <form>
       ...
       <separator/>
       ...
   </form>

`<separator>` 元素可以具有以下属性：

.. attribute:: string
   :noindex:

   作为部分标题的标题。

   :requirement: 可选
   :type: `str`
   :default: `''`

.. admonition:: 可能的结构及其渲染表示

   .. list-table::
      :class: o-showcase-table

      * - .. image:: view_architectures/form_separator.svg
             :align: center

      * - .. code-block:: xml

             <form>
                 <group>
                     <FIELD/>
                     <separator string="Title 1"/>
                     <FIELD/>
                     <group>
                         <FIELD/>
                         <separator string="Title 2"/>
                         <FIELD/>
                     </group>
                     <group>
                         <FIELD/>
                         <FIELD/>
                     </group>
                 </group>
             </form>

.. tip::
   `separator` 元素可用于在同一内部 `group` 元素内的元素之间实现视觉分隔，同时保持它们水平对齐。

.. _reference/view_architectures/form/header:

`header`: 显示工作流按钮和状态
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
`header` 元素与 :ref:`sheet <reference/view_architectures/form/sheet>` 元素结合提供了一个宽度为整个页面的区域，通常用于显示工作流 :ref:`button <reference/view_architectures/form/button>` 元素和一个作为状态小部件渲染的 :ref:`field <reference/view_architectures/form/field>` 元素。

.. code-block:: xml

   <form>
       <header>
           <BUTTONS/>
       </header>
       <sheet>
           ...
       </sheet>
   </form>

.. example::

   .. code-block:: xml

      <header>
          <button string="Reset" type="object" name="set_draft" invisible="state != 'done'"/>
          <field name="state" widget="statusbar" statusbar_visible="draft,posted" options="{'clickable': 1}"/>
      </header>

.. _reference/view_architectures/form/footer:

`footer`: 显示对话框按钮
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

`footer` 元素用于在对话框的末尾显示 :ref:`buttons <reference/view_architectures/form/button>` 元素。

.. code-block:: xml

   <form>
       <sheet>
           ...
       </sheet>
       <footer>
           <BUTTONS/>
       </footer>
   </form>

.. example::
   .. code-block:: xml

      <footer>
          <button string="Save" special="save"/>
          <button string="Feature action" type="object" name="my_action" class="btn-primary"/>
          <button string="Discard" special="cancel"/>
      </footer>

.. _reference/view_architectures/form/button_container:

按钮容器
~~~~~~~~~~~~~~~~~

可以通过具有 `button_box` 类的 `div` 元素创建 :ref:`button <reference/view_architectures/form/button>` 元素的容器。

.. code-block:: xml

   <form>
       <div name="button_box">
           <BUTTONS/>
       </div>
   <form>

.. admonition:: 可能的结构及其渲染表示

   .. list-table::
      :class: o-showcase-table

      * - .. image:: view_architectures/form_button_box.svg
             :align: center

      * - .. code-block:: xml

             <form>
                 <div name="button_box">
                     <button type="edit" name="edit" icon="fa-edit" string="Button1"/>
                     <button type="object" name="my_action" icon="fa-dollar">
                         <field name="total_inv" widget="statinfo" string="Invoices"/>
                     </button>
                 </div>
             </form>

.. _reference/view_architectures/form/title_container:

标题容器
~~~~~~~~~~~~~~~

可以通过具有 `oe_title` 类的 `div` 元素创建标题 :ref:`field <reference/view_architectures/form/field>` 元素的容器。

.. code-block:: xml

   <form>
       <sheet>
           <div class="oe_title">
               <h1><FIELD/></h1>
           </div>
       </sheet>
   <form>

.. _reference/view_architectures/settings:

设置
========
设置视图是对 :ref:`form <reference/view_architectures/form>` 视图的定制。它们用于在一个集中的地方显示设置。与普通表单视图不同，它们具有搜索栏和侧边栏。

.. example::

   .. code-block:: xml

      <app string="CRM" name="crm">
          <setting type="header" string="Foo">
              <field name="foo" title="Foo?."/>
              <button name="nameAction" type="object" string="Button"/>
          </setting>
          <block title="Title of group Bar">
              <setting help="this is bar" documentation="/applications/technical/web/settings/this_is_a_test.html">
                  <field name="bar"/>
              </setting>
              <setting string="This is Big BAR" company_specific="1">
                  <field name="bar"/>
              </setting>
          </block>
          <block title="Title of group Foo">
              <setting string="Personalize setting" help="this is full personalize setting">
                  <div>This is a different setting</div>
              </setting>
          </block>
      </app>

.. _reference/view_architectures/settings/components:

组件
----------

设置视图接受 :ref:`field <reference/view_architectures/form/field>`、:ref:`label <reference/view_architectures/form/label>` 和 :ref:`button <reference/view_architectures/form/button>` 元素，以及三个额外的子元素：:ref:`app <reference/view_architectures/settings/app>`、:ref:`block <reference/view_architectures/settings/block>` 和 :ref:`setting <reference/view_architectures/settings/setting>`。

占位符以大写字母表示。

.. _reference/view_architectures/settings/app:

`app`: 声明应用程序
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

`app` 元素用于在设置视图中声明应用程序。它在视图的侧边栏中创建一个带有应用程序徽标的条目。它还充当搜索时的分隔符。

.. code-block:: xml

   <form>
       <app string="NAME" name="TECHNICAL_NAME">
       ...
       </app>
   </form>

`app` 元素可以具有以下属性：

.. attribute:: string
   :noindex:

   应用程序的名称。

   :requirement: 强制
   :type: str

.. attribute:: name
   :noindex:

   应用程序的技术名称（模块名称）。

   :requirement: 强制
   :type: str

.. attribute:: logo
   :noindex:

   徽标的 `relative path`_。

   :requirement: 可选
   :type: path_
   :default: 根据 `name` 属性计算的路径： :file:`/{name}/static/description/icon.png`

.. todo: document attribute notApp

.. include:: view_architectures/generic_attribute_groups.rst

.. include:: view_architectures/generic_attribute_invisible.rst

.. _reference/view_architectures/settings/block:

`block`: 声明一组设置
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
`block`: 声明一组设置
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

`block` 元素用于声明一组设置。该组可以有一个标题和描述。

.. code-block:: xml

   <form>
       <app string="NAME" name="TECHNICAL_NAME">
           ...
           <block title="TITLE">
               ...
           </block>
           ...
       </app>
  </form>

`block` 元素可以具有以下属性：

.. attribute:: title
   :noindex:

   设置块的标题。可以根据其值进行搜索。

   :requirement: 可选
   :type: str
   :default: `''`

.. attribute:: help
   :noindex:

   设置块的描述。可以根据其值进行搜索。

   :requirement: 可选
   :type: str
   :default: `''`

.. include:: view_architectures/generic_attribute_groups.rst

.. include:: view_architectures/generic_attribute_invisible.rst

.. _reference/view_architectures/settings/setting:

`setting`: 声明设置
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

`setting` 元素用于声明设置本身。

设置中的第一个 :ref:`field <reference/view_architectures/form/field>` 元素用作主字段。如果它是布尔字段，则放置在左侧面板上，否则放置在右侧面板的顶部。如果未定义 `string` 属性，则该字段还用于创建设置标签。

`setting` 元素还可以包含其他元素（例如 HTML）。所有这些元素都会在右侧面板中呈现。

.. code-block:: xml

   <form>
       <app string="NAME" name="TECHNICAL_NAME">
           <block title="TITLE">
               ...
               <setting string="SETTING_NAME">
                   ...
                   <field name="FIELD_NAME"/>
                   ...
               </setting>
               ...
           </block>
       </app>
   </form>

`<setting>` 元素可以具有以下属性：

.. attribute:: type
   :noindex:

   默认情况下，设置在两个面板（左侧和右侧）上进行可视化分离，用于编辑给定的 :ref:`field <reference/view_architectures/form/field>`。通过定义 `type="header"`，将渲染一种特殊类型的设置。此设置用于修改其他设置的范围。例如，在网站应用程序中，此设置用于指示其他设置适用于哪个网站。标题设置在视觉上表现为屏幕顶部的横幅。

   :requirement: 可选
   :type: str
   :default: `''`

.. attribute:: string
   :noindex:

   用作设置标签的文本。

   :requirement: 可选
   :type: str
   :default: 第一个字段的标签

.. attribute:: title
   :noindex:

   用作工具提示的文本。

   :requirement: 可选
   :type: str
   :default: `''`

.. attribute:: help
   :noindex:

   设置的描述。此文本显示在设置标签下方（带有 `text-muted` 类）。

   :requirement: 可选
   :type: str
   :default: `''`

.. attribute:: company_dependent
   :noindex:

   设置是否特定于公司。如果设置，则在设置标签旁边显示一个图标。

   仅接受值 `'1'`。

   :requirement: 可选
   :type: str
   :default: `''`

.. attribute:: documentation
   :noindex:

   设置的 `path`_ 到文档。如果设置，则在设置标签旁边显示一个可点击的图标。路径可以是绝对路径或 `relative path`_。在后一种情况下，它是相对于 `https://www.odoo.com/documentation/<version>`。

   :requirement: 可选
   :type: `path_`
   :default: `''`

.. include:: view_architectures/generic_attribute_groups.rst

.. include:: view_architectures/generic_attribute_invisible.rst

.. _reference/view_architectures/list:

列表
====
根元素
========

列表视图的根元素是 `tree`\ [#treehistory]_。

.. admonition:: 可能的结构及其呈现

   .. list-table::
      :class: o-showcase-table

      * - .. image:: view_architectures/list.svg
             :align: center

      * - .. code-block:: xml

             <tree>
                 ...
             </tree>

.. _reference/view_architectures/list/root:

根属性
----------------

可选属性可以添加到根元素 `tree` 以自定义视图。

.. include:: view_architectures/root_attribute_string.rst

.. include:: view_architectures/root_attribute_create.rst

.. include:: view_architectures/root_attribute_edit.rst

.. include:: view_architectures/root_attribute_delete.rst

.. attribute:: import
   :noindex:

   禁用/启用从视图中的数据导入记录。

   :requirement: 可选
   :type: bool
   :default: `True`

.. attribute:: export_xlsx
   :noindex:

   禁用/启用从视图中的数据导出记录。

   :requirement: 可选
   :type: bool
   :default: `True`

.. attribute:: editable
   :noindex:

   使视图中的记录可以就地编辑，并允许从列表的一行创建新记录。它可以具有两个不同的值：

   .. attribute:: top
      :noindex:

      新记录从列表的顶部创建。

   .. attribute:: bottom
      :noindex:

      新记录从列表的底部创建。

   行内 :ref:`form <reference/view_architectures/form>` 视图的架构源自列表视图。因此，表单视图字段和按钮上有效的大多数属性也被列表视图接受，尽管如果列表视图不可编辑，这些属性可能没有任何意义。

   .. important::
      如果将 `edit` 属性设置为 `False`，则此行为被禁用。

   :requirement: 可选
   :type: str
   :default: `''`

.. attribute:: multi_edit
   :noindex:

   激活多编辑功能，允许一次为多个记录更新一个字段到相同的值。

   仅接受值 `'1'`。

   :requirement: 可选
   :type: str
   :default: `''`

.. attribute:: open_form_view
   :noindex:

   在每一行的末尾显示一个按钮，以在表单视图中打开记录。

   如果视图不可编辑，则没有效果。

   :requirement: 可选
   :type: bool
   :default: `False`

.. include:: view_architectures/root_attribute_default_group_by.rst

.. include:: view_architectures/root_attribute_default_order.rst

.. attribute:: decoration-<style>
   :noindex:

   应应用于匹配记录行的样式，作为 Python 表达式，该表达式计算为布尔值。

   `<style>` 必须替换为 `bf` (粗体), `it` (斜体), `info`, `warning`, `danger`,
   `muted`, `primary` 和 `success` 之一。

   .. example::
      .. code-block:: xml

         <tree decoration-danger="field_qty &gt; field_limit">
             ...
         </tree>

   :requirement: 可选
   :type: :ref:`Python expression <reference/view_architectures/python_expression>`
   :default: `False`

.. attribute:: limit
   :noindex:

   默认页面大小。必须是严格正数。

   :requirement: 可选
   :type: int
   :default: `80` 对于列表视图, `40` 对于表单视图中的 X2many 列表

.. attribute:: groups_limit
   :noindex:

   当列表视图分组时，页面上默认的组数。必须是严格正数。

   :requirement: 可选
   :type: int
   :default: `80` 对于列表视图, `40` 对于表单视图中的 X2many 列表

.. attribute:: expand
   :noindex:

   当列表视图分组时，是否默认打开第一层组。

   .. warning::
      根据组的数量，这可能很慢。

   :requirement: 可选
   :type: bool
   :default: `False`

.. include:: view_architectures/root_attribute_sample.rst

.. include:: view_architectures/root_attribute_banner_route.rst

.. _reference/view_architectures/list/components:

组件
----------
列表视图接受以下子元素： :ref:`field
<reference/view_architectures/list/field>`， :ref:`button
<reference/view_architectures/list/button>`， :ref:`groupby
<reference/view_architectures/list/groupby>`， :ref:`header
<reference/view_architectures/list/header>`， :ref:`control 和 create
<reference/view_architectures/list/control>`。

占位符用大写字母表示。

.. _reference/view_architectures/list/field:

`field`: 显示字段值
===========================

`field` 元素呈现（并可能允许编辑）当前所有记录的单个字段作为列。

在列表视图中多次使用同一字段是不支持的。

.. code-block:: xml

   <tree>
       <field name="FIELD_NAME"/>
   </tree>

`field` 元素可以具有以下属性：

.. include:: view_architectures/field_attribute_name.rst

.. include:: view_architectures/field_attribute_string.rst

.. attribute:: optional
   :noindex:

   使字段的可见性可选。字段的列可以通过视图标题上的按钮隐藏或显示。

   它可以具有两个不同的值：

   .. attribute:: show
      :noindex:

      字段默认显示。

   .. attribute:: hide
      :noindex:

      字段默认隐藏。

   .. example::
      .. code-block:: xml

         <field name="fname_a" optional="show"/>
         <field name="fname_b" optional="hide"/>

   :requirement: 可选
   :type: str

.. include:: view_architectures/field_attribute_readonly.rst

.. include:: view_architectures/field_attribute_required.rst

.. include:: view_architectures/generic_attribute_invisible.rst

.. include:: view_architectures/generic_attribute_column_invisible.rst

.. include:: view_architectures/generic_attribute_groups.rst

.. attribute:: decoration-<style>
   :noindex:

   应该应用于匹配记录字段的样式，作为 Python 表达式，该表达式计算为布尔值。

   `<style>` 必须替换为 `bf` (粗体)，`it` (斜体)，`info`，`warning`，`danger`，
   `muted`，`primary` 和 `success` 中的一个。

   .. example::
      .. code-block:: xml

         <field name="name" decoration-bf="1"/>
         <field name="quantity" decoration-info="state == 'draft'"/>

   :requirement: 可选
   :type: :ref:`Python expression <reference/view_architectures/python_expression>`
   :default: `False`

.. include:: view_architectures/field_attribute_widget.rst

.. attribute:: sum, avg
   :noindex:

   在列底部显示的聚合。聚合仅在当前显示的记录上计算。聚合操作必须与对应字段的 `group_operator` 匹配。

   .. example::
      .. code-block:: xml

         <field name="sent" sum="Total" />
         <field name="clicks_ratio" avg="Average"/>

   :requirement: 可选
   :type: str
   :default: `''`

.. attribute:: width
   :noindex:

   当列表中没有记录时，应用于字段列的宽度，作为绝对宽度（例如 `100px`）。

   .. important::
      当列表中有记录时，宽度由 Web 客户端设置。

   :requirement: 可选
   :type: str
   :default: `''`

.. attribute:: nolabel
   :noindex:

   字段的列标题是否应保持为空。如果设置，列将不可排序。

   仅接受值 `'1'`。

   :requirement: 可选
   :type: str
   :default: `''`

.. note::
   当列表视图分组时，数字字段会聚合并在每组中显示。此外，如果组中有太多记录，组行的右侧会出现分页器。因此，在列表视图可以分组的情况下，在最后一列中放置数字字段是不好的做法。然而，在表单视图中，X2many 字段不受到此限制，因为它们不能分组。

.. admonition:: 可能的结构及其呈现

   .. list-table::
      :class: o-showcase-table

      * - .. image:: view_architectures/list_field.svg
             :align: center

      * - .. code-block:: xml

             <tree>
                 <field name="name" string="My Custom Name"/>
                 <field name="amount" sum="Total"/>
                 <field name="company_id" invisible="1"/>
                 <field name="currency_id"/>
                 <field name="tax_id"/>
             </tree>

.. _reference/view_architectures/list/button:

`button`: 显示操作按钮
~~~~~~~~~~~~~~~~~~~~~~~~~~~~
.. code-block:: xml

   <tree>
       <button type="object" name="ACTION" string="LABEL"/>
       <button type="object" name="ACTION" icon="FONT_AWESOME"/>
   </tree>

`button` 元素可以具有以下属性：

.. include:: view_architectures/button_attribute_type.rst

.. include:: view_architectures/button_attribute_name.rst

.. include:: view_architectures/button_attribute_string.rst

.. include:: view_architectures/button_attribute_icon.rst

.. include:: view_architectures/button_attribute_help.rst

.. include:: view_architectures/button_attribute_context.rst

.. include:: view_architectures/generic_attribute_groups.rst

.. include:: view_architectures/generic_attribute_invisible.rst

.. include:: view_architectures/generic_attribute_column_invisible.rst

.. include:: view_architectures/generic_attribute_class.rst

.. admonition:: 可能的结构及其呈现

   .. list-table::
      :class: o-showcase-table

      * - .. image:: view_architectures/list_button.svg
             :align: center

      * - .. code-block:: xml

             <tree>
                 <field name="name"/>
                 <button type="edit" name="edit" icon="fa-edit" title="Edit"/>
                 <button type="object" name="my_method" string="Button1" column_invisible="context.get('hide_button')" invisible="amount &gt; 3"/>
                 <field name="amount"/>
                 <field name="currency_id"/>
                 <field name="tax_id"/>
             </tree>

.. _reference/view_architectures/list/groupby:

`groupby`: 定义分组标题
======================

`groupby` 元素用于定义在 :attr:`~odoo.fields.Many2one` 字段上分组记录时的分组标题，伴随 :ref:`button
<reference/view_architectures/list/button>` 元素。它还接受 :ref:`field
<reference/view_architectures/list/field>` 元素，这些字段可以用于修饰符。因此，这些额外字段归属于 Many2one 共同模型。这些额外字段以批量方式获取。

.. code-block:: xml

   <tree>
       ...
       <groupby name="FIELD_NAME">
           <BUTTONS/>
           <FIELDS/>
       </groupby>
   </tree>

`groupby` 元素可以具有以下属性：

.. attribute:: name
   :noindex:

   用作标题的 :attr:`~odoo.fields.Many2one` 字段的名称。

   可以定义一个特殊的 :ref:`button <reference/view_architectures/list/button>` 元素，`type="edit"`，以打开 Many2one 字段的表单视图。

   :requirement: 强制
   :type: str

.. admonition:: 可能的结构及其呈现

   .. list-table::
      :class: o-showcase-table

      * - .. image:: view_architectures/list_groupby.svg
             :align: center

      * - .. code-block:: xml

             <tree>
                 <field name="name"/>
                 <field name="amount"/>
                 <field name="currency"/>
                 <field name="tax_id"/>

                 <groupby name="partner_id">
                     <button type="edit" name="edit" icon="fa-edit" title="Edit"/>
                     <field name="email"/>
                     <button type="object" name="my_method" string="Button1" invisible="email == 'jhon@conor.com'"/>
                 </groupby>
             </tree>

.. note::
   `groupby` 元素中的字段仅用于获取和存储值，但从不显示。

.. _reference/view_architectures/list/header:

`header`: 显示工作流按钮
==========================
.. code-block:: xml

   <tree>
       <header>
           <BUTTONS/>
       </header>
       ...
   </tree>

`header` 元素接受以下子元素：

.. attribute:: button
   :noindex:

   `button` 元素允许在控制面板中定义按钮。它与 :ref:`list views 中的 button 元素 <reference/view_architectures/list/button>` 相同，但在 `header` 元素中放置时接受一个额外的属性：

   .. attribute:: display
      :noindex:

      使按钮始终可用，而无需选择记录。

      仅接受值 `always`。

      .. example::

         .. code-block:: xml

            <header>
                <button name="toDoAlways" type="object" string="Always displayed" display="always"/>
                <button name="toDoSelection" type="object" string="Displayed if selection"/>
            </header>

      :requirement: 可选
      :type: str
      :default: `''`

.. admonition:: 可能的结构及其呈现

   .. list-table::
      :class: o-showcase-table

      * - .. image:: view_architectures/list_header.svg
             :align: center

      * - .. code-block:: xml

             <tree>
                 <header>
                     <button type="object" name="to_draft" string="Button1" invisible="context.get('hide_button')"/>
                 </header>
                 <field name="name"/>
                 <field name="amount"/>
                 <field name="currency"/>
                 <field name="tax_id"/>
             </tree>

.. _reference/view_architectures/list/control:

`control` & `create`: 添加内联创建按钮
====================================

`control` 元素定义一个控制行，接受创建按钮。每个创建按钮通过 `create` 元素定义。

.. code-block:: xml

   <tree>
      <control>
          <create string="LABEL"/>
          <BUTTONS/>
       </control>
       ...
   </tree>

`control` 元素不接受任何属性。

`create` 元素可以具有以下属性：

.. attribute:: string
   :noindex:

   按钮的文本。

   :requirement: 强制
   :type: str

.. attribute:: context
   :noindex:

   在执行按钮调用时合并到视图上下文中的上下文，作为评估为字典的 Python 表达式。

   :requirement: 可选
   :type: :ref:`Python expression <reference/view_architectures/python_expression>`
   :default: `{}`

.. admonition:: 可能的结构及其呈现

   .. list-table::
      :class: o-showcase-table

      * - .. image:: view_architectures/list_control.svg
             :align: center

      * - .. code-block:: xml

             <tree>
                 <field name="name"/>
                 <field name="amount"/>
                 <field name="currency"/>
                 <field name="tax_id"/>
                 <control>
                     <create string="Add a item"/>
                     <create string="Add a section" context="{'default_type': 'section'}"/>
                     <create string="Add a note" context="{'default_type': 'note'}"/>
                 </control>
             </tree>

.. note::
   使用 `control` 元素只有在列表视图位于 :class:`~odoo.fields.One2many` 或 :class:`~odoo.fields.Many2many` 字段内时才有意义。如果定义了任何 `create` 元素，它将覆盖默认的 :guilabel:`add a line` 按钮。

.. [#treehistory] 由于历史原因，它起源于树型视图，后来重新用于更表格/列表型的显示。

.. _reference/view_architectures/search:

搜索
======
搜索视图与其他视图类型不同，因为它们不用于显示内容。尽管它们适用于特定模型，但它们用于过滤另一个视图的内容（通常是聚合视图；例如：:ref:`reference/view_architectures/list` 和 :ref:`reference/view_architectures/graph`）。

搜索视图的根元素是 `search`。

它不接受任何属性。

.. admonition:: 可能的结构及其呈现

   .. list-table::
      :class: o-showcase-table

      * - .. image:: view_architectures/search.svg
             :align: center

      * - .. code-block:: xml

             <search>
                 ...
             </search>

.. _reference/view_architectures/search/components:

组件
----

搜索视图接受以下子元素：:ref:`field <reference/view_architectures/search/field>`、:ref:`filter <reference/view_architectures/search/filter>`、:ref:`separator <reference/view_architectures/search/separator>`、:ref:`group <reference/view_architectures/search/group>` 和 :ref:`searchpanel <reference/view_architectures/search/searchpanel>`。

占位符以大写字母表示。

.. _reference/view_architectures/search/field:

`field`: 基于字段值进行过滤
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

`field` 元素定义域或上下文，使用用户提供的值。当生成搜索域时，字段域与其他域和过滤器使用 **AND** 运算符连接。

.. code-block:: xml

   <search>
       <field name="FIELD_NAME"/>
   </search>

`field` 元素可以具有以下属性：

.. attribute:: name
   :noindex:

   要过滤的字段名称。

   :requirement: 强制
   :type: str

.. include:: view_architectures/field_attribute_string.rst

.. attribute:: operator
   :noindex:

   默认情况下，字段生成形式为 :samp:`[(name, {operator}, value)]` 的域，其中 `name` 是字段的名称，`value` 是用户提供的值，可能被过滤或转换（例如，用户应提供选择字段值的 *label*，而不是值本身）。

   `operator` 属性允许覆盖默认运算符，这取决于字段的类型（例如，对于浮点字段为 `=`，但对于字符字段为 `ilike`，对于多对一字段为 `child_of`）。

   :requirement: 可选
   :type: str
   :default: `=`

.. attribute:: filter_domain
   :noindex:

   作为字段的搜索域使用的域，作为评估为 :ref:`domain <reference/orm/domains>` 的 Python 表达式。

   它可以使用 `self` 变量在自定义域中注入提供的值。与单独使用 `operator` 属性相比，它可以生成显著更灵活的域（例如，按多个字段进行搜索）。

   如果同时提供了 `operator` 和 `filter_domain` 属性，则 `filter_domain` 优先。

   :requirement: 可选
   :type: :ref:`Python expression <reference/view_architectures/python_expression>`
   :default: `[]`

.. attribute:: context
   :noindex:

   合并到搜索视图上下文中的上下文，作为评估为字典的 Python 表达式。

   它可以包含用户提供的值，这些值在 `self` 变量下可用。

   :requirement: 可选
   :type: :ref:`Python expression <reference/view_architectures/python_expression>`
   :default: `{}`

.. attribute:: domain
   :noindex:

   应用于允许自动补全的字段的补全结果的过滤器（例如，:class:`~odoo.fields.Many2one`）。

   :requirement: 可选
   :type: :ref:`Python expression <reference/view_architectures/python_expression>`
   :default: `[]`

.. include:: view_architectures/generic_attribute_groups.rst

.. include:: view_architectures/generic_attribute_invisible.rst

.. admonition:: 可能的结构及其呈现

   .. list-table::
      :class: o-showcase-table

      * - .. image:: view_architectures/search_field.svg
             :align: center

      * - .. code-block:: xml

             <search>
                 <field name="name" string="My Custom Name"/>
                 <field name="amount"/>
                 <field name="company_id" invisible="1"/>
                 <field name="currency_id"/>
                 <field name="ref" filter_domain="[('name', 'like', self)]"/>
             </search>

.. _reference/view_architectures/search/filter:

`filter`: 创建预定义过滤器
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

`filter` 元素用于创建可以在搜索视图中切换的预定义过滤器。它允许向搜索上下文添加数据 :dfn:`传递给数据视图进行搜索/过滤的上下文`，或向搜索过滤器附加新部分。

.. code-block:: xml

   <search>
       <filter string="LABEL" domain="DOMAIN"/>
   </search>

`filter` 元素可以具有以下属性：

.. attribute:: name
   :noindex:

   过滤器的技术名称。它可以用于 :ref:`enable it by default <reference/view_architectures/search/defaults>` 或作为 :ref:`inheritance hook <reference/view_records/inheritance>`。

   :requirement: 强制
   :type: str

.. attribute:: string
   :noindex:

   过滤器的标签。

   :requirement: 强制
   :type: str

.. attribute:: help
   :noindex:

   当鼠标悬停在过滤器上时显示的工具提示。

   :requirement: 可选
   :type: str
   :default: `''`

.. attribute:: domain
   :noindex:

   作为搜索域的一部分附加到操作域的域。

   :requirement: 可选
   :type: :ref:`Python expression <reference/view_architectures/python_expression>`
   :default: `[]`

.. attribute:: date
   :noindex:

   要过滤的 `date` 或 `datetime` 字段的名称。

   使用时，此属性会在 :guilabel:`Filters` 菜单中创建一组可用的过滤器，但不是动态的，因为它们的域在控制面板实例化时评估。

   .. example::
      .. code-block:: xml

         <filter string="Creation Date" name="filter_create_date" date="create_date"/>

   :requirement: 可选
   :type: str
   :default: `''`

.. attribute:: default_period
   :noindex:

   时间基于过滤器的默认周期（带有 `date` 属性）。它必须是以下之一，或以逗号分隔的列表：`today`、`this_week`、`this_month`、`last_month`、`antepenultimate_month`、`fourth_quarter`、`third_quarter`、`second_quarter`、`first_quarter`、`this_year`、`last_year` 或 `antepenultimate_year`。

   过滤器必须在视图初始化时激活的默认过滤器集中的过滤器。

   .. example::
      .. code-block:: xml

         <filter string="Creation Date" name="filter_create_date" date="create_date" default_period="this_year,last_year"/>

   :requirement: 可选
   :type: str
   :default: `this_month`
   :scope: 具有非空 `date` 属性的过滤器

.. include:: view_architectures/generic_attribute_invisible.rst

.. include:: view_architectures/generic_attribute_groups.rst

.. attribute:: context
   :noindex:

   合并到操作的域中的上下文，以生成搜索域。

   上下文键 `group_by` 设置为字段作为值可用于定义在 :guilabel:`Group By` 菜单中可用的组。当字段为 `date` 或 `datetime` 类型时，过滤器会在 :guilabel:`Group By` 菜单中生成一个子菜单，具有以下可用的间隔选项：:guilabel:`Year`、:guilabel:`Quarter`、:guilabel:`Month`、:guilabel:`Week` 和 :guilabel:`Day`。当过滤器在视图初始化时激活的默认过滤器集中时，记录按月分组为默认值。这可以通过使用语法 `date_field:interval` 来更改。

   .. example::
      .. code-block:: xml

         <filter string="Category" name="groupby_category" context="{'group_by': 'category_id'}"/>
         <filter string="Creation Date" name="groupby_create_date" context="{'group_by': 'create_date:week'}"/>

   .. note::
      对于在字段上分组的 `read_groups` 的结果可能会受到其 `group_expand` 属性的影响，允许在需要时显示空组。有关此类字段的更多信息，请参见 :class:`~odoo.fields.Field`。

   :requirement: 可选
   :type: :ref:`Python expression <reference/view_architectures/python_expression>`
   :default: `{}`

.. caution::
   一系列过滤器（没有非过滤器元素将它们分隔）被视为包含性组合：它们将使用 `OR` 组合，而不是通常的 `AND`。

   .. example::
      .. code-block:: xml

         <filter domain="[('state', '=', 'draft')]"/>
         <filter domain="[('state', '=', 'done')]"/>

      显示 `state` 字段为 `draft` 或 `done` 的记录。

   .. example::
      .. code-block:: xml

         <filter domain="[('state', '=', 'draft')]"/>
         <separator/>
         <filter domain="[('delay', '&lt;', 15)]"/>

      显示 `state` 字段为 `draft` **且** `delay` 字段小于 15 的记录。

.. admonition:: 可能的结构及其呈现

   .. list-table::
      :class: o-showcase-table

      * - .. image:: view_architectures/search_filter.svg
             :align: center

      * - .. code-block:: xml

             <search>
                 <filter string="My Custom Name" domain="[('name', 'ilike', 'AAA')]"/>
                 <filter string="My orders" domain="[('user_id', '=', uid)]"/>
                 <filter string="Category" context="{'group_by': 'category_id'}"/>
             </search>

.. _reference/view_architectures/search/separator:

`separator`: 分隔过滤器组
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

`separator` 元素用于在简单搜索视图中分隔 :ref:`filters <reference/view_architectures/search/filter>` 组。对于更复杂的搜索视图，建议使用 :ref:`group <reference/view_architectures/search/group>` 元素。

.. code-block:: xml

   <search>
       <FILTERS/>
       <separator/>
       <FILTERS/>
   </search>

`separator` 元素不接受任何属性。

.. _reference/view_architectures/search/group:

`group`: 分隔过滤器组
~~~~~~~~~~~~~~~~~~~~~~~

`group` 元素用于在拥挤的搜索视图中分隔 :ref:`filters <reference/view_architectures/search/filter>` 组。在简单的搜索视图中，可以用 :ref:`separator <reference/view_architectures/search/group>` 元素替代。

.. code-block:: xml

   <search>
       <group expand="0" string="LABEL">
           <FILTERS/>
       </group>
   </search>

`group` 元素不接受任何属性。

.. _reference/view_architectures/search/searchpanel:

`searchpanel`: 显示搜索面板
~~~~~~~~~~~~~~~~~~~~~~~~~~~

`searchpanel` 元素在多记录视图的左侧显示搜索面板。它允许根据给定字段快速过滤数据。

.. code-block:: xml

   <search>
       <searchpanel>
           <FIELDS/>
       </searchpanel>
   </search>

`searchpanel` 元素仅接受 `field` 子元素。

作为 `searchpanel` 元素的子元素使用的 `field` 元素可以具有以下属性：

.. attribute:: name
   :noindex:

   要过滤的字段名称。

   :requirement: 强制
   :type: str

.. include:: view_architectures/field_attribute_string.rst

.. attribute:: select
   :noindex:

   字段的行为和显示。它可以具有两个不同的值：

   .. attribute:: one
      :noindex:

      最多只能选择一个值。支持的字段类型为 `many2one` 和 `selection`。

   .. attribute:: multi
      :noindex:

      可以选择多个值。支持的字段类型为 `many2one`、`many2many` 和 `selection`。

   :requirement: 可选
   :type: str
   :default: `one`

.. include:: view_architectures/generic_attribute_groups.rst

.. attribute:: icon
   :noindex:

   字段的图标。

   :requirement: 可选
   :type: str
   :default: `''`

.. attribute:: color
   :noindex:

   字段的颜色。

   :requirement: 可选
   :type: str
   :default: `''`

当 `field` 元素的 `select=one` 属性设置时，可以具有以下附加属性：

.. attribute:: hierarchize
   :noindex:

   子类别是否应出现在其父类别下，或在同一层级。

   :requirement: 可选
   :type: bool
   :default: `True`
   :scope: :class:`~odoo.fields.Many2one` 字段

当 `field` 元素的 `select=multi` 属性设置时，可以具有以下附加属性：

.. attribute:: enable_counters
   :noindex:

   如果非零，是否计算并显示记录计数。

   .. tip::
      此属性存在以避免影响性能。解决性能问题的另一种方法是重写 `search_panel_select_range` 和 `search_panel_select_multi_range` 方法。

   :requirement: 可选
   :type: bool
   :default: `False`

.. attribute:: expand
   :noindex:

   是否应显示没有记录的类别和过滤器。

   :requirement: 可选
   :type: bool
   :default: `False`

.. attribute:: limit
   :noindex:

   要获取的字段的最大值。如果达到限制，则不显示任何值，并显示错误消息。如果设置为 0，则获取所有值。

   :requirement: 可选
   :type: int
   :default: `200`

.. attribute:: domain
   :noindex:

   记录必须满足的条件。

   .. example::
      .. code-block:: xml

         <searchpanel>
             <field name="department_id"/>
             <field name="manager_id" select="multi" domain="[('department_id', '=', department_id)]"/>
         </searchpanel>

   :requirement: 可选
   :type: :ref:`Python expression <reference/view_architectures/python_expression>`
   :default: `[]`

.. attribute:: groupby
   :noindex:

   应按其值分组的字段名称。

   :requirement: 可选
   :type: str
   :default: `''`
   :scope: :class:`~odoo.fields.Many2one` 和 :class:`~odoo.fields.Many2many` 字段

.. _reference/view_architectures/search/defaults:

搜索默认值
------------

搜索字段和过滤器可以通过操作的 `context` 使用 :samp:`search_default_{name}` 键进行配置。对于字段，值必须是要设置到字段的值。对于过滤器，必须是布尔值或数字。

.. example::
   对于字段 `foo` 和过滤器 `bar`，以下操作上下文将在 `acro` 上搜索 `foo` 并默认启用 `bar`：

   .. code-block:: python

      {
          'search_default_foo': 'acro',
          'search_default_bar': 1
      }

可以使用数字值（介于 1 和 99 之间）来定义默认 *groupby* 过滤器的顺序。

.. example::
   对于 `foo` 和 `bar`，两个 *groupby* 过滤器，以下操作上下文将首先启用 `bar`，然后启用 `foo`。

   .. code-block:: python

      {
          'search_default_foo': 2,
          'search_default_bar': 1
      }

.. _reference/view_architectures/kanban:

看板
======

看板视图用于 `kanban board <https://en.wikipedia.org/wiki/Kanban_board>`_ 可视化：它们将记录显示为“卡片”，介于 :ref:`list <reference/view_architectures/list>` 视图和非可编辑 :ref:`form <reference/view_architectures/form>` 视图之间。

记录可以按列分组以用于工作流可视化或操作（例如，任务或工作进度管理），或不分组（仅用于可视化记录）。

看板视图的根元素是 `kanban`。

.. admonition:: 可能的结构和表现形式

   .. list-table::
      :class: o-showcase-table

      * - .. image:: view_architectures/kanban.svg
             :align: center

      * - .. code-block:: xml

             <kanban>
                 ...
             </kanban>

.. note::
   看板视图最多加载和显示十列。之后的任何列都将关闭，但用户仍然可以打开它们。

.. _reference/view_architectures/kanban/root:

根属性
---------------

可以将可选属性添加到根元素 `kanban` 以自定义视图。

.. include:: view_architectures/root_attribute_string.rst

.. include:: view_architectures/root_attribute_create.rst

.. include:: view_architectures/root_attribute_edit.rst

.. include:: view_architectures/root_attribute_delete.rst

.. include:: view_architectures/root_attribute_default_group_by.rst

.. include:: view_architectures/root_attribute_default_order.rst

.. attribute:: class
   :noindex:

   向视图的根 HTML 元素添加 HTML 类。

   :requirement: 可选
   :type: str
   :default: `''`

.. attribute:: examples
   :noindex:

   `KanbanExamplesRegistry` 中的键，可以在分组看板视图中创建新列时浏览的示例。

   .. seealso::
      `在 utm 模块中使用 examples 属性
      <{GITHUB_PATH}/addons/utm/static/src/js/utm_campaign_kanban_examples.js>`_

   :requirement: 可选
   :type: str
   :default: `''`

.. attribute:: group_create
   :noindex:

   是否可见 :guilabel:`添加新列` 栏。

   :requirement: 可选
   :type: bool
   :default: `True`

.. attribute:: group_delete
   :noindex:

   是否可以通过齿轮菜单删除列。

   :requirement: 可选
   :type: bool
   :default: `True`

.. attribute:: group_edit
   :noindex:

   是否可以通过齿轮菜单编辑列。

   :requirement: 可选
   :type: bool
   :default: `True`

.. attribute:: groups_draggable
   :noindex:

   是否可以重新排序列。

   :requirement: 可选
   :type: bool
   :default: `True`

.. attribute:: records_draggable
   :noindex:

   是否可以在分组的看板视图中拖动记录。

   :requirement: 可选
   :type: bool
   :default: `True`

.. attribute:: archivable
   :noindex:

   是否可以在模型上定义 `active` 字段时存档和取消存档属于某列的记录。

   :requirement: 可选
   :type: bool
   :default: `True`

.. attribute:: quick_create
   :noindex:

   是否可以在不切换到表单视图的情况下创建记录。

   :requirement: 可选
   :type: bool
   :default: `True` 当看板视图按 many2one、selection、char 或 boolean 字段分组时，否则为 `False`

.. attribute:: quick_create_view
   :noindex:

   在使用快速创建记录时打开的 :ref:`form <reference/view_architectures/form>` 视图的引用。

   :requirement: 可选
   :type: str
   :default: `''`

.. attribute:: on_create
   :noindex:

   单击 :guilabel:`创建` 时要调用的自定义操作。

   如果设置为 `'quick_create'`，则使用记录的快速创建。如果禁用快速创建，则调用标准创建操作。

   :requirement: 可选
   :type: str
   :default: `''`

.. include:: view_architectures/root_attribute_sample.rst

.. include:: view_architectures/root_attribute_banner_route.rst

.. _reference/view_architectures/kanban/components:

组件
----------
看板视图接受以下子元素：:ref:`field <reference/view_architectures/kanban/field>`、:ref:`header <reference/view_architectures/kanban/header>`、:ref:`progressbar <reference/view_architectures/kanban/progressbar>` 和 :ref:`templates <reference/view_architectures/kanban/templates>`。

占位符以大写字母表示。

.. _reference/view_architectures/kanban/field:

`field`: 显示字段值
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

`field` 元素声明要在 :ref:`templates <reference/view_architectures/kanban/templates>` 中使用的字段。如果字段只是显示，则不需要预先声明。

.. code-block:: xml

   <kanban>
       <field name="FIELD_NAME"/>
       ...
   </kanban>

`field` 元素可以具有以下属性：

.. include:: view_architectures/field_attribute_name.rst

.. admonition:: 可能的结构和表现形式

   .. list-table::
      :class: o-showcase-table

      * - .. image:: view_architectures/kanban_field.svg
             :align: center

      * - .. code-block:: xml

             <kanban>
                 <templates>
                     <t t-name="kanban-box">
                         <div>
                             <field name="name"/>
                         </div>
                     </t>
                 </templates>
             </kanban>

.. _reference/view_architectures/kanban/header:

`header`: 在控制面板中显示按钮
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

`header` 元素用于在控制面板中插入自定义按钮。

.. code-block:: xml

   <kanban>
       <header>
           <BUTTONS/>
       </header>
       ...
   </kanban>

`header` 元素仅接受 `button` 子元素，与 :ref:`list views' button <reference/view_architectures/list/button>` 元素类似。

用作 `header` 元素子元素的 `button` 元素可以具有以下附加属性：

.. attribute:: display
   :noindex:

   按钮的显示模式。可以具有两种不同的值：

   .. attribute:: display
      :noindex:

      按钮仅在选择了一些记录时显示；它们的操作适用于所选记录。

   .. attribute:: always
      :noindex:

      按钮始终显示，即使没有记录被选择。

   .. important::
      由于目前还无法在看板视图中选择记录，因此仅提供 `always` 显示模式。

   .. example::
      .. code-block:: xml

         <header>
             <button name="toDoAlways" type="object" string="Always displayed" display="always"/>
             <button name="toDoSelection" type="object" string="Displayed if selection"/>
         </header>

   :requirement: 可选
   :type: str
   :default: `display`

.. _reference/view_architectures/kanban/progressbar:

`progressbar`: 在列上方显示进度条
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
`progressbar` 元素用于定义显示在看板列顶部的进度条。

.. code-block:: xml

   <kanban>
       <progressbar field="FIELD_NAME"/>
       ...
   </kanban>

`progressbar` 元素可以具有以下属性：

.. attribute:: field
   :noindex:

   进度条子组基于的字段名称。

   :requirement: 必需
   :type: str

.. attribute:: colors
   :noindex:

   将进度条字段值映射到颜色值 `muted`、`success`、`warning` 和 `danger`。

   :requirement: 必需
   :type: `JSON <https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON>`_

.. attribute:: sum_field
   :noindex:

   用于在进度条旁边显示的求和字段名称。如果未设置，则显示记录的总数。

   :requirement: 可选
   :type: str
   :default: `''`

.. admonition:: 可能的结构和表现形式

   .. list-table::
      :class: o-showcase-table

      * - .. image:: view_architectures/kanban_progressbar.svg
             :align: center

      * - .. code-block:: xml

             <kanban>
                 <progressbar field="activity_state"
                              colors="{'planned': 'success', 'today': 'warning', 'overdue': 'danger'}"
                              sum_field="expected_revenue"/>
                 <templates>
                     ...
                 </templates>
             </kanban>

.. _reference/view_architectures/kanban/templates:

`templates`: 定义卡片结构
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

`templates` 元素用于定义结构化看板卡片的 :ref:`QWeb 模板 <reference/qweb>`。

卡片结构定义可以拆分为多个模板以提高可读性，但必须定义至少一个根模板 `kanban-box`。

可以定义两个额外的模板：`kanban-menu` 和 `kanban-tooltip`。如果定义了 `kanban-menu` 模板，它将在卡片右上角的下拉菜单中渲染，用户可以通过点击垂直省略号（:guilabel:`⋮`）来切换。`kanban-tooltip` 模板在鼠标悬停看板卡片时渲染在工具提示中。

模板使用 :ref:`JavaScript QWeb <reference/qweb/javascript>` 编写。

.. code-block:: xml

   <kanban>
       ...
       <templates>
           <t t-name="kanban-box">
               <div>
                   <field name="name"/>
               </div>
           </t>
       </templates>
   </kanban>

在渲染上下文中可以使用以下变量：

.. attribute:: widget
   :noindex:

   当前的 :js:class:`KanbanRecord`。可以用来获取一些元信息。方法也可以直接在模板上下文中使用，无需通过 `widget` 访问。

   :type: str

.. attribute:: record
   :noindex:

   具有所有请求字段作为其属性的对象。每个字段都有两个属性：`value` 和 `raw_value`。前者根据当前用户参数格式化，而后者是 :meth:`~odoo.models.Model.read` 的原始值（`date` 和 `datetime` 字段除外，这些字段会根据用户区域设置格式化 <https://github.com/odoo/odoo/blob/a678bd4e/addons/web_kanban/static/src/js/kanban_record.js#L102>`_）。

   :type: str

.. attribute:: context
   :noindex:

   从打开看板视图的操作或将看板视图嵌入表单视图的 one2many 或 many2many 字段传播的当前上下文。

   :type: str

.. attribute:: read_only_mode
   :noindex:

   :type: str

.. attribute:: selection_mode
   :noindex:

   在移动环境中选择 many2one 或 many2many 字段时打开看板视图。

   :type: bool

虽然大多数看板模板是标准的 :ref:`QWeb 模板 <reference/qweb>`，但看板视图以特殊方式处理 `field`、`button` 和 `a` 元素：

- 默认情况下，字段用其格式化值替换，除非指定了 `widget` 属性，在这种情况下，其渲染和行为取决于相应的小部件。`widget` 属性可以具有不同的值，包括：

  .. attribute:: handle
     :noindex:

     允许在基于 `sequence`（或 `integer`）字段的情况下拖放记录以重新排序。

     .. todo:: 列出所有小部件并将属性定义移到 <field> 部分

- 带有 `type` 属性的按钮和链接执行不同于其标准 HTML 功能的操作。`type` 属性可以具有 `action` 和 `object` 等值，或者以下值：

  .. attribute:: open
     :noindex:

     点击该元素在只读模式下打开卡片的记录。

  .. attribute:: edit
     :noindex:

     点击该元素在可编辑模式下打开卡片的记录。

  .. attribute:: delete
     :noindex:

     点击该元素删除卡片的记录并移除卡片。

.. todo::
  - 看板特定的 CSS
  - 看板结构/小部件（小插图、详细信息等）

.. ALL VIEWS BELOW HAVE NOT YET BEEN CONVERTED TO THE NEW REFERENCE DOCUMENTATION FORMAT

.. _reference/view_architectures/qweb:

QWeb
====
QWeb 视图是标准 :ref:`reference/qweb` 模板，它们位于视图的 ``arch`` 中。由于 QWeb 视图没有特定的根元素，因此其类型必须明确指定（不能从 ``arch`` 字段的根元素推断）。

QWeb 视图有两个用例：

* 它们可以用作前端模板，在这种情况下，应使用 :ref:`reference/data/template` 作为快捷方式。
* 它们可以作为实际的 QWeb 视图（在操作内部打开），在这种情况下，它们应定义为常规视图，具有显式的 ``type``（不能推断）和模型。

Qweb 作为视图的主要补充内容与基本的 Qweb 作为模板相比如下：

* Qweb 作为视图对带有 CSS 类 ``o_qweb_cp_buttons`` 的 ``<nav>`` 元素有一个特例：其内容应为按钮，并将提取并移动到控制面板的按钮区域，而 ``<nav>`` 本身将被移除，这是针对尚不存在的控制面板视图的解决方法。
* Qweb 作为视图的渲染将多个项添加到标准 Qweb 渲染上下文中：

  .. rst-class:: o-definition-list

  ``model``
    Qweb 视图绑定的模型
  ``domain``
    搜索视图提供的域
  ``context``
    搜索视图提供的上下文
  ``records``
    ``model.search(domain)`` 的懒代理，这可以用于遍历记录而不执行更复杂的操作（例如分组）

* Qweb 作为视图还提供额外的渲染钩子：

  - ``_qweb_prepare_context(view_id, domain)`` 准备特定于 Qweb 作为视图的渲染上下文
  - ``qweb_render_view(view_id, domain)`` 是客户端调用的方法，将调用上下文准备方法并最终调用 ``env['ir.qweb'].render()``。

.. _reference/view_architectures/graph:

图表
=====

图表视图用于可视化多个记录或记录组的汇总。其根元素是 ``<graph>``，可以接受以下属性：

.. rst-class:: o-definition-list

``type`` (可选)
  图表的类型之一：``bar``（默认）、``pie`` 和 ``line``。

``stacked`` (可选)
  仅用于 ``bar`` 图。设置为 ``0`` 以防止在组内的条形图初始堆叠。

``disable_linking`` (可选)
  设置为 ``1`` 以防止在图表上单击时重定向到列表视图。

``order`` (可选)
  如果设置，x 轴值将根据给定的顺序（``asc`` 或 ``desc``）默认排序。仅用于 ``bar`` 和 ``pie`` 图。

``string`` (可选)
  重定向到列表视图时在面包屑中显示的字符串。

.. include:: view_architectures/root_attribute_sample.rst

图表视图中唯一允许的元素是 ``field``，其可以具有以下属性：

.. rst-class:: o-definition-list

``name`` (必需)
  要在视图中使用的字段名称。如果用于分组（而不是聚合）

``invisible`` (可选)
  如果为真，则该字段既不会出现在活动度量中，也不会在可选择的度量中。

``type`` (可选)
  如果设置为 ``measure``，则该字段将用作组内的聚合值，而不是分组标准。仅对具有该属性的最后一个字段有效，但对于其他字段的字符串属性（见下文）是有用的。

``interval`` (可选)
  对于日期和日期时间字段，根据指定的间隔（``day``、``week``、``month``、``quarter`` 或 ``year``）进行分组，而不是基于特定日期（固定秒分辨率）或日期（固定天分辨率）进行分组。默认值为 ``month``。

``string`` (可选)
  仅用于 ``type="measure"`` 的字段。将用于在图表视图中显示该字段的名称，覆盖字段的默认 Python 字符串属性。

度量值是从模型字段自动生成的；仅使用可聚合的字段。这些度量值也按字段字符串的字母顺序排序。

.. warning::

   图表视图的汇总是在数据库内容上执行的，不能在图表视图中使用非存储的函数字段。

在图表视图中，``field`` 可以具有 ``widget`` 属性来指示其格式。该小部件应为字段格式化器，其中最有趣的包括 ``float_time`` 和 ``monetary``。

.. code-block:: xml

   <field name="working_hours_close" widget="float_time"/>

.. _reference/view_architectures/pivot:

透视图
=====
透视图
=====
透视视图用于可视化汇总数据作为 `透视表`_。其根元素是 ``<pivot>``，可以接受以下属性：

.. rst-class:: o-definition-list

``disable_linking`` (可选)
  设置为 ``1`` 以移除表格单元格链接到列表视图的功能。

``display_quantity`` (可选)
  设置为 ``1`` 以默认显示数量列。

``default_order`` (可选)
  要在视图中使用的度量名称和顺序（asc 或 desc）。

  .. code-block:: xml

     <pivot default_order="foo asc">
        <field name="foo" type="measure"/>
     </pivot>

透视视图中唯一允许的元素是 ``field``，其可以具有以下属性：

.. rst-class:: o-definition-list

``name`` (必需)
  要在视图中使用的字段名称。如果用于分组（而不是聚合）

``string`` (可选)
  在透视视图中显示字段的名称，覆盖字段的默认 Python 字符串属性。

``type`` (可选)
  指示字段应作为分组标准还是作为组内的聚合值。可能的值有：

  .. rst-class:: o-definition-list

  ``row`` (默认)
    按指定字段进行分组，每个组获得自己的行。
  ``col``
    创建按列分组
  ``measure``
    在组内聚合的字段
  ``interval``
    对于日期和日期时间字段，根据指定的间隔（``day``、``week``、``month``、``quarter`` 或 ``year``）进行分组，而不是基于特定日期（固定秒分辨率）或日期（固定天分辨率）进行分组。

``invisible`` (可选)
  如果为真，则该字段既不会出现在活动度量中，也不会在可选择的度量中（对于不适合聚合的字段很有用，例如不同单位的字段，如 € 和 $）。

.. include:: view_architectures/root_attribute_sample.rst

度量值是从模型字段自动生成的；仅使用可聚合的字段。这些度量值也按字段字符串的字母顺序排序。

.. warning::

    与图表视图一样，透视视图在数据库内容上汇总数据，这意味着不能在透视视图中使用非存储的函数字段。

在透视视图中，``field`` 可以具有 ``widget`` 属性来指示其格式。该小部件应为字段格式化器，其中最有趣的包括 ``date``、``datetime``、``float_time`` 和 ``monetary``。

例如，可以定义一个工时透视视图如下：

    <pivot string="Timesheet">
        <field name="employee_id" type="row"/>
        <field name="date" interval="month" type="col"/>
        <field name="unit_amount" type="measure" widget="float_time"/>
    </pivot>

.. _reference/view_architectures/calendar:

日历
========

日历视图将记录显示为日历中的事件，按照每天、每周、每月或每年的方式排列。

.. note:: 默认情况下，日历视图将以当前日期（今天）为中心。您可以将特定的初始日期传递到操作的上下文中，以设置日历的初始焦点在该日期周围的期间（请参见 `mode`）。

其根元素是 ``<calendar>``。可用于日历视图的属性包括：

:string:
  字符串（默认：``''``）

  该视图标题仅在打开没有名称且目标为“new”（打开对话框）的操作时显示。

:create:
  布尔值（默认：``True``）

  禁用/启用视图上的记录创建。

:edit:
  布尔值（默认：``True``）

  禁用/启用视图上的记录编辑。

:delete:
  布尔值（默认：``True``）

  禁用/启用通过 **Action** 下拉菜单删除记录。

.. rst-class:: o-definition-list

``date_start`` (必需)
    记录的字段名称，包含事件的开始日期。
``date_stop``
    记录的字段名称，包含事件的结束日期，如果提供了 ``date_stop``，则记录可以在日历中直接拖放。
``date_delay``
    替代 ``date_stop``，提供事件的持续时间而不是结束日期（单位：天）。
``color``
    用于 *颜色分割* 的记录字段名称。同一颜色段中的记录将在日历中分配相同的高亮颜色。
    显示可见记录的 display_name/avatar 在侧边栏中。
``form_view_id``
    用户创建或编辑事件时打开的视图。如果未设置此属性，则日历视图将回退到当前操作中的表单视图的 ID（如果有）。
``event_open_popup``
    如果选项 'event_open_popup' 设置为 true，则日历视图将在 FormViewDialog 中打开事件（或记录）。否则，将在新表单视图中打开事件（使用 do_action）。
``quick_create``
    启用单击快速创建事件：只要求用户输入 ``name``（将此值保存的字段可以通过 ``rec_name`` 控制），并尝试创建具有此值和单击事件时间的新事件。如果快速创建失败，则回退到完整的表单对话框。
``quick_create_view_id``
    当设置了 ``quick_create`` 属性时，用户创建事件时打开的视图，而不是默认对话框。
``create_name_field``
    记录的字段名称，包含记录的文本表示，这在通过“快速创建”机制创建记录时使用。
``all_day``
    记录上一个布尔字段的名称，指示相应事件是否标记为整天（持续时间无关紧要）。
``mode``
    加载日历时的默认显示模式。可能的属性有：``day``、``week``、``month``、``year``。
``scales``
    逗号分隔的可用刻度列表。默认情况下，所有刻度都是可用的。有关可能的刻度值，请参见模式。
``create``、``delete``
    通过将相应属性设置为 ``false`` 来禁用视图中对应的操作。

``<field>``
  声明要聚合或在日历 *逻辑* 中使用的字段。如果字段仅在日历卡中显示。

字段可以具有附加属性：

.. rst-class:: o-definition-list

``invisible``
    使用 "True" 隐藏卡中的值。
``avatar_field``
    仅适用于 x2many 字段，以在卡中显示头像而不是 display_name。
``write_model`` 和 ``write_field`` 以及 ``filter_field``
    您可以添加一个过滤器并将结果保存在定义的模型中，过滤器将添加到侧边栏中。``filter_field`` 是可选的，允许您指定将保存过滤器状态的字段。
``filters`` 和 ``color``
    使用 "True" 将此字段添加到侧边栏中的过滤器中。您可以指定一个用于给复选框上色的 ``color`` 字段。
模型通用
-------------

.. currentmodule:: odoo.addons.base.models.ir_ui_view
.. autoattribute:: Model._date_name
    :noindex:

.. _reference/view_architectures/activity:

活动
========

活动视图用于显示与记录相关联的活动。数据以图表形式显示，记录形成行，活动类型形成列。每行的第一个单元格显示对应记录的一个（可自定义的，见 ``templates``，与 :ref:`reference/view_architectures/kanban` 非常相似）卡片。当点击其他单元格时，将显示该记录所有相同类型活动的详细描述。

.. warning::

   活动视图仅在安装了 ``mail`` 模块时可用，并且适用于继承 ``mail.activity.mixin`` 的模型。

活动视图的根元素是 ``<activity>``，它接受以下属性：

.. rst-class:: o-definition-list

``string`` (必需)
    一个标题，应描述该视图

视图元素的可能子元素有：

.. rst-class:: o-definition-list

``field``
  声明要在活动 *逻辑* 中使用的字段。如果字段仅在活动视图中显示，则无需预先声明。

  可能的属性包括：

  .. rst-class:: o-definition-list

  ``name`` (必需)
    要提取的字段名称

``templates``
  定义 :ref:`reference/qweb` 模板。卡片定义可以拆分为多个模板以提高清晰度，但活动视图 *必须* 定义至少一个根模板 ``activity-box``，该模板将为每条记录渲染一次。

  活动视图使用大多数标准 :ref:`javascript qweb <reference/qweb/javascript>` 并提供以下上下文变量（有关更多详细信息，请参见 :ref:`reference/view_architectures/kanban`）：

  .. rst-class:: o-definition-list

  ``widget``
    当前的 :js:class:`ActivityRecord`，可用于提取一些元信息。这些方法也可以直接在模板上下文中使用，无需通过 ``widget`` 访问。
  
  ``record``
    具有所有请求字段作为其属性的对象。每个字段都有两个属性 ``value`` 和 ``raw_value``。

.. _reference/view_architectures/cohort:

队列
======

.. raw:: html

   <span class="badge" style="background-color:#AD5E99">企业特性</span>

队列视图用于展示和理解某些数据随时间的变化方式。例如，想象一下，对于某个业务，客户可以订阅某种服务。队列视图可以显示每月的订阅总数，并研究客户流失的速率。当点击单元格时，队列视图会将您重定向到一个新操作，在该操作中，您只会看到单元格时间范围内的记录；该操作包含列表视图和表单视图。

.. note:: 默认情况下，队列视图将使用与操作定义的相同列表和表单视图。您可以将列表视图和表单视图传递到操作的上下文中，以设置/覆盖将使用的视图（要使用的上下文键为 `form_view_id` 和 `list_view_id`）。

例如，这里是一个非常简单的队列视图：

.. code-block:: xml

    <cohort string="Subscription" date_start="date_start" date_stop="date" interval="month"/>

队列视图的根元素是 <cohort>，它接受以下属性：

.. rst-class:: o-definition-list

``string`` (必需)
    一个标题，应描述该视图。

``date_start`` (必需)
    有效的日期或日期时间字段。该字段被视图理解为记录的开始日期。

``date_stop`` (必需)
    有效的日期或日期时间字段。该字段被视图理解为记录的结束日期。此字段将决定流失。

``disable_linking`` (可选)
  设置为 ``1`` 以防止点击队列单元格重定向到列表视图。

``mode`` (可选)
    描述模式的字符串。应为 'churn' 或 'retention'（默认）。流失模式将从 0% 开始并随时间累积，而保留将从 100% 开始并随时间减少。

``timeline`` (可选)
    描述时间线的字符串。应为 'backward' 或 'forward'（默认）。前向时间线将显示从 date_start 到 date_stop 的数据，而后向时间线将显示从 date_stop 到 date_start 的数据（当 date_start 在未来时 / 大于 date_stop）。

``interval`` (可选)
    描述时间间隔的字符串。应为 'day'、'week'、'month'（默认）或 'year'。

``measure`` (可选)
    一个可以聚合的字段。该字段将用于计算每个单元格的值。如果未设置，则队列视图将计算发生次数。

``<field>`` (可选)
  允许指定特定字段以管理可用度量，它的主要用途是将字段隐藏在可选择的度量中：

  .. rst-class:: o-definition-list

  ``name`` (必需)
    要在视图中使用的字段名称。
  
  ``string`` (可选)
    在队列视图中将用于显示该字段的名称，覆盖字段的默认 Python 字符串属性。
  
  ``invisible`` (可选)
    如果为真，则该字段既不会出现在活动度量中，也不会在可选择的度量中（对于不适合聚合的字段很有用，例如不同单位的字段，如 € 和 $）。
  
    如果值是域，则在当前行的记录上下文中评估域，如果 ``True``，则在单元格上设置相应的属性。

.. include:: view_architectures/root_attribute_sample.rst

.. _reference/view_architectures/grid:

网格
======
.. raw:: html

   <span class="badge" style="background-color:#AD5E99">企业功能</span>

限制
-----------

该视图仍在开发中，可能需要扩展或修改。

* 仅对 ``date`` 列字段进行了测试， ``selection`` 和 ``many2one`` 字段在名义上已实现并受支持，但尚未经过测试， ``datetime`` 字段完全未实现。
* 列单元格的配置很困难，必须为数字类型。
* 单元格调整默认禁用，必须配置为启用。
* 由于 ``fields_view_get`` 后处理的限制， ``create``、``edit`` 和 ``delete`` ACL 元数据不会自动设置在视图根目录中（有一个固定的显式视图类型列表会获取这些属性）。

架构
------

网格视图有自己的架构和额外的验证。视图架构为：

``<grid>`` (1)
    架构根元素

    * 必须的 ``string`` 属性
    * 可选的 ``create``、``edit`` 和 ``delete`` 属性
    * 可选的 ``adjustment`` 和 ``adjust_name`` 属性

      ``adjustment`` 可以是 ``object`` 或 ``action``，以指示单元格的调整应通过方法调用还是执行操作。 ``adjust_name`` 分别提供方法名称和操作 ID。

      在这两种情况下，调整参数作为 ``grid_adjust`` 上下文成员提供，在 ``object`` 的情况下，参数也作为位置函数参数提供（紧接在一个空列表的 ID 旁边）：

      ``row_domain``
        匹配调整单元格整行的域。
      ``column_field``
        调整单元格的列名称。
      ``column_value``
        调整单元格的列值。
      ``cell_field``
        调整单元格的度量字段。
      ``change``
        旧单元格值与调整后的值之间的差异，可以是正数或负数。

    * 可选的 ``hide_line_total`` 和 ``hide_column_total`` 属性

      ``hide_line_total``
        设置为 true 以隐藏总行（默认 false）。
      ``hide_column_total``
        设置为 true 以隐藏总列（默认 false）。

    * 可选的 ``barchart_total`` 属性

      ``barchart_total``
        设置为 ``true`` 以在网格底部显示基于列总数的条形图（默认 false）。

    * 可选的 ``create_inline`` 和 ``display_empty`` 属性

      ``create_inline``
        设置为 ``true`` 以在网格底部显示额外行并带有 ``添加一行`` 按钮（默认 false）。当此选项设置为 ``true`` 时，控制面板中的 ``添加一行`` 按钮将被隐藏。当没有可用数据且未设置 ``display_empty``（因此显示帮助内容）时，控制面板中的 ``添加一行`` 按钮将显示，以便用户创建第一条记录。
      ``display_empty``
        设置为 ``true`` 以在没有数据时继续显示网格（默认 false）。当你希望用户能够跟踪当前期间（日期显示在列标题中）时，这可能很有用。提醒一下，当没有数据时，如果未设置此属性，将显示帮助内容而不是网格。

``<button>`` (0+)
    常规 Odoo 操作按钮，显示在视图标题中

    * 必须的 ``string`` 属性（按钮标签）
    * 必须的 ``type`` 属性，可以是 ``object`` 或 ``action``

      .. note:: 不支持工作流按钮。

    * 必须的 ``name`` 属性，可以是调用的方法名称或要执行的操作的 ID。
    * 可选的 ``context``

    服务器回调提供了在视图中显示的所有记录 ID，无论是作为传递给方法的 ID（``object`` 按钮）还是作为上下文中的 ``active_ids``（``action`` 按钮）。

``<field type="row">`` (1+)
    行分组字段，如果有搜索视图的分组过滤器，将被替换。

    视图中 ``row`` 字段的顺序提供了它们的分组深度：
    如果第一个字段是 ``school``，第二个字段是 ``age``，则记录将首先按 ``school`` 分组，然后在每个学校内按 ``age`` 分组。

``<field type="col">`` (1)
    列分组字段。

    列字段可以包含 0+ 个 ``<range>`` 元素，用于指定可自定义的列范围。 ``range`` 元素有以下强制属性：

    ``name``
        可以用来通过 ``grid_range`` 上下文值覆盖默认范围（默认是第一个）。
    ``string``
        范围按钮的标签（用户可见）。
    ``span``
        要在视图中一次显示的所有列的跨度的符号名称，可能触发分页。

        对于 ``date`` 字段，目前有效的跨度是 ``week`` 和 ``month``。
    ``step``
        在一列和前一列或下一列之间的符号名称。

        对于 ``date`` 字段，目前唯一有效的跨度是 ``day``。
``<field type="measure">`` (1)
    单元格字段，由 ``read_group`` 自动累积。

    度量字段可以采用 ``widget`` 属性来定制其显示。
服务器交互
-------------------

除了可选按钮，网格视图当前调用两个方法：

* ``read_grid``（由模块在所有模型上提供）返回网格内容的几乎全部信息，格式为字典：

  * 行标题是一个字典列表，具有以下键：

    ``values``（必需）
        这映射到一个字典，每个 ``row`` 字段都有一个键，值总是以 ``[value, label]`` 形式表示。
    ``domain``（必需）
        任何记录的域，这些记录是该行的来源，以防在单元格调整期间需要复制记录。

  * 列标题是一个字典列表，至少具有一个键：

    ``values``（必需）
        见行标题值。
    ``domain``（必需）
        见列域值。
    ``current``（可选）
        布尔值，标记/高亮显示一列。

  * 网格数据作为一个列表（行）由列表（单元格）组成，每个单元格字典具有以下键：

    ``value``
        与单元格关联的数值。
    ``domain``
        与单元格记录匹配的域（应假定为不透明）。
    ``size``
        分组在单元格中的记录数。
    ``readonly``（可选）
        布尔值，指示该特定单元格不应可被客户端编辑。
    ``classes``（可选）
        要添加到单元格容器（在单元格的 TD 和潜在可编辑元素之间）的类列表（作为字符串）。

        如果此列表与基本类（以 ``o_grid_cell_`` 为前缀）发生冲突，则忽略此列表中的类。

    请注意，网格数据是 *稠密* 的，如果查询数据库未返回与单元格匹配的组，则单元格将生成一个带有默认值的“空”单元格。
  * ``prev`` 和 ``next`` 可以是 falsy（无分页）或上下文项，以合并到视图自己的上下文中以 ``read_grid`` 上一页或下一页，假定其为不透明。

* ``read_grid_domain(field, range)``（由模块在所有模型上提供）返回与当前配置的网格“跨度”匹配的域。这也是 ``read_grid`` 内部完成的，但独立调用以与单独的 ``search_count`` 或 ``read_group`` 一起使用可能是有用的或必要的。

* ``adjust_grid``，当前没有整体实现，其语义可能会随着时间和使用案例的演变而变化。

服务器钩子
------------

``read_grid`` 调用多个钩子，允许从内部自定义其操作，而无需覆盖整个方法：

``_grid_format_cell(group, cell_field)``
    将 read_group 的输出（按组）转换为单元格，格式如上所述（作为“网格数据”的一部分）。
``_grid_make_empty_cell(row_domain, column_domain, view_domain)``
    生成单元格的空版本（如果没有对应的组）。
``_grid_column_info(name, range)``
    根据列类型生成 ColumnMetadata 对象，存储的值可以直接返回（作为 ``read_grid`` 的一部分）或用于查询并将 ``read_group`` 重新格式化为 ``read_grid``：

    ``grouping``
        列的实际分组字段/查询。
    ``domain``
        在列字段分页的情况下，应用于 ``read_group`` 的域，可以是空列表。
    ``prev`` 和 ``next``
        发送到 ``read_grid`` 的上下文段，用于当前页之前和之后的页。如果 ``False``，则在该方向上禁用分页。
    ``values``
        要在“当前页面”上显示的列值，每个值都是一个字典，具有以下键：

        ``values``
            字典，将字段名称映射到整个列的值，通常为 ``name`` -> 值。
        ``domain``
            与该特定列匹配的域。
        ``is_current``
            ``True`` 如果当前列应该在网格中特别突出显示， ``False`` 否则。
        ``format``
            如何将 ``read_group`` 中的值格式化为 ``read_grid`` 格式（与 ColumnInfo 中的 ``values`` 匹配）。

ACL
---

* 如果视图不可编辑，则单个单元格将不可编辑。
* 如果视图不可创建，则不会显示 ``添加一行`` 按钮（该按钮当前会创建一个新空记录）。
上下文键
------------

``grid_range``
    选择在视图有多个范围时默认使用哪个范围。
``grid_anchor``
    （如果适用）用作列范围的默认锚点，而不是 ``read_grid`` 定义的默认值。

    对于日期字段，参考日期用于计算初始跨度。默认日期锚点为“今天”（以用户的时区为准）。

.. _reference/view_architectures/gantt:

甘特图
=====

.. raw:: html

   <span class="badge" style="background-color:#AD5E99">企业特性</span>

甘特图视图适当地显示甘特图（用于调度）。

甘特图视图的根元素是 ``<gantt/>``，它没有子元素，但可以包含以下属性：

:string:
  字符串（默认: ``''``）

  仅在打开没有名称且目标为 'new'（打开对话框）的操作时显示该视图标题。

:create:
  布尔值（默认: ``True``）

  启用/禁用在视图中创建记录。

:edit:
  布尔值（默认: ``True``）

  启用/禁用在视图中编辑记录。

:delete:
  布尔值（默认: ``True``）

  启用/禁用通过 **操作** 下拉菜单删除记录。

.. rst-class:: o-definition-list

``date_start``（必需）
  提供每条记录事件的开始日期时间字段的名称。
``date_stop``（必需）
  提供每条记录事件的结束持续时间字段的名称。
``dependency_field``
  提供两个记录之间依赖关系的 ``many2many`` 字段名称。
  如果 B 依赖于 A，``dependency_field`` 是允许从 B 获取 A 的字段。
  此字段和 ``dependency_inverted_field`` 字段都用于绘制药丸之间的依赖箭头并重新调度它们。
``dependency_inverted_field``（在提供 ``dependency_field`` 时必需）
  提供与 ``dependency_field`` 相反的依赖关系的 ``many2many`` 字段名称。
  如果 B 依赖于 A，``dependency_inverted_field`` 是允许从 A 获取 B 的字段。
``color``
  用于根据其值为药丸着色的字段名称。
``decoration-{$name}``
  `python 表达式`_，返回布尔值。

  根据相应记录的属性更改单元格文本的样式。

  ``{$name}`` 可以是以下 `bootstrap 上下文颜色`_（``danger``、``info``、``secondary``、``success`` 或 ``warning``）之一。

  根据相应记录的属性定义记录在行文本中的条件显示。

  值为 Python 表达式。对于每条记录，表达式在记录的属性作为上下文值的情况下进行评估，如果为 ``true``，则相应的样式应用于行。以下是上下文中可用的其他值：

  * ``uid``: 当前用户的 ID，
  * ``today``: 当前本地日期，格式为 ``YYYY-MM-DD``，
  * ``now``: 与 ``today`` 相同，并添加当前时间。该值格式为 ``YYYY-MM-DD hh:mm:ss``。

  .. code-block:: xml

    <gantt decoration-info="state == 'draft'"
          decoration-danger="state == 'help_needed'"
          decoration-bf="state == 'busy'">
      ...
    </gantt>
``default_group_by``
  用于按字段分组任务的名称。
``disable_drag_drop``
  如果设置为 true，则甘特图将不支持任何拖放功能。
``consolidation``
  在记录单元格中显示合并值的字段名称。
``consolidation_max``
  字典，键为“按组”字段，值为达到的最大合并值，超过该值后将以红色显示单元格（例如 ``{"user_id": 100}``）。
``consolidation_exclude``
  描述任务是否必须排除的字段名称。
  如果设置为 true，则在合并行中显示带有条纹的区域。
``create``, ``cell_create``, ``edit``, ``delete``, ``plan``
    允许通过将相应属性设置为 ``false`` 来禁用视图中的相应操作（默认: ``true``）。

    * ``create``: 如果启用，则控制面板中将有一个 ``添加`` 按钮以创建记录。
    * ``cell_create``: 如果启用且 ``create`` 启用，当悬停在时间槽单元格上时，将显示一个“**+**”按钮，以在该槽中创建新记录。
    * ``edit``: 如果启用，打开的记录将处于编辑模式（因此可编辑）。
    * ``plan``: 如果启用且 ``edit`` 启用，则将在时间槽上显示一个“放大镜”按钮，以计划未分配的记录到该时间槽中。

    .. example::

        当您不想在甘特图视图上创建记录并且模型上的开始和结束日期是必需的时，规划功能应被禁用，因为将永远找不到记录。
``offset``
  根据比例，添加到今天的单位数以计算默认周期。示例：在默认比例为周时，+1 的偏移量将打开下周的甘特图，而在默认比例为月时，-2 的偏移量将打开两个月前的甘特图。
``progress``
  提供记录事件完成百分比的字段名称，范围在 0 到 100 之间。
``string``
  甘特图的标题。
``precision``
  JSON 对象，指定药丸在每个比例中的捕捉精度。

  对于比例 ``day`` 的可能值（默认: ``hour``）：

  - ``hour``: 记录时间捕捉到整小时（例如: 7:12 变为 8:00）。

  - ``hour:half``: 记录时间捕捉到半小时（例如: 7:12 变为 7:30）。

  - ``hour:quarter``: 记录时间捕捉到四分之一小时（例如: 7:12 变为 7:15）。

  对于比例 ``week`` 的可能值（默认: ``day:half``）：

  - ``day``: 记录时间捕捉到整天（例如: 7:28 AM 变为前一天的 11:59:59 PM，10:32 PM 变为当天的 12:00 PM）。

  - ``day:half``: 记录时间捕捉到半天（例如: 7:28 AM 变为 12:00 PM）。

  对于比例 ``month`` 的可能值（默认: ``day:half``）：

  - ``day``: 记录时间捕捉到整天（例如: 7:28 AM 变为前一天的 11:59:59 PM，10:32 PM 变为当天的 12:00 PM）。

  - ``day:half``: 记录时间捕捉到半天（例如: 7:28 AM 变为 12:00 PM）。

  比例 ``year`` 始终捕捉到整天。

  精度属性的示例: ``{"day": "hour:quarter", "week": "day:half", "month": "day"}``。
``total_row``
  布尔值，用于控制是否应显示包含记录总数的行。（默认: ``false``）。
``collapse_first_level``
  布尔值，用于控制是否可以在按一个字段分组时折叠每一行。（默认: ``false``，当按两个字段分组时开始折叠）。
``display_unavailability``
  布尔值，标记由模型的 ``gantt_unavailability`` 函数返回的日期在甘特图中可用。记录仍然可以在其中进行调度，但其不可用性会以可视方式显示。（默认: ``false``）。
``default_scale``
  渲染视图时的默认比例。可能的值（默认: ``month``）：

  * ``day``
  * ``week``
  * ``month``
  * ``year``

``scales``
  逗号分隔的允许此视图的比例列表。默认情况下，所有比例都是允许的。有关可在此列表中使用的可能比例值，请参见 ``default_scale``。

``templates``
  定义 :ref:`reference/qweb` 模板 ``gantt-popover``，该模板在用户悬停在甘特图视图中的一条记录上时使用。

  甘特图视图主要使用标准 :ref:`javascript qweb
  <reference/qweb/javascript>`，并提供以下上下文变量：

  .. rst-class:: o-definition-list

  ``widget``
    当前的 :js:class:`GanttRow`，可用于获取一些元信息。 ``getColor`` 方法用于将其转换为颜色整数，也可以直接在模板上下文中使用，而无需使用 ``widget``。

  ``on_create``
    如果在视图的添加按钮上指定，则单击该按钮时将启动客户端操作，而不是打开通用对话框。
    这应持有操作的 xmlid（例如: ``on_create="%(my_module.my_wizard)d"``）。

``form_view_id``
  用户创建或编辑记录时要打开的视图。请注意，如果未设置此属性，则甘特图将回退到当前操作中的表单视图的 ID（如果有）。

``dynamic_range``
  如果设置为 true，甘特图将从第一条记录开始，而不是从年/月/日的开始处开始。

``pill_label``
  如果设置为 true，则当比例设置为周或月时，时间会出现在药丸标签中。（例如：`7:00 AM - 11:00 AM (4h) - DST Task 1`）。

``thumbnails``
  如果组是关系字段，则允许在组名称旁边显示缩略图。它期望一个 python 字典，键是活动模型上字段的名称。
  值是持有相关模型上缩略图的字段名称。

  示例：任务具有一个字段 user_id，引用 res.users。res.users 模型具有一个字段 image，保存头像，
  然后：

  .. code-block:: xml

     <gantt
        date_start="date_start"
        date_stop="date_stop"
        thumbnails="{'user_id': 'image_128'}"
      >
      </gantt>

  在按 user_id 分组时，将显示用户头像。

.. include:: view_architectures/root_attribute_sample.rst

.. _reference/view_architectures/map:

地图
===

.. raw:: html

   <span class="badge" style="background-color:#AD5E99">企业特性</span>

此视图能够在地图上显示记录及其之间的路线。记录由图钉表示。它还允许在与记录的图钉关联的弹出窗口中可视化模型的字段。

.. note::

    应用此视图的模型应包含一个 `res.partner` many2one，因为该视图依赖于 `res.partner` 的地址和坐标字段来定位记录。

API
---

该视图使用位置数据平台的 API 来获取图块（地图背景）、进行地理前向（将地址转换为一组坐标）和获取路线。
该视图实现了两个 API，OpenStreetMap 和 MapBox。默认情况下使用 OpenStreetMap，能够获取 `tiles`_ 并进行 `geoforwarding`_。此 API 不需要令牌。
只要在常规设置中提供有效的 `MapBox`_ 令牌，视图将切换到 MapBox API。此 API 更快，并允许计算路线。可以通过 `signing up`_ 到 MapBox 获取令牌。

结构组件
---------------------

视图的根元素是 ``<map>``。它可以具有以下属性：

.. rst-class:: o-definition-list

``res_partner``
    包含 `res.partner` many2one。如果未提供，视图将创建一个空地图。
``default_order``
    如果提供字段，视图将覆盖模型的默认排序。该字段必须是应用视图的模型的一部分，而不是来自 `res.partner` 的字段。
``routing``
    如果 ``1`` 显示记录之间的路线。视图需要一个有效的 MapBox 令牌和至少两个定位的记录（即记录具有 `res.partner` many2one，并且合作伙伴具有地址或有效坐标）。
``hide_name``
    如果 ``1`` 隐藏图钉弹出窗口中的名称（默认: ``0``）。
``hide_address``
    如果 ``1`` 隐藏图钉弹出窗口中的地址（默认: ``0``）。
``hide_title``
    如果 ``1`` 隐藏图钉列表中的标题（默认: ``0``）。
``panel_title``
    要显示为图钉列表标题的字符串。如果未提供，则标题为操作的名称，如果视图不在操作中，则为“项目”。
``limit``
    要获取的最大记录数（默认: ``80``）。必须是正整数。

``<map>`` 元素可以包含多个 ``<field>`` 元素。每个 ``<field>`` 元素被解释为图钉弹出窗口中的一行。字段的属性如下：

.. rst-class:: o-definition-list

``name``
    要显示的字段。
``string``
    在字段内容之前显示的字符串。可以用作描述。

例如，这里是一个地图：
    .. code-block:: xml

        <map res_partner="partner_id" default_order="date_begin" routing="1" hide_name="1">
            <field name="partner_id" string="客户名称"/>
        </map>

.. _`accesskey`: https://www.w3.org/TR/html5/editing.html#the-accesskey-attribute
.. _`bootstrap contextual color`: https://getbootstrap.com/docs/3.3/components/#available-variations
.. _`Comma-separated values`: https://en.wikipedia.org/wiki/Comma-separated_values
.. _`floats`: https://developer.mozilla.org/en-US/docs/Web/CSS/float
.. _`geoforwarding`: https://nominatim.org/release-docs/develop/
.. _`HTML`: https://en.wikipedia.org/wiki/HTML
.. _`integer`: https://docs.python.org/3/library/stdtypes.html#numeric-types-int-float-complex
.. _`keyboard_shortcut`: https://en.wikipedia.org/wiki/Keyboard_shortcut
.. _`MapBox`: https://docs.mapbox.com/api/
.. _`Odoo`: https://www.odoo.com/
.. _`path`: https://en.wikipedia.org/wiki/Path_(computing)
.. _`pivot table`: https://en.wikipedia.org/wiki/Pivot_table
.. _`python expression`: https://docs.python.org/3/library/stdtypes.html#boolean-operations-and-or-not
.. _`relative path`: https://en.wikipedia.org/wiki/URL
.. _`signing up`: https://account.mapbox.com/auth/signup/
.. _`tiles`: https://wiki.openstreetmap.org/wiki/Tile_data_server
