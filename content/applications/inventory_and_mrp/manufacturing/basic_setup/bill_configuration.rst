=================
物料清单
=================

.. |BOM| replace:: :abbr:`BoM (物料清单)`
.. |BOMs| replace:: :abbr:`BoMs (物料清单)`
.. |MO| replace:: :abbr:`MO (制造订单)`

物料清单（简称 *BoM*）记录了生产或维修产品所需的具体组件及其相应的数量。在Odoo中，|BOMs| 是制造商品和工具包的蓝图，通常还包括生产操作和逐步的指导说明。

BoM 设置
=========

要创建一个 |BOM|，请进入 :menuselection:`制造应用 --> 产品 --> 物料清单`，然后点击 :guilabel:`新建`。

接下来，将 :guilabel:`BoM 类型` 设置为 :guilabel:`制造此产品`。

然后，指定所需的 :ref:`组件 <manufacturing/basic_setup/setup-components>`，如果需要，还可以定义 :ref:`制造操作 <manufacturing/basic_setup/setup-operations>`。

.. 提示::
   通过点击任何产品表单上的 :guilabel:`物料清单` 智能按钮，也可以快速访问或创建单独的 |BOMs|，这些表单可通过*销售*、*库存*和*制造*应用访问，也可通过引用产品的任何内部链接（如字段或项目行）访问。

.. figure:: bill_configuration/bom-example.png
   :align: center
   :alt: 显示产品的物料清单，列出组件。

   `抽屉` 的 BoM，显示 **组件** 标签。

.. 参见::
   - :doc:`../advanced_configuration/kit_shipping`
   - :doc:`../subcontracting/subcontracting_basic`

.. _manufacturing/basic_setup/setup-components:

组件
----------

在 |BOM| 的 :guilabel:`组件` 标签中，通过点击 :guilabel:`添加一行` 来指定用于生产产品的组件。从 :guilabel:`组件` 下拉菜单中选择现有产品，或通过输入名称并选择 :guilabel:`创建 " "` 选项快速添加项目，或选择 :guilabel:`创建并编辑...` 选项添加组件并继续配置表单。

.. image:: bill_configuration/component.png
   :align: center
   :alt: 通过从下拉菜单中选择添加组件。

可选地，通过点击 :icon:`oi-settings-adjust` :guilabel:`(设置调整)` 图标访问额外的字段，启用以下功能的复选框以显示这些列：

- :guilabel:`应用于变体`：指定每个组件适用于哪个 :doc:`产品变体 <../advanced_configuration/product_variants>`。如果此字段留空，则组件用于所有产品变体。

.. _manufacturing/basic_setup/consumed-in-operation:

- :guilabel:`在操作中消耗`：指定使用组件的操作。用于确定 :ref:`制造准备 <manufacturing/basic_setup/manufacturing-readiness>`。
- :guilabel:`手动消耗`：选中复选框以强制操作员在制造订单 (MO) 上勾选 :guilabel:`已消耗` 复选框。

  .. image:: bill_configuration/consumed-field.png
     :align: center
     :alt: 显示制造订单，突出显示*已消耗*字段。

  不这样做将触发 :guilabel:`消耗警告` 错误消息，必须手动输入消耗的组件数量。否则，操作无法完成。

  .. image:: bill_configuration/consumption-warning.png
     :align: center
     :alt: 显示消耗警告错误消息。

.. _manufacturing/basic_setup/setup-operations:

操作
----------

在 |BOM| 中添加 *操作* 以指定生产说明并登记操作所花费的时间。要使用此功能，首先通过进入 :menuselection:`制造应用 --> 配置 --> 设置` 启用*工单*功能。在 :guilabel:`操作` 部分，勾选 :guilabel:`工单` 复选框以启用此功能。

.. 参见::
   :doc:`../advanced_configuration/work_order_dependencies`

.. image:: bill_configuration/enable-work-orders.png
   :align: center
   :alt: 设置页面中的"工单"功能。

接下来，进入 |BOM|，导航到 :menuselection:`制造应用 --> 产品 --> 物料清单` 并选择所需的 |BOM|。要添加新操作，转到 :guilabel:`操作` 标签，然后点击 :guilabel:`添加一行`。

这将打开 :guilabel:`创建操作` 弹出窗口，在该窗口中可以配置操作的各个字段：

- :guilabel:`操作`：操作的名称。
- :guilabel:`工作中心`：选择执行操作的现有位置，或输入名称并选择 :guilabel:`创建 " "` 选项创建新的工作中心。
- :guilabel:`应用于变体`：指定此操作是否仅适用于某些产品变体。如果此操作适用于所有产品变体，则将此字段留空。

  .. 参见::
     :doc:`配置BoM产品变体 <../advanced_configuration/product_variants>`

- :guilabel:`持续时间计算`：选择如何跟踪操作花费的时间。选择 :guilabel:`基于跟踪的时间计算` 使用操作的时间跟踪器，或选择 :guilabel:`手动设置持续时间`，如果操作员可以自行记录和修改时间。

  选择 :guilabel:`基于跟踪的时间计算` 选项将启用 :guilabel:`基于最近__工单` 选项，该选项会根据最近几次操作自动估算完成此操作的时间。选择 :guilabel:`手动设置持续时间` 则启用 :guilabel:`默认持续时间` 字段。
- :guilabel:`默认持续时间`：完成操作的估计时间；用于 `计划制造订单 <https://www.youtube.com/watch?v=TK55jIq00pc>`_ 和确定 `工作中心可用性 <https://www.youtube.com/watch?v=3YwFlD97Bio>`_。
- :guilabel:`公司`：指定该 |BOM| 可用的公司。

在 :guilabel:`工作表` 标签中包含操作详细信息。选择 :guilabel:`PDF` 以附加文件或 :guilabel:`Google Slide`，并将其设置为*公开*访问以共享链接。选择 :guilabel:`文本` 在 :guilabel:`描述` 文本框中键入说明。

.. 提示::
   输入 `/` 以获取格式选项和功能列表，包括ChatGPT。

   .. image:: bill_configuration/description.png
      :align: center
      :alt: 显示ChatGPT功能生成工单说明。

.. image:: bill_configuration/create-operations.png
   :align: center
   :alt: 填写创建操作弹出窗口。

最后，点击 :guilabel:`保存并关闭` 以关闭弹出窗口。要添加更多操作，请点击 :guilabel:`保存并新建`，然后重复上述步骤配置另一个操作。

.. 注意::
   每个操作都是唯一的，因为它始终只与一个 |BOM| 关联。

.. 提示::
   创建操作后，点击 :guilabel:`复制现有操作` 按钮选择要复制的操作。

   .. image:: bill_configuration/copy-existing-operations.png
      :align: center
      :alt: 显示操作标签，突出显示"复制现有操作"字段。
指示
~~~~~

.. 重要::
   要为操作添加详细的指示，必须安装 *质量* 应用。

通过点击操作的 :icon:`fa-list-ul` :guilabel:`(列表)` 图标，在 :guilabel:`指示` 列中为现有操作添加具体指示。指示列中的数字显示了该操作已存在的详细指示数量。

.. image:: bill_configuration/add-instructions.png
   :align: center
   :alt: 显示指示列和列表图标。

在 :guilabel:`步骤` 仪表板上，点击 :guilabel:`新建` 打开一个空白的质量控制点表单，用于创建新的生产步骤。在这里，为该特定指示命名一个 :guilabel:`标题`，并将 :guilabel:`类型` 设置为 :guilabel:`指示`。在表单的 :guilabel:`指示` 选项卡中，填写该操作步骤的具体指示。

.. 注意::
   可以在此表单中进行进一步的自定义，除了普通的指示，还可以包括特定类型的质量控制点，这些控制点带有具体或复杂的条件。有关质量控制点的更多详细信息，请参阅 :doc:`指示检查
   <../../quality/quality_check_types/instructions_check>` 文档。

.. image:: bill_configuration/steps.png
   :align: center
   :alt: 显示添加质量检查的页面。

杂项
-----

:guilabel:`杂项` 选项卡包含更多 |BOM| 配置，用于自定义采购、计算成本并定义如何消耗组件。

.. _manufacturing/basic_setup/manufacturing-readiness:

- :guilabel:`制造准备`：选择 :guilabel:`当第一步操作的组件可用时`，当仅有第一步操作所需的组件有库存时，显示组件状态为 **绿色** :guilabel:`不可用`。这表明尽管不是所有组件都可用，但操作员至少可以开始第一步操作。选择 :guilabel:`当所有组件可用时`，除非所有组件可用，否则显示 **红色** :guilabel:`不可用` 状态。

  .. 提示::
     在 |BOM| 中，指定哪个操作消耗每个组件，详见 :ref:`手动消耗字段 <manufacturing/basic_setup/consumed-in-operation>`。

  .. image:: bill_configuration/component-status.png
     :align: center
     :alt: 显示制造订单仪表板上的 *组件状态* 字段。

- :guilabel:`版本`：显示当前 |BOM| 版本，该功能在安装了 Odoo *PLM* 应用后可用于管理 |BOM| 变更。
- :guilabel:`灵活消耗`：指定使用的组件是否可以偏离 |BOM| 上定义的数量。选择 :guilabel:`阻止` 如果操作员 **必须** 严格遵守 |BOM| 的数量。否则，选择 :guilabel:`允许` 或 :guilabel:`允许并警告`。
- :guilabel:`路线`：选择用于在多个仓库中生产的产品的首选仓库的制造操作类型。如果留空，将默认使用该仓库的 `制造` 操作类型。
- :guilabel:`分析分配`：从列表中选择预创建的 :doc:`分析分配模型 <../../../finance/accounting/reporting/analytic_accounting>`，以自动记录产品制造成本到所选账簿。
- :guilabel:`制造交期`：定义从确认之日起完成 |MO| 所需的天数。
- :guilabel:`准备制造订单所需的天数`：补充组件或制造子组件所需的天数。

.. 参见::
   - :doc:`分析分配 <../../../finance/accounting/reporting/analytic_accounting>`
   - :doc:`交期 <../../inventory/warehouses_storage/replenishment/lead_times>`

.. image:: bill_configuration/misc-tab.png
   :align: center
   :alt: 显示BoM的 *杂项* 选项卡。

为 BoMs 添加副产品
=================

*副产品* 是在生产过程中与主产品一起生成的剩余产品。与主产品不同，|BOM| 中可以包含多个副产品。

要在 |BOM| 中添加副产品，首先在 :menuselection:`制造应用 --> 配置 --> 设置` 中启用 *副产品* 功能。在 :guilabel:`操作` 部分，勾选 :guilabel:`副产品` 复选框以启用此功能。

.. image:: bill_configuration/by-products.png
   :align: center
   :alt: 设置页面中的"副产品"功能。

启用此功能后，通过点击 :guilabel:`副产品` 选项卡，在 |BOM| 中添加副产品。点击 :guilabel:`添加一行`，填写 :guilabel:`副产品`、:guilabel:`数量` 和 :guilabel:`单位`。可选地，为副产品指定一个 :guilabel:`生产操作`。

.. 例子::
   在生产 `红酒` 时，在 `磨碎葡萄` 操作中会生成副产品 `葡萄泥`。

   .. image:: bill_configuration/add-by-product.png
      :align: center
      :alt: 显示BoM中的副产品示例。
