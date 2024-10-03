====================
营销自动化
====================

Odoo 的 *营销自动化* 应用程序使用户能够创建动态活动，其中的操作会在定义的时间内自动执行，如发送一系列定时的大规模电子邮件或根据潜在客户与营销活动的互动进行沟通。

虽然该应用程序设计为用户友好的界面，方便快速创建、启动和查看营销活动，但它还提供了高级功能，可以自动化数据库中的重复性任务。

从头开始创建一个 :ref:`新的活动 <marketing_automation/campaigns>` 或使用一个 :ref:`活动模板 <marketing_automation/campaign-templates>` 开始。

.. seealso::
   `Odoo 教程: 营销 <https://www.odoo.com/slides/marketing-27>`_

.. cards::

   .. card:: 受众目标
      :target: marketing_automation/target_audience

      配置活动的目标受众。

   .. card:: 工作流程活动
      :target: marketing_automation/workflow_activities

      定义活动中的工作流程。

   .. card:: 测试/运行活动
      :target: marketing_automation/testing_running

      启动测试或运行活动。

   .. card:: 活动指标
      :target: marketing_automation/understanding_metrics

      查看活动的指标。

配置
=============

要安装 *营销自动化* 应用程序，导航到 :menuselection:`应用程序` 并搜索 `营销自动化`。

在搜索结果列表中，点击 :guilabel:`激活` 按钮以安装 :guilabel:`营销自动化` 应用程序。

.. important::
   安装 *营销自动化* 应用程序时，还会自动安装 :doc:`电子邮件营销 <email_marketing>` 应用程序，因为 Odoo *营销自动化* 的大多数功能依赖于该应用程序。

   此外，安装 :doc:`CRM <../sales/crm>` 和 :doc:`短信营销 <sms_marketing>` 应用程序以访问 *营销自动化* 中的所有功能。

   以下文档假设数据库中已安装这三个依赖应用程序。

.. _marketing_automation/campaigns:

活动
=========

*活动* 是指根据预定义的过滤条件、触发器和活动时间，自动向目标受众执行的一系列工作流程。

可以从头创建一个新活动，也可以使用 :ref:`活动模板 <marketing_automation/campaign-templates>`。

要创建一个活动，请导航到 Odoo 仪表板中的 :menuselection:`营销自动化` 应用程序，以打开 :guilabel:`活动` 仪表板。在这里，点击 :guilabel:`新建` 按钮，进入新活动表单。

.. _marketing_automation/campaign-templates:

活动模板
------------------

Odoo 提供六个活动模板来帮助用户入门。仅当数据库中没有现有活动时，活动模板卡片才会显示。一旦创建了活动，*活动* 仪表板中的模板卡片将被现有活动的看板视图替代。

要使用模板开始，请导航到 Odoo 仪表板中的 :menuselection:`营销自动化` 应用程序，以打开 :guilabel:`活动` 仪表板，显示六个 :doc:`活动模板 <marketing_automation/campaign_templates>` 卡片：

- | :guilabel:`🏷️ 标记热联系`
  | :guilabel:`发送欢迎邮件给联系人并在他们点击后标记。`
- | :guilabel:`✌️ 欢迎流程`
  | :guilabel:`发送欢迎邮件给新订阅者，并移除那些退信的地址。`
- | :icon:`fa-check-square` :doc:`双重确认 <marketing_automation/campaign_templates/double_optin>`
  | :guilabel:`发送电子邮件给新收件人以确认其同意。`
- | :guilabel:`🔍 商业推广`
  | :guilabel:`发送免费目录并根据反应进行跟进。`
- | :guilabel:`📞 安排电话`
  | :guilabel:`如果为现有联系人创建了潜在客户，安排与其销售人员的电话。`
- | :guilabel:`⭐️ 优先处理热潜在客户`
  | :guilabel:`向新潜在客户发送电子邮件，如果他们打开邮件，则将其设为高优先级。`

.. image:: marketing_automation/campaigns-dashboard.png
   :align: center
   :alt: 营销自动化应用程序中活动仪表板上的六个活动模板卡片。

这些模板设计为创建新活动的起点。点击其中一个模板卡片即可打开活动表单。

.. tip::
   创建活动后，如果想再次显示活动模板卡片，可以在 :guilabel:`搜索...` 栏中输入数据库中不存在的活动名称，然后按 :kbd:`Enter`。

   例如，搜索 `empty` 可以再次显示活动模板卡片，只要数据库中没有名为 "empty" 的活动。

目标与过滤器
===================

在活动表单中的 :guilabel:`目标` 和 :guilabel:`过滤器` 部分（也称为域）包含用于定义活动目标受众（即数据库中的唯一联系人记录）的字段。

目标受众指定活动中可以使用的记录类型，例如 *潜在客户/机会*、*活动注册*、*联系人* 等。

记录
-------

系统中符合指定活动标准的联系人称为 *记录*。

显示在活动 :guilabel:`过滤器` 旁边的记录数量代表活动目标的记录总数。

参与者
------------

活动中参与的记录称为 *参与者*。

测试运行后参与的参与者数量显示在测试完成后出现在活动表单顶部的 *测试* 智能按钮中。

运行或停止的活动中参与的参与者数量显示在活动表单顶部的 *参与者* 智能按钮中。

.. seealso::
   :doc:`受众目标 <marketing_automation/target_audience>`

工作流程
========

*工作流程* 由一个或多个活动组成，或是按顺序排列的活动组成的工作流。活动的工作流程定义在活动表单的 :guilabel:`工作流程` 部分中。

活动
----------

*活动* 是在活动中执行的沟通方式或服务器操作，按照工作流程组织。活动运行后，每个活动显示参与者的 *成功* 和 *被拒绝* 计数。

要创建以下活动之一，请点击活动表单中 :guilabel:`工作流程` 部分中的 :guilabel:`添加新活动`：

- :ref:`电子邮件 <marketing_automation/email-activity-type>`：向目标受众发送电子邮件。
- :ref:`服务器操作 <marketing_automation/sa-activity-type>`：在数据库中执行的内部操作。
- :ref:`短信 <marketing_automation/sms-activity-type>`：向目标受众发送短信。

.. seealso::
   :doc:`marketing_automation/workflow_activities`

测试与运行
===================

创建活动后，可以对其进行测试，以确保工作流程按预期运行，检查是否存在错误，并在目标受众接收到之前修正任何错误。

测试后，可以启动活动以开始吸引目标受众。如果用户对工作流程有信心，也可以在不进行测试的情况下直接启动活动。

.. seealso::
   :doc:`marketing_automation/testing_running`

报告
=========

提供一系列报告指标来衡量每个活动的成功。导航至 :menuselection:`营销自动化应用程序 --> 报告` 以访问以下菜单选项：

- :guilabel:`链接跟踪器`：显示链接的指标以跟踪点击次数。
- :guilabel:`痕迹`：显示所有活动中所有活动的结果。
- :guilabel:`参与者`：显示所有活动参与者的概览。

此外，每个活动中的每个活动还显示其参与指标。

.. seealso::
   :doc:`marketing_automation/understanding_metrics`

.. toctree::
   :titlesonly:

   marketing_automation/target_audience
   marketing_automation/workflow_activities
   marketing_automation/testing_running
   marketing_automation/understanding_metrics
   marketing_automation/campaign_templates
