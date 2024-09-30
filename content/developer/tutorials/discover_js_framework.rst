:show-content:

==========================
探索 Web 框架
==========================

.. toctree::
    :titlesonly:
    :glob:

    discover_js_framework/*

本教程分为两部分，旨在向您介绍 Web 框架的基础知识。无论您是 Web 框架的新手，还是有一些经验，本教程都将为您提供使用 Web 框架的坚实基础。

第一部分涵盖了 Owl 组件的基础知识，这些是 Web 框架的重要组成部分。Owl 组件是可重用的 UI 组件，可用于快速高效地构建复杂的 Web 界面。我们将探索如何在 Odoo 中创建和使用 Owl 组件。在本教程的第二部分，我们将重点介绍如何使用 Odoo 的各种功能创建一个仪表板。仪表板是任何 Web 应用程序的重要组成部分，是使用和交互 Odoo 代码库的良好起点。

本教程假设您已经具备一些 Odoo 开发的基础知识（模型、控制器、QWeb 等）。如果您是 Odoo 的新手，建议您先完成 :doc:`/developer/tutorials/server_framework_101` 教程，然后再继续学习本教程。

.. note::

    本教程的每一章都是一个独立的项目。如果您对 Owl 已经比较熟悉，可以直接从第二章开始。

.. _tutorials/discover_js_framework/setup:

设置
=====

#. 克隆 `Odoo 官方教程仓库 <https://github.com/odoo/tutorials>`_ 并切换到分支 `{CURRENT_MAJOR_BRANCH}`。
#. 将克隆的仓库添加到您的 :option:`--addons-path <odoo-bin --addons-path>`。
#. 启动一个新的 Odoo 数据库并安装模块 `awesome_owl`（用于第一章）和 `awesome_dashboard`（用于第二章）。

内容
=======

- :doc:`discover_js_framework/01_owl_components`
- :doc:`discover_js_framework/02_build_a_dashboard`
