==========
上线
==========

当您完成所有网页设计和开发工作后，是时候将其部署到开发或生产数据库上了。

模块导入
=============

Odoo SaaS
---------

首次导入模块时，请按照以下步骤操作：

#. 创建模块的 ZIP 文件。
#. 连接到项目数据库。
#. 启用 :ref:`开发者模式 <developer-mode>`。
#. 前往 :guilabel:`应用`，搜索 `base_import_module`，并在必要时安装它。
#. 点击菜单中的 :guilabel:`导入模块`。
#. 上传您的 ZIP 文件，勾选 :guilabel:`强制初始化`，然后点击 :guilabel:`导入应用` 按钮。

如果在进行一些更改后需要重新导入模块，请按照相同的步骤操作，但在导入模块之前，打开开发者菜单并选择 :guilabel:`成为超级用户`。要退出超级用户模式，请注销并重新登录。

.. warning::
   ZIP 文件的大小必须小于 50 MB。

.. seealso::
   - `Odoo 电子学习：注册免费域名 <https://www.odoo.com/slides/slide/register-a-free-domain-name-1663>`_

Odoo.sh
-------

前往 :guilabel:`应用`，然后点击菜单中的 :guilabel:`更新应用列表`。在列表中搜索您的模块并安装它。

.. seealso::
   :doc:`Odoo.sh 介绍 <../../../administration/odoo_sh/overview/introduction>`
