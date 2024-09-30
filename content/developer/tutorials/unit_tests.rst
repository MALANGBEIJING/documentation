===================================
使用单元测试保护您的代码
===================================

.. important::
   本教程是 :doc:`server_framework_101` 教程的扩展。请确保您已完成该教程，并使用您构建的 `estate` 模块作为本教程练习的基础。

**参考**：
`Odoo 的测试框架：学习最佳实践 <https://www.youtube.com/watch?v=JEIscps0OOQ>`__（Odoo Experience 2020）在 YouTube 上。

编写测试是必要的，原因有很多。以下是一个不完整的列表：

* 确保代码在未来不会被破坏
* 定义代码的范围
* 提供用例示例
* 这是技术性记录代码的一种方式
* 通过在工作之前定义目标来帮助您的编码

运行测试
=========

在了解如何编写测试之前，我们需要知道如何运行它们。

.. code-block:: console

  $ odoo-bin -h
  用法：odoo-bin [选项]

  选项：
  --version             显示程序的版本号并退出
  -h, --help            显示此帮助信息并退出

  [...]

  测试配置：
    --test-file=TEST_FILE
                            运行一个 Python 测试文件。
    --test-enable           启用单元测试。
    --test-tags=TEST_TAGS
                            用逗号分隔的规范列表，用于过滤要执行的测试。如果设置了该选项，则启用单元测试。过滤规范的格式为：[-][tag][/module][:class][.method] '-' 指定我们是否要包括或排除匹配此规范的测试。tag 将匹配使用 @tagged 装饰器添加到类上的标签（所有测试类都有 'standard' 和 'at_install' 标签，直到显式移除，请参阅装饰器文档）。'*' 将匹配所有标签。如果在包含模式下省略了 tag，其值为 'standard'。如果在排除模式下省略了 tag，其值为 '*'。module、class 和 method 将分别匹配模块名、测试类名和测试方法名。例如：--test-tags :TestClass.test_func,/test_module,external 过滤和执行测试会发生两次：在每个模块安装/更新后立即进行，并在模块加载结束时进行。在每个阶段，测试都将通过 --test-tags 规范进行过滤，并另外通过动态规范 'at_install' 和 'post_install' 分别进行过滤。
    --screencasts=DIR       Screencasts 将位于 DIR/{db_name}/screencasts 中。
    --screenshots=DIR       截图将位于 DIR/{db_name}/screenshots 中。默认为 /tmp/odoo_tests。

  $ # 运行 account 模块及其安装的模块的所有测试
  $ # 已安装的依赖项不会被测试
  $ # 这需要一些时间，因为您需要安装模块，但会遵循 at_install 和 post_install
  $ odoo-bin -i account --test-enable
  $ # 运行此文件中的所有测试
  $ odoo-bin --test-file=addons/account/tests/test_account_move_entry.py
  $ # 测试标签可以帮助您轻松过滤
  $ odoo-bin --test-tags=/account:TestAccountMove.test_custom_currency_on_account_1

集成机器人
===========

.. note:: 本节仅适用于 Odoo 员工和为 `github.com/odoo` 做出贡献的人。我们强烈建议您自行设置持续集成（CI）。

编写测试后，确保在对源代码进行修改时测试始终通过非常重要。为了自动化此任务，我们使用一种称为持续集成（CI）的开发实践。这就是为什么我们在不同的时刻运行所有测试的原因。

无论您是否在 Odoo 工作，如果您尝试合并到 `odoo/odoo`、`odoo/enterprise`、`odoo/upgrade` 或 odoo.sh 上，您都必须经过 CI。如果您正在处理另一个项目，您应该考虑添加自己的 CI。

Runbot
------

**参考**：与此主题相关的文档可在
`Runbot FAQ <https://runbot.odoo.com/doc>`__ 中找到。

每次在 GitHub 上推送提交时，大多数测试都会在 `Runbot <https://runbot.odoo.com>`__ 上运行。

您可以通过在 Runbot 仪表板上过滤来查看提交/分支的状态。

对于每个分支都会创建一个 **bundle**。bundle 包含一个配置和批次。

**批次（batch）** 是一组构建，取决于 bundle 的参数。
如果所有构建都是绿色的（通过测试），则批次为绿色。

**构建（build）** 是我们启动服务器时的过程。它可以分为子构建。通常，对于社区版和企业版（仅当有企业版分支时，但您可以强制构建）以及该分支的迁移都会有构建。
如果每个子构建都是绿色的，则构建为绿色。

**子构建（sub-build）** 只执行完整构建的一部分。它用于加速 CI 过程。通常用于将 post_install 测试拆分为 4 个并行实例。
如果所有测试都通过且没有记录错误/警告，则子构建为绿色。

.. note::
  * 无论进行了哪些修改，所有测试都会运行。修正错误消息中的拼写错误或重构整个模块都会触发相同的测试。所有模块也都会被安装。这意味着即使 Runbot 是绿色的，也可能有些东西无法正常工作，即您的更改依赖于一个模块，但更改所在的模块并不依赖该模块。
  * 本地化模块（即特定国家的模块）不会在 Runbot 上安装（除非是通用模块）。一些具有外部依赖项的模块也可能被排除。
  * 每晚都会运行一个额外的测试：模块操作、本地化、单模块安装、多构建用于非确定性错误等。
    这些未包含在标准 CI 中，以缩短执行时间。

您还可以登录到 Runbot 构建的构建中。有 3 个可用的用户：`admin`、`demo` 和 `portal`。密码与登录名相同。这对于在不同版本上快速测试事物而无需在本地构建非常有用。完整的日志也可用；这些用于监控。

Robodoo
-------

您可能需要获得更多经验才能有权召唤 robodoo，但这里还是有一些备注。

Robodoo 是在您的 PR 上发送 CI 状态标签的人，但他也是那个友好地将您的提交集成到主存储库中的人。

当最后一个批次是绿色时，审阅者可以请求 robodoo 合并您的 PR（这更像是 `rebase` 而不是 `merge`）。然后它将进入 mergebot。

Mergebot
--------

`Mergebot <https://mergebot.odoo.com>`__ 是在合并 PR 之前的最后一个测试阶段。

它将获取您的分支中尚未在目标上存在的提交，进行阶段并再次运行测试，包括企业版，即使您只是在社区版中更改了某些内容。

此步骤可能会失败，并显示 `Staging failed` 错误消息。这可能是由于

* 一个非确定性错误已存在于目标上。如果您是 Odoo 员工，您可以在此处查看这些错误：https://runbot.odoo.com/runbot/errors
* 您引入的非确定性错误，但之前在 CI 中未检测到
* 与刚刚合并的另一个提交以及您尝试合并的内容不兼容
* 如果您只在社区存储库中进行了更改，与企业存储库不兼容

在请求合并机器人重试之前，请始终检查问题是否不是由您引起的：将您的分支重新基于目标并在本地重新运行测试。

模块
====

由于 Odoo 是模块化的，测试也需要模块化。这意味着测试在添加您正在添加的功能的模块中定义，测试不能依赖于您的模块不依赖的模块中的功能。

**参考**：与此主题相关的文档可在 :ref:`特殊标签<reference/testing/tags>` 中找到。

.. code-block:: python

  from odoo.tests.common import TransactionCase
  from odoo.tests import tagged

  # CI 将在所有模块安装后运行这些测试，
  # 而不是在安装定义它的模块后立即运行。
  @tagged('post_install', '-at_install')  # 添加 `post_install` 并移除 `at_install`
  class PostInstallTestCase(TransactionCase):
      def test_01(self):
          ...

  @tagged('at_install')  # 这是默认值
  class AtInstallTestCase(TransactionCase):
      def test_01(self):
          ...

如果您要测试的行为可能会因安装另一个模块而改变，您需要确保设置了 `at_install` 标签；否则，您可以使用 `post_install` 标签来加速 CI，并确保如果不应该改变，就不会改变。

编写测试
=========

**参考**：与此主题相关的文档可在
`Python unittest <https://docs.python.org/3/library/unittest.html>`__
和 :ref:`测试 Odoo<reference/testing>` 中找到。

在编写测试之前，需要考虑以下几点

* 测试应独立于数据库中当前的数据（包括演示数据）
* 测试不应通过留下/更改残留数据来影响数据库。这通常由测试框架通过回滚完成。因此，您在测试中绝不能调用 ``cr.commit``（也不能在业务代码中的其他任何地方调用）。
* 对于错误修复，测试应在应用修复之前失败，应用修复后通过。
* 不要测试已经在其他地方测试的内容；您可以信任 ORM。业务模块中的大多数测试应该只测试业务流程。
* 您不应需要将数据刷新到数据库中。

.. note:: 请记住，``onchange`` 仅适用于表单视图，而不是通过在 Python 中更改属性。这也适用于测试。如果您想模拟表单视图，可以使用 ``odoo.tests.common.Form``。

测试应位于模块根目录下的 ``tests`` 文件夹中。每个测试文件名应以 `test_` 开头，并在测试文件夹的 ``__init__.py`` 中导入。您不应在模块的 ``__init__.py`` 中导入测试文件夹/模块。

.. code-block:: bash

  estate
  ├── models
  │   ├── *.py
  │   └── __init__.py
  ├── tests
  │   ├── test_*.py
  │   └── __init__.py
  ├── __init__.py
  └── __manifest__.py

所有测试都应继承自 ``odoo.tests.common.TransactionCase``。通常，您会定义一个 ``setUpClass`` 和测试。在编写 `setUpClass` 之后，您可以在类中使用 `env` 并开始与 ORM 交互。

这些测试类构建在 Python 的 ``unittest`` 模块之上。

.. code-block:: python

  from odoo.tests.common import TransactionCase
  from odoo.exceptions import UserError
  from odoo.tests import tagged

  # CI 将在所有模块安装后运行这些测试，
  # 而不是在安装定义它的模块后立即运行。
  @tagged('post_install', '-at_install')
  class EstateTestCase(TransactionCase):

      @classmethod
      def setUpClass(cls):
          # 在 cls 上添加 env 和许多其他内容
          super(EstateTestCase, cls).setUpClass()

          # 为每个测试创建数据。通过在 setUpClass 中而不是在 setUp 或每个测试用例中完成，可以减少测试时间和代码重复。
          cls.properties = cls.env['estate.property'].create([...])

      def test_creation_area(self):
          """测试 total_area 是否按预期计算。"""
          self.properties.living_area = 20
          self.assertRecordValues(self.properties, [
             {'name': ..., 'total_area': ...},
             {'name': ..., 'total_area': ...},
          ])

      def test_action_sell(self):
          """测试在出售物业时一切是否按预期运行。"""
          self.properties.action_sold()
          self.assertRecordValues(self.properties, [
             {'name': ..., 'state': ...},
             {'name': ..., 'state': ...},
          ])

          with self.assertRaises(UserError):
              self.properties.forbidden_action_on_sold_property()

.. note:: 为了更好的可读性，根据测试的范围将您的测试拆分为多个文件。您还可以有一个大多数测试应继承的 Common 类；这个公共类可以定义模块的整个设置。例如，在
`account <{GITHUB_PATH}/addons/account/tests/common.py>`__ 中。

.. exercise:: 更新代码，使得没有人可以：

  - 为已售出的物业创建报价
  - 出售没有被接受的报价的物业

  并为这两种情况创建测试。此外，检查可售出的物业在出售后是否正确标记为已售出。

.. exercise:: 有人不断破坏取消选中花园复选框时重置花园面积和朝向的功能。确保它不会再次发生。

  .. tip:: 提示：请记住上面关于 `Form` 的说明。
