.. _odoosh-gettingstarted-create:

===================
创建您的项目
===================

部署您的平台
====================

前往 `Odoo.sh <https://www.odoo.sh/>`_ 并点击 *Deploy your platform* 按钮。

.. image:: create/deploy.png
   :align: center

使用 Github 登录
===================

使用您的 Github 帐户登录。如果您还没有帐户，请点击 *Create an account* 链接。

.. image:: create/github-signin.png
   :align: center

授权 Odoo.sh
=================

点击 *Authorize* 按钮授予 Odoo.sh 所需的访问权限。

.. image:: create/github-authorize.png
   :align: center

Odoo.sh 主要需要以下权限：

* 了解您的 Github 登录信息和电子邮件，
* 创建新的存储库，前提是您选择从头开始创建，
* 读取您现有的存储库，包括您所在组织的存储库，以便您从现有存储库开始，
* 创建 webhook 以便在您每次推送更改时收到通知，
* 提交更改以简化您的部署，例如合并分支或添加新的 `submodules <https://git-scm.com/book/en/v2/Git-Tools-Submodules>`_。

提交您的项目
===================

选择您是否希望通过创建一个新存储库从头开始，或是否希望使用现有存储库。

然后，选择一个名称或选择您想要使用的存储库。

选择您想要使用的 Odoo 版本。如果您打算导入现有的数据库或现有的应用程序集，您可能需要选择相应的版本。如果您从头开始，建议使用最新版本。

输入您的 *订阅代码*。这也被称为 *订阅推荐*、*合同编号* 或 *激活码*。

它应该是包含 Odoo.sh 的企业订阅的代码。

合作伙伴可以使用其合作伙伴代码开始试用。若客户想要启动一个项目，他们应获得包括 Odoo.sh 的企业订阅并使用其订阅代码。合作伙伴将获得金额的 50% 作为佣金。请联系您的销售代表或客户经理以获取相关信息。

提交表单后，如果您收到通知提示您的订阅无效，这可能意味着：

* 它不是现有的订阅，
* 它不是合作伙伴订阅，
* 它是企业订阅，但不包括 Odoo.sh，
* 它既不是合作伙伴订阅也不是企业订阅（例如，在线订阅）。

如果对您的订阅有疑问，请联系 `Odoo 支持 <https://www.odoo.com/help>`_。

.. image:: create/deploy-form.png
   :align: center

完成啦！
=============

您可以开始使用 Odoo.sh。您的第一个构建即将创建。您将很快能够连接到您的第一个数据库。

.. image:: create/deploy-done.png
   :align: center

.. _odoo_sh_import_your_database:

导入您的数据库
====================

只要您的数据库处于 Odoo 的 :doc:`支持版本 </administration/supported_versions>`，您就可以将其导入到您的 Odoo.sh 项目中。

将您的模块推送到生产环境
-------------------------------

如果您使用的是社区或自定义模块，请将它们添加到您的 Github 存储库的一个分支中。
托管在 Odoo.com 在线平台上的数据库没有任何自定义模块。
这些数据库的用户可以跳过此步骤。

您可以根据需要组织您的模块，Odoo.sh 将自动检测包含 Odoo 插件的文件夹。例如，您可以将所有模块文件夹放在存储库的根目录中，或按您定义的类别（例如会计、项目等）将模块分组到文件夹中。

对于可在公共 Git 存储库中找到的社区模块，
您也可以考虑使用 :ref:`Submodules <odoosh-advanced-submodules>` 添加它们。

然后，您可以 :ref:`将此分支设置为生产分支 <odoosh-gettingstarted-branches-stages>`，
或 :ref:`将其合并到您的生产分支 <odoosh-gettingstarted-branches-mergingbranches>`。

下载备份
-----------------

本地部署的数据库
~~~~~~~~~~~~~~~~~~~~

访问您的本地部署数据库的 URL :file:`/web/database/manager` 并下载备份。

.. Warning::

  如果您无法访问数据库管理器，则可能已被系统管理员禁用。
  请参阅 :ref:`数据库管理器安全文档 <db_manager_security>`。

您将需要数据库服务器的主密码。如果没有，请联系您的系统管理员。

.. image:: create/create-import-onpremise-backup.png
   :align: center

选择包括文件存储的 zip 作为备份格式。

.. image:: create/create-import-onpremise-backup-dialog.png
  :align: center

Odoo 在线数据库
~~~~~~~~~~~~~~~~~~~~~

`访问您的数据库管理器 <https://accounts.odoo.com/my/databases/manage>`_ 并下载数据库备份。

.. image:: create/create-import-online-backup.png
   :align: center

.. Warning::

  在线版本（例如 *saas-**）不支持在 Odoo.sh 上运行。

上传备份
-----------------

然后，在您的 Odoo.sh 项目中，在生产分支的备份选项卡中，导入您刚刚下载的备份。

.. image:: create/create-import-production.png
   :align: center

备份导入后，您可以通过分支历史记录中的 *Connect* 按钮访问数据库。

.. image:: create/create-import-production-done.png
   :align: center

检查您的外发邮件服务器
---------------------------------

Odoo.sh 提供了一个默认的邮件服务器。
要使用它，您的数据库中 :menuselection:`Settings --> Technical --> Outgoing Mail Servers` 中必须没有启用的外发邮件服务器（必须激活 :ref:`开发者模式 <developer-mode>`）。

在导入数据库后，所有外发邮件服务器都被禁用，因此您将使用 Odoo.sh 提供的默认邮件服务器。

.. warning::
   端口 25 已经关闭并将保持关闭。如果您想连接到外部 SMTP 服务器，应该使用端口 465 和 587。

检查您的计划操作
----------------------------

导入后，所有计划操作都被禁用。

这是为了防止新导入的数据库执行可能影响您运行中的生产环境的操作，例如发送队列中的邮件、处理大规模邮件或与第三方服务同步（如日历、文件托管等）。

如果您打算将导入的数据库作为生产数据库，请启用所需的计划操作。
您可以检查源数据库中启用了哪些操作，并在导入的数据库中启用相同的操作。计划操作位于 :menuselection:`Settings --> Technical --> Automation --> Scheduled Actions` 下。

注册您的订阅
--------------------------

导入后，您的订阅将被解除链接。

导入的数据库默认被视为重复，因此企业订阅将被移除，因为每个订阅只能关联一个数据库。

如果您打算将其作为生产数据库，请将原数据库与订阅解除链接，并注册新导入的数据库。请阅读 :doc:`数据库注册文档 <../../on_premise>` 以获取说明。
