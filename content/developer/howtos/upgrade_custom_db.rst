=============================
升级自定义数据库
=============================

将 Odoo 升级到新版本可能会很具挑战性，特别是当您的数据库包含自定义模块时。本页面旨在解释如何升级包含自定义模块的数据库的技术流程。有关如何升级没有自定义模块的数据库的指南，请参阅 :doc:`升级文档 </administration/upgrade>`。

我们将任何扩展 Odoo 标准代码且不是通过 Studio 应用构建的模块视为自定义模块。在升级这样的模块或请求其升级之前，请查看 :ref:`upgrade-sla`，以确保明确由谁负责。

在进行我们所说的数据库的 **自定义升级** 时，请牢记升级的目标：

#. 保持支持
#. 获取最新功能
#. 享受性能提升
#. 减少技术债务
#. 受益于安全性改进

随着每个 Odoo 新版本的发布，都会引入一些变化。这些变化可能会影响您开发了自定义的模块。因此，升级包含自定义模块的数据库需要额外的步骤来升级源代码。

升级自定义数据库的步骤如下：

#. :ref:`停止开发并质疑它们 <upgrade_custom/stop_developments>`。
#. :ref:`请求一个升级后的数据库 <upgrade_custom/request_upgrade>`。
#. :ref:`确保您的模块在空数据库上可安装 <upgrade_custom/empty_database>`。
#. :ref:`确保您的模块在升级后的数据库上可安装 <upgrade_custom/upgraded_database>`。
#. :ref:`进行广泛测试并进行演练 <upgrade_custom/testing_rehearsal>`。
#. :ref:`升级生产数据库 <upgrade_custom/production>`。

.. _upgrade_custom/stop_developments:

步骤 1：停止开发
=============================

启动升级需要承诺和开发资源。如果在升级过程中仍然继续开发，那么每次更改这些功能时都需要重新升级和测试。这就是为什么我们建议在开始升级过程时完全冻结代码库的原因。显然，bug 修复不在此建议之列。

一旦您停止开发，评估所做的开发并将其与当前版本和目标版本之间引入的功能进行比较是一个好的做法。尽可能挑战开发并寻找功能性解决方案。消除开发与 Odoo 标准版本之间的冗余将简化升级过程，并减少技术债务。

.. note::
   您可以在 `发布说明 <https://odoo.com/page/release-notes>`_ 中找到版本之间的更改信息。

.. _upgrade_custom/request_upgrade:

步骤 2：请求一个升级后的数据库
====================================

当自定义模块的开发停止并挑战已实施的功能以消除冗余和不必要的代码时，下一步是请求一个升级后的测试数据库。根据您的数据库托管类型，按照 :ref:`升级请求测试 <upgrade-request-test>` 中的步骤操作。

此阶段的目的不是立即开始在升级后的数据库中使用自定义模块，而是确保标准升级过程无缝进行，并且测试数据库已正确交付。如果不是这样，并且升级请求失败，请通过选择与测试升级相关的选项，在 `支持页面 <https://odoo.com/help?stage=migration>`_ 上请求 Odoo 的帮助。

.. _upgrade_custom/empty_database:

步骤 3：空数据库
======================

在升级后的测试数据库上进行工作之前，我们建议您先使自定义开发在目标版本的空数据库上运行。这可以确保自定义模块与 Odoo 的新版本兼容，分析其如何与新功能交互，并确保它们在升级数据库时不会引发问题。

使自定义模块在空数据库上工作还有助于避免生产数据库中的更改和错误配置（如 Studio 自定义、自定义网站页面、电子邮件模板或翻译）。这些内容本质上与自定义模块无关，可能会在升级过程中引发不必要的问题。

要使自定义模块在空数据库上运行，建议按以下步骤操作：

#. :ref:`upgrade_custom/empty_database/modules_installable`
#. :ref:`upgrade_custom/empty_database/test_fixes`
#. :ref:`upgrade_custom/empty_database/clean_code`
#. :ref:`确保标准测试运行成功 <upgrade_custom/empty_database/standard_test>`

.. _upgrade_custom/empty_database/modules_installable:

使自定义模块可安装
-------------------------------

第一步是确保自定义模块在新的 Odoo 版本中可安装。这意味着在安装它们时确保没有回溯或警告。为此，请在新 Odoo 版本的空数据库中逐个安装自定义模块，并修复安装过程中出现的回溯和警告。

此过程将帮助您在安装模块时检测问题。例如：

- 无效的模块依赖。
- 语法更改：资源声明、OWL 更新、属性。
- 对不存在或已重命名的标准字段、模型、视图的引用。
- Xpath 在视图中移动或被移除。
- 方法重命名或移除。
- ...

.. _upgrade_custom/empty_database/test_fixes:

测试和修复
--------------

当模块安装时没有更多回溯时，下一步是测试它们。即使自定义模块可以在空数据库上安装，这也不能保证它们在执行过程中没有错误。因此，我们鼓励您彻底测试所有自定义内容，以确保一切按预期运行。

此过程将帮助您检测在模块安装过程中未识别的其他问题，这些问题只能在运行时检测到。例如，调用标准 Python 或 OWL 函数时的已弃用调用，或对不存在的标准字段的引用等。

我们建议您测试所有自定义内容，尤其是以下元素：

- 视图
- 电子邮件模板
- 报告
- 服务器操作和自动化操作
- 标准工作流中的更改
- 计算字段

我们还鼓励您编写自动化测试，以在测试迭代期间节省时间，提高测试覆盖率，并确保所做的更改和修复不会破坏现有流程。如果自定义中已实现了测试，请确保将它们升级到新版本的 Odoo，并成功运行，修复可能存在的问题。

.. _upgrade_custom/empty_database/clean_code:

清理代码
--------------

在升级过程的这一阶段，我们还建议尽可能清理代码。这包括：

- 删除冗余和不必要的代码。
- 删除现在已经成为 Odoo 标准功能的特性，如 :ref:`upgrade_custom/stop_developments` 中所述。
- 清理不再需要的注释代码。
- 如果需要，重构代码（函数、字段、视图、报告等）。

.. _upgrade_custom/empty_database/standard_test:

标准测试
--------------

完成前面的步骤后，我们建议确保与自定义模块依赖相关的所有标准测试都能通过。
标准测试可以验证代码逻辑并防止数据损坏。它们将帮助您在处理数据库之前识别错误或不期望的行为。

如果标准测试失败，我们建议分析失败的原因：

- 自定义更改了标准工作流：适应标准测试以匹配您的工作流。
- 自定义没有考虑到特殊流程：调整自定义以确保其适用于所有标准工作流。

.. _upgrade_custom/upgraded_database:

步骤 4：升级后的数据库
=========================

当自定义模块可以安装并在空数据库上正常工作后，就可以让它们在 :ref:`升级后的数据库 <upgrade-request-test>` 上工作。

为了确保自定义代码在新版本中完美运行，请按照以下步骤操作：

- :ref:`升级数据 <upgrade_custom/upgraded_database/migrate_data>`
- :ref:`测试自定义模块 <upgrade_custom/upgraded_database/test_custom>`

.. _upgrade_custom/upgraded_database/migrate_data:

迁移数据
----------------

在升级自定义模块时，您可能需要使用 :doc:`升级脚本 <../reference/upgrades/upgrade_scripts>` 来反映源代码中的更改及其对应的数据。结合升级脚本，您还可以使用 :doc:`../reference/upgrades/upgrade_utils` 及其辅助函数。

- 在自定义代码升级过程中重命名的任何技术数据（模型、字段、外部标识符）应使用升级脚本重命名，以避免在模块升级过程中丢失数据。另见：:meth:`rename_field`, :meth:`rename_model`, :meth:`rename_xmlid`。
- 从较新 Odoo 版本的源代码中移除的标准模型中的数据以及在标准升级过程中从数据库中移除的数据，可能需要从旧模型表中恢复（如果它仍然存在）。

   .. example::
      从 Odoo 15 升级到 Odoo 16 时，模型 ``sale.subscription`` 的自定义字段不会自动迁移（该模型已合并到 ``sale.order``）。在这种情况下，可以在升级脚本中执行 SQL 查询，将数据从一个表移到另一个表。请注意，所有列/字段必须已经存在，因此建议在 ``post-`` 脚本中执行此操作（参见 :ref:`upgrade-scripts/phases`）。

      .. spoiler::

         .. code-block:: python

            def migrate(cr, version):
               cr.execute(
                  """
                  UPDATE sale_order so
                     SET custom_field = ss.custom_field
                    FROM sale_subscription ss
                   WHERE ss.new_sale_order_id = so.id
                  """
               )

         有关更多信息，请查看 :doc:`升级脚本 <../reference/upgrades/upgrade_scripts>`。

升级脚本还可以用于：

- 缓解升级过程的处理时间。例如，使用 SQL 查询存储具有大量记录的模型上计算的存储字段的值。
- 如果字段的值计算发生变化，重新计算字段。另见 :meth:`recompute_fields`。
- 卸载不需要的自定义模块。另见 :meth:`remove_module`。
- 修正错误数据或错误配置。

运行和测试升级脚本
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tabs::

   .. group-tab:: Odoo Online

      由于 Odoo Online 数据库不允许安装包含 Python 文件的自定义模块，因此无法在此平台上运行升级脚本。

   .. group-tab:: Odoo.sh

      正如在 :ref:`升级请求测试 <upgrade-request-test>` 的 Odoo.sh 标签中所解释的，Odoo.sh 已集成到升级平台中。

      一旦将升级分支设置为“在提交时更新”模式，每次向分支推送提交时，都会恢复升级后的备份，并更新所有自定义模块。此更新包括执行升级脚本。

      在升级生产数据库时，升级脚本的执行也是升级后的数据库恢复时平台对自定义模块进行更新的一部分。

   .. group-tab:: On-premise

      一旦您从 `升级平台 <https://upgrade.odoo.com>`_ 收到升级后的数据库转储，部署数据库并通过在 shell 中调用 :doc:`odoo-bin </developer/reference/cli>` 命令更新所有自定义模块。
      要更新自定义模块，请使用选项：`-u <modules>, --update <modules>`。

      .. important::
         正如 :doc:`CLI 文档 </developer/reference/cli>` 中提到的，调用 CLI 的命令取决于您如何安装 Odoo。

.. _upgrade_custom/upgraded_database/test_custom:

测试自定义模块
-----------------------

为了确保自定义模块在升级后的数据库中正常工作，它们也需要进行测试。这有助于确保数据库中存储的标准数据和自定义数据一致，并且在升级过程中没有丢失任何数据。

需要注意的事项：

- 视图无法正常工作：在升级过程中，如果某个视图由于其内容而导致问题，则该视图将被禁用。您可以在升级报告中找到有关禁用视图的信息。此视图需要重新激活（如果不再有用，可以移除）。为此，我们建议使用升级脚本。
- :doc:`模块数据 <../tutorials/define_module_data>` 未更新：自定义记录具有 ``noupdate`` 标志，在升级数据库中的模块时不会更新。对于由于新版本中的更改而需要更新的自定义数据，建议使用升级脚本进行更新。另见：:meth:`update_record_from_xml`。

.. _upgrade_custom/testing_rehearsal:

步骤 5：测试和演练
=============================

当自定义模块在升级后的数据库中正常工作时，进行另一轮测试非常重要，以评估数据库的可用性，并检测以前测试中可能未发现的问题。有关测试升级数据库的更多信息，请参阅 :ref:`升级测试 <upgrade-testing>`。

正如 :ref:`升级生产 <upgrade-production>` 中所述，标准升级脚本和数据库都在不断演变。因此，我们强烈建议频繁请求新的升级测试数据库，并确保升级过程仍然成功。

此外，升级生产数据库前一天要进行完整的升级流程演练，以避免升级期间出现不良行为，并检测迁移数据可能发生的任何问题。

.. _upgrade_custom/production:

步骤 6：生产升级
==========================

当您对升级生产数据库充满信心时，请根据数据库的托管类型，按照 :ref:`升级生产 <upgrade-production>` 中描述的流程进行操作。
