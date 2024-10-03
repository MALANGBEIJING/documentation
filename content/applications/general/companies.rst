:show-content:
=========
公司管理
=========

集中的管理环境允许管理员同时选择多个公司，并设置各自的仓库、客户、设备和联系人。此功能还可以生成汇总数据报告，无需切换界面，从而简化日常任务并增强整体管理流程。

.. danger::
   在 Odoo 数据库中使用多公司环境需要将订阅从 *标准*（或 *一款应用免费*）更改为 *自定义* 计划。如果管理员在添加另一家公司后未将订阅迁移到 *自定义* 计划，则数据库可能会被停用。有关详细信息，请咨询 `Odoo 定价页面 <https://www.odoo.com/pricing-plan>`_。

   如果客户处于 *标准* 计划，并在数据库中添加了多公司功能，则会触发 *自定义* 计划的升级。

   如果他们处于 *年度* 或 *多年* 合同中，添加多公司功能会创建一个升级订单，具有 30 天期限（风险为停用）。如果在此期间完全移除了多公司功能，则当数据库再次检查时，升级订单和期限会自动取消。

   如果客户处于 *月度* 合同中，在生成下一个账单时，如果仍检测到多公司功能且他们处于 *标准* 计划中，他们将自动切换并按照 *自定义* 计划收费。

   如果客户希望返回 *标准* 计划，必须联系客户成功团队。

要创建新公司，请转到 :menuselection:`设置应用 --> 公司部分`，然后点击 :guilabel:`管理公司`。接着，点击 :guilabel:`新建` 以创建新公司。

填写出现的新公司表单。

.. tip::
   要归档公司，请转到 :menuselection:`设置应用 --> 公司部分 --> 管理公司`。然后勾选要归档的公司左侧的复选框。如果 :guilabel:`公司` 页面未以列表视图显示，请点击页面右上角的 :guilabel:`≣ (四条线)` 图标。

   选择相应的公司后，点击 :guilabel:`⚙️ 操作` 图标，并从下拉菜单中选择 :guilabel:`归档`。

   若要确保归档公司的所有记录均已归档，请联系 Odoo 的 `支持团队 <https://www.odoo.com/help>`_。

   如果某些记录未归档，可能会重新激活归档的公司，从而再次触发升级。

.. _companies/manage:

管理公司和记录
================

转到 :menuselection:`设置应用 --> 公司部分 --> 管理公司`。然后，点击 :guilabel:`新建` 并填写公司信息表单，或选择现有公司进行编辑。

.. image:: companies/company-info.png
   :align: center
   :alt: Odoo 中新公司表单概览。

.. tip::
   激活 :ref:`开发者模式 <developer-mode>` 以设置社交媒体账户和公司特定的电子邮件参数。请参阅有关 :doc:`../marketing/social_marketing` 和 :doc:`email_communication` 的文档。

   在 :ref:`开发者模式 <developer-mode>` 下，公司表单中还可以设置 :guilabel:`母公司`。

在公司之间切换
----------------

要在多个公司之间切换（或选择）公司，可以点击位于数据库任意位置右上角菜单栏中的公司名称。勾选所需公司名称旁边的复选框以激活它们。高亮的公司代表当前正在使用的环境。要切换环境，请点击所需的公司名称。

.. example::
   在下面的例子中，用户可以访问八家公司，已激活两家公司，并且当前数据库环境属于：*我的公司（旧金山）*。

   .. image:: companies/multi-companies-menu-dashboard.png
      :align: center
      :alt: Odoo 主仪表盘中的公司菜单视图。

共享记录
-------------

可以共享数据（例如产品、联系人和设备），或设置为仅针对特定公司显示。要执行此操作，在这些记录的表单上选择以下选项之一：

- *空白字段*：记录在所有公司中共享。
- *添加公司*：记录仅对登录到该特定公司的用户可见。

.. image:: companies/product-form-company.png
   :align: center
   :alt: Odoo 销售中产品表单的公司字段视图。

当从顶部菜单选择一个环境，并添加另一个公司时，记录将在这两个公司之间共享。
=========
分支机构
=========

可以为公司添加分支机构。要添加分支机构，导航至 :menuselection:`设置应用 --> 公司部分 --> 管理公司`，然后从列表中选择所需的公司。在公司详细信息表单中，打开 :guilabel:`分支` 选项卡。要添加分支，请点击 :guilabel:`添加一行`，并填写出现的 :guilabel:`创建分支` 弹出表单。

.. image:: companies/add-branch.png
   :align: center
   :alt: 添加分支界面，其中高亮显示添加分支和添加一行选项。

.. tip::
   激活 :ref:`开发者模式 <developer-mode>` 以设置社交媒体账户和公司特定的电子邮件系统参数。请参阅有关 :doc:`../marketing/social_marketing` 和 :doc:`email_communication` 的文档。

   分支还可以在 :ref:`开发者模式 <developer-mode>` 中设置 :guilabel:`母公司`。分支的会计和财务本地化设置在 :guilabel:`母公司` 中。为此，请从顶部菜单的 *公司选择器* 中选择公司，并转到 :menuselection:`设置应用 --> 会计 --> 财务本地化`。

.. danger::
   如果数据库处于标准 *付费* 定价计划中，向公司添加分支将触发价格提升。因为添加一个或多个分支会将数据库转换为多公司设置，因此需要切换到 *自定义* 定价计划。这不会影响处于 *一款应用免费* 计划的数据库。

   有关定价的更多信息，请参阅 `Odoo 定价页面 <https://www.odoo.com/pricing-plan>`_。

.. _general/employee-access:

员工访问权限
===============

一旦创建了公司，就可以管理员工的 *多公司* 访问权限。

要访问 *访问权限*，请导航至 :menuselection:`设置应用 --> 用户部分 --> 管理用户`。

在 :guilabel:`用户` 页面中，从列表中选择要修改的用户。然后，可以更改 :guilabel:`允许的公司` 或 :guilabel:`默认公司` 字段。

可以为 :guilabel:`允许的公司` 设置多个公司，而 *默认公司* 只能设置一个。

.. image:: companies/access-rights-multi-companies.png
   :align: center
   :alt: Odoo 用户表单中的多公司字段视图，显示在访问权限标签下。

如果管理员在数据库中激活了多个公司，并正在编辑记录，则编辑将发生在记录相关的公司上。

.. example::
   如果在 `JS Store US` 环境下编辑了销售订单，但工作在 `JS Store Belgium` 环境中，则更改将应用于 `JS Store US`（即生成该销售订单的公司）。

创建记录时，考虑的公司为：

- 屏幕右上角公司选择器中当前选择的公司（已高亮/激活的公司）

**或**

- 未设置公司（因为未在产品和联系人表单中设置公司）

**或**

- 设置的公司是与文档关联的公司（如同编辑记录时一样）

文档格式
============

要根据各个公司设置文档格式，请 *激活* 并 *选择* 相应的公司，并在 :menuselection:`设置应用 --> 公司部分` 下，点击 :guilabel:`配置文档布局`，并根据需要编辑信息。

.. image:: companies/document-layout.png
   :align: center
   :alt: Odoo 设置页面中文档布局字段视图。

:guilabel:`公司详细信息` 可在文档布局中编辑。默认情况下，此字段是从以下路径中列出的公司信息中填充的： :menuselection:`设置应用 --> 公司部分 --> 管理公司`，然后从列表中选择公司。

.. _general/inter-company:

公司间交易
============

首先，激活 :ref:`开发者模式 <developer-mode>`。然后，确保每家公司都正确设置了以下内容：

- :doc:`科目表 <../finance/accounting/get_started/chart_of_accounts>`
- :doc:`税务 <../finance/accounting/taxes>`
- :doc:`财务职位 <../finance/accounting/taxes/fiscal_positions>`
- :doc:`日记账 <../finance/accounting/bank>`
- :doc:`财务本地化 <../finance/fiscal_localizations>`
- :doc:`价格表 <../sales/sales/products_prices/prices/pricing>`

接下来，导航至 :menuselection:`设置应用 --> 公司部分 --> 管理公司`。然后，从列表中选择所需的公司。在公司表单中，选择单个公司详细信息表单上的 :guilabel:`公司间交易` 选项卡。

激活并选择相关公司后，选择以下 :guilabel:`规则` 选项之一：

- :guilabel:`不同步`: 不同步任何公司间交易。
- :guilabel:`同步发票/账单`: 当公司确认所选公司的发票/账单时，生成发票/账单。
- :guilabel:`同步销售订单`: 当确认所选公司的销售订单时，使用所选公司仓库生成草稿销售订单。如果应将草稿销售订单验证，而不是草稿订单，请启用 :guilabel:`自动验证`\*。
- :guilabel:`同步采购订单`: 当确认所选公司的采购订单时，使用所选公司仓库生成草稿采购订单。如果应将草稿采购订单验证，而不是草稿订单，请启用 :guilabel:`自动验证`\*。
- :guilabel:`同步销售和采购订单`: 当确认所选公司的销售/采购订单时，使用所选公司仓库生成草稿采购/销售订单。如果应将草稿采购/销售订单验证，而不是草稿订单，请启用 :guilabel:`自动验证`\*。

  \* 需要选择给定选项，才能在配置中显示 :guilabel:`自动验证`。

.. image:: companies/inter-company-transactions.png
   :align: center
   :alt: Odoo 设置页面中的公司间交易字段视图。

.. note::
   产品 **必须** 配置为 :guilabel:`可销售`，并在公司之间共享。请参阅 :doc:`../inventory_and_mrp/inventory/product_management/configure/type`。

.. example::
   :guilabel:`同步发票/账单`: 在 `JS Store Belgium` 上发布的针对 `JS Store US` 的发票会自动生成一张供应商账单，并在确认销售/采购订单时，使用所选公司仓库生成草稿采购/销售订单。如果应将草稿采购/销售订单验证，而不是草稿订单，请启用 :guilabel:`自动验证`。

   :guilabel:`同步销售/采购订单`: 当在 `JS Store Belgium` 确认 `JS Store US` 的销售订单时，`JS Store Belgium` 上会自动创建（并确认，如果启用了 :guilabel:`自动验证` 功能）采购订单。

.. tip::
   请记得以 *非管理员* 用户的身份测试所有工作流程。

.. seealso::
   - :doc:`多公司指南 <../../developer/howtos/company>`
   - :doc:`../finance/accounting/get_started/multi_currency`

.. toctree::
   :titlesonly:

   companies/digest_emails
   companies/email_template
