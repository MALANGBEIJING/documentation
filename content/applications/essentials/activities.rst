==========
活动
==========

.. |clock| replace:: :icon:`fa-clock-o` :guilabel:`(时钟)` 图标

*活动* 是绑定到 Odoo 数据库中记录的后续任务。

.. _activities/important:

显示活动的图标会根据 :ref:`活动类型 <activities/types>` 变化：

- :icon:`fa-clock-o` :guilabel:`(时钟)` 图标：默认活动图标。
- :icon:`fa-phone` :guilabel:`(电话)` 图标：安排了电话。
- :icon:`fa-envelope` :guilabel:`(信封)` 图标：安排了电子邮件。
- :icon:`fa-check` :guilabel:`(勾选)` 图标：安排了“待办”任务。
- :icon:`fa-users` :guilabel:`(人员)` 图标：安排了会议。
- :icon:`fa-upload` :guilabel:`(上传)` 图标：安排了文件上传。
- :icon:`fa-pencil-square-o` :guilabel:`(请求签名)` 图标：安排了签名请求。

安排活动
===================

可以在包含 :ref:`聊天 <activities/chatter>` 线程、:ref:`看板视图 <activities/kanban>`、:ref:`列表视图 <activities/list>` 或应用的 :ref:`活动视图 <activities/activity>` 的数据库页面上安排活动。

.. _activities/chatter:

聊天
-------

可以从任何记录的聊天中创建活动。

要安排新活动，点击聊天顶部的 :guilabel:`活动` 按钮。在出现的 :guilabel:`安排活动` 弹出窗口中，:ref:`填写安排活动表单 <activities/form>`。

.. image:: activities/chatter.png
   :align: center
   :alt: 新活动类型表单。

.. _activities/kanban:

看板视图
-----------

也可以从 :icon:`oi-view-kanban` :guilabel:`(看板)` 视图创建活动。

为此，点击位于单个记录底部的 |clock|。

点击 :guilabel:`+ 安排活动`，然后继续 :ref:`填写安排活动表单 <activities/form>`。

.. image:: activities/schedule-kanban-activity.png
   :align: center
   :alt: CRM 管道的看板视图和安排活动的选项。

.. note::
   如果记录已经有安排的活动，则 |clock| 会被现有活动的图标替代。点击活动类型的图标可以安排另一个活动。

.. _activities/list:

列表视图
---------

也可以从 :icon:`oi-view-list` :guilabel:`(列表)` 视图创建活动。

如果 :guilabel:`活动` 列被隐藏，使用第一行右侧的 :icon:`oi-settings-adjust` :guilabel:`(设置调整)` 图标将其显示出来。

然后，点击要为其添加活动的记录的 |clock|，然后点击 :guilabel:`+ 安排活动`。继续 :ref:`填写安排活动表单 <activities/form>`。

.. note::
   如果记录已经有安排的活动，则 |clock| 会被现有活动的图标替代。点击活动类型的图标可以安排另一个活动。

.. image:: activities/schedule-list-activity.png
   :align: center
   :alt: CRM 管道的列表视图和安排活动的选项。

.. _activities/activity:

活动视图
-------------

Odoo 中的大多数应用都有 *活动* 视图。如果有，主菜单栏右上角的视图选项图标中会显示一个 |clock|。

要打开活动视图，请点击 |clock|。

.. image:: activities/activities.png
   :align: center
   :alt: 右上菜单中的活动图标。

在此视图中，所有可用的活动都列在列中，而水平条目表示所有的单个记录。

显示绿色的活动未来到期，显示橙色的活动今天到期，而显示红色的活动已过期。

每列中的颜色条代表特定活动类型的记录，并显示一个数字，指示为该类型安排的活动数量。

如果为记录安排了多种活动类型，则框中会显示一个数字，指示安排的活动总数。

.. note::
   活动颜色及其与活动到期日的关系在 Odoo 中是始终一致的，无论活动类型或视图。

要为记录安排活动，悬停在相应字段上。点击出现的 :icon:`fa-plus` :guilabel:`(加号)` 图标，然后 :ref:`填写安排活动表单 <activities/form>`。

.. image:: activities/activity-view.png
   :align: center
   :alt: CRM 管道的活动视图和安排活动的选项。

.. _activities/form:

安排活动表单
----------------------

可以从许多不同的地方安排活动，例如从记录的 :ref:`聊天 <activities/chatter>` 或应用中的多个视图之一：:ref:`看板视图 <activities/kanban>`、:ref:`列表视图 <activities/list>` 或 :ref:`活动视图 <activities/activity>`。

在表单中输入以下信息：

- :guilabel:`活动类型`: 从下拉菜单中选择活动类型。默认选项有：:guilabel:`电子邮件`、:guilabel:`电话`、:guilabel:`会议` 或 :guilabel:`待办`。根据安装的其他应用，可能会有其他选项。
- :guilabel:`摘要`: 输入活动的简短标题，例如“讨论提案”。
- :guilabel:`到期日`: 使用日历弹出窗口选择活动的截止日期。
- :guilabel:`分配给`: 默认情况下，该字段会填写当前用户。要将活动分配给其他用户，请从下拉菜单中选择。
- :guilabel:`备注`: 在此字段中添加活动的任何其他信息。

当 :guilabel:`安排活动` 弹出窗口填写完毕后，点击以下按钮之一：

- :guilabel:`打开日历`: 打开用户的日历以添加并安排活动。

  点击活动的期望日期和时间，将出现一个 :guilabel:`新事件` 弹出窗口。*安排活动* 弹出窗口的摘要会填充 :guilabel:`标题` 字段。

  在 :guilabel:`新事件` 弹出窗口中输入信息，然后点击 :guilabel:`保存并关闭` 以安排活动。安排后，活动将添加到聊天的 :guilabel:`计划活动` 部分。

  .. important::
    只有当 :guilabel:`活动类型` 设置为 :guilabel:`电话` 或 :guilabel:`会议` 时，才会出现 :guilabel:`打开日历` 按钮。

- :guilabel:`安排`: 安排活动，并将活动添加到聊天的 :guilabel:`计划活动` 部分。
- :guilabel:`安排并标记为完成`: 将活动的详细信息添加到聊天的 :guilabel:`今天` 部分。活动不会安排，并自动标记为已完成。
- :guilabel:`完成并安排下一个`: 将活动的详细信息添加到聊天的 :guilabel:`今天` 部分。活动不会安排，并自动标记为已完成，同时会弹出新的 :guilabel:`安排活动` 弹出窗口。
- :guilabel:`取消`: 放弃在 :guilabel:`安排活动` 弹出窗口中所做的任何更改。

.. image:: activities/schedule-pop-up.png
   :align: center
   :alt: 查看 CRM 线索并安排活动的选项。

.. _activities/all:

所有安排的活动
========================

要查看按应用程序组织的活动列表，请点击右上角的 |clock|。

如果有安排的活动，|clock| 上的红色气泡会显示活动数量。

每个应用程序的所有活动会进一步分为子部分，指示活动在应用程序中的完成位置。每个子部分列出了 :guilabel:`逾期`、:guilabel:`今天` 和 :guilabel:`未来` 安排的活动数量。

.. example::
   在 *休假* 应用中，安排了一个活动需要在 *所有休假请求* 仪表板中完成，六个活动需要在 *分配* 仪表板中完成。

   这些请求出现在所有活动下拉菜单中的两个单独列表中：一个标为 `休假`，另一个标为 `休假分配`。

   .. image:: activities/activities-menu.png
      :align: center
      :alt: 从主菜单栏访问的活动列表。高亮显示了休假应用的两个条目。

请求文件
------------------

在 :ref:`所有安排的活动 <activities/all>` 列表的底部可以看到 :guilabel:`请求文件` 选项。点击 :guilabel:`请求文件`，会弹出一个 :guilabel:`请求文件` 窗口。

在表单中输入以下信息：

- :guilabel:`文件名称`: 输入请求的文件的名称。
- :guilabel:`请求人`: 从下拉菜单中选择请求文件的用户。
- :guilabel:`到期时间`: 输入一个数字值，表示文件到期的时间。此字段旁边显示 :guilabel:`天` 字段。点击默认选项 :guilabel:`天`，显示下拉菜单。从列表中选择所需的时间框架选项。选项有 :guilabel:`天`、:guilabel:`周` 或 :guilabel:`月`。
- :guilabel:`工作区`: 使用下拉菜单，选择文件要上传到的特定 :ref:`工作区 <documents/workspaces>`。
- :guilabel:`标签`: 从下拉菜单中选择任何所需的标签。显示的可用标签基于为所选 :guilabel:`工作区` 配置的标签。
- :guilabel:`信息`: 在此字段中输入以澄清文件请求的消息。

完成所有字段后，点击 :guilabel:`请求` 发送文件请求。

.. image:: activities/request-doc.png
   :align: center
   :alt: 请求文件表单，所有字段都已填写以请求合同。

.. _activities/types:

活动类型
==============

要查看当前配置的数据库中的活动类型，请导航到 :menuselection:`设置应用 --> 讨论部分 --> 活动设置 --> 活动类型`。

.. image:: activities/settings-activities-types.png
   :align: center
   :alt: 设置应用中讨论部分的活动类型按钮。

这样会显示 :guilabel:`活动类型` 页面，在那里可以找到现有的活动类型。

.. tip::
   各个应用程序有专门为其应用程序提供的 *活动类型* 列表。例如，要查看和编辑 *CRM* 应用的可用活动，请转到 :menuselection:`CRM 应用 --> 配置 --> 活动类型`。

.. image:: activities/activity-list.png
   :align: center
   :alt: 已配置和可用的活动类型列表。

编辑活动类型
-------------------

要编辑现有的 :ref:`活动类型 <activities/types>`，点击该活动类型，活动类型表单将加载。

对活动类型表单进行任何所需的更改。表单会自动保存，但可以随时手动保存，点击位于页面左上角的 :guilabel:`手动保存` 选项，表示为 :icon:`fa-cloud-upload` :guilabel:`(云上传)` 图标。

创建新活动类型
-------------------------

要创建新的 :ref:`活动类型 <activities/types>`，从 :guilabel:`活动类型` 页面点击 :guilabel:`新建`，加载空白的活动类型表单。

在表单顶部为活动类型输入一个 :guilabel:`名称`，然后在表单上输入以下信息。

活动设置部分
~~~~~~~~~~~~~~~~~~~~~~~~~

- :guilabel:`操作`: 使用下拉菜单，选择与此新活动类型关联的操作。某些操作在安排活动后会触发特定行为，例如：

  - :guilabel:`上传文件`: 如果选择，将自动在计划活动的聊天中添加上传文件的链接。
  - :guilabel:`电话` 或 :guilabel:`会议`: 如果选择，用户可以选择打开日历以选择活动的日期和时间。
  - :guilabel:`请求签名`: 如果选择，将自动在计划活动的聊天中添加一个打开签名请求弹出窗口的链接。这需要安装 Odoo *签名* 应用。

  .. note::
     可用的活动类型取决于数据库中安装的应用程序。

- :guilabel:`文件夹`: 选择要保存文件到的特定 :ref:`工作区 <documents/workspaces>` 文件夹。仅当为 :guilabel:`操作` 选择了 :guilabel:`上传文件` 时，才会显示此字段。

  使用下拉菜单，选择保存文件的 :guilabel:`文件夹`。

- :guilabel:`默认用户`: 从下拉菜单中选择一个用户，在安排此活动类型时自动将该活动分配给该用户。如果此字段留空，则将活动分配给创建活动的用户。
- :guilabel:`默认摘要`: 输入每次创建此活动类型时包含的备注。

  .. note::
     在创建活动时会包含 :guilabel:`默认用户` 和 :guilabel:`默认摘要` 字段中的信息。不过，在安排或保存活动之前可以进行更改。

- :guilabel:`保留完成`: 勾选此复选框，以使标记为 `完成` 的活动在 :ref:`活动视图 <activities/activity>` 中可见。
- :guilabel:`默认备注`: 输入要与活动一起显示的任何备注。

下一个活动部分
~~~~~~~~~~~~~~~~~~~~~

可以建议或触发另一个活动。为此，请配置 :guilabel:`下一个活动` 部分。

- :guilabel:`链接类型`: 从下拉菜单中选择 :guilabel:`建议下一个活动` 或 :guilabel:`触发下一个活动`。根据选择的选项，将显示 :guilabel:`建议` 或 :guilabel:`触发` 字段。

  .. note::
     如果为 :guilabel:`操作` 选择了 :guilabel:`上传文件`，则不会显示 :guilabel:`链接类型` 字段。

- :guilabel:`建议/触发`: 根据为 :guilabel:`链接类型` 选择的内容，该字段会显示 :guilabel:`建议` 或 :guilabel:`触发`。使用下拉菜单，选择要作为活动类型后续任务推荐或安排的活动。
- :guilabel:`安排`: 配置建议或触发下一个活动的时间。

  首先，输入一个数字值，指示建议或触发活动的时间。

  此字段旁边显示 :guilabel:`天` 字段。点击默认选项 :guilabel:`天`，显示下拉菜单。从列表中选择所需的时间框架选项。选项有 :guilabel:`天`、:guilabel:`周` 或 :guilabel:`月`。

  最后，使用下拉菜单，选择活动是 :guilabel:`在上一个活动截止日期后` 还是 :guilabel:`在完成日期后` 安排或触发。

.. image:: activities/new-activity.png
   :align: center
   :alt: 填写了所有字段的新活动表单。

.. seealso::
   - :doc:`../productivity/discuss`
   - :doc:`../productivity/discuss/team_communication`
   - :doc:`../sales/crm/optimize/utilize_activities`
