========
分支
========

概述
========

分支视图让您概览您的存储库中不同的分支。

.. image:: branches/interface-branches.png
   :align: center

.. _odoosh-gettingstarted-branches-stages:

阶段
======

Odoo.sh 提供了三个不同的分支阶段：生产、预发布和开发。

您可以通过拖放分支到阶段部分标题来更改分支的阶段。

.. image:: branches/interface-branches-stagechange.png
   :align: center

.. _stage_production:

生产
----------

这是运行您生产数据库代码的分支。
只能有一个生产分支。

当您向此分支推送新的提交时，您的生产服务器将使用新修订版的代码进行更新，并随后重启。

如果您的更改需要更新模块（例如表单视图中的更改），并且您希望自动执行更新，请增加模块清单 (*__manifest__.py*) 中的版本号。
平台将负责执行更新，在此期间，实例将暂时不可用以进行维护。

此方法相当于通过应用菜单执行模块升级，或通过
:code:`-u` 命令行开关进行升级。
:doc:`命令行文档 </developer/reference/cli>`。

如果提交中的更改阻止了服务器重启，或如果模块更新失败，
服务器将自动回滚到先前成功的代码修订版，数据库也会回滚到更新前的状态。
您仍然可以访问失败更新的日志，以便进行故障排除。

演示数据不会加载，因为不建议在生产数据库中使用。
单元测试不会执行，因为这会增加更新期间生产数据库的不可用时间。

试用项目的合作伙伴应注意，他们的生产分支及所有预发布分支将在30天后自动恢复为开发阶段。

预发布
-------

预发布分支旨在使用生产数据测试新功能，而不会用测试记录破坏实际的生产数据库。它们将创建中和的生产数据库副本。

中和包括：

* 禁用计划动作。如果您想测试它们，您可以手动触发其动作或重新启用它们。请注意，如果没有人在使用数据库，平台会减少触发它们的频率以节省资源。
* 禁用外发邮件，并通过邮件拦截器拦截它们。提供了一个
  :ref:`查看邮件的界面 <odoosh-gettingstarted-branches-tabs-mails>`，这样您就不必担心将测试邮件发送给联系人。
* 将支付提供商和运输提供商设置为测试模式。
* 禁用 IAP 服务。

最新的数据库将无限期保留，来自同一分支的较旧数据库可能会被垃圾回收以腾出空间用于新数据库。有效期为3个月，之后您需要重新构建分支。
如果您在这些数据库中进行配置或视图更改，请确保记录它们，或者直接在分支模块中编写它们，使用 XML 数据文件覆盖默认配置或视图。

不会执行单元测试，因为在 Odoo 中，它们当前依赖于演示数据，而演示数据不会加载到生产数据库中。如果将来 Odoo 支持在没有演示数据的情况下运行单元测试，
Odoo.sh 将考虑在预发布数据库上运行测试。

开发
-----------

开发分支使用演示数据创建新数据库以运行单元测试。
安装的模块是分支中包含的模块。您可以在
:ref:`项目设置 <odoosh-gettingstarted-settings-modules-installation>`中更改要安装的模块列表。

当您向这些分支中的一个推送新的提交时，
将启动一个新服务器，使用从头开始创建的数据库以及分支的新修订版。
演示数据将被加载，并且默认情况下将执行单元测试。
这会验证您的更改是否不会破坏任何已测试的功能。如果您愿意，您可以禁用测试或在
:ref:`分支设置 <odoosh-gettingstarted-branches-tabs-settings>`中允许使用自定义标签运行特定测试。

与预发布分支类似，邮件不会发送，而是由邮件拦截器拦截，并且只要数据库未使用，就不会触发计划动作。

开发分支创建的数据库的预期寿命约为三天。
之后，它们可以在没有事先通知的情况下自动被垃圾回收，以腾出空间用于新数据库。

.. _odoosh-gettingstarted-branches-mergingbranches:

合并您的分支
---------------------

您可以通过拖放分支轻松地将其合并到另一个分支中。

.. image:: branches/interface-branches-merge.png
   :align: center

当您想使用生产数据测试开发分支的更改时，您可以：

* 将开发分支拖放到预发布分支上，以便将其合并到所需的预发布分支中，
* 将开发分支拖放到预发布部分标题上，使其成为一个预发布分支。

当您的最新更改准备好进入生产环境时，
您可以将预发布分支拖放到生产分支上，
将最新功能合并并部署到生产环境中。

如果您足够大胆，
也可以将开发分支合并到生产分支中。
这意味着您跳过了通过预发布分支使用生产数据验证更改的步骤。

您可以将开发分支彼此合并，也可以将预发布分支彼此合并。

当然，您也可以直接在工作站上使用 :code:`git merge` 合并您的分支。
当您的分支中推送了新修订版时，Odoo.sh 将会收到通知。

将预发布分支合并到生产分支中只会合并源代码：您在预发布数据库中进行的任何配置更改都不会传递到生产数据库中。

如果您在预发布分支中测试了配置更改，并希望将它们应用于生产环境，您可以选择：

* 将配置更改写入 XML 数据文件中
  覆盖默认配置或分支中的视图，
  然后在模块的清单 (*__manifest__.py*) 中增加模块的版本号，以在将预发布分支合并到生产分支时触发模块更新。
  这是最佳实践，能提高开发的可扩展性，因为您将使用 Git 的版本控制功能
  记录所有配置更改，从而确保更改的可追溯性。
* 手动将它们从预发布分支传递到生产数据库中，通过复制粘贴。

.. _odoosh-gettingstarted-branches-tabs:

标签页
======

历史记录
-------

您的分支历史概览：

* 提交的消息及其作者，
* 与平台相关的各种事件，如阶段更改、数据库导入、备份还原等。

.. image:: branches/interface-branches-history.png
   :align: center

对于每个事件，状态显示在右上角。
它可以提供有关数据库的当前操作信息（安装、更新、备份导入等），
或其结果（测试反馈、成功的备份导入等）。
当操作成功时，您可以通过 *连接* 按钮访问数据库。

.. _odoosh-gettingstarted-branches-tabs-mails:

邮件
-----

此标签包含邮件拦截器。它显示您的数据库发送的电子邮件概览。
邮件拦截器适用于开发和预发布分支，而生产数据库的电子邮件则会真正发送而不是被拦截。

.. image:: branches/interface-branches-mails.png
   :align: center
   :scale: 50%

Shell
-----

可以通过Shell访问您的容器。您可以执行基本的Linux命令（如 :code:`ls`, :code:`top`），
并通过输入 :code:`psql` 打开数据库的Shell。

.. image:: branches/interface-branches-shell.png
   :align: center

您可以打开多个标签页，并通过拖放它们来按照自己的意愿排列布局，
例如并排显示。

.. 注::
  长时间运行的Shell实例不受保证。闲置的Shell可能会随时断开连接以释放资源。

编辑器
------

一个在线集成开发环境 (IDE)，用于编辑源代码。
您还可以打开终端、Python控制台，甚至是Odoo Shell控制台。

.. image:: branches/interface-branches-editor.png
   :align: center

您可以打开多个标签页，并通过拖放它们来按照自己的意愿排列布局，
例如并排显示。

监控
----------

此链接包含当前构建的各种监控指标。

.. image:: branches/interface-branches-monitoring.png
   :align: center

您可以缩放、调整时间范围或选择每个图表上的特定指标。
在图表上，注释有助于您与构建的更改相关联（如数据库导入、git推送等）。

.. _odoosh/logs:

日志
----

查看您的服务器日志的查看器。

.. image:: branches/interface-branches-logs.png
   :align: center

可用的不同日志有：

* install.log：数据库安装日志。在开发分支中，还包括测试日志。
* pip.log：Python依赖项的安装日志。
* odoo.log：运行服务器的日志。
* update.log：数据库更新的日志。
* pg_long_queries.log：执行时间异常长的psql查询日志。

如果日志中添加了新行，它们会自动显示。
如果您滚动到页面底部，浏览器每次添加新行时都会自动滚动。

您可以通过单击视图右上角的相应按钮暂停日志获取。
获取将在5分钟后自动停止。您可以通过点击播放按钮重新启动它。

.. _odoo_sh_branches_backups:

备份
-------

可供下载和还原的备份列表，执行手动备份和导入数据库的能力。

.. image:: branches/interface-branches-backups.png
   :align: center

Odoo.sh会对生产数据库进行每日备份。它保留7个每日备份、4个每周备份和3个每月备份。
每个备份包括数据库转储、文件存储（附件、二进制字段）、日志和会话。

预发布和开发数据库不会备份。
然而，您仍然可以将生产数据库的备份还原到预发布分支中进行测试，
或手动恢复因意外删除生产数据库中的数据。

列表包含保存在托管生产数据库的服务器上的备份。
此服务器仅保留一个月的备份：7个每日备份和4个每周备份。

专用备份服务器保留相同的备份，以及另外3个每月备份。
如需还原或下载这些每月备份，请 `联系我们 <https://www.odoo.com/help>`_。

如果您合并了更新一个或多个模块版本（在 :file:`__manifest__.py` 中）或其相关的Python依赖项（在 :file:`requirements.txt` 中）的提交，
那么 Odoo.sh 将自动执行备份（列表中标记为类型Update），
因为安装新的pip包会更改容器，或者模块更新之后会更改数据库。在这两种情况下，我们都会进行备份，因为这可能会破坏系统。

如果您合并的提交只更改了一些代码而没有上述修改，则 Odoo.sh 不会执行备份，
因为既没有修改容器，也没有修改数据库，所以平台认为这是足够安全的。当然，作为额外的预防措施，您可以在对生产源进行重大更改之前手动备份，以防万一出了问题（这些手动备份约一周内可用）。为了防止滥用，我们将手动备份限制为每天最多5个。

*导入数据库* 功能接受以下格式提供的数据库归档：

* 标准Odoo数据库管理器
  （适用于本地部署的Odoo服务器，位于 :code:`/web/database/manager`）
* Odoo在线数据库管理器，
* 此 *Backups* 标签中的 Odoo.sh 备份下载按钮，
* :ref:`构建视图 <odoosh-gettingstarted-builds>` 中的 Odoo.sh 转储下载按钮。

.. _odoo_sh/upgrade:

升级
-------
可用于有效项目的生产和预发布分支。

.. seealso::
    :doc:`升级文档 <../../upgrade>`

.. _odoosh-gettingstarted-branches-tabs-settings:

设置
--------

在此页面，您可以找到仅适用于当前选定分支的几个设置。

.. image:: branches/interface-branches-settings.jpg
   :align: center

**新提交时的行为**

对于开发和预发布分支，您可以更改分支在接收到新提交时的行为。默认情况下，开发分支会创建一个新的构建，而预发布分支则会更新之前的构建（参见 :ref:`生产阶段 <stage_production>`）。如果您正在开发的功能需要特定的设置或配置，避免每次提交后手动设置，则此选项特别有用。如果您选择为预发布分支创建新构建，那么每次提交推送后，它都会从生产构建中创建一个新的副本。如果将分支从预发布设置为开发状态，它将自动设置为“什么也不做”。

**模块安装**

选择自动为您的开发构建安装的模块。

.. image:: branches/interface-settings-modulesinstallation.png
   :align: center

* *仅安装我的模块* 将只安装分支中的模块，这是默认选项。
  :ref:`子模块 <odoosh-advanced-submodules>` 被排除在外。
* *完全安装（所有模块）* 将安装分支中的模块、子模块中的模块以及Odoo的所有标准模块。在执行完全安装时，测试套件将被禁用。
* *安装模块列表* 将安装输入框中指定的模块。名称为模块的技术名称，必须用逗号分隔。

如果启用了测试，标准Odoo模块套件的运行可能需要长达1小时。
此设置仅适用于开发构建。
预发布构建会复制生产构建，而生产构建仅安装基本模块。

**测试套件**

对于开发分支，您可以选择启用或禁用测试套件。默认情况下是启用的。
启用测试套件时，您可以通过指定测试标签来限制它们 :ref:`测试标签
<developer/reference/testing/selection>`。

**Odoo版本**

仅对于开发分支，您可以更改Odoo的版本，如果您希望测试升级后的代码或在生产数据库升级到新版本的过程中开发功能。

此外，对于每个版本，您有两个选项来处理代码更新。

* 您可以选择自动获取最新的漏洞修复、安全补丁和性能优化。您的Odoo服务器源代码每周都会更新一次。这是“最新”选项。
* 您可以选择将Odoo源代码固定在特定的版本，通过选择日期列表中的一个修订版来实现。修订版将在3个月后过期。当到期日期临近时，您将收到电子邮件通知，如果在此之后没有采取行动，您将自动设置为最新修订版。

**自定义域名**

在此页面，您可以为选定的分支配置额外的域名。可以添加其他 *<name>.odoo.com* 域名或您自己的自定义域名。对于后者，您需要：

* 拥有或购买域名，
* 在此列表中添加域名，
* 在您的域名注册商的域名管理器中，使用 ``CNAME`` 记录将该域名配置为指向您的生产数据库域名。

例如，要将 *www.mycompany.com* 关联到您的数据库 *mycompany.odoo.com*：

* 在 Odoo.sh 中，将 *www.mycompany.com* 添加到项目设置的自定义域名中，
* 在您的域名管理器（如 *godaddy.com*, *gandi.net*, *ovh.com*）中，将 *www.mycompany.com* 配置为 ``CNAME`` 记录，值为 *mycompany.odoo.com*。

裸域名（如 *mycompany.com*）不被接受：

* 它们只能通过 ``A`` 记录进行配置，
* ``A`` 记录只接受IP地址作为值，
* 您的数据库IP地址可能会更改，原因包括升级、硬件故障或您希望在其他国家或大陆托管您的数据库。

因此，由于IP地址的更改，裸域名可能突然停止工作。

此外，如果您希望 *mycompany.com* 和 *www.mycompany.com* 都可以与您的数据库一起工作，建议将第一个域名重定向到第二个域名。这也是 `SEO 最佳实践 <https://support.google.com/webmasters/answer/7451184?hl=zh>`_（请参阅 *提供一个版本的URL来访问文档*）。因此，您可以将 *mycompany.com* 配置为重定向到 *www.mycompany.com*。大多数域名管理器都有配置此重定向的功能，这通常称为网页重定向。

**HTTPS/SSL**

如果正确设置了重定向，平台将自动在一小时内使用 `Let's Encrypt <https://letsencrypt.org/about/>`_ 生成SSL证书，您的域名将通过HTTPS访问。

虽然目前无法在 Odoo.sh 平台上配置您自己的SSL证书，但如果有足够的需求，我们将考虑此功能。
**SPF和DKIM合规**

如果用户的电子邮件域名使用SPF（发件人策略框架）或DKIM（域名密钥标识邮件），请不要忘记在您的域名设置中授权Odoo作为发送主机，以提高外发电子邮件的可送达性。配置步骤详见 :ref:`SPF <email-domain-spf>` 和 :ref:`DKIM <email-domain-dkim>` 文档。

.. Warning::
  如果忘记配置SPF或DKIM来授权Odoo作为发送主机，可能会导致您的邮件被投递到联系人收件箱的垃圾邮件文件夹中。

Shell命令
==============

在视图的右上角，可以使用不同的Shell命令。

.. image:: branches/interface-branches-shellcommands.png
   :align: center

每个命令都可以复制到剪贴板以用于终端中，有些命令可以直接从Odoo.sh中通过点击 *run* 按钮运行。在这种情况下，会弹出一个窗口，提示用户定义一些占位符，如 ``<URL>``、``<PATH>`` 等。

Clone
-----

下载Git仓库。

.. code-block:: bash

  $ git clone --recurse-submodules --branch master git@github.com:odoo/odoo.git

克隆仓库 *odoo/odoo*。

* :code:`--recurse-submodules`: 下载您仓库中的子模块。包含的子模块也会被下载。
* :code:`--branch`: 检出仓库中的一个特定分支，在本例中为 *master*。

*run* 按钮不适用于此命令，因为它适用于您本地的机器。

Fork
----

基于当前分支创建一个新分支。

.. code-block:: bash

  $ git checkout -b feature-1 master

创建一个名为 *feature-1* 的新分支，基于 *master* 分支，然后检出该分支。

.. code-block:: bash

  $ git push -u origin feature-1

将新分支 *feature-1* 上传到您的远程仓库。

Merge
-----

将当前分支合并到另一个分支。

.. code-block:: bash

  $ git merge staging-1

将分支 *staging-1* 合并到当前分支。

.. code-block:: bash

  $ git push -u origin master

将您刚刚添加到 *master* 分支中的更改上传到您的远程仓库。

SSH
---

设置
~~~~~
为了使用SSH，您需要设置个人资料SSH公钥（如果还未设置）。请按照以下步骤操作：

#. `生成新的SSH密钥
   <https://help.github.com/en/github/authenticating-to-github/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent#generating-a-new-ssh-key>`_
#. `将SSH密钥复制到剪贴板
   <https://help.github.com/en/github/authenticating-to-github/adding-a-new-ssh-key-to-your-github-account>`_
   （只需执行第1步）
#. 将复制的内容粘贴到您的个人资料SSH密钥中并点击"Add"

   .. image:: branches/SSH-key-pasting.png
      :align: center

#. 密钥应该显示在下面

   .. image:: branches/SSH-key-appearing.png
      :align: center

连接
~~~~~~~~~~

要使用SSH连接到您的构建，您可以在终端中使用以下命令：

.. code-block:: bash

  $ ssh <build_id>@<domain>

您可以在右上角的SSH选项卡中找到此命令的快捷方式。

.. image:: branches/SSH-panel.png
   :align: center

前提是您拥有该项目的 :ref:`正确访问权限 <odoosh-gettingstarted-settings-collaborators>`，
您将被授予SSH访问构建的权限。

.. Note::
  长时间运行的SSH连接没有保障。空闲的连接可能会被断开以释放资源。

子模块
---------

将另一个存储库中的分支作为 *子模块* 添加到当前分支中。

*子模块* 允许您在项目中使用其他存储库的模块。

子模块的功能在本文档的章节
:ref:`Submodules <odoosh-advanced-submodules>` 中有详细说明。

.. code-block:: bash

  $ git submodule add -b master <URL> <PATH>

将存储库 *<URL>* 的 *master* 分支作为子模块添加到当前分支中的路径 *<PATH>* 下。

.. code-block:: bash

  $ git commit -a

提交所有当前更改。

.. code-block:: bash

  $ git push -u origin master

将您刚刚在 *master* 分支中添加的更改上传到远程仓库。

删除
------

从存储库中删除分支。

.. code-block:: bash

  $ git push origin :master

删除远程存储库中的分支。

.. code-block:: bash

  $ git branch -D master

删除本地副本中的分支。
