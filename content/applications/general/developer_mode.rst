.. _developer-mode:

===========================
开发者模式（调试模式）
===========================

开发者模式，也称为调试模式，允许访问 Odoo 中的高级 :ref:`工具和设置 <developer-mode/tools>`。

.. warning::
   请谨慎操作，因为某些开发者工具和技术设置被认为是高级功能，可能存在相关风险。仅在理解其含义并有信心时使用这些功能。

.. note::
   开发者模式也可与 :ref:`assets <frontend/framework/assets_debug_mode>` 一起使用，用于调试 JavaScript 代码，或与 :ref:`测试资源 <frontend/framework/tests_debug_mode>` 一起使用，用于运行测试流程。

.. _developer-mode/activation:

激活
=====

要激活开发者模式，打开 :guilabel:`设置` 应用，向下滚动到 :guilabel:`开发者工具` 部分，然后点击 :guilabel:`激活开发者模式`。

激活后， :guilabel:`关闭开发者模式` 选项将变为可用。

.. image:: developer_mode/settings.png
   :alt: 在设置应用中激活开发者模式

要 **从数据库的任何位置** 激活开发者模式，在 `/web` 后面添加 `?debug=1` 到 URL 中（例如，`https://example.odoo.com/web?debug=1#action=menu&cids=1`）。要禁用它，请使用 `?debug=0`。

使用 `?debug=assets` 激活带有资源的开发者模式，使用 `?debug=tests` 激活带有测试资源的开发者模式。

.. tip::
   按 `Ctrl + K` 或 `Cmd ⌘ + K` 打开 **命令面板**，然后输入 `debug` 来激活带有资源的开发者模式或关闭它。

.. admonition:: 浏览器扩展

   `Odoo Debug <https://github.com/Droggol/OdooDebug>`_ 浏览器扩展添加了一个图标，可以从浏览器的工具栏中切换开发者模式。它可以在 `Chrome 网上应用店 <https://chromewebstore.google.com/detail/odoo-debug/hmdmhilocobgohohpdpolmibjklfgkbi>`_ 和 `Firefox 附加组件 <https://addons.mozilla.org/firefox/addon/odoo-debug/>`_ 上找到。

.. _developer-mode/tools:

开发者工具和技术菜单
=====================

一旦激活开发者模式，可以通过点击 :icon:`fa-bug` :guilabel:`(bug)` 图标来访问开发者工具。菜单包含一些有助于理解或编辑技术数据的工具，如视图的字段、筛选器或操作。可用选项取决于从何处访问菜单。

.. image:: developer_mode/tools.png
   :alt: 访问开发者工具

数据库管理员可以从 :guilabel:`设置` 应用访问技术菜单。技术菜单包含一些与数据库结构、安全性、操作等相关的高级数据库设置。

.. image:: developer_mode/technical.png
   :alt: 访问技术菜单
