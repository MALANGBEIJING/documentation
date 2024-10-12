=========  
审批  
=========  

.. |ECO| replace:: :abbr:`ECO (工程变更单)`  
.. |ECOs| replace:: :abbr:`ECOs (工程变更单)`  

.. _plm/approvals:  

通过将审批者分配到正在审核的 :ref:`工程变更单 <plm/eco>` (ECO) 的阶段，自动通知利益相关者和管理人员。只有在指定审批者接受变更后，才能应用这些变更。审批确保团队成员进行审查，防止错误和过早操作。  

.. seealso::  
   :ref:`阶段配置 <plm/eco/stage-config>`  

添加审批者  
============  

要添加审批者，首先进入 :menuselection:`PLM 应用`，点击ECO类型的项目卡以打开|ECOs|的甘特视图。  

在 :guilabel:`工程变更单`页面上，悬停在所需的阶段上，选择:guilabel:`⚙️ (齿轮)`图标。然后，点击:guilabel:`编辑`以打开弹出窗口。  

.. note::  
   审批者可以添加到任何阶段，但强烈建议将其分配到*验证*阶段，此阶段位于*关闭*阶段之前，关闭阶段是应用|ECOs|并更新:abbr:`BOM (物料清单)`版本的阶段。  

   有关更多信息，请参阅 :ref:`阶段类型 <plm/eco/stage-config>` 的文档。  

.. _plm/approvals/approval-type:  

在:guilabel:`编辑`阶段的弹出窗口中，点击位于:guilabel:`审批`下方的:guilabel:`添加一行`按钮。然后，在:guilabel:`角色`中输入审批者的职位（或职称）（例如`工程经理`、`质量团队`等），并从下拉菜单中选择相关的:guilabel:`用户`。  

接下来，将:guilabel:`审批类型`设置为:guilabel:`需要审批`、:guilabel:`批准，但审批为可选`，或:guilabel:`仅评论`。  

.. example::  
   将`CTO`（首席技术官）“Mitchell Admin”分配为`新产品引入`ECO类型中`验证`阶段的强制审批者。  

   质量团队和市场团队的审批**不需要**应用到|ECO|的更改中，因为它们的:guilabel:`审批类型`分别设置为:guilabel:`可选审批`和:guilabel:`仅评论`。  

   .. image:: approvals/approvers.png  
      :align: center  
      :alt: 设置“需要审批”类型的审批者在“验证”阶段审批ECO。  

管理审批  
============  

审批者可以通过进入 :menuselection:`PLM 应用`，查看ECO类型的项目卡来轻松跟踪他们的待办审批。  

以下是每个ECO项目卡按钮的功能：  

#. :guilabel:`#工程变更`按钮显示该ECO类型中正在进行的|ECOs|的数量。点击该按钮可打开:guilabel:`工程变更单`页面的甘特视图。  
#. :guilabel:`我的验证`显示审批者必须接受或拒绝的|ECOs|数量。点击该按钮会显示待审批或被拒绝的|ECOs|（标有红色:guilabel:`被阻止`状态）。  
#. :guilabel:`所有验证`按钮显示任何审批者等待审批或被拒绝的|ECOs|数量。点击此按钮会显示这些待处理的|ECOs|。  
#. :guilabel:`待应用`显示用户需要应用更改的|ECOs|数量。点击该按钮会显示所有待审批和应用更改的|ECOs|，这些ECOs位于验证阶段。  

   标记为绿色:guilabel:`已完成`阶段的|ECOs|已获得批准，用户只需点击|ECO|进入表单视图，然后点击:guilabel:`应用更改`按钮即可。  

.. image:: approvals/validation-overview.png  
   :align: center  
   :alt: 显示待办验证数量的按钮以及打开筛选ECO列表的按钮。  

审批ECOs  
------------  

登录为指定审批者后，导航到验证阶段的|ECO|，即可看到:guilabel:`批准`、:guilabel:`拒绝`和:guilabel:`应用更改`按钮。  

要批准|ECO|并将更改应用到生产:abbr:`BOM (物料清单)`中，点击:guilabel:`批准`，然后点击:guilabel:`应用更改`。  

请注意，除非先点击了:guilabel:`批准`按钮，否则:guilabel:`应用更改`按钮将**无法**生效。此外，讨论区会记录按钮点击的历史记录。  

.. warning::  
   当:guilabel:`审批类型`**不是**设置为:guilabel:`需要审批`时，应用更改前不需要相关用户的审批。因此，:guilabel:`应用更改`按钮**将正常工作**，而不需要先点击:guilabel:`批准`按钮。  

自动活动  
--------------------  

当|ECO|移动到验证阶段时，自动为指定审批者创建一个计划活动以审核|ECO|。审批者会在活动收件箱中收到通知，可通过页面顶部的:guilabel:`🕘 (时钟)`图标访问。  

在待办任务列表中，:guilabel:`工程变更单 (ECO)`通知显示标记为:guilabel:`迟到`、:guilabel:`今天`和:guilabel:`将来`的活动数量。点击这些按钮中的每一个都会显示相应|ECOs|的筛选甘特视图。  

.. example::  
   计划的活动显示为:guilabel:`🕘 (时钟)`图标上的一个数字，显示有`5`个|ECOs|需要在:guilabel:`今天`审批。目前没有:guilabel:`迟到`或:guilabel:`将来`的|ECOs|。  

    .. image:: approvals/todo-list.png  
       :align: center  
       :alt: 显示用户的计划审批通知。  

点击待处理的|ECO|，讨论区会记录一项*guilabel:`ECO审批`*的计划活动。点击:guilabel:`i (信息)`图标查看更多信息，包括审批的:guilabel:`创建日期`、指定的审批者以及到期日期。  

.. image:: approvals/planned-activity.png  
   :align: center  
   :alt: 显示计划的ECO审批的详细信息。  

后续活动  
~~~~~~~~~~~~~~~~~~~~  

当|ECOs|被拒绝时，需要为项目成员分配任务以进行必要的修改，然后再进行|ECO|审批。要创建带有截止日期的任务，请导航到被拒绝的|ECO|表单，并进入讨论区。  

在讨论区的:guilabel:`计划活动`部分选择:guilabel:`标记为完成`按钮以关闭该活动，并打开一个用于创建任务的弹出窗口。  

.. image:: approvals/mark-as-done.png  
   :align: center  
   :alt: 显示*标记为完成*窗口，显示*完成并安排下一个*、*完成*和*放弃*按钮以关闭计划活动。  

在:guilabel:`标记为完成`窗口中，点击:guilabel:`完成并安排下一个`以打开新的:guilabel:`安排活动`窗口。接下来，设置完成更改的:guilabel:`指派给`团队成员和:guilabel:`到期日期`。在:guilabel:`摘要`字段和文本框中提供任务详细信息。点击:guilabel:`安排`按钮关闭窗口。  

关闭窗口后，在|ECO|表单上，将|ECO|退回到上一个阶段。这样，当团队成员完成更改并将|ECO|返回到验证阶段时，系统会为审批者创建一个新的:guilabel:`ECO审批`任务。  

.. example::  
   审批者为|ECO|的:guilabel:`负责人`“Laurie Poiret”创建一项活动，详细说明了审批者需要接受|ECO|所需的更改。点击:guilabel:`安排`按钮为Laurie创建了一个计划活动，截止日期为`2023年8月15日`。  

   .. image:: approvals/schedule-an-activity.png  
      :align: center  
      :alt: 为被拒绝的ECO创建后续更改的计划活动。  