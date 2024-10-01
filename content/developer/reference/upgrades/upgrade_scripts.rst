===============
升级脚本
===============

升级脚本是一个包含名为 :meth:`migrate` 的函数的 Python 文件，该函数在模块更新过程中被调用。

.. method:: migrate(cr, version)

   :param cr: 当前数据库游标
   :type cr: :class:`~odoo.sql_db.Cursor`
   :param str version: 模块的已安装版本

通常，此函数执行一个或多个 SQL 查询，并且可以访问 Odoo 的 ORM，以及 :doc:`./upgrade_utils`。

编写升级脚本
=======================

升级脚本遵循特定的树结构和命名约定，以确定它们的执行时机。

升级脚本路径的结构为 :file:`$module/migrations/$version/{pre,post,end}-*.py`，其中 `$module` 是脚本将运行的模块，`$version` 是模块的完整版本（包括 Odoo 的主要版本和模块的次要版本），`{pre|post|end}-*.py` 是需要执行的文件。文件名将决定执行该模块和版本的 :ref:`阶段 <upgrade-scripts/phases>` 和顺序。

.. note::
   从 Odoo 13 开始，升级脚本的顶级目录也可以命名为 `upgrades`。这种命名更为合适，因为 *migrate* 可能与 *moving out of Odoo* 混淆。因此 :file:`$module/upgrades/$version/` 也是有效的。

.. note::
   只有在模块被更新时，才会执行升级脚本。因此，`$version` 目录中设置的模块次要版本需要高于已安装的模块版本，并且需要等于或低于模块的更新版本。

.. example::

   自定义模块 `awesome_partner` 升级到 Odoo 17 的版本 `2.0` 的升级脚本目录结构。

   .. code-block:: text

      awesome_partner/
      |-- migrations/
      |   |-- 17.0.2.0/
      |   |   |-- pre-exclamation.py

   两个升级脚本示例，内容为 :file:`pre-exclamation.py`，该文件在合作伙伴的名字后添加 "!"。

   .. code-block:: python

      import logging

      _logger = logging.getLogger(__name__)


      def migrate(cr, version):
          cr.execute("UPDATE res_partner SET name = name || '!'")
          _logger.info("更新了 %s 个合作伙伴", cr.rowcount)

   .. code-block:: python

      import logging
      from odoo.upgrade import util

      _logger = logging.getLogger(__name__)


      def migrate(cr, version):
          env = util.env(cr)

          partners = env["res.partner"].search([])
          for partner in partners:
              partner.name += "!"

          _logger.info("更新了 %s 个合作伙伴", len(partners))

   注意，在第二个示例中，脚本利用 :doc:`./upgrade_utils` 访问 ORM。请查看文档以了解有关此库的更多信息。

.. _upgrade-scripts/phases:

升级脚本的阶段
=========================

升级过程由每个模块的每个版本的三个阶段组成：

  #. 预阶段，在模块加载之前。
  #. 后阶段，在模块及其依赖项加载和更新后。
  #. 结束阶段，在所有模块已加载并更新到该版本后。

升级脚本根据文件名的第一部分分组到相应的阶段。在每个阶段内，文件按词法顺序执行。

.. admonition:: 单个模块在一个版本中的示例脚本执行顺序

   #. :file:`pre-10-do_something.py`
   #. :file:`pre-20-something_else.py`
   #. :file:`post-do_something.py`
   #. :file:`post-something.py`
   #. :file:`end-01-migrate.py`
   #. :file:`end-migrate.py`
