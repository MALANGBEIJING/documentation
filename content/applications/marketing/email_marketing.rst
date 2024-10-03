:show-content:

===============
电子邮件营销
===============

Odoo *电子邮件营销* 应用程序提供了拖放设计工具、预构建模板以及其他互动功能，帮助创建引人入胜的电子邮件营销活动。*电子邮件营销* 应用程序还提供了详细的报告指标，以跟踪活动的整体效果。

.. seealso::
   `Odoo 教程：电子邮件营销
   <https://www.odoo.com/slides/slide/email-marketing-essentials-989?fullscreen=1>`_

.. cards::

   .. card:: 邮件列表
      :target: email_marketing/mailing_lists

      将联系人分组到特定的邮件列表中。

   .. card:: 管理退订（黑名单）
      :target: email_marketing/unsubscriptions

      允许收件人退订并列入未来邮件的黑名单。

   .. card:: 重新激活丢失线索的邮件
      :target: email_marketing/lost_leads_email

      使用电子邮件营销定位丢失的潜在客户。

   .. card:: 分析指标
      :target: email_marketing/analyze_metrics

      分析营销活动的指标。

电子邮件营销仪表板
=========================

安装应用程序后，点击 Odoo 主仪表板中的 :menuselection:`电子邮件营销` 应用图标。这将显示默认列表视图中的主 :guilabel:`邮件` 仪表板。

.. image:: email_marketing/mailings-dashboard.png
   :align: center
   :alt: Odoo 电子邮件营销应用程序的主仪表板视图。

在搜索栏中，默认的过滤器 :guilabel:`我的邮件` 会显示当前用户相关的所有邮件。要移除该过滤器，请点击搜索栏中的 :guilabel:`✖️（移除）` 图标。这将显示数据库中的所有邮件。

:guilabel:`邮件` 仪表板的信息有四种不同的查看选项，位于右上角的各个图标中。

这些查看选项从左到右依次为：

- :ref:`列表 <email_marketing/list-view>`（默认视图）
- :ref:`看板 <email_marketing/kanban-view>`
- :ref:`日历 <email_marketing/calendar-view>`
- :ref:`图表 <email_marketing/graph-view>`

.. _email_marketing/list-view:

列表视图
---------

列表视图由右上角的 :guilabel:`☰（水平线）` 图标表示，是 :guilabel:`电子邮件营销` 应用中 :guilabel:`邮件` 仪表板的默认视图。

在列表视图中，相关电子邮件的信息会按列显示。列包括：

- :guilabel:`日期`: 电子邮件发送的日期。
- :guilabel:`主题`: 电子邮件的主题。
- :guilabel:`负责人`: 创建电子邮件的用户，或已分配负责该邮件的用户。
- :guilabel:`发送次数`: 电子邮件发送的次数。
- :guilabel:`送达率（%）`: 发送的电子邮件中成功送达的百分比。
- :guilabel:`打开率（%）`: 发送的电子邮件中被收件人打开的百分比。
- :guilabel:`点击率（%）`: 发送的电子邮件中被收件人点击的百分比。
- :guilabel:`回复率（%）`: 发送的电子邮件中被收件人回复的百分比。
- :guilabel:`状态`: 电子邮件的状态（:guilabel:`草稿`、:guilabel:`队列中` 或 :guilabel:`已发送`）。

要添加或删除列，请点击列表视图中列标题最右侧的 :guilabel:`更多选项（两条带点的横线）` 图标。这将显示额外列选项的下拉菜单。

.. _email_marketing/kanban-view:

看板视图
-----------

看板视图由右上角的 :guilabel:`（倒置条形图）` 图标表示，可以从 :guilabel:`电子邮件营销` 应用中 :guilabel:`邮件` 仪表板的右上角访问。

.. image:: email_marketing/kanban-view.png
   :align: center
   :alt: Odoo 电子邮件营销应用程序主仪表板的看板视图。

在看板视图中，电子邮件信息显示在各个阶段。

这些阶段包括：:guilabel:`草稿`、:guilabel:`队列中`、:guilabel:`发送中` 和 :guilabel:`已发送`。

- :guilabel:`草稿`: 电子邮件仍在编写/创建中。
- :guilabel:`队列中`: 电子邮件计划在稍后发送。
- :guilabel:`发送中`: 电子邮件正在发送给收件人。
- :guilabel:`已发送`: 电子邮件已发送给收件人。

在每个阶段中，代表已创建/已发送的电子邮件的卡片可以拖放，所处阶段代表该邮件的当前状态。

在 :guilabel:`邮件` 仪表板上，每个邮件卡片提供与该特定电子邮件相关的关键信息。

当光标悬停在电子邮件活动卡片的右上角时，会出现一个 :guilabel:`⋮（三竖点）` 图标。点击该图标时，会显示一个小型下拉菜单，提供为邮件设置颜色编码、:guilabel:`删除` 电子邮件或将消息 :guilabel:`归档` 以便将来使用的选项。

.. image:: email_marketing/three-dot-dropdown.png
   :align: center
   :alt: Odoo 电子邮件营销仪表板上的三点下拉菜单视图。

.. _email_marketing/calendar-view:

日历视图
-------------

日历视图表现为一个 :guilabel:`📆 (日历)` 图标，可以通过在 :guilabel:`邮件` 仪表板右上角的 :guilabel:`电子邮件营销` 应用程序中访问。

在日历视图中，默认情况下会显示每月的日历，显示邮件的发送时间或计划发送的时间。

.. image:: email_marketing/calendar-view.png
   :align: center
   :alt: 电子邮件营销应用程序中邮件仪表板的日历视图。

当前日期通过日历上的 :guilabel:`🔴（红圈）` 图标表示。

在日历右侧，提供了通过复选框按 :guilabel:`负责人` 和/或 :guilabel:`状态` 筛选结果的选项。

.. tip::
   要隐藏右侧边栏，请单击侧边栏上方的 :guilabel:`(panel-right)` 图标。

在日历上方的左上角，通过下拉菜单可以更改显示的时间段，默认显示为 :guilabel:`月`。点击后，下拉菜单显示以下选项：:guilabel:`日`、:guilabel:`周`、:guilabel:`月`（默认）、:guilabel:`年` 和 :guilabel:`显示周末`（默认选择）。

点击任一选项后，日历显示将更改为所需的时间段。

点击 :guilabel:`⬅️（左箭头）` 或 :guilabel:`➡️（右箭头）` 图标，可以根据选定的时间段调整日历到之前或之后的时间。

要跳回当前日期，请点击 :guilabel:`今天` 按钮。

.. _email_marketing/graph-view:

图表视图
----------

图表视图由 :guilabel:`（折线图）` 图标表示，可以通过在 :guilabel:`邮件` 仪表板右上角的 :guilabel:`电子邮件营销` 应用程序中访问。

在图表视图中，邮件状态通过条形图显示，但如果需要，可以实施其他图表视图选项。

.. image:: email_marketing/graph-view.png
   :align: center
   :alt: Odoo 电子邮件营销应用程序中的图表视图。

在图表上方的左上角，有一个 :guilabel:`度量` 下拉菜单。点击后，会出现不同的筛选选项，以进一步自定义图表视图。

这些 :guilabel:`度量` 选项包括：:guilabel:`A/B 测试百分比` 和 :guilabel:`计数`（默认）。

在 :guilabel:`度量` 下拉菜单右侧，如果安装了 *文档* 应用程序，会出现一个 :guilabel:`插入电子表格` 按钮。点击该按钮时，会弹出一个窗口，提供将图表添加到电子表格或仪表板的选项。

在 :guilabel:`度量` 下拉菜单和 :guilabel:`插入电子表格` 按钮旁边，有不同的图表视图选项。从左到右，这些图表视图选项包括：:guilabel:`（条形图）`（默认）、:guilabel:`（折线图）` 和 :guilabel:`（饼图）`。

.. note::
   每个图表视图选项都有其自己的额外视图选项，会显示在所选图表视图选项的右侧。

搜索选项
--------------
无论在 :guilabel:`电子邮件营销` 应用程序的 :guilabel:`邮件` 仪表板上选择了哪个视图，始终可以使用 :guilabel:`筛选器`、:guilabel:`分组依据` 和 :guilabel:`收藏` 选项来进一步自定义显示的信息。

要访问这些选项，请点击搜索栏右侧的 :guilabel:`（下箭头）` 图标。点击后，将显示包含这些筛选和分组选项的下拉菜单。

.. image:: email_marketing/search-mega-menu.png
   :align: center
   :alt: Odoo 电子邮件营销应用程序中的搜索选项下拉菜单。

这些选项提供了多种指定和组织在 :guilabel:`邮件` 仪表板上看到的信息的方式。

.. tabs::

   .. tab:: 筛选器

      此下拉菜单部分提供了在 :guilabel:`电子邮件营销` 应用程序的 :guilabel:`邮件` 仪表板上筛选电子邮件结果的不同方式。

      .. image:: email_marketing/filters-dropdown.png
         :align: center
         :alt: Odoo 电子邮件营销仪表板上的筛选器下拉菜单选项视图。

      筛选器选项包括：:guilabel:`我的邮件`、:guilabel:`发送日期`、:guilabel:`A/B 测试`、:guilabel:`待审 A/B 测试`、:guilabel:`已归档` 和 :guilabel:`添加自定义筛选器`。

      如果选择了 :guilabel:`添加自定义筛选器`，Odoo 会显示一个弹出窗口，其中有三个可自定义字段，填写后可以创建自定义筛选规则，让 Odoo 检索符合更具体条件的结果。

      .. image:: email_marketing/add-custom-filter-popup.png
         :align: center
         :alt: Odoo 电子邮件营销应用程序中出现的自定义筛选器弹出窗口。

   .. tab:: 分组依据

      此下拉菜单部分提供了在 :guilabel:`电子邮件营销` 应用程序的 :guilabel:`邮件` 仪表板上按不同方式分组电子邮件结果的选项。

      .. image:: email_marketing/group-by-dropdown.png
         :align: center
         :alt: Odoo 电子邮件营销应用程序中的分组依据下拉菜单视图。

      使用此部分，数据可以按邮件的 :guilabel:`状态` 或 :guilabel:`发送人` 进行分组。

      还可以选择按 :guilabel:`发送周期` 分组数据，该选项有自己的子菜单可供选择。:guilabel:`发送周期` 选项包括 :guilabel:`年`、:guilabel:`季度`、:guilabel:`月`、:guilabel:`周` 和 :guilabel:`日`。

      如果以上 :guilabel:`分组依据` 选项都不能提供所需结果，请点击 :guilabel:`添加自定义分组`，该选项位于 :guilabel:`分组依据` 部分的底部。点击后，将显示一个下拉菜单，您可以选择并应用自定义条件，从而获得所需的任何数据分组。

   .. tab:: 收藏

      此部分提供了保存自定义筛选器和/或分组以供将来使用的机会。要使用此部分，请点击 :guilabel:`保存当前搜索` 字段，该字段会显示更多选项。

      .. image:: email_marketing/favorites-dropdown.png
         :align: center
         :alt: Odoo 电子邮件营销应用程序中的收藏下拉菜单视图。

      在复选框 :guilabel:`默认筛选器` 和 :guilabel:`共享` 之上的空白行中，为收藏的筛选器/分组命名。

      勾选 :guilabel:`默认筛选器` 框将此收藏的筛选器/分组设置为默认选项。勾选 :guilabel:`共享` 框则允许其他用户查看和使用此收藏的筛选器/分组。

      配置完所有所需选项后，点击 :guilabel:`保存` 按钮，将筛选器/分组保存在下拉菜单的 :guilabel:`收藏` 部分中。

设置
========

要查看和修改 *电子邮件营销* 设置，请导航到 :menuselection:`电子邮件营销应用程序 --> 配置 --> 设置`。

.. image:: email_marketing/configuration-settings.png
   :align: center
   :alt: Odoo 电子邮件营销应用程序中设置页面的配置菜单视图。

在 :guilabel:`设置` 页面上，有四个可用的功能。

.. image:: email_marketing/settings.png
   :align: center
   :alt: Odoo 电子邮件营销应用程序中设置页面的视图。

这些功能包括：

- :guilabel:`邮件活动`: 启用管理批量邮件活动的选项。
- :guilabel:`退订时的黑名单选项`: 允许收件人在退订过程中将自己列入未来邮件的黑名单。
- :guilabel:`专用服务器`: 提供使用单独的专用服务器进行邮件发送的选项。启用后，Odoo 会显示一个新字段（及链接），用户必须在其中输入特定的服务器配置，以便正确连接到 Odoo。
- :guilabel:`24小时邮件统计报告`: 允许用户在邮件发送后的第二天查看邮件的效果。

.. _email_marketing/create_email:

创建电子邮件
===============
要创建电子邮件，请打开 :menuselection:`电子邮件营销` 应用程序，并点击 :guilabel:`新建` 按钮，该按钮位于 :guilabel:`邮件` 仪表板页面的左上角。

点击 :guilabel:`新建` 后，将显示一个空白的电子邮件表单。

.. image:: email_marketing/blank-email-detail-form.png
   :align: center
   :alt: Odoo 电子邮件营销应用程序中的空白电子邮件详细表单视图。

在电子邮件表单上，有用于电子邮件的 :ref:`主题 <email_marketing/subject>` 和 :ref:`收件人 <email_marketing/recipients>` 的字段。

在这些字段下，有三个选项卡：:ref:`邮件正文 <email_marketing/mail_body>`、:ref:`A/B 测试 <email_marketing/ab_tests>` 和 :ref:`设置 <email_marketing/settings_tab>`。

.. _email_marketing/subject:

主题
-------

首先，输入电子邮件的 :guilabel:`主题`。主题会在收件人的收件箱中显示，让他们快速了解消息内容。

.. note::
   :guilabel:`主题` 字段是必填项。没有 :guilabel:`主题` 无法发送电子邮件。

:guilabel:`主题` 字段末尾的 :guilabel:`（带加号的笑脸）` 图标表示可以将表情符号添加到 :guilabel:`主题` 字段。点击该图标后，将显示一个弹出菜单，提供可用的表情符号。

在 :guilabel:`主题` 字段末尾的 :guilabel:`（带加号的笑脸）` 图标旁边是一个空的 :guilabel:`（星星）` 图标。点击后，:guilabel:`（星星）` 图标会变为金色，并将电子邮件保存为 :guilabel:`邮件正文` 选项卡中的模板，供将来使用。

.. _email_marketing/recipients:

收件人
----------

在电子邮件表单中的 :guilabel:`主题` 字段下方是 :guilabel:`收件人` 字段。在此字段中，选择电子邮件的收件人。默认情况下，:guilabel:`邮件列表` 选项已被选中，但点击该字段将显示其他收件人选项的下拉菜单。

在选择默认的 :guilabel:`邮件列表` 选项时，必须从相邻的 :guilabel:`选择邮件列表` 字段的下拉菜单中选择特定的邮件列表。

.. tip::
   可以从 :guilabel:`选择邮件列表` 字段中选择多个邮件列表。

然后，Odoo 将电子邮件发送到该特定邮件列表中的联系人。

.. seealso::
   :doc:`email_marketing/mailing_lists`

点击 :guilabel:`收件人` 字段时，将显示其他选项的下拉菜单。每个选项提供不同的方式，Odoo 可以为电子邮件创建目标受众。

.. image:: email_marketing/recipients-dropdown.png
   :align: center
   :alt: Odoo 电子邮件营销应用程序中的收件人下拉菜单视图。

除了默认的 :guilabel:`邮件列表` 选项外，这些选项还提供了创建更具体的收件人筛选器的选项，以类似于方程的格式显示在 :guilabel:`收件人` 字段下方。

:guilabel:`收件人` 字段的选项，除了默认的 :guilabel:`邮件列表` 选项外，还包括以下内容：

- :guilabel:`联系人`: 与 *联系人* 应用程序直接相关，包括数据库中输入的所有联系人。
- :guilabel:`活动注册`: 与 *活动* 应用程序直接相关，提供与活动注册者互动的机会，以便传达重要的活动信息，或促进其他有价值的操作，例如活动后调查、购买等。
- :guilabel:`线索/机会`: 与 *CRM* 应用程序中的记录直接相关，提供了影响销售或购买决策的机会。
- :guilabel:`邮件联系人`: 与 *电子邮件营销* 应用程序直接相关，重点是特定的邮件联系人，这些联系人已在该应用程序中输入，并与特定的邮件列表相关。这些联系人是独特的，因为他们在 *联系人* 应用程序中没有自己的联系卡。可以通过导航到 :menuselection:`电子邮件营销应用程序 --> 邮件列表 --> 邮件列表联系人` 访问此列表。
- :guilabel:`销售订单`: 与 *销售* 应用程序直接相关，重点是数据库中的特定销售订单。

添加收件人筛选器
~~~~~~~~~~~~~~~~~~~~

要为任何 :guilabel:`收件人` 选项添加更具体的收件人筛选器，请选择任意收件人选项（除 :guilabel:`邮件列表` 外），然后点击 :guilabel:`修改筛选器（右箭头）` 图标，该图标位于 :guilabel:`收件人` 字段下方，显示三个后续的筛选规则字段，格式类似于方程式。

强烈建议用户为 :guilabel:`收件人` 字段实施详细的目标定位标准。通常，一行目标逻辑不足以满足电子邮件营销活动的需求。

虽然 :guilabel:`邮件列表` 选项对于 :guilabel:`收件人` 字段是合适的，但 :guilabel:`线索/机会` 和 :guilabel:`活动注册` 选项提供了更详细的目标定位标准，可以在这些基础资源的基础上添加。

.. example::
   例如，选择了 :guilabel:`线索/机会` 选项后，用户可以添加与 :guilabel:`创建日期`、:guilabel:`阶段`、:guilabel:`标签`、:guilabel:`丢失原因`、:guilabel:`销售团队`、:guilabel:`活跃` 状态、:guilabel:`国家` 等相关的自定义条件。

   .. image:: email_marketing/detailed-filter-records.png
      :align: center
      :alt: 在 Odoo 电子邮件营销中如何自定义收件人筛选器的视图。

点击每个字段以显示筛选规则字段中的子菜单选项，并进行所需选择，直到达到所需配置。

数据库中符合配置规则的 :guilabel:`记录` 数量以绿色表示在配置的筛选规则下方。

.. image:: email_marketing/filter-records.png
   :align: center
   :alt: 在 Odoo 电子邮件营销中如何自定义收件人筛选器的视图。

.. note::
   第一规则字段中的某些子菜单选项允许进行第二个选择，以提供更多的特定性。

在每条规则的右侧，有三个额外的选项，分别由 :guilabel:`➕（加号）`、:guilabel:`（站点地图）` 和 :guilabel:`🗑️（垃圾桶）` 图标表示。

- :guilabel:`➕（加号）` 图标添加一个新节点（行）到总体的目标逻辑中。
- :guilabel:`（站点地图）` 图标为该节点添加一个分支。分支包含与该规则相关的两个额外的缩进子节点，为上方的父行提供更多的特定性。
- :guilabel:`🗑️（垃圾桶）` 图标删除逻辑数组中的特定节点（行）。

.. _email_marketing/mail_body:

邮件正文选项卡
-------------
在 :guilabel:`邮件正文` 选项卡中，有许多预配置的消息模板可供选择。

.. image:: email_marketing/mail-body-templates.png
   :align: center
   :alt: Odoo 电子邮件营销应用程序中的邮件正文选项卡模板视图。

选择所需的模板，然后使用 Odoo 的拖放构建块来修改其设计细节的每个元素，这些构建块在选择模板后会显示在右侧边栏。

.. image:: email_marketing/template-building-blocks.png
   :align: center
   :alt: Odoo 电子邮件营销应用程序中的邮件正文选项卡构建块视图。

用于创建和自定义电子邮件的边栏功能分为三个部分：:guilabel:`块`、:guilabel:`自定义` 和 :guilabel:`设计`。

每个构建块提供独特的功能和专业设计元素。要使用构建块，只需将所需的块元素拖放到正在构建的电子邮件正文中。一旦放置，可以自定义构建块的各个方面。

.. tip::
   要从头开始构建电子邮件，而不使用任何构建块元素，请选择 :guilabel:`纯文本` 模板。选择此模板时，Odoo 会提供一个完全空白的电子邮件画布，您可以使用前端富文本编辑器以多种方式进行自定义，该编辑器接受正斜杠 `/` 命令。

   当在 :guilabel:`纯文本` 模板的电子邮件空白正文中输入 `/` 时，会显示各种设计元素的下拉菜单，您可以使用这些元素创建所需的电子邮件设计。

   .. image:: email_marketing/template-blank-slash.png
      :align: center
      :alt: Odoo 电子邮件营销应用程序中的富文本编辑器下拉菜单视图。

.. _email_marketing/ab_tests:

A/B 测试选项卡
-------------

最初，当在电子邮件表单上打开 :guilabel:`A/B 测试` 选项卡时，唯一可用的选项是 :guilabel:`允许 A/B 测试`。这是一个**非**必需的选项。

如果启用了此选项，则整个活动中收件人只会收到一次邮件。

这使用户可以将同一邮件的不同版本发送给随机选择的收件人，以评估不同设计、格式、布局、内容等的效果——而不会发送重复的消息。

当勾选 :guilabel:`允许 A/B 测试` 复选框时，会出现一个 :guilabel:`百分比 (%)` 字段，用户可以在其中确定预配置收件人的百分比，这些收件人将作为测试的一部分收到当前版本的邮件。

.. note::
   :guilabel:`百分比 (%)` 字段的默认值为 `10`，但此数字可以随时更改。

在此字段下，会出现两个额外的字段：

:guilabel:`胜者选择` 字段提供一个下拉菜单，用户可以在其中决定应使用哪些标准来确定发送邮件测试的“胜者”版本。

:guilabel:`胜者选择` 字段中的选项如下：

- :guilabel:`手动`: 允许用户手动确定邮件的“胜者”版本。此选项会删除 :guilabel:`发送最终邮件于` 字段。
- :guilabel:`最高打开率`（默认）: 拥有最高打开率的邮件被确定为“胜者”版本。
- :guilabel:`最高点击率`: 拥有最高点击率的邮件被确定为“胜者”版本。
- :guilabel:`最高回复率`: 拥有最高回复率的邮件被确定为“胜者”版本。
- :guilabel:`线索`: 生成最多潜在客户的邮件被确定为“胜者”版本。
- :guilabel:`报价`: 生成最多报价的邮件被确定为“胜者”版本。
- :guilabel:`收入`: 生成最多收入的邮件被确定为“胜者”版本。

:guilabel:`发送最终邮件于` 字段允许用户选择日期，Odoo 会根据此日期确定“胜者”邮件，并随后将该版本发送给其余收件人。

.. image:: email_marketing/ab-test-tab.png
   :align: center
   :alt: Odoo 电子邮件营销应用程序中的 A/B 测试选项卡视图。

在这些字段的右侧，有一个 :guilabel:`创建一个替代版本` 按钮。点击后，Odoo 会显示一个新的 :guilabel:`邮件正文` 选项卡，供用户创建测试的替代电子邮件版本。

.. _email_marketing/settings_tab:

设置选项卡
------------

邮件表单中的 :guilabel:`设置` 选项卡中的选项分为两部分：:guilabel:`电子邮件内容` 和 :guilabel:`跟踪`。

.. note::
   :guilabel:`设置` 选项卡中可用的选项取决于是否激活了 *邮件活动* 功能，路径为 :menuselection:`电子邮件营销 --> 配置 --> 设置`。有关详细信息，请参阅 :ref:`email_marketing/mailing-campaigns`。

如果未激活 *邮件活动* 功能，电子邮件表单中的 :guilabel:`设置` 选项卡仅包含 :guilabel:`预览文本`、:guilabel:`发送人`、:guilabel:`回复至`、:guilabel:`附件` 和 :guilabel:`负责人` 字段。

.. image:: email_marketing/settings-without-features.png
   :align: center
   :alt: Odoo 电子邮件营销应用程序中未激活活动设置时的设置选项卡视图。

电子邮件内容
~~~~~~~~~~~~~

- :guilabel:`预览文本`: 允许用户输入预览句子以鼓励收件人打开电子邮件。在大多数收件箱中，它会显示在主题旁边。如果留空，则会显示电子邮件内容的前几个字符。此字段也可以通过 :guilabel:`（带加号的笑脸）` 图标添加表情符号。
- :guilabel:`发送人`: 指定一个显示为该电子邮件发件人的电子邮件别名。
- :guilabel:`回复至`: 指定一个电子邮件别名，所有对该电子邮件的回复都会发送到此地址。
- :guilabel:`附件`: 如果该电子邮件需要（或有帮助）任何特定文件，请点击 :guilabel:`附件` 按钮，并将所需文件上传到电子邮件中。

跟踪
~~~~~~~~
- :guilabel:`负责人`: 指定数据库中的用户负责该电子邮件。

.. note::
   如果激活了 *邮件活动* 功能，则在 :guilabel:`设置` 选项卡的 :guilabel:`跟踪` 部分中会出现一个额外的 :guilabel:`活动` 字段。

   .. image:: email_marketing/settings-tab-with-campaign.png
      :align: center
      :alt: 激活活动设置时，Odoo 电子邮件营销应用程序中设置选项卡的视图。

   额外的 :guilabel:`活动` 字段允许用户将此电子邮件附加到一个邮件活动中（如果需要）。

   如果所需的活动在初始下拉菜单中不可用，请选择 :guilabel:`查看更多` 以显示数据库中所有邮件活动的完整列表。

   或者，在 :guilabel:`活动` 字段中键入所需的邮件活动的名称，直到 Odoo 在下拉菜单中显示所需的活动，然后选择该活动。

发送、安排、测试
====================

邮件确认完成后，可以通过电子邮件表单左上角的按钮使用以下选项：:ref:`发送 <email_marketing/send>`、:ref:`安排 <email_marketing/schedule>` 和 :ref:`测试 <email_marketing/test>`。

.. _email_marketing/send:

发送
----

点击 :guilabel:`发送` 按钮后，会显示一个 :guilabel:`准备好发送电子邮件了吗？` 的弹出窗口。

.. image:: email_marketing/send-popup.png
   :align: center
   :alt: 点击电子邮件表单上的发送按钮时出现的弹出窗口视图。

点击 :guilabel:`发送给所有人` 按钮后，Odoo 会将电子邮件发送给所需的收件人。Odoo 发送完邮件后，状态变为 :guilabel:`已发送`。

.. _email_marketing/schedule:

安排
--------

点击 :guilabel:`安排` 按钮后，会显示一个 :guilabel:`您想何时发送邮件？` 的弹出窗口。

.. image:: email_marketing/schedule-popup.png
   :align: center
   :alt: 点击电子邮件表单上的安排按钮时出现的弹出窗口视图。

在此弹出窗口中，点击 :guilabel:`发送于` 字段，以显示日历弹出窗口。

.. image:: email_marketing/schedule-popup-calendar.png
   :align: center
   :alt: 点击电子邮件表单上的安排按钮时出现的日历弹出窗口视图。

在日历弹出窗口中，选择 Odoo 发送此电子邮件的未来日期和时间。然后，点击 :guilabel:`✔️ 应用`。选择日期和时间后，点击 :guilabel:`安排` 按钮，邮件状态变为 :guilabel:`队列中`。

.. _email_marketing/test:

测试
----

点击 :guilabel:`测试` 按钮后，会显示一个 :guilabel:`测试邮件` 的弹出窗口。

.. image:: email_marketing/test-popup.png
   :align: center
   :alt: 点击电子邮件表单上的测试按钮时出现的弹出窗口视图。

在此弹出窗口中，在 :guilabel:`收件人` 字段中输入要接收测试电子邮件的联系人的电子邮件地址。如果需要，可以在此字段中添加多个联系人。

在 :guilabel:`收件人` 字段中输入所有所需的电子邮件地址后，点击 :guilabel:`发送测试` 按钮。

.. warning::
   默认情况下，所有应用程序中的**所有电子邮件**的发送都有每日限制。因此，如果在达到限制后还有剩余的电子邮件要发送，这些邮件将不会**自动**在第二天发送。需要通过打开邮件并点击 :guilabel:`重试` 来强制发送。

.. _email_marketing/mailing-campaigns:

邮件活动
=================

*电子邮件营销* 应用程序为用户提供了创建邮件活动的能力。

要创建和自定义邮件活动，必须在 *电子邮件营销* 应用程序的*设置*页面中激活 *邮件活动* 功能。为此，请导航到 :menuselection:`电子邮件营销应用程序 --> 配置 --> 设置`，勾选 :guilabel:`邮件活动` 旁边的框，然后点击 :guilabel:`保存` 按钮。

.. image:: email_marketing/campaigns-feature.png
   :align: center
   :alt: Odoo 电子邮件营销中的活动功能设置视图。

激活 :guilabel:`邮件活动` 功能后，标题中会出现一个新的 :guilabel:`活动` 菜单选项。

点击该选项后，Odoo 会显示一个单独的 :guilabel:`活动` 页面，显示数据库中的所有邮件活动及其当前阶段，并以默认的看板视图展示。

.. image:: email_marketing/campaigns-page.png
   :align: center
   :alt: Odoo 电子邮件营销应用程序中的活动页面视图。

.. note::
   这些信息也可以通过点击右上角的 :guilabel:`☰（水平线）` 图标以列表形式查看。

点击 :guilabel:`活动` 页面中的任一活动会显示该活动的表单。

在 *电子邮件营销* 应用程序中有两种不同的方式来创建和自定义活动，您可以通过 :ref:`活动页面 <email_marketing/campaign-page>` 或通过电子邮件表单上的 :ref:`设置选项卡 <email_marketing/campaign-settings>` 来完成。

.. _email_marketing/campaign-page:

创建邮件活动（通过活动页面）
---------------------------------------------

激活 *邮件活动* 功能后，*电子邮件营销* 应用程序的标题中会出现一个新的 *活动* 选项。可以直接在 *电子邮件营销* 应用程序的 *活动* 页面中创建活动。

为此，请导航到 :menuselection:`电子邮件营销应用程序 --> 活动 --> 新建`。

看板视图
~~~~~~~~~~~
当在 :guilabel:`活动` 页面上的默认看板视图中点击 :guilabel:`新建` 按钮时，一个看板卡片会出现在 :guilabel:`新建` 阶段。

.. image:: email_marketing/campaigns-kanban-popup.png
   :align: center
   :alt: Odoo 电子邮件营销应用程序中的活动弹出看板视图。

也可以通过点击 :guilabel:`➕（加号）`，该图标位于 :guilabel:`活动` 页面上的任何看板阶段顶部，来创建新的活动卡片。

当新的活动看板卡片出现时，用户可以填写 :guilabel:`活动名称`、:guilabel:`负责人` 和 :guilabel:`标签`。

要将活动添加到看板阶段，点击 :guilabel:`添加` 按钮。

要删除活动，点击 :guilabel:`🗑️（垃圾桶）` 图标。

要进一步自定义活动，点击 :guilabel:`编辑` 按钮，这将显示活动表单以进行其他修改。

.. note::
   必须在看板卡片中输入 :guilabel:`活动名称`，才能点击 :guilabel:`编辑` 按钮并显示活动表单以进行进一步修改。

列表视图
~~~~~~~~~

要进入 :guilabel:`活动` 页面上的列表视图，点击右上角的 :guilabel:`☰（水平线）` 图标。这样会以列表格式显示所有活动信息。

.. image:: email_marketing/campaign-page-list-view.png
   :align: center
   :alt: Odoo 电子邮件营销应用程序中列表视图的活动页面视图。

在列表视图中创建活动时，点击 :guilabel:`新建` 按钮。这将显示一个空白的活动表单。

.. image:: email_marketing/blank-campaign-form.png
   :align: center
   :alt: Odoo 电子邮件营销应用程序中的空白活动表单视图。

在此活动表单中，可以添加 :guilabel:`活动名称`、:guilabel:`负责人` 和 :guilabel:`标签`。

在表单顶部，可以看到各种与活动相关的指标按钮。这些智能按钮包括：:guilabel:`收入`、:guilabel:`报价`、:guilabel:`机会` 和 :guilabel:`点击量`。

.. note::
   一旦输入并保存了 :guilabel:`活动名称`，活动表单顶部会出现额外的按钮。

   这些额外的按钮是：:guilabel:`发送邮件` 和 :guilabel:`发送短信`。

活动表单
-------------

在活动表单中（通过点击看板卡片上的 :guilabel:`编辑` 或从 :guilabel:`活动` 页面选择现有活动），可以看到更多选项和指标。

.. image:: email_marketing/campaign-form.png
   :align: center
   :alt: Odoo 电子邮件营销应用程序中的活动表单视图。

在表单顶部，可以看到与活动相关的各种智能按钮。这些按钮包括：:guilabel:`收入`、:guilabel:`报价`、:guilabel:`机会` 和 :guilabel:`点击量`。

还有 :guilabel:`发送邮件`、:guilabel:`发送短信`、:guilabel:`添加帖子` 和 :guilabel:`发送推送`（推送通知）按钮。

.. note::
   如果 :guilabel:`发送邮件` 和 :guilabel:`发送短信` 按钮不可用，请输入 :guilabel:`活动名称`，然后保存（手动或自动）。这样将显示这些按钮。

还可以在活动表单的右上角查看活动的状态。

.. _email_marketing/campaign-settings:

通过设置选项卡创建邮件活动
-------------------------------------------

要从邮件表单的 :guilabel:`设置` 选项卡创建新的活动，点击 :guilabel:`活动` 字段并开始输入新活动的名称。然后，从出现的下拉菜单中选择 :guilabel:`创建 "[活动名称]"` 或 :guilabel:`创建并编辑...`。

.. image:: email_marketing/mailing-campaign-settings.png
   :align: center
   :alt: Odoo 电子邮件营销应用程序中设置选项卡的邮件活动创建视图。

选择 :guilabel:`创建` 以将此新邮件活动添加到数据库，并在未来修改其设置。

选择 :guilabel:`创建并编辑...` 以将此新邮件活动添加到数据库，并显示一个 :guilabel:`创建活动` 弹出窗口。

.. image:: email_marketing/mailing-campaign-popup.png
   :align: center
   :alt: Odoo 电子邮件营销应用程序中的邮件活动弹出窗口视图。

在这里，可以进一步自定义新的邮件活动。用户可以调整 :guilabel:`活动名称`，指定 :guilabel:`负责人`，并添加 :guilabel:`标签`。

还有 :guilabel:`添加帖子` 或 :guilabel:`发送推送`（推送通知）按钮。

在 :guilabel:`创建活动` 弹出窗口的右上角也有状态显示。

准备好所有修改后，点击 :guilabel:`保存并关闭`。要删除整个活动，点击 :guilabel:`放弃`。

.. seealso::
   - :doc:`email_marketing/mailing_lists`
   - :doc:`email_marketing/unsubscriptions`
   - :doc:`email_marketing/lost_leads_email`
   - :doc:`email_marketing/analyze_metrics`

.. toctree::
   :titlesonly:

   email_marketing/mailing_lists
   email_marketing/unsubscriptions
   email_marketing/lost_leads_email
   email_marketing/analyze_metrics
