章节 1: 架构概述
=====================

多层应用程序
=====================

Odoo 遵循 `多层架构`_，这意味着展示层、业务逻辑层和数据存储层是分开的。更具体地说，它采用三层架构（来自维基百科）：

.. image:: 01_architecture/three_tier.svg
    :align: center
    :alt: 三层架构

展示层是 HTML5、JavaScript 和 CSS 的组合。逻辑层完全用 Python 编写，而数据层仅支持 PostgreSQL 作为 RDBMS。

根据模块的范围，Odoo 开发可以在这三个层中进行。因此，在进一步学习之前，如果您对这些主题的理解不是中级水平，最好先复习一下。

为了通过本教程，您需要对 HTML 有基本了解，并对 Python 有中级水平的理解。高级主题将需要更深入的知识。互联网上有很多免费教程，因此我们无法推荐其中的一个，因为这取决于您的背景。

作为参考，这是官方的 `Python 教程`_。

.. note::
  从版本 15.0 开始，Odoo 正在积极过渡到使用其自家开发的 `OWL 框架 <https://odoo.github.io/owl/>`_ 作为其展示层的一部分。遗留的 JavaScript 框架仍然受到支持，但将逐步淘汰。关于这一点将在高级主题中进一步讨论。

Odoo 模块
============

服务器和客户端扩展打包为 *模块*，可选地加载到 *数据库* 中。模块是一组针对单一目的的功能和数据。

Odoo 模块可以为 Odoo 系统添加全新的业务逻辑，或者修改和扩展现有的业务逻辑。可以创建一个模块来添加您国家的会计规则到 Odoo 的通用会计支持中，而另一个模块则添加对公交车队实时可视化的支持。

Odoo 中的一切都以模块开始和结束。

术语：开发人员将其业务功能组合成 Odoo *模块*。面向用户的主要模块标记为 *应用*，但大多数模块不是应用。 *模块* 也可以称为 *附加组件*，Odoo 服务器找到它们的目录形成 `addons_path`。

模块的组成
---------------------

Odoo 模块 **可以** 包含多个元素：

:ref:`业务对象 <reference/orm>`
    业务对象（例如发票）被声明为 Python 类。这些类中定义的字段会通过 :abbr:`ORM (对象关系映射)` 层自动映射到数据库列。

:doc:`对象视图 <../../reference/user_interface/view_architectures>`
    定义用户界面显示

:ref:`数据文件 <reference/data>`
    声明模型数据的 XML 或 CSV 文件：

    * :doc:`视图 <../../reference/user_interface/view_architectures>` 或
      :ref:`报告 <reference/reports>`,
    * 配置数据（模块参数化、:ref:`安全规则 <reference/security>`），
    * 演示数据
    * 以及更多

:ref:`Web 控制器 <reference/controllers>`
    处理来自网页浏览器的请求

静态网页数据
    用于网页界面或网站的图像、CSS 或 JavaScript 文件

这些元素都不是强制性的。一些模块可能只添加数据文件（例如特定国家的会计配置），而其他模块可能只添加业务对象。在本培训中，我们将创建业务对象、对象视图和数据文件。

模块结构
----------------

每个模块都是一个位于 *模块目录* 内的目录。模块目录通过使用 :option:`--addons-path <odoo-bin --addons-path>` 选项来指定。

Odoo 模块通过其 :ref:`清单 <reference/module/manifest>` 声明。

当 Odoo 模块包含业务对象（即 Python 文件）时，它们会组织为一个 `Python 包 <https://docs.python.org/3/tutorial/modules.html#packages>`_，其中包含一个 ``__init__.py`` 文件。此文件包含对模块中各种 Python 文件的导入指令。

以下是简化的模块目录：

.. code-block:: bash

    module
    ├── models
    │   ├── *.py
    │   └── __init__.py
    ├── data
    │   └── *.xml
    ├── __init__.py
    └── __manifest__.py

Odoo 版本
=============

Odoo 提供 `两个版本`_：Odoo 企业版（许可和共享源代码）和 Odoo 社区版（开源）。除了提供支持或升级等服务外，企业版还为 Odoo 提供额外的功能。从技术角度来看，这些功能只是安装在社区版模块之上的新模块。

准备好开始了吗？现在是时候 :doc:`编写您自己的应用程序 <02_newapp>` 了！

.. _multitier architecture:
    https://en.wikipedia.org/wiki/Multitier_architecture

.. _Python tutorial:
    https://docs.python.org/3.7/tutorial/

.. _two versions:
    https://www.odoo.com/page/editions
