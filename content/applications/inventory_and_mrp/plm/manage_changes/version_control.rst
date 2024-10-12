===============  
版本控制  
===============  

.. |BOM| replace:: :abbr:`BoM (物料清单)`  
.. |BOMs| replace:: :abbr:`BoMs (物料清单)`  
.. |ECO| replace:: :abbr:`ECO (工程变更单)`  
.. |ECOs| replace:: :abbr:`ECOs (工程变更单)`  

使用Odoo的*产品生命周期管理(PLM)*来管理物料清单(BOM)的历史版本。存储以前的装配指示、组件详细信息和过去的产品设计文件，同时将这些旧信息保存在生产|BOM|之外。  

在需要时，轻松恢复到以前的|BOM|版本。此外，使用*PLM*追踪哪些|BOM|版本在特定日期处于活动状态，以应对召回或客户投诉。  

每个|BOM|版本都存储在*工程变更单(ECO)*中，以便有组织地进行测试和改进，而不会中断正常的生产操作。  

.. seealso::  
   :ref:`工程变更单 <plm/eco>`  

当前BOM版本  
===================  

要查看生产中使用的当前|BOM|版本，请转到 :menuselection:`PLM应用 --> 主数据 --> 物料清单`，并从列表中选择所需的|BOM|。然后，切换到:guilabel:`杂项`选项卡，当前活动的|BOM|版本将在:guilabel:`版本`字段中显示。  

.. note::  
   也可以从 :menuselection:`制造应用 --> 产品 --> 物料清单` 访问|BOM|。  

.. image:: version_control/current-version.png  
   :align: center  
   :alt: 在杂项选项卡中显示当前版本BOM。  

版本历史  
===============  

要管理所有过往、当前和未来的|BOM|版本，请先导航到 :menuselection:`制造应用 --> 产品 --> 物料清单` 并点击所需的|BOM|。  

在|BOM|页面上，点击:guilabel:`ECO`智能按钮，然后通过点击右上角的:guilabel:`≣ (四条水平线)`图标切换到列表视图。  

.. note::  
   仅当安装了*PLM*应用时，:guilabel:`ECO`智能按钮才会在|BOM|上显示。  

.. image:: version_control/eco-smart-button.png  
   :align: center  
   :alt: 在BOM上显示ECO智能按钮。  

在产品的|ECO|列表中，导航到顶部的搜索栏，点击右侧的:guilabel:`▼ (向下箭头)`图标，以访问:guilabel:`筛选器`的下拉菜单。  

接下来，筛选:guilabel:`已完成`的|ECOs|，以查看: |BOM|的修订历史、应用更改的:guilabel:`负责人`，以及|BOM|的:guilabel:`生效日期`。  

点击每个|ECO|以查看与|BOM|相关的过去组件、操作和设计文件。  

.. image:: version_control/eco-list.png  
   :align: center  
   :alt: 显示产品的BOM修订历史的ECO列表。  

.. note::  
   如果:guilabel:`生效日期`字段为空，则|ECO|的:guilabel:`生效`日期自动设置为:guilabel:`尽快生效`，修订历史中不会记录任何日期。  

   .. image:: version_control/no-effective-date.png  
      :align: center  
      :alt: BOM生效日期列表。  

.. tip::  
   检查|BOM|生效时间的一个解决方法是导航到讨论区，悬停在|ECO|移动到 :ref:`关闭阶段 <plm/eco/stage-config>` 的时间上。  

设计文件  
============  

将计算机辅助设计(CAD)文件、PDF、图片或其他设计材料附加到|BOM|本身。  

要进行操作，请导航到 :menuselection:`PLM应用 --> 主数据 --> 物料清单`，并选择所需的|BOM|。在|BOM|上，导航到讨论区，并点击:guilabel:`📎 (回形针)`图标。  

与|BOM|关联的文件显示在:guilabel:`文件`部分。要添加更多设计文件，请选择:guilabel:`附加文件`按钮。  

.. image:: version_control/attach-files.png  
    :align: center  
    :alt: 显示讨论区中的回形针图标，用于将文件附加到BOM。  

在ECO中管理设计文件  
-----------------------------  

在|ECO|中添加、修改和删除文件。一次|ECO|被批准并应用，新文件会自动链接到生产|BOM|。归档文件从|BOM|中删除，但仍可在|ECO|中访问。  

要在|ECO|中管理设计文件，首先导航到 :menuselection:`PLM应用 --> 变更` 并选择所需的|ECO|。接下来，点击:guilabel:`文档`智能按钮，打开:guilabel:`附件`页面。  

悬停在每个附件上，显示:guilabel:`︙ (三点)`图标。从这里，可以选择:guilabel:`编辑`、:guilabel:`删除`或:guilabel:`下载`文件。对这些文件所做的任何更改都包含在|ECO|中，只有当:ref:`更改被应用 <plm/eco/apply-changes>`后，这些更改才会应用于生产|BOM|。  

.. example::  
   在`创建60%键盘`|ECO|中，设计文件来自原始的`100%键盘`|BOM|。要替换键盘PDF，首先选择:guilabel:`文档`智能按钮。  

   .. image:: version_control/documents-smart-button.png  
      :align: center  
      :alt: 显示活跃ECO中的“文档”智能按钮。  

   在:guilabel:`附件`页面上，悬停在`100%键盘手册.pdf`设计文件上，点击:guilabel:`︙ (三点)`图标。然后，点击:guilabel:`删除`选项以归档文件。  

   接下来，在相同的:guilabel:`附件`页面上，点击:guilabel:`上传`按钮，上传新的设计文件，命名为`60%键盘手册`。  

   .. image:: version_control/attachments.png  
      :align: center  
      :alt: 从“文档”智能按钮查看“附件”页面。显示一个已归档的附件和一个新上传的附件。  

.. note::  
   已归档的文件**不会**被永久删除——它们仍然可以在以前的|ECO|中访问，或作为最新|ECO|中的归档文件进行访问。  

应用重新基线  
============  

Odoo简化了在同一产品上并行|ECO|的合并冲突解决。  

当生产|BOM|更新时，同时有其他|ECO|正在修改先前的版本，可能会发生冲突。新旧生产|BOM|之间的差异显示在:guilabel:`以前的Eco Bom更改`选项卡中，只有在这种情况下才会显示。  

要解决冲突并保留|ECO|更改，请点击:guilabel:`应用重新基线`按钮。  

.. example::  
   当前|BOM|版本为`5`时，创建了两个|ECO|，`ECO0011`和`ECO0012`。在`ECO0011`中，添加了一个新组件`空格稳定器`，并且更改被应用。这意味着当前|BOM|版本已经变为`6`。  

   .. image:: version_control/branch-change.png  
      :align: center  
      :alt: 应用更改以更新生产BOM的ECO。  

   这意味着`ECO0012`正在修改过时的|BOM|。如:guilabel:`以前的Eco Bom更改`选项卡所示，|BOM|缺少`空格稳定器`。  

   为确保`ECO0011`所应用的更改在`ECO0012`的更改中得以保留，请点击:guilabel:`应用重新基线`按钮，以应用以前的|ECO|更改，而不会影响已经在`ECO0012`中做出的更改。  

   .. image:: version_control/merge-change.png  
      :align: center  
      :alt: 点击*应用重新基线*按钮以更新BOM以匹配生产BOM。  