====================
维护日历
====================

.. |MO| replace:: :abbr:`MO (制造订单)`

避免设备故障和仓库工作中心的停滞需要持续的设备维护。对突然故障的机器和工具进行及时的纠正性维护，以及为避免此类问题进行预防性维护，是保持仓库运作顺利的关键。

在 Odoo *维护* 模块中，用户可以访问 *维护日历*，创建、安排和编辑纠正性和预防性维护请求，以确保设备和工作中心的正常运行。

创建维护请求
==========================

维护请求可以直接在 *维护日历* 中创建。要访问日历，请前往 :menuselection:`维护应用 --> 维护 --> 维护日历`。

要创建新请求，点击日历上的任意位置。这样会弹出一个 :guilabel:`新事件` 弹窗。在 :guilabel:`名称:` 字段中，为新请求指定标题。

.. image:: maintenance_calendar/maintenance-calendar-new-event-popup.png
   :align: center
   :alt: 新事件创建弹窗。

点击弹窗中的 :guilabel:`创建` 按钮可以保存新请求而无需填写其他细节。如果需要取消请求的创建，点击 :guilabel:`取消`。

要添加更多详细信息并为请求安排特定的日期和时间，点击 :guilabel:`编辑`。

点击 :guilabel:`编辑` 会打开一个空白的维护请求表单，在这里可以填写有关请求的各种详细信息。

编辑维护请求
------------------------

在 :guilabel:`请求` 字段中，为新请求指定一个标题。在 :guilabel:`创建者` 字段中，从下拉菜单中选择请求的创建者。默认情况下，该字段会填充实际创建请求的用户。

.. image:: maintenance_calendar/maintenance-calendar-new-request-form.png
   :align: center
   :alt: 新维护请求表单创建。

在 :guilabel:`为` 字段中，从下拉菜单中选择该请求是为 :guilabel:`设备` 还是 :guilabel:`工作中心` 创建的。

.. note::
   如果在 :guilabel:`为` 字段的下拉菜单中选择了 :guilabel:`工作中心`，表单中将出现两个额外的字段：:guilabel:`工作中心` 和 :guilabel:`阻止工作中心`。

   在 :guilabel:`工作中心` 字段中，选择该维护请求适用的仓库中的工作中心。

   如果勾选了 :guilabel:`阻止工作中心` 选项，则在执行该请求期间，无法在此工作中心计划工作订单或其他维护请求。

如果在 :guilabel:`为` 字段中选择了 :guilabel:`设备`（默认情况下为此选项），请从 :guilabel:`设备` 字段中选择需要维护的机器或工具。一旦选择了特定的设备，会显示一个灰色的 :guilabel:`类别` 字段，列出设备所属的 *设备类别*。

在 :guilabel:`工作表模板` 字段中，如果需要，点击下拉菜单选择一个工作表模板。这些模板是可由执行维护的员工填写的自定义模板。

在 :guilabel:`类别` 字段下，:guilabel:`请求日期` 字段显示了请求进行维护的日期。

:guilabel:`维护类型` 字段提供了两个可选的单选按钮选项：:guilabel:`纠正性` 和 :guilabel:`预防性`。

:guilabel:`纠正性` 维护是针对诸如设备故障等即时需求而发出的请求，而 :guilabel:`预防性` 维护是计划中的请求，以避免未来的故障。

如果该请求与特定的 |MO| 相关，请从 :guilabel:`制造订单` 字段中选择该 |MO|。

从 :guilabel:`团队` 字段的下拉菜单中，选择负责执行维护的团队。在 :guilabel:`负责人` 字段中，选择负责该请求的技术人员。

.. image:: maintenance_calendar/maintenance-calendar-filled-out-form.png
   :align: center
   :alt: 填写完成的维护请求表单详细信息。

在 :guilabel:`计划日期` 字段中，点击日期以打开日历弹窗。从弹窗中选择维护的计划日期，然后点击 :guilabel:`应用` 保存日期。

在 :guilabel:`持续时间` 字段中，输入计划进行维护的小时数（格式为 `00:00`）。

在 :guilabel:`优先级` 字段中，选择一个优先级，介于一到三颗 :guilabel:`⭐⭐⭐ (星)` 之间。这表示维护请求的重要性。

如果是在多公司环境中操作，请从 :guilabel:`公司` 字段的下拉菜单中选择该维护请求所属的公司。

在表单的底部有两个选项卡：:guilabel:`备注` 和 :guilabel:`指示`。

在 :guilabel:`备注` 选项卡中，如果需要，可以为分配到请求的团队或技术人员输入任何内部备注。

在 :guilabel:`指示` 选项卡中，如果需要，选择三个单选按钮中的一个，为分配的团队或技术人员提供维护指示。可用的指示方法包括 :guilabel:`PDF`、:guilabel:`Google 幻灯片` 或 :guilabel:`文本`。

.. image:: maintenance_calendar/maintenance-calendar-instructions-tab.png
   :align: center
   :alt: 维护请求表单上的指示选项卡选项。

日历元素
=================

*维护日历* 提供了多种视图、搜索功能和过滤器，帮助跟踪正在进行的和计划中的维护请求的进度。

以下部分描述了日历中各视图的元素。

过滤器和收藏
---------------------

要访问维护日历，请前往 :menuselection:`维护应用 --> 维护 --> 维护日历`。

要添加和删除 *维护日历* 上的数据过滤器，请点击页面顶部搜索栏右侧的 :guilabel:`🔻 (向下三角形)` 图标。

结果下拉菜单的左侧列出了用户可以选择的所有不同的 :guilabel:`过滤器`。默认情况下，:guilabel:`待办` 和 :guilabel:`活动` 被选中，因此显示所有打开的请求。

.. tip::
   要为 *维护日历* 添加自定义过滤器，点击下拉菜单中 :guilabel:`过滤器` 部分下的 :guilabel:`添加自定义过滤器`。这样会打开一个 :guilabel:`添加自定义过滤器` 弹窗。

   从此弹窗中，配置新过滤器规则的属性。准备就绪后，点击 :guilabel:`添加`。

结果下拉菜单的右侧列出了 :guilabel:`收藏`，即已保存为收藏的搜索，供以后重新访问。

.. image:: maintenance_calendar/maintenance-calendar-favorites-popover.png
   :align: center
   :alt: 过滤器下拉菜单中的收藏部分。

要保存新的 :guilabel:`收藏` 搜索，选择所需的 :guilabel:`过滤器`。然后，点击 :guilabel:`保存当前搜索`。在 :guilabel:`保存当前搜索` 直接下方的字段中，为搜索指定一个名称。

在分配的名称下方，有两个选项可以将当前搜索保存为 :guilabel:`默认过滤器` 或 :guilabel:`共享过滤器`。

选择 :guilabel:`默认过滤器`，将此过滤器设置为打开此日历视图时的默认过滤器。

选择 :guilabel:`共享过滤器` 将此过滤器共享给其他用户。

准备就绪后，点击 :guilabel:`保存`。点击后，新的 :guilabel:`收藏` 过滤器将出现在 :guilabel:`收藏` 列中，并且过滤器名称旁边的搜索栏中会出现一个 :guilabel:`⭐ (金色星星)` 图标。

视图
-----

:guilabel:`维护日历` 有六种不同的视图：:guilabel:`日历`（默认）、:guilabel:`看板`、:guilabel:`列表`、:guilabel:`数据透视表`、:guilabel:`图表` 和 :guilabel:`活动`。

.. image:: maintenance_calendar/maintenance-calendar-view-type-icons.png
   :align: center
   :alt: 维护日历的不同视图类型图标。

日历视图
~~~~~~~~~~~~~

:guilabel:`日历` 是打开 :guilabel:`维护日历` 时默认显示的视图。此视图类型中有多个选项可用于排序和分组有关维护请求的信息。

在页面左上角，默认情况下，下拉菜单设置为 :guilabel:`周`。点击该下拉菜单可显示日历可查看的不同时间段：:guilabel:`天`、:guilabel:`月` 和 :guilabel:`年`。还有一个 :guilabel:`显示周末` 选项，默认情况下被选中。如果取消选中，则不显示周末。
.. image:: maintenance_calendar/maintenance-calendar-period-dropdown.png
   :align: center
   :alt: 日历周期下拉菜单选项.

在这个菜单的左侧，有一个 :guilabel:`⬅️ (左箭头)` 图标和一个 :guilabel:`➡️ (右箭头)` 图标。点击这些箭头可以分别向前或向后移动日历。

在默认设置为 :guilabel:`Week` 的下拉菜单右侧，有一个 :guilabel:`Today` 按钮。点击此按钮将日历重置为显示今天的日期，无论之前查看的是哪个时间点。

在页面的最右侧是一个侧边栏，包含一个设置为今天日期的缩略日历，以及一个 :guilabel:`Technician` 列表，显示所有当前打开请求的技术人员。点击此侧边栏顶部的 :guilabel:`(panel)` 图标可以打开或关闭侧边栏。

.. note::
   :guilabel:`Technician` 列表仅在技术人员被分配到打开的请求时显示，并且只有当某个技术人员至少负责一个维护请求时，才会在列表中显示该技术人员。

看板视图
~~~~~~~~~~~

在 :guilabel:`看板视图` 中，所有打开的维护请求以看板风格的列显示，按照维护流程中的各个阶段展示。

每个维护请求都会以任务卡片的形式出现，且每张任务卡片可以被拖放到看板管道的不同阶段。

每列都有一个名称（例如 :guilabel:`In Progress`）。将鼠标悬停在列的顶部会显示一个 :guilabel:`⚙️ (齿轮)` 图标。点击 :guilabel:`⚙️ (齿轮)` 图标会显示该列的选项列表：:guilabel:`Fold`，:guilabel:`Edit`，:guilabel:`Automations`，以及 :guilabel:`Delete`。

.. image:: maintenance_calendar/maintenance-calendar-kanban-column.png
   :align: center
   :alt: 看板视图列中的阶段选项.

点击 :guilabel:`Fold` 可以折叠该列以隐藏其内容。

点击 :guilabel:`Edit` 会弹出 :guilabel:`Edit: (阶段名称)` 窗口，允许编辑该列的详细信息。以下是可以编辑的列选项：

.. image:: maintenance_calendar/maintenance-calendar-edit-stage-popup.png
   :align: center
   :alt: 编辑进行中的弹出窗口.

- :guilabel:`Name`: 看板管道中阶段的名称。
- :guilabel:`Folded in Maintenance Pipe`: 当勾选此项时，此阶段的列在 :guilabel:`看板` 视图类型中默认是折叠的。
- :guilabel:`Request Confirmed`: 当未勾选此框时，如果维护请求类型设置为工作中心，则创建维护请求时不会为相应的工作中心创建请假。若勾选此框，系统会自动在列出的时间内阻止该工作中心，无论是在指定日期，还是尽可能快地执行，如果工作中心不可用。
- :guilabel:`Sequence`: 维护流程中的顺序，决定此阶段的显示位置。
- :guilabel:`Request Done`: 如果勾选此框，表示此阶段为维护流程的最终步骤，移动到此阶段的请求会被关闭。

完成后，点击 :guilabel:`Save & Close` 保存并关闭。如果没有进行任何更改，点击 :guilabel:`Discard` 或点击 :guilabel:`X` 图标关闭弹出窗口。

列表视图
~~~~~~~~~

在 :guilabel:`列表视图` 中，所有打开的维护请求会以列表形式显示，并显示每个请求的相关信息。

此视图类型中显示的信息列如下：

- :guilabel:`Subjects`: 分配给维护请求的名称。
- :guilabel:`Employee`: 创建维护请求的员工。
- :guilabel:`Technician`: 负责维护请求的技术人员。
- :guilabel:`Category`: 设备维修所属的类别。
- :guilabel:`Stage`: 维护请求当前所在的阶段。
- :guilabel:`Company`: 如果是多公司环境，请求所属的公司。

数据透视表视图
~~~~~~~~~~

在 :guilabel:`数据透视表视图` 中，维护请求会以数据透视表的形式显示，并且可以自定义显示不同的数据指标。

要向数据透视表中添加更多数据，请点击 :guilabel:`Measures` 按钮以显示下拉菜单。默认情况下，选中 :guilabel:`Count`。还可以选择其他选项，如 :guilabel:`Additional Leaves to Plan Ahead`，:guilabel:`Duration`，和 :guilabel:`Repeat Every`。

.. image:: maintenance_calendar/maintenance-calendar-measures-menu.png
   :align: center
   :alt: 数据透视表页面上的测量选项。

在 :guilabel:`Measures` 按钮的右侧是 :guilabel:`Insert in Spreadsheet` 按钮。点击此按钮会弹出一个标题为 :guilabel:`选择要插入数据透视表的电子表格。` 的弹窗。

弹窗中有两个标签页：:guilabel:`电子表格` 和 :guilabel:`仪表盘`。点击其中一个标签页，然后选择数据库中的电子表格或仪表盘，将数据透视表添加到其中。准备好后，点击 :guilabel:`Confirm`。如果不想将此表格添加到电子表格或仪表盘中，点击 :guilabel:`Cancel` 或点击 :guilabel:`X` 图标关闭弹窗。

在 :guilabel:`Insert in Spreadsheet` 按钮的右侧有三个按钮：

- :guilabel:`Flip axis`: 翻转数据透视表的数据轴。
- :guilabel:`Expand all`: 完全展开数据透视表中的所有行和列。
- :guilabel:`Download xlsx`: 将数据透视表下载为 .xlsx 文件。

图形视图
~~~~~~~~~~

选择图形视图后，在搜索栏与数据可视化之间会显示以下选项。这些特定于图形的选项位于 :guilabel:`Measures` 和 :guilabel:`Insert in Spreadsheet` 按钮的右侧。

.. image:: maintenance_calendar/maintenance-calendar-graph-view-icons.png
   :align: center
   :alt: 图形视图页面上的图形类型图标。

用户可以选择三种不同类型的图形来查看数据：

- :guilabel:`Bar Chart`: 数据以条形图显示。
- :guilabel:`Line Chart`: 数据以折线图显示。
- :guilabel:`Pie Chart`: 数据以饼图显示。

当以 :guilabel:`Bar Chart`（条形图）方式查看数据时，可以用以下方式格式化数据：

- :guilabel:`Stacked`: 数据以堆叠方式显示。
- :guilabel:`Descending`: 数据按降序显示。
- :guilabel:`Ascending`: 数据按升序显示。

当以 :guilabel:`Line Chart`（折线图）方式查看数据时，可以用以下方式格式化数据：

- :guilabel:`Stacked`: 数据以堆叠方式显示。
- :guilabel:`Cumulative`: 数据累积增加。
- :guilabel:`Descending`: 数据按降序显示。
- :guilabel:`Ascending`: 数据按升序显示。

当以 :guilabel:`Pie Chart`（饼图）方式查看数据时，所有相关数据都会默认显示，且没有其他额外的格式选项。

活动视图
~~~~~~~~~

选择 :guilabel:`活动视图` 后，所有打开的维护请求都会以行显示，并可以为这些请求安排活动。

.. image:: maintenance_calendar/maintenance-calendar-activity-view-type.png
   :align: center
   :alt: 活动视图中的维护请求。

维护请求在 :guilabel:`维护请求` 列中以活动的形式显示。点击某个请求会打开 :guilabel:`维护请求` 弹窗，显示请求的状态以及负责的技术人员。要直接从弹窗中安排活动，点击 :guilabel:`➕ 安排活动`。这会打开一个 :guilabel:`安排活动` 弹窗。

在弹窗中，选择 :guilabel:`活动类型`，提供一个 :guilabel:`概要`，安排一个 :guilabel:`截止日期`，并在 :guilabel:`分配给` 字段中选择负责用户。

.. image:: maintenance_calendar/maintenance-calendar-schedule-activity-popover.png
   :align: center
   :alt: 安排活动弹窗。

在灰色的 :guilabel:`记录备注...` 字段下方的空白处输入活动的额外备注。点击时，该字段会变为 :guilabel:`输入"/"以获取命令`。

准备就绪后，点击 :guilabel:`安排` 来安排活动。或者，点击 :guilabel:`安排并标记为完成` 关闭活动，点击 :guilabel:`完成并安排下一个` 关闭活动并打开一个新活动，或点击 :guilabel:`取消` 取消活动。

在选择 :guilabel:`活动视图` 后，每种活动类型都会以自己的列显示。这些列包括 :guilabel:`电子邮件`，:guilabel:`电话`，:guilabel:`会议`，:guilabel:`维护请求`，:guilabel:`待办`，:guilabel:`上传文档`，:guilabel:`请求签名`，以及 :guilabel:`授予批准`。

要为某一特定活动类型安排活动，点击该维护请求的对应行中的任意空白框，点击 :guilabel:`➕ (加号)` 图标。这会打开一个 :guilabel:`Odoo` 弹窗，在其中可以安排活动。

.. image:: maintenance_calendar/maintenance-calendar-odoo-activity-popup.png
   :align: center
   :alt: Odoo 弹窗安排活动窗口。

.. seealso::
   - :doc:`维护请求`
   - :doc:`添加新设备`
