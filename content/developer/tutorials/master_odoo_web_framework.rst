:show-content:

========================
掌握 Web 框架
========================

.. toctree::
    :titlesonly:
    :glob:

    master_odoo_web_framework/*

本教程适合已经完成了 :doc:`discover_js_framework` 教程，并希望深入了解 Web 框架的用户。本教程由四个独立的项目组成，每个项目侧重于 Odoo 的不同功能。

.. note::

    每一章都可以独立完成，顺序不限。此外，请注意，有些章节包含了大量内容，因此可能会比较长。

第一个项目是构建一个 `点击游戏 <https://en.wikipedia.org/wiki/Incremental_game>`_。在这个项目中，您将学习 Web 框架的各种方面：系统托盘、命令面板、对话框、通知、自定义现有组件等。

第二个项目侧重于一个重要的组件类别：字段。字段组件表示记录的字段值，出现在 Web 客户端的许多地方：显然是在表单视图中，但也包括看板视图和列表视图，甚至可以单独使用。由于其重要性，学习如何创建和操作这些组件是很有意义的。

在 Web 框架的上下文中，视图通常是指组件的 JavaScript 实现，该组件根据描述 (`ir.ui.view`) 来表示一个或多个记录。这样的组件实际上非常复杂，通常需要各种子系统（渲染器、模型、控制器、结构解析器等）。在第三章中，我们将从头开始创建一个新的视图，用来表示一组图片列表。

最后，第四章的项目是通过在左侧添加搜索面板来自定义现有的看板视图。它展示了如何使用现有代码并进行修改以满足我们的需求。这也是一个非常现实的项目，涵盖了在 Odoo 中常见的许多问题。

.. _tutorials/master_odoo_web_framework/setup:

设置
=====

#. 克隆 `Odoo 官方教程仓库 <https://github.com/odoo/tutorials>`_ 并切换到分支 `{CURRENT_MAJOR_BRANCH}`。
#. 将克隆的仓库添加到您的 :option:`--addons-path <odoo-bin --addons-path>`。
#. 启动一个新的 Odoo 数据库，并安装您要学习的章节对应的模块：`awesome_clicker`（第一章）、`awesome_fields`（第二章）、`awesome_gallery`（第三章）或 `awesome_kanban`（第四章）。

内容
=======

- :doc:`master_odoo_web_framework/01_build_clicker_game`
- :doc:`master_odoo_web_framework/02_create_gallery_view`
- :doc:`master_odoo_web_framework/03_customize_kanban_view`
