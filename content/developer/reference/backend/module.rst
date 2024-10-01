================
模块清单
================

.. _reference/module/manifest:

清单文件
========

清单文件用于声明一个 Python 包为 Odoo 模块，并指定模块的元数据。

它是一个名为 ``__manifest__.py`` 的文件，包含一个 Python 字典，其中每个键指定了模块的元数据。

::

    {
        'name': "模块名称",
        'version': '1.0',
        'depends': ['base'],
        'author': "作者名称",
        'category': '类别',
        'description': """
        描述文本
        """,
        # 在安装时始终加载的数据文件
        'data': [
            'views/mymodule_view.xml',
        ],
        # 可选加载的演示数据文件
        'demo': [
            'demo/demo_data.xml',
        ],
    }

可用的清单字段有：

``name`` (``str``, 必填)
    模块的可读名称
``version`` (``str``)
    模块的版本号，应遵循 `语义版本控制`_ 规则
``description`` (``str``)
    模块的扩展描述，使用 reStructuredText 格式
``author`` (``str``)
    模块作者的名称
``website`` (``str``)
    模块作者的网站 URL
``license`` (``str``, 默认: ``LGPL-3``)
    模块的发行许可证。
    可选值有：

    * `GPL-2`
    * `GPL-2 或任何更高版本`
    * `GPL-3`
    * `GPL-3 或任何更高版本`
    * `AGPL-3`
    * `LGPL-3`
    * `其他 OSI 认可的许可证`
    * `OEEL-1` (Odoo 企业版许可 v1.0)
    * `OPL-1` (Odoo 专有许可 v1.0)
    * `其他专有`

``category`` (``str``, 默认: ``未分类``)
    Odoo 中的分类类别，用于粗略描述模块的业务领域。

    尽管推荐使用 `现有类别`_，但该字段是自由格式的，未知类别会被自动创建。类别层次可以通过 ``/`` 分隔符创建，例如 ``Foo / Bar`` 将创建一个名为 ``Foo`` 的类别，并将 ``Bar`` 作为 ``Foo`` 的子类别，并将该模块的类别设为 ``Bar``。
``depends`` (``list(str)``)
    在此模块之前必须加载的 Odoo 模块，因为该模块使用它们创建的功能或修改它们定义的资源。

    当安装一个模块时，所有依赖项都会先于该模块安装。同样，依赖项会在加载该模块之前被加载。

.. note::
    模块 `base` 始终在任何 Odoo 实例中安装。
    但您仍然需要将其指定为依赖项，以确保在 `base` 更新时您的模块也能更新。

``data`` (``list(str)``)
    必须始终与模块一起安装或更新的数据文件列表。是从模块根目录开始的路径列表。
``demo`` (``list(str)``)
    仅在 *演示模式* 下安装或更新的数据文件列表。
``auto_install`` (``bool`` 或 ``list(str)``, 默认: ``False``)
    如果为 ``True``，此模块将在其所有依赖项安装后自动安装。

    该字段通常用于 "链接模块"，它实现了两个原本独立的模块之间的协同集成。

    例如，``sale_crm`` 依赖于 ``sale`` 和 ``crm``，并且设置为 ``auto_install``。当同时安装了 ``sale`` 和 ``crm`` 时，它会自动向销售订单添加 CRM 活动跟踪，而无需让 ``sale`` 或 ``crm`` 互相了解。

    如果它是一个列表，则必须包含依赖项的子集。只要安装了该子集中的所有依赖项，该模块就会自动安装。其余的依赖项也会自动安装。如果列表为空，则无论其依赖项如何，此模块始终会自动安装，并且这些依赖项也会安装。

``external_dependencies`` (``dict(key=list(str))``)
    包含 Python 和/或二进制依赖项的字典。

    对于 Python 依赖项，必须为该字典定义 ``python`` 键，并为其分配要导入的 Python 模块列表。

    对于二进制依赖项，必须为该字典定义 ``bin`` 键，并为其分配应在主机机器的 PATH 环境变量中找到的二进制可执行文件的名称列表。

    如果主机机器上未安装 Python 模块或未找到二进制可执行文件，则不会安装该模块。
``application`` (``bool``, 默认: ``False``)
    模块是否应被视为一个完整的应用程序（``True``），或者只是一个为现有应用程序模块提供一些额外功能的技术模块（``False``）。
``assets`` (``dict``)
    定义如何在各种资源包中加载所有静态文件。有关如何描述资源包的更多详细信息，请参阅 :ref:`资源 <reference/assets>` 页面。
``installable`` (``bool``, 默认: ``True``)
    用户是否可以通过 Web UI 安装该模块。
``maintainer`` (``str``)
    负责维护该模块的人员或实体，默认情况下，假设作者就是维护者。
``{pre_init, post_init, uninstall}_hook`` (``str``)
    模块安装/卸载的钩子，其值应为在模块的 ``__init__.py`` 中定义的函数的名称。

    ``pre_init_hook`` 接受一个游标作为其唯一参数，此函数在模块安装之前执行。

    ``post_init_hook`` 接受一个游标和一个注册表作为其参数，此函数在模块安装后立即执行。

    ``uninstall_hook`` 接受一个游标和一个注册表作为其参数，此函数在模块卸载后执行。

    只有在通过 API 非常困难或不可能进行模块设置/清理时，才应使用这些钩子。
``active`` (``bool``)
    已弃用。被 ``auto_install`` 取代。

.. _语义版本控制: https://semver.org
.. _现有类别: {GITHUB_PATH}/odoo/addons/base/data/ir_module_category_data.xml
