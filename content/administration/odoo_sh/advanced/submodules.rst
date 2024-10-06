.. _odoosh-advanced-submodules:

==========
子模块
==========

概述
========

`Git 子模块 <https://git-scm.com/book/en/v2/Git-Tools-Submodules>`_ 允许您将其他 Git 项目集成到您的代码中，而无需复制粘贴它们的所有代码。

实际上，您的自定义模块可以依赖于其他存储库中的模块。对于 Odoo，这个功能允许您将其他 Git 存储库中的模块添加到您的存储库分支中。通过子模块将这些依赖项添加到您的分支，使得代码和服务器的部署更加简单，因为在克隆您的存储库时，可以同时克隆作为子模块添加的存储库。

此外，您可以选择作为子模块添加的存储库的分支，并控制所需的修订版本。您可以决定是否将子模块固定到特定的修订版本，以及何时更新到较新的修订版本。

在 Odoo.sh 上，子模块使您可以使用和依赖其他存储库中的模块。平台会检测到您在分支中通过子模块添加的模块，并自动将它们添加到您的插件路径中，以便您可以在数据库中安装它们。

如果您在分支中添加了私有存储库作为子模块，您需要在 Odoo.sh 项目设置和存储库设置中配置部署密钥。否则，Odoo.sh 将无法下载它们。该过程在章节 :ref:`Settings > Submodules <odoosh-gettingstarted-settings-submodules>` 中详细说明。

添加子模块
==================

使用 Odoo.sh（简单方法）
---------------------

.. warning::
   目前无法通过此方法添加**私有**存储库。然而，您仍然可以 :ref:`通过 Git <odoosh-advanced-submodules-withgit>` 进行操作。

在 Odoo.sh 上的项目分支视图中，选择要添加子模块的分支。

在右上角，点击 *Submodule* 按钮，然后点击 *Run*。

.. image:: submodules/advanced-submodules-button.png
   :align: center

会显示一个带有表单的对话框。按如下方式填写输入项：

* Repository URL：存储库的 SSH URL。
* Branch：要使用的分支。
* Path：要在分支中添加此子模块的文件夹。

.. image:: submodules/advanced-submodules-dialog.png
   :align: center

在 Github 上，您可以通过存储库的 *Clone or download* 按钮获取存储库的 URL。确保*使用 SSH*。

.. image:: submodules/advanced-submodules-github-sshurl.png
  :align: center

.. _odoosh-advanced-submodules-withgit:

使用 Git（高级方法）
-------------------

在终端中，在您的 Git 存储库克隆的文件夹中，切换到您想要添加子模块的分支：

.. code-block:: bash

  $ git checkout <branch>

然后，使用以下命令添加子模块：

.. code-block:: bash

  $ git submodule add -b <branch> <git@yourprovider.com>:<username/repository.git> <path>

替换以下内容：

* *<git@yourprovider.com>:<username/repository.git>* 为您要添加为子模块的存储库的 SSH URL，
* *<branch>* 为要使用的存储库分支，
* *<path>* 为您要在其中添加此子模块的文件夹。

提交并推送更改：

.. code-block:: bash

  $ git commit -a && git push -u <remote> <branch>

替换以下内容：

* <remote> 为您要推送更改的存储库。对于标准的 Git 设置，这是 *origin*。
* <branch> 为您要推送更改的分支，通常是您在第一步使用 :code:`git checkout` 切换的分支。

您可以阅读 `git-scm.com 文档 <https://git-scm.com/book/en/v2/Git-Tools-Submodules>`_ 了解更多关于 Git 子模块的细节。例如，如果您想更新子模块以获取它们的最新修订版本，可以参阅章节
`拉取子模块上游变更 <https://git-scm.com/book/en/v2/Git-Tools-Submodules#_pulling_in_upstream_changes_from_the_submodule_remote>`_。

忽略模块
==============

如果您正在添加包含大量模块的存储库，您可能希望忽略其中一些模块，以防某些模块被自动安装。为此，您可以在子模块文件夹前加上 :code:`.`。平台将忽略该文件夹，您可以通过从其他文件夹创建符号链接手动选择要使用的模块。
