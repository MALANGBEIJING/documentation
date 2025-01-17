第2章: 新应用程序
============================

本章的目的是为创建一个全新的 Odoo 模块奠定基础。我们将从零开始，只保留识别模块所需的最低内容。在接下来的章节中，我们将逐步添加功能，以构建一个现实的业务案例。

房地产广告模块
====================================

我们的新模块将涵盖一个非常特定的业务领域，因此不包括在标准模块集中：房地产。值得注意的是，在开发新模块之前，最好先验证 Odoo 是否已经提供了解决特定业务案例的方法。

下面是包含一些广告的主列表视图的概述：

.. image:: 02_newapp/overview_list_view_01.png
   :align: center
   :alt: 列表视图 01

表单视图的顶部区域总结了物业的重要信息，例如名称、物业类型、邮政编码等。第一个选项卡包含描述物业的信息：卧室、客厅、车库、花园...

.. image:: 02_newapp/overview_form_view_01.png
   :align: center
   :alt: 表单视图 01

第二个选项卡列出了该物业的报价。我们可以看到，潜在买家可以提出高于或低于预期售价的报价，是否接受报价由卖方决定。

.. image:: 02_newapp/overview_form_view_02.png
   :align: center
   :alt: 表单视图 02

这里有一个快速视频展示模块的工作流程。

希望这个视频很快就会录制完成 :-)

准备附加组件目录
===========================

**参考**：与此主题相关的文档可以在 :ref:`manifest <reference/module/manifest>` 中找到。

.. note::

   **目标**：本节的目标是使 Odoo 识别我们的新模块，目前它将是一个空壳。它将在应用程序列表中列出：

   .. image:: 02_newapp/app_in_list.png
      :align: center
      :alt: 新模块在列表中出现

模块创建的第一步是创建它的目录。在 `tutorials` 目录中，添加一个新目录 :file:`estate`。

一个模块至少必须包含两个文件： ``__manifest__.py`` 文件和 ``__init__.py`` 文件。``__init__.py`` 文件可以暂时保持空白，我们将在下一章回来处理它。另一方面， ``__manifest__.py`` 文件必须描述我们的模块，不能保持为空。它唯一的必填字段是 ``name`` ，但通常包含更多信息。

请查看 `CRM 文件 <https://github.com/odoo/odoo/blob/fc92728fb2aa306bf0e01a7f9ae1cfa3c1df0e10/addons/crm/__manifest__.py#L1-L67>`__ 作为示例。除了提供模块的描述（ ``name`` 、 ``category`` 、 ``summary`` 、 ``website`` ...）外，它还列出了其依赖关系（ ``depends`` ）。依赖关系意味着 Odoo 框架会确保在安装我们的模块之前，这些模块已经安装。此外，如果其中一个依赖关系被卸载，则我们的模块和 **任何依赖于它的模块也将被卸载**。想想你最喜欢的 Linux 发行版包管理器（ ``apt`` 、 ``dnf`` 、 ``pacman``...）：Odoo 的工作方式与此类似。

.. exercise:: 创建所需的附加组件文件。

    创建以下文件夹和文件：

    - ``/home/$USER/src/tutorials/estate/__init__.py``
    - ``/home/$USER/src/tutorials/estate/__manifest__.py``

    ``__manifest__.py`` 文件应仅定义模块的名称和依赖关系。现在唯一必要的框架模块是 ``base``。

重新启动 Odoo 服务器并转到应用程序。单击更新应用程序列表，搜索 ``estate``，然后... tadaaa，你的模块出现了！如果没有出现，可能尝试移除默认的“应用程序”过滤器 ;-)

.. warning::
   请记住启用 :ref:`开发者模式 <developer-mode>`，如前一章所述。否则您将看不到 :guilabel:`更新应用程序列表` 按钮。

.. exercise:: 使您的模块成为“应用程序”。

    在 ``__manifest__.py`` 中添加适当的键，以便在“应用程序”过滤器打开时显示模块。

您甚至可以安装模块！但显然它是一个空壳，因此不会出现任何菜单。

一切顺利吗？如果是的话，那就让我们 :doc:`创建我们的第一个模型 <03_basicmodel>` 吧！
