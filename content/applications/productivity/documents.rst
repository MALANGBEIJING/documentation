=========
文档管理
=========

**Odoo 文档** 允许您在 Odoo 内存储、查看和管理文件。

您可以上传任何文件（在 Odoo 在线版中每个文件的最大上传限制为 64MB），并将它们组织到不同的工作区中。

.. seealso::
   - `Odoo 文档: 产品页面 <https://www.odoo.com/app/documents>`_
   - `Odoo 教程: 文档基础知识 [视频] <https://www.odoo.com/slides/slide/documents-basics-6841?fullscreen=1>`_
   - `Odoo 教程: 使用文档与您的会计应用集成 [视频] <https://www.odoo.com/slides/slide/accounting-integration-and-workflow-actions-6853?fullscreen=1>`_

配置
=============

通过导航至 :menuselection:`文档 --> 配置 --> 设置`，您可以启用与您活动的特定区域相关的文件集中管理。例如，勾选 :guilabel:`人力资源` 后，您的 HR 文档将自动在 HR 工作区中可用，而与薪资相关的文档则会自动在薪资子工作区中可用。您可以使用下拉菜单更改默认工作区，并通过点击 :icon:`fa-arrow-right` (:guilabel:`内部链接`) 图标编辑其属性。

.. image:: documents/files-centralization.png
   :alt: 启用与您的活动特定区域相关的文件集中管理。

.. note::
   - 如果您启用了会计文件和文档的集中管理，您需要点击 :guilabel:`日记账` 并独立定义每个日记账以允许自动同步。

     .. image:: documents/accounting-files-centralization.png
        :alt: 启用与您的会计相关的文件集中管理。

   - 如果选择了一个新工作区，现有的文档不会被移动。只有新创建的文档会出现在新的工作区下。

.. _documents/workspaces:

工作区
==========

工作区是具有其自己的一组 :ref:`标签 <documents/tags>` 和 :ref:`操作 <documents/workflow-actions>` 的层级文件夹。默认工作区已存在，但您可以通过导航至 :menuselection:`文档 --> 配置 --> 工作区` 并点击 :guilabel:`新建` 创建您自己的工作区。在新页面中，填写以下信息：

- :guilabel:`名称`
- :guilabel:`父工作区`: 如果您想创建一个子工作区，选择其 :guilabel:`父工作区`。

三个选项卡可供使用：:ref:`标签 <documents/tags>`，:ref:`访问权限 <documents/access-rights>` 和 :ref:`描述 <documents/description>`。

.. _documents/tags:

标签
----

标签用于在工作区内为文档添加区分层次。它们按类别组织，并可使用过滤器进行排序。

从 :guilabel:`标签` 选项卡中，点击 :guilabel:`添加一行`，创建 :guilabel:`标签类别` 并为您的标签命名。

.. note::
   - 父工作区的标签自动应用于子工作区；
   - 标签可以通过导航至 :menuselection:`文档 --> 配置 --> 标签` 创建和修改；
   - 标签也可以通过点击左侧面板的 :icon:`fa-gear` (:guilabel:`齿轮`) 图标创建或编辑；
   - 使用 :ref:`电子邮件别名 <documents/upload>` 可以根据分配的标签自动将接收到的文档发送到特定的工作区。

.. _documents/access-rights:

访问权限
-------------

要管理您的工作区访问权限，请导航至 :guilabel:`访问权限` 选项卡。您可以添加具有查看、创建和编辑工作区文档权限的 :guilabel:`写入组`。您还可以添加只能查看工作区文档的 :guilabel:`读取组`。

.. tip::
   启用 :guilabel:`仅限自己的文档` 来限制 :guilabel:`读取组` 和 :guilabel:`写入组` 只能访问他们作为所有者的文档。

.. _documents/description:

描述
-----------

您可以通过导航至 :guilabel:`描述` 选项卡为工作区添加描述信息。

.. note::
   工作区也可以通过点击左侧面板的 :icon:`fa-gear` (:guilabel:`齿轮`) 图标创建和编辑。

.. _documents/management:

文档管理
====================

选择或打开文档时，右侧面板显示不同的选项，例如：

- :icon:`fa-download` (:guilabel:`下载`);
- :icon:`fa-share-alt` (:guilabel:`分享此选项`): 分享的 URL 会复制到您的剪贴板；
- :icon:`fa-retweet` (:guilabel:`替换`): 选择一个新文件替换现有文件。向下滚动至右侧面板底部可查看 :guilabel:`历史记录` 并 :guilabel:`还原`、:guilabel:`下载` 或 :guilabel:`删除` 文档；
- :icon:`fa-unlock` (:guilabel:`锁定`);
- :icon:`fa-scissors` (:ref:`拆分 <documents/split>`).

您还可以 :icon:`fa-comments` :guilabel:`打开聊天框` 或通过点击 :icon:`fa-trash` (:guilabel:`移至回收站`) 图标删除文档。

.. note::
   移至回收站的项目将在 30 天后永久删除。

要修改文件的名称，点击 :guilabel:`名称`。可以分配一个 :guilabel:`联系人` 或 :guilabel:`所有者`。可以修改相关的 :guilabel:`工作区`，并且可以访问相关的 :guilabel:`日记账分录` 或添加 :guilabel:`标签`。

.. note::
   - :guilabel:`联系人` 是与文档相关的人员，他们只能对文档拥有只读 :ref:`访问权限 <documents/access-rights>`，例如，您数据库中的现有供应商；
   - 文档的创建者自动分配为其 :guilabel:`所有者`，并获得该文档的完整访问权限。要更换文档的所有者，请从下拉列表中选择所需用户作为 :guilabel:`所有者`。

.. tip::
   员工必须是用户并且是文档的所有者，才能在 **我的个人资料** 中查看它。

根据文档存储的工作区，右侧面板底部提供了不同的 :ref:`操作 <documents/workflow-actions>`。

.. _documents/split:

拆分 PDF 文档
-------------------

选择您要拆分的 PDF，并点击 :icon:`fa-scissors` (:guilabel:`剪刀`) 图标。新视图显示文档的所有页面。

默认情况下，点击 :guilabel:`拆分` 时所有页面都会被拆分。要移除两页之间的拆分，点击 :icon:`fa-scissors` (:guilabel:`剪刀`) 图标。

.. image:: documents/split-pdf.png
   :alt: 拆分您的文档

.. tip::
   要从仪表板合并文档，选择它们并点击 :icon:`fa-scissors` (:guilabel:`剪刀`) 图标。点击两个文档之间的剪刀图标，并点击 :guilabel:`拆分` 以合并文档。

其他功能
-------------------

选择一个工作区，并点击 :icon:`fa-caret-down` (:guilabel:`下箭头`) 图标，访问更多功能：

请求文件
~~~~~~~~~

您可以请求文件并将其组织为文档，以提醒用户下载它们。

选择文件应存储的工作区，点击 :icon:`fa-caret-down` (:guilabel:`下箭头`) 图标，然后点击 :guilabel:`请求`。添加 :guilabel:`文档名称` 并在 :guilabel:`请求人` 字段中选择您需要文档的人。您还可以填写 :guilabel:`截止日期`，确认文档应归属的 :guilabel:`工作区`，并添加 :guilabel:`标签` 和 :guilabel:`消息`。然后，点击 :guilabel:`请求`。占位符文档将在工作区中创建。

当文档可用时，点击占位符上传文档。

您可以通过 **活动** 视图和 :guilabel:`请求的文档` 列查看所有缺少的文档。

.. tip::
   在 **活动** 视图中，您可以向您期待提供文档的用户发送 **提醒电子邮件**。转到 :guilabel:`请求的文档` 列，点击 :icon:`fa-ellipsis-v` (:guilabel:`省略号`) 图标，并点击 :guilabel:`文档请求：提醒`。点击日期以查看特定请求的详细信息。您可以点击 :icon:`fa-pencil` (:guilabel:`笔`) 图标更新它，或点击 :guilabel:`立即发送`

      .. image:: documents/reminder-email.png
         :alt: send a reminder email from the Activity view

.. _documents/add-a-link:
添加链接
~~~~~~~~~

要在您的文档仪表板中添加一个链接，请点击 :guilabel:`添加链接`，输入 :guilabel:`URL` 和 :guilabel:`名称`。

分享
~~~~~

您可以通过分享 URL 使文档或工作区对任何人可访问。

分享文档
****************

要生成文档的 **分享链接**，选择该文档，点击 :icon:`fa-caret-down` (:guilabel:`下箭头`)，然后点击 :guilabel:`分享`。

在弹出窗口中，您可以为分享链接 :guilabel:`命名`，通过填写 :guilabel:`有效期至` 字段设置有效期，并选择 :guilabel:`网站` 以确保正确的域名反映在 URL 中。

点击 :guilabel:`复制` 或 :guilabel:`分享` 将 URL 发送给您想要的任何人。

.. tip::
   您还可以通过选择文档，进入右侧面板，点击 :icon:`fa-share-alt` (:guilabel:`分享此选项`) 图标生成分享 URL。

分享工作区
*****************

您可以分享工作区的链接，并允许用户 :guilabel:`下载` 其内容或 :guilabel:`下载和上传` 文件到该工作区。

要分享工作区，请转到仪表板的左侧列。在 :guilabel:`工作区` 部分，选择要分享的工作区，可能还有一个或多个自动添加到上传文档的标签。然后点击 :icon:`fa-caret-down` (:guilabel:`下箭头`) 并选择 :guilabel:`分享`。

在弹出窗口中，显示了一个您可以 :guilabel:`复制` 的分享 :guilabel:`URL`。您可以为分享链接 :guilabel:`命名`，通过填写 :guilabel:`有效期至` 字段设置有效期，勾选 :guilabel:`包含子文件夹` 框（如果您想分享工作区的子文件夹），并选择 :guilabel:`网站` 来确保分享链接反映正确的域名。

然后，允许用户选择 :guilabel:`下载` 工作区中的文件，或者 :ref:`下载和上传 <documents/upload>` 文件到工作区。

.. note::
   - 使用 :ref:`添加链接 <documents/add-a-link>` 选项添加到工作区的链接无法分享，因此会被排除；
   - 当标签应用于共享的工作区时，用户只能访问与那些标签相关的文档。

.. _documents/upload:

通过电子邮件上传
^^^^^^^^^^^^^^^

选择 :guilabel:`下载和上传` 选项，允许用户使用 :guilabel:`电子邮件别名` 上传他们的文件到您的工作区。要创建电子邮件别名，在 :guilabel:`电子邮件别名` 字段中输入名称。默认情况下，:doc:`域名 <../general/email_communication>` 已设置，但您可以通过点击它进行修改。

发送到该电子邮件别名的文档将使用所选的 :ref:`标签 <documents/tags>` 上传到工作区。

.. note::
   - 默认情况下，:guilabel:`文档所有者` 是将文件上传到工作区的人员，但您可以选择其他用户。您还可以设置 :guilabel:`联系人`，通常为外部人员，例如合作伙伴。
   - 启用 :guilabel:`创建新活动` 可在文档上传时自动创建活动。您可以从下拉列表中选择 :guilabel:`活动类型`，并设置 :guilabel:`截止日期`。您还可以添加 :guilabel:`摘要` 和指派负责此活动的 :guilabel:`负责人`。

.. tip::
   转到 :menuselection:`配置 --> 分享和电子邮件` 以查看和管理您的分享链接。选择一行并点击 :guilabel:`删除` 以禁用 URL。收到该链接的人员将无法再访问文档或工作区。

新建电子表格
~~~~~~~~~~~~~~~

要创建一个新的 :doc:`电子表格 <spreadsheet>`，点击 :guilabel:`新建电子表格`。您可以选择 :guilabel:`空白电子表格` 或 :doc:`现有模板 <spreadsheet/templates>`。

.. _documents/workflow-actions:

工作流操作
================

工作流操作有助于管理文档和整体业务操作。这些是为每个工作区创建和自定义的自动化操作。您可以通过单击来执行操作，例如创建、移动、签署文档、添加标签或处理账单。

当文档符合设置的条件时，这些工作流操作会显示在右侧面板中。
创建工作流操作
-----------------

要更新现有的工作流操作或创建新的工作流操作，请转到 :menuselection:`文档 --> 配置 --> 操作` 并点击 :guilabel:`新建`。

.. note::
   操作适用于您选择的 :guilabel:`相关工作区` 下的所有 **子工作区**。

设置条件
-------------

定义 :guilabel:`操作名称`，然后设置触发选择文件时在右侧面板上显示 :icon:`fa-play` (:guilabel:`播放`) 图标的条件。

您可以设置三种基本类型的条件：

#. :guilabel:`标签`：您可以使用 :guilabel:`包含` 和 :guilabel:`不包含` 条件，意味着文件 *必须具有* 或 *不能具有* 此处设置的标签；

#. :guilabel:`联系人`：文件必须与此处设置的联系人相关联；

#. :guilabel:`所有者`：文件必须与此处设置的所有者相关联。

.. image:: documents/basic-condition-example.png
   :alt: Odoo 文档工作流操作的基本条件示例

.. tip::
   如果不设置任何条件，操作按钮会出现在所选工作区内的所有文件上。

高级条件类型：领域
~~~~~~~~~~~~~~~~~~

.. important::
   建议对 Odoo 开发有一定的了解，以便正确配置 *领域* 过滤器。

需要激活 :ref:`开发者模式 <developer-mode>` 才能从 :guilabel:`操作` 选项卡访问 :guilabel:`领域` 条件。完成后，选择 :guilabel:`领域` 条件类型并点击 :guilabel:`新建规则`。

要创建规则，通常选择一个 :guilabel:`字段`、一个 :guilabel:`操作符` 和一个 :guilabel:`值`。例如，如果您想为工作区中的所有 PDF 文件添加工作流操作，请将 :guilabel:`字段` 设置为 *MIME 类型*，将 :guilabel:`操作符` 设置为 *包含*，并将 pdf 设为 :guilabel:`值`。

.. image:: documents/domain-condition-example.png
   :alt: Odoo 文档工作流操作领域条件示例

点击 :icon:`fa-plus` (:guilabel:`添加新规则`) 图标和 :icon:`fa-sitemap` (:guilabel:`添加分支`) 图标以添加条件和子条件。然后可以指定规则是应匹配 :guilabel:`所有` 条件还是 :guilabel:`任何` 条件。您还可以使用 :guilabel:`代码编辑器` 直接编辑规则。

配置操作
------------

选择 :guilabel:`操作` 选项卡设置您的操作。您可以同时执行以下操作：

- **移动到工作区**：将文件移动到任何工作区；
- **创建**：创建附加到数据库中的文件之一：

  - **链接到记录**：在文档和特定模型的记录之间创建链接；
  - **产品模板**：创建一个可以直接编辑的产品；
  - **任务**：创建一个可以直接编辑的项目任务；
  - **签名 PDF 模板**：创建一个新的签名模板以发送；
  - **待签署的 PDF**：创建一个可直接签署的签名模板；
  - **申请人**：创建一个可以直接编辑的 HR 申请；
  - **供应商账单**：使用 OCR 和 AI 从文件内容中提取信息创建供应商账单；
  - **客户发票**：使用 OCR 和 AI 从文件中提取信息创建发票；
  - **供应商信用凭证**：使用 OCR 和 AI 从文件中提取信息创建供应商信用凭证；
  - **信用凭证**：使用 OCR 和 AI 从文件中提取信息创建客户信用凭证；
  - **杂项操作**：在杂项操作日记帐中创建条目；
  - **银行对账单**：导入银行对账单；
  - **采购收据**：创建供应商收据；
  - **费用**：创建 HR 费用。

- **设置联系人**：向文件添加联系人，或将现有联系人替换为新联系人；
- **设置所有者**：向文件添加所有者，或将现有所有者替换为新所有者；
- **设置标签**：添加、删除和替换任意数量的标签；
- **活动 - 全部标记为已完成**：将与文件链接的所有活动标记为已完成；
- **活动 - 安排活动**：创建一个与文件链接的新活动，按操作中配置的方式设置活动。您可以选择将活动设置为文档所有者的活动。

.. image:: documents/workflow-action-example.png
   :alt: Odoo 文档工作流操作示例

使用 AI 和光学字符识别 (OCR) 进行文档数字化
============================================

可以数字化存储在财务工作区中的文档。选择要数字化的文档，点击 :guilabel:`创建账单`、:guilabel:`创建客户发票` 或 :guilabel:`创建信用凭证`，然后点击 :guilabel:`发送进行数字化`。

.. seealso::
   :doc:`AI 驱动的文档数字化 <../finance/accounting/vendor_bills/invoice_digitization>`
