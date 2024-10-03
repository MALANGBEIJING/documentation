:show-content:

===========
招聘
===========

Odoo 通过一系列预先配置的步骤和阶段，帮助组织所有的求职者。每个阶段都有具体的步骤，例如安排电话会议、进行面试或发送工作邀请。这一过程称为“申请人流程”。

当申请人申请职位时，Odoo 的 *Recruitment* 应用会自动为该职位创建一个 *申请人卡片*。随着申请人在招聘流程中的进展，招聘团队会将他们的卡片从一个阶段移动到下一个阶段。

:ref:`阶段可以配置 <recruitment/modify-stages>`，以便当申请人的卡片进入某个阶段时，系统会使用预先配置的模板自动发送电子邮件。这些自动化的电子邮件由申请人流程中的每个阶段定义。

本文档中解释的申请人流程是 Odoo 的默认流程，并使用 *Recruitment* 应用的默认配置进行。申请人流程可以根据任何企业的具体招聘流程进行修改。

.. note::
   申请人流程及其所有阶段是通用的，适用于所有职位，除非另有说明。:ref:`可以将某个特定阶段配置为职位特定的 <recruitment/customize-stages>`，这意味着该特定阶段仅对该特定职位可见。否则，如果创建了新的阶段或修改了现有阶段，这些更改将应用于所有职位。

.. _recruitment/settings:

设置
========

在 Odoo 中创建职位之前，首先配置 *Recruitment* 应用的必要设置。要查看和编辑设置，请导航到 :menuselection:`Recruitment app --> Configuration --> Settings`。进行任何更改后，点击左上角的 :guilabel:`保存` 按钮以保存所有更改。

职位发布
-----------

*Recruitment* 应用的 :guilabel:`职位发布` 设置部分只有一个选项。如果要将职位发布到公司网站上，请启用 :guilabel:`在线发布` 选项。

.. note::
   只有在安装了 *Website* 应用时，:guilabel:`在线发布` 选项才可用。

招聘流程
-------------------

设置页面的 :guilabel:`招聘流程` 部分指定了数据库在招聘过程中可以执行的操作。

发送面试调查
~~~~~~~~~~~~~~~~~~~~~

Odoo 可以向申请人发送调查，以收集更多关于他们的信息。调查可以被视为考试或问卷，并且可以以多种方式定制，为招聘团队提供申请人的宝贵见解。

启用 :guilabel:`发送面试调查` 选项即可向申请人发送调查。启用后，会出现 :icon:`fa-arrow-right` :guilabel:`面试调查` 内部链接。点击 :icon:`fa-arrow-right` :guilabel:`面试调查` 链接，查看已创建的所有调查列表。

该列表包括数据库中创建的所有调查，而不仅仅是 *Recruitment* 应用中使用的调查。如果尚未创建任何调查，则调查列表会显示 :guilabel:`未找到调查` 消息，并提供从几个预先配置的调查模板中创建调查的选项。

.. seealso::
   有关调查的详细信息，请参阅 :doc:`survey essentials <../marketing/surveys/create>` 文档。

.. note::
   启用 :guilabel:`发送面试调查` 选项时，如果尚未安装 *Surveys* 应用，则在保存设置后会自动安装该应用。

发送短信
~~~~~~~~

可以直接通过 *Recruitment* 应用向申请人发送短信。要这样做，启用 :guilabel:`发送短信` 选项。此选项需要购买积分，启用该功能后，可以通过点击出现的 :icon:`fa-arrow-right` :guilabel:`购买积分` 内部链接进行购买。

.. seealso::
   有关更多信息，请参阅 :doc:`SMS pricing and FAQs <../marketing/sms_marketing/pricing_and_faq>` 文档。

简历显示
~~~~~~~~~~

当申请人提交申请时，默认要求填写简历或 :abbr:`CV (curriculum vitae)`。所有简历都存储在 *Documents* 应用中，可以通过申请人的卡片访问。

可以选择在申请人表单上显示简历，点击申请人卡片即可查看简历。简历显示在屏幕右侧。如果未启用此功能，简历可以通过聊天中的链接访问，需要点击展开查看或下载。

启用 :guilabel:`简历显示` 选项，即可默认在申请人卡片上显示简历，并且可以通过文档链接查看。当启用时，简历显示在申请人卡片的右侧。

.. note::
   要在右侧显示简历，浏览器窗口必须处于全屏模式（浏览器覆盖整个屏幕）。

   如果浏览器窗口未设置为全屏（不是全屏模式），则简历不会显示在右侧。相反，简历会出现在申请人卡片下方的 :guilabel:`文件` 部分。

.. image:: recruitment/cv-display.png
   :align: center
   :alt: 在申请人卡片右侧显示的简历。

简历数字化 (OCR)
~~~~~~~~~~~~~~~~~~~~~

无论是通过在线申请提交、发送简历到职位别名的电子邮件，还是直接从数据库创建申请记录，Odoo 都可以从简历中提取申请人的姓名、电话号码和电子邮件地址，并填充到申请人的表单中。要启用此功能，请启用 :guilabel:`简历数字化 (OCR)` 选项。

启用后，会出现额外的选项。点击相应的单选按钮，选择以下选项之一：

- :guilabel:`不进行数字化`: 关闭简历数字化功能。
- :guilabel:`仅按需数字化`: 仅在请求时数字化简历。申请人卡片上会出现 :guilabel:`数字化文档` 按钮。点击后，简历将被扫描，并更新申请人卡片。
- :guilabel:`自动数字化`: 所有提交的简历将自动进行数字化处理。

在这些选项下方还有两个附加链接。点击 :icon:`fa-arrow-right` :guilabel:`购买积分` 按钮以购买简历数字化的积分。点击 :icon:`fa-arrow-right` :guilabel:`查看我的服务` 链接可查看所有当前服务及其剩余积分余额。

有关文档数字化和 :abbr:`IAP (应用内购买)` 的更多信息，请参阅 :doc:`In-app purchase (IAP) <../essentials/in_app_purchase>` 文档。

.. note::
   :guilabel:`不要数字化` 选项对于 :guilabel:`简历数字化 (OCR)` 可能一开始看起来是重复的。这似乎与禁用 :guilabel:`简历数字化 (OCR)` 选项相同。

   当启用 :guilabel:`简历数字化 (OCR)` 选项时，会安装一个模块，以便扫描简历。禁用此选项将卸载该模块。

   如果有一段时间希望暂时停止简历数字化，可以选择 :guilabel:`不要数字化` 选项。此选项的存在是为了防止卸载模块，从而在将来只需选择另外两个选项之一即可重新启用数字化。

薪资包配置器
~~~~~~~~~~~~~~~~~~~~~~~~~~~

在向申请人发送报价时，可以为报价设置有效期。在 :guilabel:`天数` 字段中输入报价有效的天数。在设定的天数之后，如果申请人未接受报价，则该报价将不再可用。

看板视图
===========

要访问某职位的看板视图，请导航到主 :menuselection:`Recruitment app` 仪表板，这是打开应用时的默认视图。所有职位都会显示在主仪表板上。点击职位卡片上的 :guilabel:`(#) 新申请` 智能按钮，即可导航到该职位所有申请人的看板视图。

.. image:: recruitment/new-applicants-button.png
   :align: center
   :alt: 职位卡片主仪表板视图，显示新申请按钮。

在职位申请页面中，会显示看板阶段，每个申请人都会在相应的列中，表示他们当前所处的阶段。Odoo 中有六个默认阶段：

- :ref:`新 <recruitment/new>`
- :ref:`初步资格审查 <recruitment/initial-qualification>`
- :ref:`第一次面试 <recruitment/first-interview>`
- :ref:`第二次面试 <recruitment/second-interview>`
- :doc:`合同提议 <recruitment/offer_job_positions>`
- :ref:`合同签署 <recruitment/offer_job_positions/contract-signed>`

最后一列 :guilabel:`合同签署` 是默认折叠的。折叠的列显示为灰色，申请人会被隐藏。要展开折叠的阶段并查看该列的申请人卡片，请点击显示阶段名称的细灰列，该列会展开，显示申请人。

.. image:: recruitment/stages.png
   :align: center
   :alt: 在看板视图中点击以展开折叠列。

每个阶段名称下方都有一个颜色编码的条，提供该阶段中申请人的状态信息。状态颜色如下：

- :guilabel:`绿色`: 申请人准备进入下一阶段。
- :guilabel:`红色`: 申请人被阻止进入下一阶段。
- :guilabel:`灰色`: 申请人仍在当前阶段进行中，尚未准备好或被阻止进入下一阶段。

每个卡片的状态是手动设置的。要设置状态，请点击申请人卡片左下角的小圆圈。会弹出状态选择窗口，点击申请人的所需状态。申请人卡片上的状态点和状态条会更新。

.. image:: recruitment/status-dots.png
   :align: center
   :alt: 申请人卡片状态和状态条。

.. tip::
   如果需要，可以修改三种状态颜色（`进行中`、`阻止` 和 `准备进入下一阶段`）的名称 :ref:`<recruitment/modify-stages>`。

.. _recruitment/customize-stages:

自定义阶段
================

阶段可以进行修改、添加或删除，以最佳满足特定企业的招聘步骤。

新阶段
---------

要创建新阶段，请点击 :icon:`fa-plus` :guilabel:`阶段`，将出现一个新列。在 :guilabel:`阶段标题` 字段中输入新阶段的标题，然后点击 :guilabel:`添加`。新列会出现，并可以继续创建另一个新阶段。如果不需要新的阶段，点击屏幕上的任意位置退出新阶段创建。

.. image:: recruitment/add-column.png
   :align: center
   :alt: 点击加号以添加新列到看板阶段。

.. _recruitment/modify-stages:

修改阶段
------------

要修改阶段的设置，将鼠标悬停在阶段名称上，右上角会出现一个 :icon:`fa-cog` :guilabel:`(齿轮)` 图标。点击 :icon:`fa-cog` :guilabel:`(齿轮)` 图标，会出现一个菜单。然后点击 :guilabel:`编辑` 选项。会出现 :guilabel:`编辑: (阶段)` 表单。进行任何所需的修改，然后点击 :guilabel:`保存并关闭`。

.. image:: recruitment/gear.png
   :align: center
   :alt: 将鼠标悬停在列名上时出现的齿轮图标，以及点击后显示的下拉菜单。

.. _recruitment/edit-stage:

编辑阶段表单
~~~~~~~~~~~~~~~
:guilabel:`编辑: (阶段)` 表单用于配置阶段的设置。唯一必填字段是 :guilabel:`阶段名称`。

需要填写或修改的字段有：

- :guilabel:`阶段名称`: 输入阶段的名称。
- :guilabel:`电子邮件模板`: 从下拉菜单中选择一个电子邮件模板。如果选择了模板，当申请人卡片进入该阶段时，系统会自动向申请人发送一封使用选定模板的电子邮件。
- :guilabel:`在看板中折叠`: 勾选此框，使该阶段始终以折叠（隐藏）形式出现在默认视图中。
- :guilabel:`聘用阶段`: 勾选此框，如果此阶段表示申请人已被聘用。当申请人的卡片进入此阶段时，卡片右上角会显示一个 :guilabel:`已聘用` 的横幅。如果勾选了此框，此阶段将用于确定申请人的聘用日期。
- :guilabel:`职位特定`: 如果该阶段仅适用于特定的职位，请从下拉菜单中选择职位。可以选择多个职位。
- :guilabel:`在推荐中显示`: 勾选此框，若该阶段应在 *推荐* 应用中显示，并允许推荐人当其推荐的申请人到达此阶段时累计积分。如果此功能启用，会出现一个 :guilabel:`积分` 字段。输入当申请人进入该阶段时员工获得的推荐积分。要使用此选项，必须安装 *推荐* 应用。
- :guilabel:`工具提示` 部分: 每个申请人卡片都有三个预配置的状态标签（彩色圆圈），指示其状态。这些颜色显示在每个阶段的顶部，以反映该阶段中申请人的状态。可以修改这些标签的 *名称*，但标签本身（颜色）不能更改。默认名称和标签是：:guilabel:`进行中`（灰色）、:guilabel:`阻止`（红色）和 :guilabel:`准备进入下一阶段`（绿色）。
- :guilabel:`要求`: 输入此阶段的任何内部说明，以解释该阶段的要求。

删除阶段
------------

如果不再需要某个阶段，可以删除该阶段。要删除阶段，将鼠标悬停在阶段名称上，右上角会出现一个 :icon:`fa-cog` :guilabel:`(齿轮)` 图标。首先，点击 :icon:`fa-cog` :guilabel:`(齿轮)` 图标以显示下拉菜单，然后点击 :guilabel:`删除`。会弹出一个 :guilabel:`确认` 弹窗，警告并询问 :guilabel:`您确定要删除此列吗？` 点击 :guilabel:`删除` 来删除该列。

.. important::
   如果在删除阶段时，该阶段中仍有申请人，会弹出错误信息。需要将该阶段中的记录删除、归档或移动到其他阶段后，才能删除该阶段。

电子邮件模板
===============

为了与申请人进行沟通，Odoo 提供了多个预配置的电子邮件模板可以使用。以下是预配置电子邮件模板及其使用场景：

- :guilabel:`招聘: 申请确认`: 此模板用于告知申请人已收到他们的申请。当申请人进入 :guilabel:`新建` 阶段时，系统会自动发送此电子邮件。
- :guilabel:`招聘: 感兴趣`: 此模板用于告知申请人他们的申请引起了招聘人员的注意，并且他们已被列入电话或面试的候选名单。
- :guilabel:`招聘: 安排面试`: 此模板用于告知申请人他们已通过 :guilabel:`初步资格审查` 阶段，并会联系他们安排与招聘人员的面试。当申请人进入 :guilabel:`初步资格审查` 阶段时，系统会自动发送此电子邮件。
- :guilabel:`招聘: 不再感兴趣`: 此模板用于当申请人告知他们不再对该职位感兴趣时，表示感谢并祝愿其未来成功。
- :guilabel:`招聘: 拒绝`: 此模板用于告知申请人他们不再被考虑为该职位的候选人。

.. note::
   可以创建、修改和删除电子邮件模板，以满足企业的需求。有关电子邮件模板的更多信息，请参阅 :doc:`../general/companies/email_template` 文档。

要手动发送电子邮件，请点击聊天框中的 :guilabel:`发送消息`。会出现一个文本框，以及申请人的电子邮件地址。

.. image:: recruitment/full-composer.png
   :align: center
   :alt: 从聊天框发送电子邮件。

点击聊天框中 :guilabel:`发送消息` 选项卡右下角的 :icon:`fa-expand` :guilabel:`(展开)` 全编写图标。会弹出一个 :guilabel:`编写电子邮件` 弹窗，收件人和主题字段会预先填充。申请人的电子邮件地址已输入 :guilabel:`收件人` 行，:guilabel:`主题` 显示为 `(职位)`。电子邮件正文默认是空的。

要使用预配置的电子邮件模板，请点击窗口底部的 :guilabel:`加载模板` 字段旁边的下拉菜单。选择要使用的电子邮件模板。

预配置的电子邮件模板可能包含动态占位符，因此可以在电子邮件中填入唯一信息，以便向申请人发送更个性化的消息。可供选择的预配置电子邮件模板有多个。根据选择的模板，电子邮件的主题和/或正文可能会更改。
.. note::
   只有为模型配置的电子邮件模板才会加载。Odoo中还有其他预配置的电子邮件模板，
   但如果它们未配置用于招聘应用程序，则不会出现在可用模板列表中。

如果需要添加附件，请单击左下角的 :guilabel:`附件` 按钮。导航到要附加的文件，
然后单击 :guilabel:`打开` 以附加它。要删除附件，请单击附件右侧的 
:icon:`fa-close` :guilabel:`(删除)` 图标。

如果需要对电子邮件进行修改，可以编辑电子邮件的正文。如果要保存编辑内容以供将来使用，
可以将电子邮件保存为新模板。单击底部的 :guilabel:`保存模板` 按钮。
要发送电子邮件，请单击 :guilabel:`发送`，邮件将发送给申请人。然后该邮件会出现在对话中。

.. image:: recruitment/send-survey.png
   :align: center
   :alt: 使用预配置模板向申请人发送自定义调查（也称为面试表）。

.. seealso::
   - :doc:`recruitment/new_job`
   - :doc:`recruitment/add-new-applicants`
   - :doc:`recruitment/schedule_interviews`
   - :doc:`recruitment/offer_job_positions`
   - :doc:`recruitment/refuse_applicant`
   - :doc:`recruitment/source_analysis`
   - :doc:`recruitment/recruitment_analysis`
   - :doc:`recruitment/time_in_stage`
   - :doc:`recruitment/team_performance`

.. toctree::
   :titlesonly:

   recruitment/new_job
   recruitment/recruitment-flow
   recruitment/add-new-applicants
   recruitment/schedule_interviews
   recruitment/offer_job_positions
   recruitment/refuse_applicant
   recruitment/source_analysis
   recruitment/recruitment_analysis
   recruitment/time_in_stage
   recruitment/team_performance
