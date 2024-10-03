======
活动
======

探索 Odoo **活动** 详细的仪表板和有用的设置，利用这些功能生成并收集有关活动（及其参与者）的有价值数据，进而帮助改进决策和活动策划。

.. seealso::
   `Odoo 教程: 活动 <https://www.odoo.com/slides/surveys-63>`_

.. cards::

   .. card:: 创建活动
      :target: events/create_events

      探索如何使用 Odoo 创建活动。

   .. card:: 销售活动票
      :target: events/sell_tickets

      学习如何创建、配置和销售活动票。

   .. card:: 跟踪和管理讲座
      :target: events/track_manage_talks

      了解如何使用 Odoo 创建、跟踪和管理活动的讲座。

   .. card:: 活动模板
      :target: events/event_templates

      使用活动模板加快活动创建过程。

   .. card:: 活动轨迹
      :target: events/event_tracks

      了解如何使用 Odoo 创建、跟踪和管理活动轨迹。

   .. card:: 活动展位
      :target: events/event_booths

      创建、管理和销售活动展位。

   .. card:: 注册柜台
      :target: events/registration_desk

      使用 Odoo 的注册柜台功能快速为活动参与者授予访问权限。

   .. card:: 收入报告
      :target: events/revenues_report

      使用 Odoo 分析活动的财务成功情况。

活动仪表板
================

打开 **活动** 应用程序时，Odoo 会显示主要的 :guilabel:`活动` 仪表板，可以通过多种不同方式查看。可以通过右上角的一系列视图相关图标按钮访问这些不同的视图选项。

默认情况下，:guilabel:`活动` 仪表板以 :icon:`oi-view-kanban` :guilabel:`看板` 视图显示，展示了一系列不同的管道阶段。

.. image:: events/kanban-dashboard.png
   :align: center
   :alt: Odoo 活动中的看板视图设置概览。

此视图展示了数据库中所有活动在各自阶段的情况。默认阶段为：:guilabel:`新建`、:guilabel:`预定`、:guilabel:`已公布`、:guilabel:`结束` 和 :guilabel:`取消`。

.. note::
   默认情况下，:guilabel:`结束` 和 :guilabel:`取消` 阶段被折叠，并位于其他阶段的右侧。

每张活动卡上显示活动的日期、活动名称、地点、预计的 :guilabel:`参与者` 数量、与活动相关的计划活动、活动状态以及负责该活动的人员。

要快速将新活动添加到管道中，请点击阶段顶部的 :icon:`fa-plus` :guilabel:`（加号）` 图标，以显示一个空白的看板卡片并填写信息。

.. image:: events/blank-kanban-card.png
   :align: center
   :alt: Odoo 活动应用程序中的典型空白看板卡片。

在此空白看板卡片中，输入 :guilabel:`活动名称`，以及开始和结束的 :guilabel:`日期` 和时间。

然后，点击 :guilabel:`添加` 以将其添加到阶段中，稍后再进行编辑，或点击 :guilabel:`编辑` 以将活动添加到阶段，并在单独页面上编辑其配置。

每个活动卡片可以拖放到看板管道中的任何阶段，提供了便捷的组织访问方式。

设置
========

要访问 Odoo **活动** 中的设置和功能选项，请导航至 :menuselection:`活动应用程序 --> 配置 --> 设置`。在此页面上，勾选所需设置和/或功能旁边的复选框，然后点击 :guilabel:`保存` 以激活它们。

活动部分
--------------

在 :guilabel:`设置` 页面的 :guilabel:`活动` 部分，可以启用一些可选功能，以便为使用 Odoo **活动** 应用程序创建的活动添加不同元素。

.. image:: events/settings-events-section.png
   :align: center
   :alt: Odoo 活动应用程序设置页面中的活动部分视图。

:guilabel:`日程和轨迹` 功能允许用户管理并发布包含活动轨迹的日程。*轨迹* 是一个统称，涵盖了讲座、演示、展示、报告和其他类似的活动元素，用户可以选择将这些元素包含在活动中。

启用 :guilabel:`日程和轨迹` 功能后，会在其下方显示两个额外的字段：:guilabel:`现场直播` 和 :guilabel:`活动游戏化`。

:guilabel:`现场直播` 功能让用户可以通过 *YouTube* 集成在线直播活动轨迹。

:guilabel:`活动游戏化` 功能让用户可以在任何活动轨迹后分享一个测验，以便参与者评估他们从该轨迹中学到了多少。公司还可以利用此功能，通过参与者的答题结果来判断公司展示内容的强项和弱项。

接下来是 :guilabel:`在线展商` 功能。此功能允许用户在活动页面上展示赞助商和展商，这可以作为鼓励合作伙伴和企业参与活动的宝贵激励。

:guilabel:`Jitsi 服务器域` 字段代表与 Odoo 集成的外部会议服务，用于创建和主办虚拟会议、社区房间和其他类似活动元素。

:guilabel:`社区聊天室` 功能允许用户为活动参与者创建虚拟会议室，为他们提供一个集中的讨论场所，讨论与活动相关的任何话题。

最后是 :guilabel:`展位管理` 功能。此功能为用户提供了创建和管理活动展位及展位预订的能力。启用后，用户可以创建不同的展位等级，设置各自的价格，并将其出售给感兴趣的客户。

注册部分
--------------------

在 :guilabel:`设置` 页面的 :guilabel:`注册` 部分，提供了与活动注册直接相关的可选设置。

.. image:: events/settings-registration-section.png
   :align: center
   :alt: Odoo 活动应用程序设置页面中的注册部分视图。

:guilabel:`门票` 设置允许用户通过标准销售订单销售活动票。

:guilabel:`在线售票` 设置在产品表单上创建一个可选择的 *活动票* 产品类型，允许用户通过其网站/电子商务商店在线销售活动票。

参与部分
------------------

在 :guilabel:`设置` 页面的 :guilabel:`参与` 部分，有一个与参与者如何参加/进入活动直接相关的可选设置。

.. image:: events/settings-attendance-section.png
   :align: center
   :alt: Odoo 活动应用程序设置页面中的参与部分视图。

:guilabel:`使用活动条码` 设置启用后，允许通过条码（和二维码）扫描参与者进入活动。这为参与者提供了快速访问权限，帮助 Odoo 用户轻松跟踪、管理和分析所有活动参与者。

:guilabel:`条码命名规则` 字段位于 :guilabel:`使用活动条码` 设置下方，默认为 :guilabel:`默认命名规则`，但可以随时更改。

创建活动
=============

使用 Odoo **活动**，可以手动从头创建活动或基于预制模板构建活动。

启动后，**活动** 应用程序与 **网站** 应用程序集成，便于前端的活动推广和参与者注册；与 **销售** 应用程序集成，便于付费票的购买；与 **CRM** 应用程序通过可自定义的线索生成规则集成。

.. seealso::
   :doc:`events/create_events`

销售活动票
==================

在活动模板表单的 *门票* 选项卡下，为潜在的活动参与者创建自定义票务等级（具有不同的价格点）。

Odoo 提供了多种支付方式，简化了购票过程。

.. seealso::
   :doc:`events/sell_tickets`

跟踪和管理讲座
======================

了解如何访问各种活动轨迹（讲座、演示等）、查看完整议程，以及了解参与者如何为活动提出讲座。

.. seealso::
   :doc:`events/track_manage_talks`

活动模板
===============

了解如何自定义和配置活动模板，以加快活动创建过程。

.. seealso::
   :doc:`events/event_templates`

活动展位
============

探索使用 Odoo **活动** 应用程序创建、管理和销售展位的各种方式。

.. seealso::
   :doc:`events/event_booths`

活动轨迹
============

了解如何使用 Odoo 创建、管理和安排不同的活动体验（即 *轨迹*）。

.. seealso::
   :doc:`events/event_tracks`

注册柜台
=================

使用 Odoo **活动** 的 *注册柜台* 功能，快速为活动参与者授予访问权限。

.. seealso::
   :doc:`events/registration_desk`

收入报告
===============

通过可自定义的报告和指标，深入了解与活动相关的收入情况。

.. seealso::
   :doc:`events/revenues_report`

.. toctree::

   events/create_events
   events/sell_tickets
   events/track_manage_talks
   events/event_templates
   events/event_booths
   events/event_tracks
   events/registration_desk
   events/revenues_report
