=================
维护设置
=================

.. |MTBF| replace:: :abbr:`MTBF (平均故障间隔时间)`
.. |MTTR| replace:: :abbr:`MTTR (平均修复时间)`

Odoo *维护*模块帮助企业对仓库中使用的设备进行纠正性和预防性维护。这有助于企业避免设备故障、仓库工作中心的阻塞以及紧急维修费用。

维护团队
==========

创建维护请求时，可以分配一个*维护团队*负责处理请求。

要查看现有的维护团队，请导航到 :menuselection:`维护应用 --> 配置 --> 维护团队`。

在显示的 :guilabel:`团队` 页面上，会列出所有现有的团队（如果有），默认显示的列包括 :guilabel:`团队名称`、:guilabel:`团队成员` 和 :guilabel:`公司`。

.. image:: maintenance_setup/maintenance-setup-teams-list.png
   :align: center
   :alt: 维护团队页面上的团队列表.

要添加新团队，点击 :guilabel:`新建`。这将在团队列表的底部添加一条空行。在显示的 :guilabel:`团队名称`列下的空白字段中，为新维护团队命名。

在 :guilabel:`团队成员`列中，点击字段以显示数据库中的现有用户下拉菜单。选择应成为新维护团队成员的用户。

点击 :guilabel:`搜索更多...` 以打开 :guilabel:`搜索：团队成员` 弹出窗口，搜索下拉菜单中未显示的用户。

.. image:: maintenance_setup/maintenance-setup-search-team-members.png
   :align: center
   :alt: 搜索：团队成员弹出窗口.

在 :guilabel:`公司` 列中，如果处于多公司环境下，点击下拉菜单选择此新维护团队所属的公司。

完成后，点击 :guilabel:`保存` 以保存更改。

.. tip::
   分配给维护团队的成员在查看*维护日历*时也称为*技术人员*。

   导航到 :menuselection:`维护应用 --> 维护 --> 维护日历`，点击现有的维护请求。在结果弹出窗口中，找到 :guilabel:`技术人员` 字段。字段中显示的名字是团队成员，负责处理该特定请求的用户。

   .. image:: maintenance_setup/maintenance-setup-popover-technician.png
      :align: center
      :alt: 显示技术人员字段的维护请求弹出窗口.

   页面右侧的边栏包含一个设置为今天日期的最小化日历，以及一个 :guilabel:`技术人员` 列表，显示所有技术人员（或团队成员）当前有开放的请求。

设备
====

在 Odoo *维护*模块中，*设备*指的是仓库工作中心中内部使用的机器和工具。设备可以包括计算机或平板电脑等技术设备、电动工具、制造机器等。

设备类别
----------

每件设备都属于一个*设备类别*。在添加新设备之前，请确保已创建适合的设备类别。

要创建新的设备类别，请导航到 :menuselection:`维护应用 --> 配置 --> 设备类别`，然后点击 :guilabel:`新建`。这样会打开一个空白的设备类别表单。

.. image:: maintenance_setup/maintenance-setup-category-form.png
   :align: center
   :alt: 填写了各种信息的设备类别表单.

在空白表单上，在 :guilabel:`类别名称`字段中为设备类别分配一个名称。

在 :guilabel:`负责人`字段中，如果需要，分配一个用户负责该类别中的设备。默认情况下，创建类别的用户会被自动选为 :guilabel:`负责人`。

如果处于多公司环境中，点击 :guilabel:`公司`字段中的下拉菜单，选择此类别中的设备所属的公司。

在 :guilabel:`电子邮件别名`字段中，必要时为该类别分配一个电子邮件别名。

在 :guilabel:`评论`字段中，输入任何供内部用户参考的与此类别相关的备注或说明，如果需要。

.. note::
   一旦创建了新的设备类别，属于该类别的所有设备以及任何过去或当前的维护请求都可以从设备类别表单中查看。

   导航到 :menuselection:`维护应用 --> 配置 --> 设备类别`，选择一个类别以查看。在表单顶部找到 :guilabel:`设备` 和 :guilabel:`维护` 智能按钮。

   .. image:: maintenance_setup/maintenance-setup-smart-buttons.png
      :align: center
      :alt: 设备类别表单上的设备和维护智能按钮.

   点击 :guilabel:`设备` 智能按钮以查看该类别中的所有设备。点击 :guilabel:`维护` 智能按钮以查看任何过去或当前的维护请求。

机器与工具
----------

要添加新设备，请导航到 :menuselection:`维护应用 --> 设备 --> 机器与工具`，然后点击 :guilabel:`新建`。这样会打开一个空白的设备表单。

在 :guilabel:`名称`字段中为新设备分配一个名称。在 :guilabel:`设备类别`字段中，点击下拉菜单选择此新设备应属于的类别。

如果处于多公司环境中，点击 :guilabel:`公司`字段中的下拉菜单，选择新设备所属的公司。

在 :guilabel:`使用者`字段中，从三个单选按钮选项中选择一个：:guilabel:`部门`、:guilabel:`员工`或 :guilabel:`其他`。

.. image:: maintenance_setup/maintenance-setup-new-equipment-left-side.png
   :align: center
   :alt: 新设备表单上的左侧信息字段.

如果选择了 :guilabel:`部门`，则在 :guilabel:`使用者`字段下方会出现一个 :guilabel:`部门`字段。点击下拉菜单选择使用该设备的部门。

如果选择了 :guilabel:`员工`，则在 :guilabel:`使用者`字段下方会出现一个 :guilabel:`员工`字段。点击下拉菜单选择使用该设备的员工。

如果选择了 :guilabel:`其他` 选项，则在 :guilabel:`使用者` 字段下方会同时出现 :guilabel:`部门` 和 :guilabel:`员工`字段。点击相应字段的下拉菜单，选择使用该设备的部门和员工。

在 :guilabel:`维护团队`字段中，选择负责该设备的团队。在 :guilabel:`技术人员`字段中，选择负责该设备的团队成员/用户。

.. image:: maintenance_setup/maintenance-setup-new-equipment-right-side.png
   :align: center
   :alt: 新设备表单上的右侧信息字段.

在 :guilabel:`使用位置`字段中，输入该设备的使用位置（如果不在内部工作中心，例如在办公室）。

在 :guilabel:`工作中心`字段中，点击下拉菜单选择该设备将使用的工作中心。

在表单底部的 :guilabel:`描述` 选项卡下的空白处，添加任何相关信息供用户参考。

产品信息选项卡
~~~~~~~~~~~~~~~

要在创建新设备时添加任何相关信息，请从设备表单中点击 :guilabel:`产品信息`选项卡。

.. image:: maintenance_setup/maintenance-setup-product-information.png
   :align: center
   :alt: 显示了可用字段的产品信息选项卡.

在 :guilabel:`供应商`字段中，添加购买该设备的供应商。在 :guilabel:`供应商参考`字段中，添加从供应商获得的产品参考编号（如果适用）。

在 :guilabel:`型号`字段中，指定此设备的型号（如果适用）。如果该设备有序列号，请在 :guilabel:`序列号`字段中添加序列号。

在 :guilabel:`生效日期`字段中，点击日期以显示日历弹出窗口，然后选择日期。此日期表示该设备首次投入使用的时间，并将用于计算设备 :guilabel:`维护`选项卡中的平均故障间隔时间 (MTBF)。

在 :guilabel:`成本`字段中，指定获取该设备的费用（如果适用）。

如果设备有保修，选择 :guilabel:`保修到期日`字段中的日历弹出窗口中的日期。

维护选项卡
~~~~~~~~~~~~~

每件设备都有各种维护指标，并基于纠正性维护和计划中的预防性维护自动计算。

要查看特定设备的维护指标，请从设备表单中点击 :guilabel:`维护`选项卡。

.. image:: maintenance_setup/maintenance-setup-metrics.png
   :align: center
   :alt: 设备表单上显示计算的指标字段的维护选项卡.

这将显示以下字段：

- :guilabel:`预计平均故障间隔时间`: 距离下一次故障的预期时间（天）。这是唯一未灰显的字段，用户可以编辑的唯一字段。
- :guilabel:`平均故障间隔时间`: 报告故障之间的时间（天）。该值根据完成的纠正性维护计算。
- :guilabel:`预计下次故障`: 预计下一次故障的日期。该日期根据最新故障日期 + |MTBF| 计算得出。
- :guilabel:`最新故障`: 最新故障的日期。此字段中的值会在报告该设备的故障后更新。
- :guilabel:`平均修复时间`: 修复该设备故障所需的时间（天）。该值会在为此设备完成维护请求后更新。

工作中心
==========

要查看设备使用的工作中心及其在工作中心中的使用方式，请导航到 :menuselection:`维护应用 --> 设备 --> 工作中心`，然后点击某个工作中心。

在显示的工作中心表单中，点击 :guilabel:`设备` 选项卡以查看该工作中心使用的所有机器和工具。

每件设备都会列出一些相关信息：:guilabel:`设备名称`、负责的 :guilabel:`技术人员`、其所属的 :guilabel:`设备类别`，以及一些重要的维护指标：其 |MTBF|、|MTTR| 和 :guilabel:`预计下次故障`日期。

.. image:: maintenance_setup/maintenance-setup-work-center.png
   :align: center
   :alt: 包含在工作中心中的设备列表.

.. tip::
   要直接从工作中心表单添加新设备，请点击 :guilabel:`设备`选项卡下的 :guilabel:`添加一行`。这将打开一个 :guilabel:`添加：维护设备` 弹出窗口。

   在弹出窗口中，选择应添加到工作中心的设备，然后点击 :guilabel:`选择`。

.. seealso::
   :doc:`add_new_equipment`
