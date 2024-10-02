:orphan:

==================
创建网站
==================

.. danger::
   本教程已过时。我们建议阅读 :doc:`server_framework_101` 以获取最新信息。

.. warning::

    * 本指南假设您具有 `Python 基础知识 <http://docs.python.org/2/tutorial/>`_
    * 本指南假设您已经 :doc:`安装了 Odoo </administration/on_premise>`

创建基础模块
=======================

在 Odoo 中，任务通过创建模块来执行。

模块可以通过添加新行为或修改现有行为（包括其他模块添加的行为）来自定义 Odoo 的行为。

:ref:`Odoo 的脚手架工具 <reference/cmdline/scaffold>` 可以设置一个基础模块。快速入门的方法是执行以下命令：

.. code-block:: console

    $ ./odoo-bin scaffold Academy my-modules

这将自动创建一个 ``my-modules`` *模块目录*，其中包含一个名为 ``academy`` 的模块。如果您愿意，该目录可以是现有的模块目录，但模块名称在目录中必须是唯一的。

演示模块
=======================

我们现在有一个“完整的”模块，可以进行安装。

虽然它目前什么也不做，但我们可以安装它：

* 启动 Odoo 服务器

  .. code-block:: console

      $ ./odoo-bin --addons-path addons,my-modules

* 访问 http://localhost:8069
* 创建一个包含演示数据的新数据库
* 转到 :menuselection:`设置 --> 应用 --> 应用`
* 在右上角删除 *已安装* 筛选器，搜索 *academy*
* 点击 *Academy* 模块的 :guilabel:`安装` 按钮

前往浏览器
==================

:ref:`控制器 <reference/controllers>` 解释浏览器请求并返回数据。

添加一个简单的控制器，并确保通过 ``__init__.py`` 导入它（这样 Odoo 能够找到它）：

.. code-block:: python
    :caption: ``academy/controllers.py``

    # -*- coding: utf-8 -*-
    from odoo import http

    class Academy(http.Controller):

        @http.route('/academy/academy/', auth='public')
        def index(self, **kw):
            return "Hello, world"

关闭您的服务器 (:kbd:`^C`) 并重新启动：

.. code-block:: console

    $ ./odoo-bin --addons-path addons,my-modules

然后访问 http://localhost:8069/academy/academy/，您应该会看到您的“页面”：

.. figure:: website/helloworld.png

模板
================

在 Python 中生成 HTML 不是很方便。

常见的解决方案是使用模板_，即包含占位符和显示逻辑的伪文档。Odoo 允许使用任何 Python 模板系统，但提供了自己的 :ref:`QWeb <reference/qweb>` 模板系统，并与其他功能集成。

创建一个模板，并确保在 ``__manifest__.py`` 中注册模板文件，然后修改控制器以使用我们的模板：

.. code-block:: python
    :caption: ``academy/controllers.py``

    class Academy(http.Controller):

        @http.route('/academy/academy/', auth='public')
        def index(self, **kw):
            return http.request.render('academy.index', {
                'teachers': ["Diana Padilla", "Jody Caroll", "Lester Vaughn"],
            })

.. code-block:: xml
    :caption: ``academy/templates.xml``

    <odoo>

        <template id="index">
            <title>Academy</title>
            <t t-foreach="teachers" t-as="teacher">
                <p><t t-esc="teacher"/></p>
            </t>
        </template>

    </odoo>

模板遍历了所有教师（通过 *模板上下文* 传递），并在每个段落中打印教师的名字。

最后，重启 Odoo 并更新模块的数据（以安装模板），操作方法是转到 :menuselection:`设置 --> 应用 --> 应用 --> Academy` 并点击 :guilabel:`升级`。

.. tip::

    或者，Odoo 可以通过 :option:`同时更新模块 <odoo-bin -u>` 进行重启：

    .. code-block:: console

        $ odoo-bin --addons-path addons,my-modules -d academy -u academy

访问 http://localhost:8069/academy/academy/ 现在应该会显示：

.. image:: website/basic-list.png
在 Odoo 中存储数据
===================

:ref:`Odoo 模型 <reference/orm/model>` 映射到数据库表。

在上一节中，我们仅显示了在 Python 代码中静态输入的字符串列表。这不允许修改或持久存储，因此我们现在将数据移至数据库中。

定义数据模型
-------------

定义一个教师模型，并确保在 ``__init__.py`` 中导入该模型，以便正确加载：

.. code-block:: python
    :caption: ``academy/models.py``

    from odoo import models, fields, api

    class Teachers(models.Model):
        _name = 'academy.teachers'

        name = fields.Char()

然后为该模型设置 :ref:`基础访问控制 <reference/security/acl>`，并将其添加到模块清单中：

.. code-block:: python
    :caption: ``academy/__manifest__.py``

    # 总是加载
    'data': [
        'security/ir.model.access.csv',
        'templates.xml',
    ],

.. code-block:: csv
    :caption: ``academy/security/ir.model.access.csv``

    id,name,model_id:id,group_id:id,perm_read,perm_write,perm_create,perm_unlink
    access_academy_teachers,access_academy_teachers,model_academy_teachers,,1,0,0,0

这简单地为所有用户提供了读取权限（ ``perm_read`` ）（``group_id:id`` 留空）。

.. note::

    :ref:`数据文件 <reference/data>`（XML 或 CSV）必须添加到模块清单中，而 Python 文件（模型或控制器）则不需要，但必须从 ``__init__.py`` 中导入（直接或间接）。

.. warning::

    管理员用户绕过了访问控制，他们拥有对所有模型的访问权限，即使未赋予访问权限。

演示数据
---------

第二步是向系统添加一些演示数据，以便可以轻松测试。通过添加一个 ``demo`` :ref:`数据文件 <reference/data>` 完成此操作，该文件必须从模块清单中链接：

.. code-block:: xml
    :caption: ``academy/demo.xml``

    <odoo>

        <record id="padilla" model="academy.teachers">
            <field name="name">Diana Padilla</field>
        </record>
        <record id="carroll" model="academy.teachers">
            <field name="name">Jody Carroll</field>
        </record>
        <record id="vaughn" model="academy.teachers">
            <field name="name">Lester Vaughn</field>
        </record>

    </odoo>

.. tip::

    :ref:`数据文件 <reference/data>` 可以用于演示和非演示数据。演示数据仅在“演示模式”下加载，可用于流程测试和演示，非演示数据始终加载并用作系统的初始设置。

    在这种情况下，我们使用的是演示数据，因为系统的实际用户可能希望输入或导入自己的教师名单，而此名单仅用于测试。
访问数据
--------

最后一步是修改模型和模板以使用我们的演示数据：

#. 从数据库中获取记录，而不是使用静态列表
#. 因为 :meth:`~odoo.models.Model.search` 返回与过滤器匹配的记录集（这里为“所有记录”），所以需要修改模板以打印每位教师的 ``name``

.. code-block:: python
   :caption: ``academy/controllers.py``

   class Academy(http.Controller):

        @http.route('/academy/academy/', auth='public')
        def index(self, **kw):
            Teachers = http.request.env['academy.teachers']
            return http.request.render('academy.index', {
                'teachers': Teachers.search([])
            })

.. code-block:: xml
   :caption: ``academy/templates.xml``

   <odoo>

       <template id="index">
            <title>Academy</title>
            <t t-foreach="teachers" t-as="teacher">
                <p><t t-esc="teacher.id"/> <t t-esc="teacher.name"/></p>
            </t>
       </template>

   </odoo>

重启服务器并更新模块（以更新清单和模板并加载演示文件），然后访问 http://localhost:8069/academy/academy/。页面看起来会稍有不同：名称前应带有一个编号（教师的数据库标识符）。

网站支持
========

Odoo 捆绑了一个专用于构建网站的模块。

到目前为止，我们已经直接使用了控制器，但 Odoo 8 通过 ``website`` 模块添加了更深的集成和其他一些服务（例如默认样式、主题）。

#. 首先，将 ``website`` 添加为 ``academy`` 的依赖项
#. 然后在控制器上添加 ``website=True`` 标志，这会在 :ref:`请求对象 <reference/http/request>` 上设置一些新变量，并允许在我们的模板中使用网站布局
#. 在模板中使用网站布局

.. code-block:: python
    :caption: ``academy/__manifest__.py``

    'version': '0.1',

    # 此模块正常工作所需的任何模块
    'depends': ['website'],

    # 总是加载
    'data': [

.. code-block:: python
    :caption: ``academy/controllers.py``

    class Academy(http.Controller):

         @http.route('/academy/academy/', auth='public', website=True)
         def index(self, **kw):
             Teachers = http.request.env['academy.teachers']
             return http.request.render('academy.index', {
                 'teachers': Teachers.search([])
             })

.. code-block:: xml
    :caption: ``academy/templates.xml``

    <odoo>

        <template id="index">
            <t t-call="website.layout">
                <t t-set="title">Academy</t>
                <div class="oe_structure">
                    <div class="container">
                        <t t-foreach="teachers" t-as="teacher">
                            <p><t t-esc="teacher.id"/> <t t-esc="teacher.name"/></p>
                        </t>
                    </div>
                </div>
            </t>
        </template>

    </odoo>

在重启服务器并更新模块（以更新清单和模板）后，访问 http://localhost:8069/academy/academy/ 应该会显示一个更美观的页面，带有品牌标识和多个内置页面元素（顶级菜单、页脚等）。

.. image:: website/layout.png

网站布局还提供了编辑工具支持：点击页面右上角的 :guilabel:`登录` 按钮，输入凭据（默认是 ``admin`` / ``admin``），然后点击 :guilabel:`登录`。

现在，您已经进入 Odoo 的“管理界面”：点击左上角的 :guilabel:`网站` 菜单项，返回到网站界面，但这次是以管理员身份进入，具有 *website* 模块提供的高级编辑功能：

* 一个模板代码编辑器 (:menuselection:`自定义 --> HTML 编辑器`)，您可以在其中查看和编辑当前页面使用的所有模板
* 页面左上角的 :guilabel:`编辑` 按钮可切换到“编辑模式”，在该模式下可用块（片段）和富文本编辑
* 其他功能如移动预览或 :abbr:`SEO（搜索引擎优化）`
URLs 和路由
==========

控制器方法通过 :func:`~odoo.http.route` 装饰器与 *路由* 关联，该装饰器接收一个路由字符串和若干用于自定义其行为或安全性的属性。

我们已经见过一个“字面”的路由字符串，它精确匹配 URL 的一部分，但路由字符串也可以使用 `转换器模式`_，它匹配 URL 的一部分并将这些部分作为局部变量提供。例如，我们可以创建一个新的控制器方法，该方法接收 URL 的一部分并将其输出：

.. code-block:: python
    :caption: ``academy/controllers.py``

    # 新路由
    @http.route('/academy/<name>/', auth='public', website=True)
    def teacher(self, name):
        return '<h1>{}</h1>'.format(name)

重启 Odoo，访问 http://localhost:8069/academy/Alice/ 和 http://localhost:8069/academy/Bob/，看看有什么不同。

顾名思义，`转换器模式`_ 不仅仅是提取，它们还执行 *验证* 和 *转换*，因此我们可以将新的控制器修改为仅接受整数：

.. code-block:: python
    :caption: ``academy/controllers.py``

    @http.route('/academy/<int:id>/', auth='public', website=True)
    def teacher(self, id):
        return '<h1>{} ({})</h1>'.format(id, type(id).__name__)

重启 Odoo，访问 http://localhost:8069/academy/2，注意到旧值是字符串，而新值被转换为整数。尝试访问 http://localhost:8069/academy/Carol/，您会发现页面无法找到：由于“Carol”不是整数，路由被忽略，未找到匹配的路由。

Odoo 提供了一个额外的转换器，称为 ``model``，它可以在给定 ID 时直接提供记录。让我们使用这个功能为教师的简历创建一个通用页面：

.. code-block:: python
    :caption: ``academy/controllers.py``

    @http.route('/academy/<model("academy.teachers"):teacher>/', auth='public', website=True)
    def teacher(self, teacher):
        return http.request.render('academy.biography', {
            'person': teacher
        })

.. code-block:: xml
    :caption: ``academy/templates.xml``

    <template id="biography">
        <t t-call="website.layout">
            <t t-set="title">Academy</t>
            <div class="oe_structure"/>
            <div class="oe_structure">
                <div class="container">
                    <h3><t t-esc="person.name"/></h3>
                </div>
            </div>
            <div class="oe_structure"/>
        </t>
    </template>

然后修改模型列表，以链接到我们新的控制器：

.. code-block:: xml
    :caption: ``academy/templates.xml``

    <template id="index">
        <t t-call="website.layout">
            <t t-set="title">Academy</t>
            <div class="oe_structure">
                <div class="container">
                    <t t-foreach="teachers" t-as="teacher">
                        <p>
                            <a t-attf-href="/academy/{{ slug(teacher) }}">
                            <t t-esc="teacher.name"/></a>
                        </p>
                    </t>
                </div>
            </div>
        </t>
    </template>

重启 Odoo 并升级模块，然后您可以访问每位教师的页面。作为练习，尝试在教师页面上添加块来编写简历，然后转到其他教师的页面。您会发现，您的简历在所有教师之间共享，因为块是添加到 *模板* 中的，而 *biography* 模板在所有教师之间共享，当编辑一个页面时，所有页面都会同时被编辑。
字段编辑
==========

特定于记录的数据应保存在该记录上，因此让我们为教师添加一个新的简历字段：

.. code-block:: python
    :caption: ``academy/models.py``

    class Teachers(models.Model):
        _name = 'academy.teachers'

        name = fields.Char()
        biography = fields.Html()

.. code-block:: xml
    :caption: ``academy/templates.xml``

    <template id="biography">
        <t t-call="website.layout">
            <t t-set="title">Academy</t>
            <div class="oe_structure"/>
            <div class="oe_structure">
                <div class="container">
                    <h3><t t-esc="person.name"/></h3>
                    <div><t t-esc="person.biography"/></div>
                </div>
            </div>
            <div class="oe_structure"/>
        </t>
    </template>

重启 Odoo 并更新视图，重新加载教师页面，您会发现字段是不可见的，因为它为空。

.. todo:: 视图被设置为 noupdate，因为之前进行了修改，可以通过 ``-i`` 强制更新或采取其他操作？

对于记录字段，模板可以使用特殊的 ``t-field`` 指令，该指令允许从网站中使用字段特定的接口编辑字段内容。将 *person* 模板更改为使用 ``t-field``：

.. code-block:: xml
    :caption: ``academy/templates.xml``

    <div class="oe_structure">
        <div class="container">
            <h3 t-field="person.name"/>
            <div t-field="person.biography"/>
        </div>
    </div>

重启 Odoo 并升级模块，现在教师姓名下有一个占位符，并且在 :guilabel:`编辑` 模式下有一个新的块区域。放置在该区域的内容会存储在对应教师的 ``biography`` 字段中，因此这些内容特定于该教师。

教师的姓名也是可编辑的，保存后，变更会在索引页面上可见。

``t-field`` 还可以接受取决于具体字段的格式化选项。例如，如果我们显示教师记录的修改日期：

.. code-block:: xml
    :caption: ``academy/templates.xml``

    <div class="oe_structure">
        <div class="container">
            <h3 t-field="person.name"/>
            <p>最后修改时间: <i t-field="person.write_date"/></p>
            <div t-field="person.biography"/>
        </div>
    </div>

它以非常“计算机化”的方式显示，难以阅读，但我们可以要求显示为人类可读的格式：

.. code-block:: xml
    :caption: ``academy/templates.xml``

    <div class="oe_structure">
        <div class="container">
            <h3 t-field="person.name"/>
            <p>最后修改时间: <i t-field="person.write_date" t-options='{"format": "long"}'/></p>
            <div t-field="person.biography"/>
        </div>
    </div>

或者以相对时间显示：

.. code-block:: xml
    :caption: ``academy/templates.xml``

    <div class="oe_structure">
        <div class="container">
            <h3 t-field="person.name"/>
            <p>最后修改时间: <i t-field="person.write_date" t-options='{"widget": "relative"}'/></p>
            <div t-field="person.biography"/>
        </div>
    </div>

管理与 ERP 集成
================

Odoo 管理的简要介绍
--------------------

我们在 `网站支持`_ 部分简要介绍了 Odoo 管理界面。您可以通过菜单中的 :menuselection:`Administrator --> Administrator` 返回到管理界面（或如果您已退出登录，可以点击 :guilabel:`登录`）。

Odoo 后端的概念结构很简单：

#. 首先是菜单，菜单是一个树结构（菜单可以有子菜单），其中的记录。没有子菜单的菜单映射到……
#. 操作。操作有多种类型：链接、报告、Odoo 应执行的代码或数据展示。数据展示操作称为 *窗口操作*，它告诉 Odoo 显示给定的 *模型* 并根据一组视图展示……
#. 视图有一个类型，一个对应的广泛类别（如列表、图表、日历），并且视图有一个 *架构*，用于自定义模型在视图中的展示方式。
在 Odoo 管理界面中进行编辑
---------------------------

默认情况下，Odoo 模型对用户是不可见的。要使其可见，它必须通过操作进行访问，而操作本身需要通过菜单访问。

让我们为我们的模型创建一个菜单：

.. code-block:: python
    :caption: ``academy/__manifest__.py``

    # 总是加载
    'data': [
        'security/ir.model.access.csv',
        'templates.xml',
        'views.xml',
    ],

.. code-block:: xml
    :caption: ``academy/views.xml``

    <odoo>
        <record id="action_academy_teachers" model="ir.actions.act_window">
            <field name="name">Academy teachers</field>
            <field name="res_model">academy.teachers</field>
        </record>

        <menuitem sequence="0" id="menu_academy" name="Academy"/>
        <menuitem id="menu_academy_content" parent="menu_academy"
                    name="Academy Content"/>
        <menuitem id="menu_academy_content_teachers"
                    parent="menu_academy_content"
                    action="action_academy_teachers"/>
    </odoo>

然后访问 http://localhost:8069/web/，在左上角应该有一个 :guilabel:`Academy` 菜单，默认选择它，因为它是第一个菜单，并打开教师列表。从列表中，您可以 :guilabel:`创建` 新的教师记录，并切换到按记录查看的“表单”视图。

如果没有定义如何呈现记录的视图 (:doc:`view <../reference/user_interface/view_records>`)，Odoo 将自动即时创建一个基本视图。就目前而言，列表视图可以正常工作（只显示教师的姓名），但在表单视图中，HTML ``biography`` 字段与 ``name`` 字段并排显示，且没有足够的空间。让我们定义一个自定义表单视图，以改进查看和编辑教师记录的体验：

.. code-block:: xml
    :caption: ``academy/views.xml``

    <record id="academy_teacher_form" model="ir.ui.view">
        <field name="name">Academy teachers: form</field>
        <field name="model">academy.teachers</field>
        <field name="arch" type="xml">
            <form>
                <sheet>
                    <field name="name"/>
                    <field name="biography"/>
                </sheet>
            </form>
        </field>
    </record>

模型之间的关系
----------------

我们已经看到了存储在记录中的一对“基本”字段。Odoo 提供了 :ref:`许多基本字段 <reference/fields/basic>`。另一类重要字段是 :ref:`关系字段 <reference/fields/relational>`，用于链接记录（无论是在同一模型还是跨模型之间）。

为了演示，让我们创建一个 *课程* 模型。每个课程应该有一个 ``teacher`` 字段，链接到单个教师记录，但每位教师可以教授多门课程：

.. code-block:: python
    :caption: ``academy/models.py``

    class Courses(models.Model):
        _name = 'academy.courses'

        name = fields.Char()
        teacher_id = fields.Many2one('academy.teachers', string="Teacher")

.. code-block:: csv
    :caption: ``academy/security/ir.model.access.csv``

    id,name,model_id:id,group_id:id,perm_read,perm_write,perm_create,perm_unlink
    access_academy_teachers,access_academy_teachers,model_academy_teachers,,1,0,0,0
    access_academy_courses,access_academy_courses,model_academy_courses,,1,0,0,0

我们还可以添加视图，以便查看和编辑课程的教师信息：

.. code-block:: xml
    :caption: ``academy/views.xml``

    <record id="action_academy_courses" model="ir.actions.act_window">
        <field name="name">Academy courses</field>
        <field name="res_model">academy.courses</field>
    </record>
    <record id="academy_course_search" model="ir.ui.view">
        <field name="name">Academy courses: search</field>
        <field name="model">academy.courses</field>
        <field name="arch" type="xml">
            <search>
                <field name="name"/>
                <field name="teacher_id"/>
            </search>
        </field>
    </record>
    <record id="academy_course_list" model="ir.ui.view">
        <field name="name">Academy courses: list</field>
        <field name="model">academy.courses</field>
        <field name="arch" type="xml">
            <tree string="Courses">
                <field name="name"/>
                <field name="teacher_id"/>
            </tree>
        </field>
    </record>
    <record id="academy_course_form" model="ir.ui.view">
        <field name="name">Academy courses: form</field>
        <field name="model">academy.courses</field>
        <field name="arch" type="xml">
            <form>
                <sheet>
                    <field name="name"/>
                    <field name="teacher_id"/>
                </sheet>
            </form>
        </field>
    </record>

    <menuitem sequence="0" id="menu_academy" name="Academy"/>
    <menuitem id="menu_academy_content" parent="menu_academy"
                name="Academy Content"/>
    <menuitem id="menu_academy_content_courses"
                parent="menu_academy_content"
                action="action_academy_courses"/>
    <menuitem id="menu_academy_content_teachers"
                parent="menu_academy_content"
                action="action_academy_teachers"/>

也应该可以直接从教师的页面创建新课程，或者查看他们教授的所有课程，因此请为 *教师* 模型添加 :class:`逆向关系 <odoo.fields.One2many>`：

.. code-block:: python
    :caption: ``academy/models.py``

    class Teachers(models.Model):
        _name = 'academy.teachers'

        name = fields.Char()
        biography = fields.Html()

        course_ids = fields.One2many('academy.courses', 'teacher_id', string="Courses")

    class Courses(models.Model):
        _name = 'academy.courses'

        name = fields.Char()
        teacher_id = fields.Many2one('academy.teachers', string="Teacher")

.. code-block:: xml
    :caption: ``academy/views.xml``

    <record id="academy_teacher_form" model="ir.ui.view">
        <field name="name">Academy teachers: form</field>
        <field name="model">academy.teachers</field>
        <field name="arch" type="xml">
            <form>
                <sheet>
                    <field name="name"/>
                    <field name="biography"/>
                    <field name="course_ids">
                        <tree string="Courses" editable="bottom">
                            <field name="name"/>
                        </tree>
                    </field>
                </sheet>
            </form>
        </field>
    </record>
讨论与通知
-----------------------------

Odoo 提供了一些技术模型，这些模型虽然不会直接满足业务需求，但可以为业务对象增加功能，而无需手动构建它们。

其中一个是 *Chatter* 系统，它是 Odoo 的邮件和消息系统的一部分，可以为任何模型添加通知和讨论线程。模型只需 :attr:`~odoo.models.Model._inherit` ``mail.thread``，并在其表单视图中添加 ``message_ids`` 字段即可显示讨论线程。讨论线程是按记录进行的。

对于我们的学院，允许讨论课程是合理的，例如处理排课变更或教师与助教之间的讨论：

.. code-block:: python
    :caption: ``academy/__manifest__.py``

    'version': '0.1',

    # 使该模块正常运行所需的任何模块
    'depends': ['website', 'mail'],

    # 始终加载
    'data': [

.. code-block:: python
    :caption: ``academy/models.py``

    class Courses(models.Model):
        _name = 'academy.courses'
        _inherit = 'mail.thread'

        name = fields.Char()
        teacher_id = fields.Many2one('academy.teachers', string="Teacher")

.. code-block:: xml
    :caption: ``academy/views.xml``

    <record id="academy_course_form" model="ir.ui.view">
        <field name="name">Academy courses: form</field>
        <field name="model">academy.courses</field>
        <field name="arch" type="xml">
            <form>
                <sheet>
                    <field name="name"/>
                    <field name="teacher_id"/>
                </sheet>
                <div class="oe_chatter">
                    <field name="message_follower_ids" widget="mail_followers"/>
                    <field name="message_ids" widget="mail_thread"/>
                </div>
            </form>
        </field>
    </record>

现在，每个课程表单的底部都有一个讨论线程，系统用户可以在其中留下消息，并关注或取消关注与特定课程相关的讨论。

销售课程
---------------

Odoo 还提供了更直接满足业务需求的业务模型。例如，``website_sale`` 模块基于 Odoo 系统中的产品建立了一个电子商务网站。我们可以通过将课程作为特定类型的产品来轻松销售课程订阅。

与之前的经典继承不同，这意味着用 *产品* 模型替换 *课程* 模型，并在原位扩展产品（以添加我们需要的内容）。

首先，我们需要添加对 ``website_sale`` 的依赖，以便我们同时获得产品（通过 ``sale``）和电子商务界面：

.. code-block:: python
    :caption: ``academy/__manifest__.py``

    'version': '0.1',

    # 使该模块正常运行所需的任何模块
    'depends': ['mail', 'website_sale'],

    # 始终加载
    'data': [

重启 Odoo，更新模块，现在网站上有一个 :guilabel:`商店` 部分，列出了通过演示数据预填的产品。

第二步是用 ``product.template`` 替换 *课程* 模型，并为课程添加一个新的产品类别：

.. code-block:: python
    :caption: ``academy/__manifest__.py``

        'security/ir.model.access.csv',
        'templates.xml',
        'views.xml',
        'data.xml',
    ],
    # 仅在演示模式下加载
    'demo': [

.. code-block:: xml
    :caption: ``academy/data.xml``

    <odoo>
        <record model="product.public.category" id="category_courses">
            <field name="name">Courses</field>
            <field name="parent_id" ref="website_sale.categ_others"/>
        </record>
    </odoo>

.. code-block:: xml
    :caption: ``academy/demo.xml``

    <record id="course0" model="product.template">
        <field name="name">Course 0</field>
        <field name="teacher_id" ref="padilla"/>
        <field name="public_categ_ids" eval="[(4, ref('academy.category_courses'), False)]"/>
        <field name="website_published">True</field>
        <field name="list_price" type="float">0</field>
        <field name="type">service</field>
    </record>
    <record id="course1" model="product.template">
        <field name="name">Course 1</field>
        <field name="teacher_id" ref="padilla"/>
        <field name="public_categ_ids" eval="[(4, ref('academy.category_courses'), False)]"/>
        <field name="website_published">True</field>
        <field name="list_price" type="float">0</field>
        <field name="type">service</field>
    </record>
    <record id="course2" model="product.template">
        <field name="name">Course 2</field>
        <field name="teacher_id" ref="vaughn"/>
        <field name="public_categ_ids" eval="[(4, ref('academy.category_courses'), False)]"/>
        <field name="website_published">True</field>
        <field name="list_price" type="float">0</field>
        <field name="type">service</field>
    </record>


.. code-block:: python
    :caption: ``academy/models.py``

    class Courses(models.Model):
        _name = 'academy.courses'
        _inherit = ['mail.thread', 'product.template']

        name = fields.Char()
        teacher_id = fields.Many2one('academy.teachers', string="Teacher")

安装此模块后，商店中现在提供了一些课程，尽管可能需要进行搜索才能找到它们。

.. note::

    * 要在原位扩展模型，可以 :attr:`继承 <odoo.models.Model._inherit>`，而不为其赋予新的 :attr:`~odoo.models.Model._name`
    * ``product.template`` 已经使用了讨论系统，因此我们可以从扩展模型中移除它
    * 我们默认将课程创建为 *已发布*，因此不需要登录即可查看它们
修改现有视图
-----------------------

到目前为止，我们已经简要了解了以下内容：

* 创建新模型
* 创建新视图
* 创建新记录
* 修改现有模型

现在我们要了解修改现有记录和修改现有视图。我们将在 :guilabel:`商店` 页面上进行这两个操作。

视图的修改是通过创建 *扩展* 视图来完成的，这些扩展视图应用在原始视图之上，对其进行修改。这些修改视图可以在不修改原始视图的情况下添加或移除，这使得尝试新功能和回滚更改更加容易。

由于我们的课程是免费的，因此没有理由在商店页面上显示其价格，因此我们将修改视图并在价格为 0 时隐藏价格。第一步是找出显示价格的视图，可以通过 :menuselection:`自定义 --> HTML 编辑器` 来查看呈现页面的各种模板。查看几个模板后，"Product item" 看起来是个很可能的模板。

视图架构的修改分三步完成：

#. 创建一个新视图
#. 通过将新视图的 ``inherit_id`` 设置为要修改视图的外部 ID，扩展该视图
#. 在架构中，使用 ``xpath`` 标签选择并修改要修改视图中的元素

.. code-block:: xml
   :caption: ``academy/templates.xml``

    <template id="product_item_hide_no_price" inherit_id="website_sale.products_item">
        <xpath expr="//div[hasclass('product_price')]/b" position="attributes">
            <attribute name="t-if">product.price &gt; 0</attribute>
        </xpath>
    </template>

第二个我们要更改的是使产品类别侧边栏默认可见：:menuselection:`自定义 --> 产品类别` 允许你切换一个产品类别树（用于过滤主显示）开关。

这是通过扩展模板的 ``customize_show`` 和 ``active`` 字段完成的：扩展模板（例如我们刚创建的那个）可以设置为 *customize_show=True*。此选项将在 :guilabel:`自定义` 菜单中显示该视图的复选框，允许管理员激活或禁用它们（并轻松定制网站页面）。

我们只需修改 *产品类别* 记录并将其默认设置为 *active="True"*：

.. code-block:: xml
    :caption: ``academy/templates.xml``

    <record id="website_sale.products_categories" model="ir.ui.view">
        <field name="active" eval="True"/>
    </record>

有了这个，当 *Academy* 模块安装时，*产品类别* 侧边栏将自动启用。
