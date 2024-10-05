=============
数据清理
=============

Odoo *数据清理*应用程序通过以下功能帮助保持数据库整洁有序：

- :ref:`去重 <data_cleaning/deduplication>`：合并或删除重复的条目，以确保数据的唯一性。
- :ref:`回收 <data_cleaning/recycle>`：识别过时的记录以进行归档或删除。
- :ref:`合并 <data_cleaning/merge-action-manager>`：将多个相似的条目合并为一个简化的记录。
- :ref:`格式化 <data_cleaning/field-cleaning>`：通过查找和替换来标准化文本数据，以满足特定需求。

通过可自定义的自动规则（或手动操作），数据库中的单个记录及其相关的文本数据将始终保持最新和一致的格式，符合公司规范。

.. _data_cleaning/install-modules:

安装模块
===============

*数据清理*应用程序包含多个模块。:ref:`安装 <general/install>`以下模块以访问所有可用功能：

.. list-table::
   :header-rows: 1
   :widths: 25 25 50

   * - 名称
     - 技术名称
     - 描述
   * - :guilabel:`数据回收`
     - `data_recycle`
     - 启用回收功能的基础模块，适用于Odoo社区版。
   * - :guilabel:`数据清理`
     - `data_cleaning`
     - 启用字段清理功能，格式化多个记录中的文本数据，仅适用于Odoo企业版。
   * - :guilabel:`数据清理（合并）`
     - `data_merge`
     - 启用去重功能，以查找相似（或重复）的记录并合并它们，仅适用于Odoo企业版。

.. spoiler:: 另外，还有几个特定应用的模块可供使用

   .. list-table::
      :widths: 25 25 50

      * - :guilabel:`CRM去重`
        - `data_merge_crm`
        - 启用*CRM*应用中的去重功能，并使用 :doc:`CRM默认合并功能 <../sales/crm/pipeline/merge_similar>`。
      * - :guilabel:`帮助台合并操作`
        - `data_merge_helpdesk`
        - 启用*帮助台*应用的合并功能。
      * - :guilabel:`项目合并操作`
        - `data_merge_project`
        - 启用*项目*应用的合并功能。
      * - :guilabel:`UTM去重`
        - `data_merge_utm`
        - 启用*UTM Tracker*应用的合并功能。
      * - :guilabel:`WMS会计合并`
        - `data_merge_stock_account`
        - 在合并可能影响库存估值的产品时发出警告，如果安装了*库存*应用。

.. _data_cleaning/deduplication:
去重
=============

在 :guilabel:`重复项` 仪表板 (:menuselection:`数据清理应用程序 --> 去重`) 中，Odoo 根据 :ref:`去重规则 <data_cleaning/deduplication-rules>` 设置的条件，建议将一组相似的记录进行 :ref:`合并 <data_cleaning/merge-records>`。

.. image:: data_cleaning/data-cleaning-duplicates.png
   :align: center
   :alt: 数据清理应用程序中的去重仪表板。

在左侧的 :guilabel:`规则` 侧边栏中列出了所有活动的去重规则，并显示了每个规则旁边检测到的重复项的总数。

默认情况下，选中 :guilabel:`全部` 规则。显示的记录按照规则分组，并在列表视图中显示相似度评分（满分100%），包括以下列：

- :guilabel:`创建日期`：原始记录的创建日期和时间。
- :guilabel:`名称`：原始记录的名称或标题。
- :guilabel:`字段值`：用于检测重复项的字段值。
- :guilabel:`使用于`：列出引用原始记录的其他模型。
- :guilabel:`ID`：原始记录的唯一ID。
- :guilabel:`是否为主记录`：重复项将被合并到主记录中。在相似记录的分组中只能有 **一条** 主记录。

在 :guilabel:`规则` 侧边栏中选择特定规则，以筛选重复记录。

.. _data_cleaning/merge-records:

合并重复记录
-----------------------

要合并记录，首先在相似记录的分组中选择一条 *主记录*。主记录作为基础，其他相似记录的附加信息将合并到其中。

可以选择不设置主记录，此时Odoo会随机选择一条记录进行合并。

接下来，点击相似记录分组顶部的 :guilabel:`合并` 按钮。然后点击 :guilabel:`确定` 确认合并。

记录合并后，会在主记录的聊天框中记录一条消息，描述此次合并。某些记录（如 *项目* 任务）会在聊天框中记录一个链接，方便参考旧记录。

.. tip::
   通过点击 :guilabel:`丢弃` 按钮丢弃分组。这样，分组会从列表中隐藏并归档。

   通过从 :ref:`搜索栏 <search/filters>` 中选择 :guilabel:`已丢弃` 筛选器，查看已丢弃的分组。

.. _data_cleaning/deduplication-rules:
去重规则
-------------------

在 :guilabel:`去重规则` 页面 (:menuselection:`数据清理应用程序 --> 配置 --> 规则: 去重`) 中，可以设置检测重复记录的条件。

这些规则可以针对数据库中的每个模型进行配置，并具有不同的具体性级别。

.. tip::
   去重规则默认情况下每天运行一次，作为计划操作时间表 (*数据合并: 查找重复记录*) 的一部分。然而，可以随时 :ref:`手动运行 <data_cleaning/run-deduplication-rule>` 每个规则。

修改去重规则
~~~~~~~~~~~~~~~~~~~~~~~~~~~

选择一个默认规则进行编辑，或点击 :guilabel:`新建` 按钮，创建一个新规则，位于 :guilabel:`去重规则` 页面 (:menuselection:`数据清理应用程序 --> 配置 --> 规则: 去重`)。

首先，选择一个 :guilabel:`模型` 作为此规则的目标。选择模型后，规则标题将更新为选定的模型。

可选地，配置 :guilabel:`域` 以指定此规则适用的记录。符合条件的记录数量显示在 :icon:`oi-arrow-right` :guilabel:`# 条记录` 链接中。

根据所选的 :guilabel:`模型`，会显示 :guilabel:`重复项处理方式` 字段。选择是 :guilabel:`归档` 还是 :guilabel:`删除` 合并的记录。

接下来，选择一个 :guilabel:`合并模式`：

- :guilabel:`手动`：需要手动合并每组重复项，同时启用 :guilabel:`通知用户` 字段。
- :guilabel:`自动`：自动合并重复项，不通知用户，基于相似度百分比高于 :guilabel:`相似度阈值` 字段设定值的记录。

启用 :guilabel:`激活` 切换按钮以使此规则保存后开始捕捉重复项。

最后，在 :guilabel:`去重规则` 字段中，至少创建一个去重规则，通过点击 :guilabel:`添加一行`，位于 :guilabel:`唯一ID字段` 列下。

- 从 :guilabel:`唯一ID字段` 下拉菜单中选择模型中的一个字段。该字段将用于相似记录的检测。
- 在 :guilabel:`匹配条件` 字段中选择一个匹配条件，以根据 :guilabel:`唯一ID字段` 中的文本应用去重规则：

  - :guilabel:`完全匹配`：文本中的字符完全匹配。
  - :guilabel:`大小写/重音不敏感匹配`：文本中的字符匹配，忽略大小写和语言特定的重音差异。

.. important::
   如果在 :guilabel:`去重规则` 字段中没有设置至少一个去重规则，则此规则不会捕捉重复项。

.. tip::
   可以启用更多字段进行高级配置。

   如果在多公司数据库中， :guilabel:`跨公司` 字段可用。启用后，跨公司重复项将被建议。

   启用 :ref:`开发者模式` 以显示 :guilabel:`建议阈值` 字段。低于此字段设定值的相似度的重复项不会被建议。

规则配置完成后，可以关闭规则表单，或 :ref:`手动运行规则 <data_cleaning/run-deduplication-rule>` 以立即捕捉重复记录。

.. _data_cleaning/run-deduplication-rule:
手动运行去重规则
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

要随时手动运行特定的去重规则，导航到 :menuselection:`数据清理应用程序 --> 配置 --> 规则: 去重`，然后选择要运行的规则。

接下来，在规则表单上，选择左上角的 :guilabel:`去重` 按钮。这样做后，:icon:`fa-clone` :guilabel:`重复项` 智能按钮将显示捕获的重复项数量。

点击 :icon:`fa-clone` :guilabel:`重复项` 智能按钮以管理这些记录。

.. _data_cleaning/recycle:

回收记录
===============

使用 *回收记录* 功能清理数据库中的旧记录和过时记录。

在 :guilabel:`字段回收记录` 仪表板 (:menuselection:`数据清理应用程序 --> 回收记录`) 上，Odoo 通过匹配由 :ref:`回收记录规则 <data_cleaning/recylce-rule>` 设置的条件，检测可以归档或删除的记录。

.. image:: data_cleaning/data-cleaning-recycle.png
   :align: center
   :alt: 数据清理应用程序中的字段回收记录仪表板。

:guilabel:`回收规则` 侧边栏列出了每个活动的回收记录规则，并在每个规则旁显示检测到的记录总数。

默认情况下，选择 :guilabel:`全部` 选项。记录以列表视图显示，列中包括以下信息：

- :guilabel:`记录ID`：原始记录的ID。
- :guilabel:`记录名称`：原始记录的名称或标题。

在 :guilabel:`回收规则` 侧边栏中选择特定的规则，以筛选重复记录。

要回收记录，点击记录行上的 :icon:`fa-check` :guilabel:`验证` 按钮。

执行此操作后，记录将根据规则配置进行回收，要么被归档，要么从数据库中删除。

.. tip::
   通过点击 :icon:`fa-times` :guilabel:`丢弃` 按钮丢弃分组。执行此操作后，记录将从列表中隐藏，并且以后不会再被回收规则检测到。

   通过从 :ref:`搜索栏 <search/filters>` 下拉菜单中选择 :guilabel:`丢弃` 筛选器来查看丢弃的记录。

.. _data_cleaning/recylce-rule:

回收记录规则
--------------------

在 :guilabel:`回收记录规则` 页面 (:menuselection:`数据清理应用程序 --> 配置 --> 规则: 回收记录`) 中，可以设置回收记录的条件。

这些规则可以针对数据库中的每个模型进行配置，并具有不同的具体性级别。

.. tip::
   默认情况下，回收规则每天运行一次，作为计划操作 (*数据回收: 清理记录*) 的一部分。不过，每个规则都可以 :ref:`手动运行 <data_cleaning/run-recycle-rule>`。

默认情况下，不存在回收记录规则。点击 :guilabel:`新建` 按钮创建新规则。

在回收记录规则表单上，首先选择一个 :guilabel:`模型` 作为此规则的目标。选择模型后，规则标题将更新为选定的模型。

可选地，配置 :guilabel:`过滤器` 以指定此规则适用的记录。符合条件的记录数量显示在 :icon:`oi-arrow-right` :guilabel:`# 条记录` 链接中。

接下来，配置字段和时间范围，以确定规则如何检测要回收的记录：

- :guilabel:`时间字段`：选择一个字段作为时间依据 (:dfn:`时间间隔`)。
- :guilabel:`时间间隔`：输入时长，必须是一个整数 (例如 `7`)。
- :guilabel:`时间单位`：选择时间单位 (:guilabel:`天`、:guilabel:`周`、:guilabel:`月` 或 :guilabel:`年`)。

然后，选择一个 :guilabel:`回收模式`：

- :guilabel:`手动`：需要手动回收每条检测到的记录，同时启用 :guilabel:`通知用户` 字段。
- :guilabel:`自动`：自动合并回收分组，不通知用户。

最后，选择一个 :guilabel:`回收操作`，要么 :guilabel:`归档`，要么 :guilabel:`删除` 记录。如果选择 :guilabel:`删除`，请选择是否在规则中 :guilabel:`包含已归档` 记录。

规则配置完成后，可以关闭规则表单，或 :ref:`手动运行规则 <data_cleaning/run-recycle-rule>` 以立即捕捉要回收的记录。

.. example::
   可以将回收规则配置为删除一年未更新的已归档线索和商机，并指定一个特定的丢失原因，使用以下配置：

   - :guilabel:`模型`: :guilabel:`线索/商机`
   - :guilabel:`过滤器`:

     - `活动` `未设置`
     - `丢失原因` `在` `太贵了`

   - :guilabel:`时间字段`: :guilabel:`最后更新时间 (线索/商机)`
   - :guilabel:`时间间隔`: `1`
   - :guilabel:`时间单位`: :guilabel:`年`
   - :guilabel:`回收模式`: :guilabel:`自动`
   - :guilabel:`回收操作`: :guilabel:`删除`
   - :guilabel:`包含已归档`: :icon:`fa-check-square`

   .. image:: data_cleaning/data-cleaning-recycle-rule.png
      :align: center
      :alt: 线索/商机的回收记录规则表单。

.. _data_cleaning/run-recycle-rule:
手动运行回收规则
~~~~~~~~~~~~~~~~~~~~~~~~~~~

要随时手动运行特定的回收规则，导航到 :menuselection:`数据清理应用程序 --> 配置 --> 规则: 回收记录`，并选择要运行的规则。

然后，在规则表单中，点击左上角的 :guilabel:`立即运行` 按钮。执行此操作后，:icon:`fa-bars` :guilabel:`记录` 智能按钮将显示捕获的记录数量。

点击 :icon:`fa-bars` :guilabel:`记录` 智能按钮以管理这些记录。

.. _data_cleaning/field-cleaning:

字段清理
==============

在 :guilabel:`字段清理记录` 仪表板 (:menuselection:`数据清理应用程序 --> 字段清理`) 上，Odoo 根据字段清理规则建议对记录中的字段数据进行格式化更改，以遵循指定的规范。

.. _data_cleaning/merge-action-manager:

合并操作管理器
====================

:guilabel:`合并操作管理器` (:menuselection:`数据清理应用程序 --> 配置 --> 合并操作管理器`) 启用或禁用数据库模型中 *操作* 菜单中的 *合并* 操作功能。
