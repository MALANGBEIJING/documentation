==========================================
第4章：安全性 - 简要介绍
==========================================

在 :doc:`上一章 <03_basicmodel>` 中，我们创建了第一个用于存储业务数据的表。在像 Odoo 这样的业务应用程序中，首先要考虑的问题之一是谁\ [#who]_ 可以访问数据。Odoo 提供了一种安全机制，以允许特定用户组访问数据。

有关安全性主题的详细信息，请参阅 :doc:`../restrict_data_access`。本章旨在涵盖我们新模块所需的最低要求。

数据文件（CSV）
================

Odoo 是一个高度数据驱动的系统。尽管通过 Python 代码自定义行为，但模块的部分价值在于它在加载时设置的数据。加载数据的一种方法是通过 CSV 文件。一个例子是安装 `base` 模块时加载的 `国家州列表 <{GITHUB_PATH}/odoo/addons/base/data/res.country.state.csv>`_。

.. code-block:: text

    "id","country_id:id","name","code"
    state_au_1,au,"澳大利亚首都领地","ACT"
    state_au_2,au,"新南威尔士","NSW"
    state_au_3,au,"北领地","NT"
    state_au_4,au,"昆士兰","QLD"
    ...

- ``id`` 是一个 :term:`外部标识符`。它可以用于引用记录（而不需要知道其数据库内标识符）。
- ``country_id:id`` 通过其 :term:`外部标识符` 引用国家。
- ``name`` 是州的名称。
- ``code`` 是州的代码。

这三个字段在 ``res.country.state`` 模型中
`定义 <https://github.com/odoo/odoo/blob/2ad2f3d6567b6266fc42c6d2999d11f3066b282c/odoo/addons/base/models/res_country.py#L108-L111>`__。

根据约定，导入数据的文件位于模块的 ``data`` 文件夹中。当数据与安全性相关时，它位于 ``security`` 文件夹中。当数据与视图和操作相关时（稍后会讨论），它位于 ``views`` 文件夹中。此外，所有这些文件必须在 ``__manifest__.py`` 文件中的 ``data`` 列表中声明。我们的示例文件在 `base` 模块的清单中定义 <https://github.com/odoo/odoo/blob/e8697f609372cd61b045c4ee2c7f0fcfb496f58a/odoo/addons/base/__manifest__.py#L29>`__。

还要注意，数据文件的内容仅在安装或更新模块时加载。

.. 警告::

    数据文件按其在 ``__manifest__.py`` 文件中的顺序依次加载。这意味着如果数据 ``A`` 引用数据 ``B``，您必须确保 ``B`` 在 ``A`` 之前加载。

    在国家州的情况下，您会注意到
    `国家列表 <https://github.com/odoo/odoo/blob/e8697f609372cd61b045c4ee2c7f0fcfb496f58a/odoo/addons/base/__manifest__.py#L22>`__
    在
    `国家州列表 <https://github.com/odoo/odoo/blob/e8697f609372cd61b045c4ee2c7f0fcfb496f58a/odoo/addons/base/__manifest__.py#L29>`__ 之前加载。
    这是因为州引用了国家。

这一切对安全性为什么重要？因为模型的所有安全配置都是通过数据文件加载的，正如我们将在下一节中看到的。

访问权限
=============

**参考**：有关此主题的文档可以在 :ref:`reference/security/acl` 中找到。

.. 注意::

    **目标**：在本节结束时，以下警告不应再出现：

    .. code-block:: text

        WARNING rd-demo odoo.modules.loading: 模型 ['estate.property'] 没有访问规则...

当模型上未定义访问权限时，Odoo 确定没有用户可以访问数据。它甚至会在日志中收到通知：

.. code-block:: text

    WARNING rd-demo odoo.modules.loading: 模型 ['estate.property'] 在模块 estate 中没有访问规则，请考虑添加一些，例如：
    id,name,model_id:id,group_id:id,perm_read,perm_write,perm_create,perm_unlink

访问权限定义为 ``ir.model.access`` 模型的记录。每个访问权限与一个模型、一个组（或没有组以实现全局访问）以及一组权限相关：创建、读取、写入和删除\ [#unlink]_. 这种访问权限通常在名为 ``ir.model.access.csv`` 的 CSV 文件中定义。

以下是我们之前的 `test_model` 的示例：

.. code-block:: text

    id,name,model_id/id,group_id/id,perm_read,perm_write,perm_create,perm_unlink
    access_test_model,access_test_model,model_test_model,base.group_user,1,0,0,0

- ``id`` 是一个 :term:`外部标识符`。
- ``name`` 是 ``ir.model.access`` 的名称。
- ``model_id/id`` 引用适用访问权限的模型。引用模型的标准方式是 ``model_<model_name>``，其中 ``<model_name>`` 是模型的 ``_name``，并用 ``_`` 替换 ``.``。看起来麻烦吗？确实是...
- ``group_id/id`` 引用适用访问权限的组。
- ``perm_read,perm_write,perm_create,perm_unlink``：读取、写入、创建和删除权限。

.. 练习:: 添加访问权限。

    在适当的文件夹中创建 ``ir.model.access.csv`` 文件，并在 ``__manifest__.py`` 文件中定义它。

    为组 ``base.group_user`` 提供读取、写入、创建和删除权限。

    提示：日志中的警告消息为您提供了大部分解决方案 ;-)

重新启动服务器，警告消息应该消失了！

现在是时候最终 :doc:`与用户界面互动 <05_firstui>` 了！

.. [#who] 指的是哪个 Odoo 用户（或用户组）

.. [#unlink] 'unlink' 相当于 'delete'
