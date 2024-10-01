=============
升级工具
=============

`Upgrade utils <https://github.com/odoo/upgrade-util/>`_ 是一个包含辅助函数的库，用于简化升级脚本的编写。该库被 Odoo 用于标准模块的升级脚本，提供可靠性并加快升级过程：

- 辅助函数有助于确保数据库中的数据一致性。
- 它处理更新记录的间接引用。
- 允许调用函数，避免编写代码，从而节省时间并降低开发风险。
- 辅助工具使开发者能够专注于升级的重要部分，而不必考虑细节。

安装
============

在本地克隆 `Upgrade utils repository <https://github.com/odoo/upgrade-util/>`_，并通过将 ``src`` 目录添加到 ``--upgrade-path`` 选项来启动 ``odoo``。

.. code-block:: console

   $ ./odoo-bin --upgrade-path=/path/to/upgrade-util/src,/path/to/other/upgrade/script/directory [...]

在无法自己管理 Odoo 的平台上，可以通过 `pip` 安装此库：

.. code-block:: console

   $ python3 -m pip install git+https://github.com/odoo/upgrade-util@master

在 `Odoo.sh <https://www.odoo.sh/>`_ 上，建议将其添加到自定义存储库的 :file:`requirements.txt` 文件中。为此，在文件中添加以下行::

   odoo_upgrade @ git+https://github.com/odoo/upgrade-util@master

使用升级工具
===================

安装后，以下软件包可用于升级脚本：

- :mod:`odoo.upgrade.util`: 辅助工具本身。
- :mod:`odoo.upgrade.testing`: 基础测试用例类。

在升级脚本中使用它，只需导入：

.. code-block:: python

   from odoo.upgrade import util


   def migrate(cr, version):
      # 脚本的其余部分

现在，可以通过 ``util`` 调用辅助函数。

辅助函数
==============

升级工具提供了许多有用的函数来简化升级过程。在这里，我们描述一些最有用的函数。请参阅 `util folder <https://github.com/odoo/upgrade-util/tree/master/src/util>`_ 以获取辅助函数的全面声明。

.. note::

   在 util 函数中的 :attr:`cr` 参数始终指代数据库游标。请传入在 :meth:`migrate` 中接收到的游标参数。并非所有函数都需要此参数。

.. currentmodule:: odoo.upgrade.util

模块
-------

.. automodule:: odoo.upgrade.util.modules
   :members:

模型
------

.. automodule:: odoo.upgrade.util.models
   :members:

字段
------

.. automodule:: odoo.upgrade.util.fields
   :members:

记录
-------

.. automodule:: odoo.upgrade.util.records
   :members:

ORM
---

.. automodule:: odoo.upgrade.util.orm
   :members:

.. automodule:: odoo.upgrade.util.domains
   :members:

SQL
---

.. automodule:: odoo.upgrade.util.pg
   :members:

杂项
----

.. automodule:: odoo.upgrade.util.misc
   :members:
