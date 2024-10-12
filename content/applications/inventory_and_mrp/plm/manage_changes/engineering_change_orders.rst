=========================
工程变更订单 (ECO)
=========================

.. |BOM| replace:: :abbr:`BoM (Bill of Materials)`
.. |BOMs| replace:: :abbr:`BoMs (Bills of Materials)`
.. |ECO| replace:: :abbr:`ECO (Engineering Change Order)`
.. |ECOs| replace:: :abbr:`ECOs (Engineering Change Orders)`

.. _plm/eco:

使用*工程变更订单* (*ECOs*) 来跟踪、实施和还原产品及 :doc:`物料清单 <../../manufacturing/basic_setup/bill_configuration>` (|BOMs|) 的更改版本。

工程变更订单可以通过以下方式创建：

#. :ref:`直接在 ECO 类型 <plm/eco/create-eco>` 中创建。
#. 由操作员在 :ref:`操作的平板视图 <plm/eco/tablet-view>` 中创建。
#. 从反馈自动生成，提交至 :ref:`ECO 类型的电子邮件别名 <plm/eco/eco-type>`。

.. _plm/eco/create-eco:

创建 ECO
==========

要创建新的 |ECO|，请导航到 *PLM* 应用，然后选择将用于跟踪更改进度的 |ECO| 类型卡片。在 :guilabel:`工程变更订单` 页面，点击左上角的 :guilabel:`新建` 按钮。

.. note::
   了解如何创建新的 :ref:`ECO 类型 <plm/eco/eco-type>` 来分类和组织变更订单。这样可确保员工仅查看与其职责相关的 |ECOs|，无论是新产品引入、针对特定产品线的更新，还是法规遵从性。

在 |ECO| 表单中，填写以下字段：

- :guilabel:`描述` 是改进的简要摘要。
- :guilabel:`类型`：用于指定 |ECO| 类型项目，以组织 |ECOs|。
- :guilabel:`适用于` 确定 |ECO| 是更改 :guilabel:`物料清单` 还是 :guilabel:`仅更改产品`。
- :guilabel:`产品` 指示正在改进的产品。
- :guilabel:`物料清单` 指定更改的 |BOM|。如果 :guilabel:`产品` 字段中的产品已有现有 |BOM|，则该字段会自动填充。如果有多个 |BOM|，从下拉菜单中选择适当的选项。
- :guilabel:`公司` 字段在多公司数据库中使用。指定更改是否适用于特定公司的产品，或留空表示适用于所有公司。
- :guilabel:`负责人` 表示负责此 |ECO| 的指派人员。（可选）
- :guilabel:`生效` 指定 |ECO| 何时生效。选择 :guilabel:`尽快` 表示当授权用户 :ref:`应用更改 <plm/eco/apply-changes>` 后，|ECO| 会立即适用于生产 |BOM|。

  另一方面，选择 :guilabel:`指定日期` 并设置特定日期，可以更容易跟踪 |BOM| 的版本历史记录，以及生产中使用的特定 |BOM| 日期。
- :guilabel:`标签` 用于分配 |ECOs| 的优先级和组织。通过在字段中输入名称并从下拉菜单中选择 :guilabel:`创建`，可以创建新的标签。（可选）

填写完 |ECO| 表单后，点击 :guilabel:`开始修订` 按钮以开始实施更改。

按下 :guilabel:`开始修订` 后，会发生以下三件事：

#. :guilabel:`文档` 智能按钮出现，用于存储相关的 |BOM| 文件。
#. 生产 |BOM| 的副本会存储在新出现的 |ECO| 的 :guilabel:`修订` 智能按钮中。系统还会分配下一个可用的版本号（例如，`V2`, `V3`, ...），以便跟踪所有 |BOM| 版本。
#. |ECO| 类型的阶段会显示在 |ECO| 的右上角。

.. note::
   当 :guilabel:`适用于物料清单` 被选中并且按下 :guilabel:`开始修订` 按钮时，:guilabel:`修订` 智能按钮才会可用。

.. image:: engineering_change_orders/eco-form.png
   :align: center
   :alt: 包含右上角阶段概述的 ECO，以及 *修订* 智能按钮。

更改组件
=================

要修改 |BOM| 中的组件，请点击 |ECO| 中的 :guilabel:`修订` 按钮，进入 |BOM| 的新版本。Odoo 会将非生产版本的 |BOM| 标记为 :guilabel:`归档` 标签，以便与当前版本区分开来。

.. example::
   点击产品 `[D_0045 凳子]` 的 |ECO| 的 :guilabel:`开始修订` 按钮后，可以通过点击 :guilabel:`修订` 智能按钮更改产品的 |BOM|。这样将打开标有红色 :guilabel:`归档` 标签的归档 |BOM|。

   .. image:: engineering_change_orders/archived-bom.png
      :align: center
      :alt: 显示已归档的物料清单。

在新的 |BOM| 中，在 :guilabel:`组件` 选项卡中，继续修改组件列表，变更现有组件的 :guilabel:`数量`，使用 :guilabel:`添加行` 按钮添加新组件，并使用 :guilabel:`🗑️ (删除)` 图标移除组件。

.. _plm/eco/example-keyboard:

.. example::
   在键盘 |BOM| 的第二个版本中，减少了组件数量，并添加了新组件 `稳定器`。

   .. image:: engineering_change_orders/version-2-bom.png
      :align: center
      :alt: 通过 *修订* 智能按钮进入新 BoM 并更改组件。

比较更改
---------------

完成更改后，导航回 |ECO|，通过点击左上角的面包屑 `ECO00X` 返回。在 |ECO| 表单中，新的 :guilabel:`BOM 更改` 选项卡会显示当前 |BOM| 与新版本之间的差异。

蓝色文本表示添加到修订版 |BOM| 的新组件，这些组件在生产 |BOM| 中没有。黑色文本表示两个 |BOM| 共享的更新，而红色文本表示修订版 |BOM| 中删除的组件。

更改和测试都封装在修订版 |BOM| 中，并且不会影响当前用于生产的 |BOM|。也就是说，直到 :ref:`应用更改 <plm/eco/apply-changes>` 后，才会影响。

.. example::
   在 |ECO| 的 :guilabel:`BOM 更改` 选项卡中查看当前与修订版键盘 |BOM| 之间的差异摘要。

   .. image:: engineering_change_orders/bom-changes.png
      :align: center
      :alt: 在 *BOM 更改* 选项卡中查看组件更改的摘要。

更改操作
=================

要修改 |BOM| 中的操作，请点击 |ECO| 中的 :guilabel:`修订` 按钮，进入存档的 |BOM| 新版本。

在新的 |BOM| 版本中，切换到 :guilabel:`操作` 选项卡以查看和编辑 |BOM| 操作。要进行更改，请选择每个操作，这会打开相应的 :guilabel:`打开：操作` 弹出窗口。

.. note::
   :guilabel:`操作` 选项卡默认不可用。要启用它，请导航至 :menuselection:`制造应用 --> 配置 --> 设置`，并勾选 :guilabel:`工作单` 复选框。

对 :guilabel:`打开：操作` 弹出窗口中的任何字段进行更改，完成后点击 :guilabel:`保存`。

通过点击 :guilabel:`添加行` 按钮创建新操作，并通过点击 :guilabel:`归档操作` 按钮删除新操作。

比较更改
---------------

完成更改后，导航回 |ECO|，通过点击左上角的面包屑 `ECO00X` 返回。

在 |ECO| 表单中，新的 :guilabel:`操作更改` 选项卡显示当前生产 |BOM| 与新版本之间的差异。


蓝色文本表示在修订版 |BOM| 中添加的新操作，这些操作在生产 |BOM| 中尚不存在。黑色文本代表两个 |BOM| 共享的更新，而红色文本表示在修订版 |BOM| 中删除的操作。

在 |ECO| 中对 |BOM| 的修改**不会**影响用于生产的 |BOM|。也就是说，直到 :ref:`更改被应用 <plm/eco/apply-changes>` 之后，才会生效。

在 :guilabel:`操作更改` 选项卡中，表格下方的每一行详细信息反映了以下信息：

- :guilabel:`操作`: 被修改的操作名称。
- :guilabel:`步骤`: 指定操作中的质量控制点，当操作包括详细说明时可见。

.. note::
   若要检查说明，点击 |BOM| 的 :guilabel:`操作` 选项卡中的操作条目。然后，在 :guilabel:`打开操作` 弹出窗口中查找显示在顶部的 :guilabel:`说明` 智能按钮。

.. example::
   `装配` :guilabel:`操作` 包括完成操作所需的 `10` 个详细 :guilabel:`说明`。

   .. image:: engineering_change_orders/instructions-smart-button.png
      :align: center
      :alt: 显示 *说明* 智能按钮，以检查操作是否有附加说明。

- :guilabel:`步骤类型`: 详细说明操作中的质量控制类型。
- :guilabel:`类型`: 对应于彩色文本，指定修订版 |BOM| 与生产 |BOM| 之间的差异。操作更改类型可以是 :guilabel:`添加`、:guilabel:`删除` 或 :guilabel:`更新`。
- :guilabel:`工作中心`: 指定执行操作的工作中心。
- :guilabel:`手动时长变更`: 指的是 :guilabel:`默认时长` 字段中的更改，指定完成操作的预期时间。

.. example::
   :guilabel:`操作更改` 选项卡比较了生产 |BOM| 与修订版 |BOM| 中的操作。

   在修订版 |BOM| 中，添加了一个新的 `装配` :guilabel:`操作`，该操作在 :guilabel:`工作中心` `装配线 1` 处执行。此外，操作的预期时长为 `20.00` 分钟，由 :guilabel:`手动时长变更` 指定。

   为了补充 `装配` 操作，添加了两个质量控制点说明：

   1. 第一个是 :guilabel:`步骤` `QCP00039`，其 :guilabel:`步骤类型` 为 :guilabel:`登记组件生产`。
   2. 第二个 :guilabel:`步骤` 是 `QCP00034`，其 `说明` :guilabel:`步骤类型` 提供了额外的装配细节。

.. image:: engineering_change_orders/operation-changes.png
      :align: center
      :alt: 显示 |ECO| 中 *操作更改* 选项卡。

.. _plm/eco/apply-changes:

应用更改
=============

在验证更改后，将 |ECO| 移动到 :ref:`验证阶段 <plm/eco/stage-config>`，这是要求批准的阶段，修订的更改在被批准之前无法应用到生产 |BOM|。

一旦批准者接受更改，:guilabel:`应用更改` 按钮就会变得可用。单击此按钮后，|ECO| 将自动移动到结束阶段，更改会被应用，原始的生产 |BOM| 会被存档，而修订版 |BOM| 将成为新的生产 |BOM|。

验证更改
--------------

要确保更改已生效，从刚刚按下 :guilabel:`应用更改` 按钮的 |ECO| 中，点击 :guilabel:`修订` 智能按钮返回修订版 |BOM|。

在修订版 |BOM| 上，显示的红色大 :guilabel:`已存档` 标记将被移除。

要进一步验证更改，请检查生产 |BOM|，路径为：:menuselection:`制造应用 --> 产品 --> 产品`，然后选择产品。

接着，在产品表单中，点击 :guilabel:`物料清单` 智能按钮，从列表中选择 |BOM|。在 |BOM| 的 :guilabel:`杂项` 选项卡中，:guilabel:`版本` 字段将更新，以匹配最新 |ECO| 的 :guilabel:`修订` 智能按钮中显示的版本号。

.. example::
   在应用了针对 :ref:`键盘 <plm/eco/example-keyboard>` 的 |ECO| 更改后，可在 :guilabel:`杂项` 选项卡中查看当前键盘 |BOM| 的版本。在此处，:guilabel:`版本` 号已更新为 `2`，与 |ECO| 中 :guilabel:`修订` 智能按钮中显示的 `V2` 一致。

   .. image:: engineering_change_orders/bom-version.png
      :align: center
      :alt: 在 *杂项* 选项卡中查看当前 *BOM* 版本。

.. _plm/eco/tablet-view:

从平板视图创建 |ECO|
===========================

操作员在执行制造订单 (MO) 时，可以直接建议更清晰的操作说明，操作路径为 *制造应用*。

要以这种方式创建 |ECO|，首先导航到 :menuselection:`制造应用 --> 操作 --> 制造订单`。然后选择所需的 :abbr:`MO (制造订单)`，切换到 :guilabel:`工单` 选项卡。接着，点击所需工单的 :guilabel:`📱 (手机)` 图标以打开操作的*平板视图*。

.. important::
   只有当 :abbr:`MO (制造订单)` 处于 :guilabel:`已确认` 或 :guilabel:`进行中` 状态时，才会显示 :guilabel:`📱 (手机)` 图标。

.. image:: engineering_change_orders/tablet-icon.png
      :align: center
      :alt: 查找每个操作的平板图标，位于从右数第二个位置。

接着，通过点击操作平板视图中的 :guilabel:`☰ (三条横线)` 图标来添加说明步骤。这样会打开 :abbr:`MO (制造订单)` 的 :guilabel:`菜单`。然后点击 :guilabel:`添加步骤` 按钮。

.. image:: engineering_change_orders/additional-options-menu.png
      :align: center
      :alt: 点击平板视图中的三条横线图标打开 *添加步骤* 弹出窗口。

点击按钮会显示 :guilabel:`添加步骤` 弹出窗口，提交拟议的更改。

在 :guilabel:`标题` 字段中输入简短的步骤描述。接着，在 :guilabel:`说明` 文本字段中详细填写步骤说明。可选地，可以在 :guilabel:`文档` 字段中添加图片。完成后，点击 :guilabel:`提出更改` 按钮。

.. example::
   若要提出额外的破损组件检查请求，请在 :guilabel:`添加步骤` 弹出窗口中输入详细信息。这将创建一个指令性质量控制点，并将在下一节中进行审查。

   .. image:: engineering_change_orders/add-a-step.png
      :align: center
      :alt: 填写 *添加步骤* 表单以建议附加的质量控制点。

基于 :guilabel:`添加步骤` 弹出窗口中的输入，系统会创建一个 |ECO|，其中包含以下信息：

#. :guilabel:`描述` 是操作名称，后跟参考编号的 :abbr:`MO (制造订单)`。
#. |ECO| :guilabel:`类型` 自动分配为 `BOM 更改`。
#. :guilabel:`产品` 和 :guilabel:`物料清单` 字段会自动填充，基于 :abbr:`MO (制造订单)` 中使用的 |BOM|。
#. :guilabel:`负责人` 是提交反馈的操作员。
查看 ECO
--------

要查看拟议的更改，请导航到 :menuselection:`PLM app --> 概览`。在 `BOM 更新` |ECO| 类型卡片中，:guilabel:`X 工程更改` 按钮代表从平板视图创建的操作更改数量。

点击 :guilabel:`X 工程更改` 按钮打开 |ECO| 类型的看板视图。要查看建议，选择 `新` 阶段中的 |ECO|。

在 |ECO| 上，在 :guilabel:`操作更改` 选项卡中查看拟议更改的摘要。点击 :guilabel:`修订` 智能按钮，导航至修订的 |BOM| 并详细查看拟议的更改。

.. example::
   一名操作员在执行产品 `键盘` 的 :abbr:`MO (制造订单)` `WH/MO/00010` 的 `组装开关` 操作时，通过平板视图添加了一个检查损坏组件的步骤。

   然后，这个创建的 |ECO| 可以通过导航至 :menuselection:`PLM app --> 概览` 中的 `BOM 更改` |ECO| 类型找到。默认情况下，从平板视图创建的 |ECO| 设置为显示在 `新` 阶段中。

   :guilabel:`负责人` 字段分配给提出建议的操作员，使修改 |BOM| 的员工可以向提出更改建议的人寻求进一步的澄清。

   .. image:: engineering_change_orders/view-bom-change.png
      :align: center
      :alt: 在 *新* 阶段的 "BOM 更改" 类型中找到新的 ECO。

在修订的 |BOM| 上，切换到 :guilabel:`操作` 选项卡，然后选择 :guilabel:`☰ (三横)` 图标。这样做会打开一个操作步骤列表，最新的说明标题为 `新步骤建议：`，后跟用户输入的标题。点击该行以查看建议的更改。

.. image:: engineering_change_orders/show-instructions.png
      :align: center
      :alt: 在 *BOM* 的操作选项卡中查看 "显示说明" 图标。

在 :ref:`质量控制点 <quality/quality_management/quality-control-points>` 表单中，确保以下表单字段准确填写，以为操作员提供详细的操作说明：

- :guilabel:`标题`：重命名以给出简明的说明描述。
- :guilabel:`控制方式`：使用下拉菜单，确定该说明是否适用于 :guilabel:`产品`，是否仅适用于该 :guilabel:`操作`，或适用于特定数量的产品。
- :guilabel:`类型`：对控制点类型进行分类。从下拉菜单中选择 :guilabel:`说明`，为工人提供说明。要接收工人的反馈，请选择 :guilabel:`拍照`、:guilabel:`登记已用材料`、:guilabel:`打印标签`，或其他 :ref:`质量检查选项 <quality/quality_management/quality-control-points>`。

.. seealso::
   :ref:`配置质量控制点 <quality/quality_management/quality-control-points>`

质量控制点配置完成后，使用面包屑导航返回 :guilabel:`步骤` 列表。最后，拖动最后一个质量控制行项至其预期的操作说明顺序。

.. example::
   拖动并重新排列 `检查损坏的开关` 说明，点击并拖动其左侧的 "6 点" 图标，将其从底部移动到第二位。

   .. image:: engineering_change_orders/reorder.png
      :align: center
      :alt: 通过选择左侧的 "6 点" 图标拖动并重新排列说明顺序。
