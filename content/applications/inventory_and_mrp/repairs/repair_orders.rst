=====================
处理维修订单
=====================

.. |SO| replace:: :abbr:`SO (销售订单)`
.. |DO| replace:: :abbr:`DO (交货单)`
.. |RO| replace:: :abbr:`RO (维修订单)`
.. |UoM| replace:: :abbr:`UoM (计量单位)`

有时，交付给客户的产品可能会损坏或在运输过程中受损，需要退回进行退款、更换或维修。

在 Odoo 中，可以通过 *维修* 应用来跟踪客户退回的产品的维修进度。维修完成后，产品可以重新交付给客户。

损坏产品的退货和维修流程通常包括以下步骤：

#. :ref:`处理损坏产品的退货订单 <repairs/repair_orders/return-order>`
#. :ref:`为退回的产品创建维修订单 <repairs/repair_orders/repair>`
#. :ref:`将维修后的产品退还给客户 <repairs/repair_orders/return-customer>`

.. _repairs/repair_orders/return-order:

退货订单
=========

退货可以通过 *逆向转移* 在 Odoo 中处理，逆向转移可以直接从客户的销售订单（SO）创建。

要创建退货，请导航到 :menuselection:`销售应用`，并点击需要退货的 |SO|。然后，在 |SO| 表单中，点击 :guilabel:`交货` 智能按钮。这将打开交货单 (DO) 表单。

在该表单中，点击 :guilabel:`退货`。这将打开一个 :guilabel:`逆向转移` 弹出窗口。

.. image:: repair_orders/repair-orders-reverse-transfer.png
   :align: center
   :alt: 交货单表单上的逆向转移弹出窗口。

此弹出窗口列出了订单中的 :guilabel:`产品`、交付给客户的 :guilabel:`数量` 和产品的 :guilabel:`计量单位`。

如有必要，点击 :guilabel:`数量` 字段中的数值来更改要退回的产品数量。

如有需要，点击产品行最右侧的 :guilabel:`🗑️ (垃圾桶)` 图标将其从退货中删除。

准备就绪后，点击 :guilabel:`退货` 来确认退货。这样会为退回的产品创建新的入库单。

产品退回到仓库后，可以通过点击逆向转移表单中的 :guilabel:`验证` 按钮在数据库中登记退货。

.. tip::
   退货的逆向转移验证后，原始 |SO| 上的 :guilabel:`已交付` 列中的数值将更新，反映原始订单的 :guilabel:`数量` 与客户退回的 :guilabel:`数量` 之间的差异。

   .. image:: repair_orders/repair-orders-quantity-delivered.png
      :align: center
      :alt: 退货后销售订单上的已交付和数量列。

.. _repairs/repair_orders/repair:

创建维修订单
===========

产品退回后，可以通过创建维修订单 (RO) 来跟踪其维修情况。

要创建新的 |RO|，导航到 :menuselection:`维修应用`，然后点击 :guilabel:`新建`。这将打开一个空白的 |RO| 表单。

.. image:: repair_orders/repair-orders-left-hand-form.png
   :align: center
   :alt: 空白维修订单表单的左侧。

在此表单中，首先选择 :guilabel:`客户`。所选客户应是将对订单进行开票和交付的客户。

在 :guilabel:`维修产品` 字段中，点击下拉菜单选择需要维修的产品。如有必要，点击 :guilabel:`查看更多...` 来打开 :guilabel:`搜索：维修产品` 弹出窗口，浏览数据库中的所有产品。

选择好 :guilabel:`维修产品` 后，一个新的 :guilabel:`产品数量` 字段会出现在其下方。在该字段中，输入需要维修的产品数量（格式为 `0.00`）。

在该数值右侧，点击下拉列表选择产品的计量单位 (UoM)。

在 :guilabel:`退货单` 字段中，点击下拉菜单选择退货的订单，该订单中的产品需要维修。

如果维修的产品在保修期内，勾选 :guilabel:`在保修期内` 复选框。如果勾选了此复选框，客户将不会为维修订单中使用的所有零件收费。

在 :guilabel:`计划日期` 字段中，点击日期以显示日历弹出窗口。然后从日历中选择维修日期并点击 :guilabel:`应用`。

.. image:: repair_orders/repair-orders-completed-repair-form.png
   :align: center
   :alt: 空白维修订单表单的右侧。

在 :guilabel:`负责人` 字段中，点击下拉菜单选择应负责此次维修的用户。

如果处于多公司环境中，可以在 :guilabel:`公司` 字段中选择该 |RO| 所属的公司。

在 :guilabel:`标签` 字段中，点击下拉菜单选择应应用于此 |RO| 的标签。

零件标签
---------

在 :guilabel:`零件` 标签中添加、移除或回收零件。为此，点击表单底部的 :guilabel:`添加行`。

在 :guilabel:`类型` 列中，点击框以显示三个选项：:guilabel:`添加`（默认选择）、:guilabel:`移除` 和 :guilabel:`回收`。

.. image:: repair_orders/repair-orders-type-column.png
   :align: center
   :alt: 零件标签下新零件的类型列选项。

选择 :guilabel:`添加` 将该零件添加到 |RO|。添加零件列出了维修中使用的组件。如果组件已使用，完成维修的用户可以记录其使用情况。如果未使用，用户可以指出这一点，组件将保存以供其他用途。

选择 :guilabel:`移除` 将该零件从 |RO| 中移除。移除零件列出了在维修过程中从被维修产品中移除的组件。完成维修的用户可以记录其移除情况。

选择 :guilabel:`回收` 将该零件回收到 |RO|，并将其指定为稍后使用或重新用于仓库的其他用途。

在 :guilabel:`产品` 列中，选择应添加、移除或回收的产品（零件）。在 :guilabel:`需求` 列中，根据需要更改数量，指示在维修过程中应使用该零件的数量。

在 :guilabel:`完成` 列中，完成添加、移除或回收零件后，更改数值（格式为 `0.00`）。

在 :guilabel:`计量单位` 列中，选择该零件的 |UoM|。

最后，在 :guilabel:`已用` 列中，勾选复选框，确认该零件已在维修过程中使用。

要为该行添加其他列，请点击标题行最右侧的 :guilabel:`（可选列下拉）` 图标。选择要添加到该行的所需选项。

.. image:: repair_orders/repair-orders-additional-options.png
   :align: center
   :alt: 新零件行的可选附加选项。

维修备注和杂项标签
--------------------

点击 :guilabel:`维修备注` 标签以添加有关此特定 |RO| 的内部备注，以及执行维修的用户可能需要了解的任何内容。

点击空白文本字段开始编写备注。

点击 :guilabel:`杂项` 标签以查看此次维修的 :guilabel:`操作类型`。默认情况下，设置为 :guilabel:`您的公司：维修`，表明这是维修类型操作。

完成所有所需配置后，点击 :guilabel:`确认维修`。这样会将 |RO| 移动到 :guilabel:`已确认` 阶段，并预留维修所需的组件。

在 :guilabel:`零件` 标签下的产品行上会出现一个新的 :guilabel:`预测` 列，显示维修所需组件的可用性。

准备就绪后，点击 :guilabel:`开始维修`。这会将 |RO| 移动到 :guilabel:`维修中` 阶段（右上角）。如果需要取消 |RO|，点击 :guilabel:`取消维修`。

所有产品成功维修后，|RO| 完成。要在数据库中注册此信息，请点击 :guilabel:`结束维修`。

.. note::
   如果未使用所有添加到 |RO| 的零件，点击 :guilabel:`结束维修` 会弹出一个 :guilabel:`未完成的移动` 弹出窗口。

   .. image:: repair_orders/repair-orders-uncomplete-moves.png
      :align: center
      :alt: 未使用零件的未完成移动弹出窗口。

   弹出窗口会通知用户初始需求和实际使用数量之间存在差异。

   如果需要更改 :guilabel:`已用` 数量，请点击 :guilabel:`放弃` 或关闭弹出窗口。如果订单应确认，请点击 :guilabel:`验证`。

这会将 |RO| 移动到 :guilabel:`已维修` 阶段。表单上方还会出现一个 :guilabel:`产品移动` 智能按钮。

点击 :guilabel:`产品移动` 智能按钮可以查看产品在维修过程中的移动历史。

.. image:: repair_orders/repair-orders-product-moves.png
   :align: center
   :alt: 包含在维修订单中的产品移动历史。

.. _repairs/repair_orders/return-customer:

将产品退还给客户
------------------

产品在保修期内
~~~~~~~~~~~~~~~

产品成功维修后，可以将其退还给客户。

产品不在保修期内
~~~~~~~~~~~~~~~~~

如果产品不在保修期内，或应由客户承担维修费用，请点击 :guilabel:`创建报价`。这将打开一个新的 |SO| 表单，预先填充了 |RO| 中使用的零件，并计算出了维修的总成本。

.. image:: repair_orders/repair-orders-new-quotation.png
   :align: center
   :alt: 为维修订单中包含的零件预先填充的新报价。

如果此 |SO| 应发送给客户，点击 :guilabel:`确认`，并继续为维修开具发票。

.. tip::
   如果需要向客户收取维修服务费用，可以创建一个服务类型的产品，并将其添加到已维修产品的 |SO| 中。

要将产品退还给客户，请导航到 :menuselection:`销售应用`，然后选择最初处理退货的 |SO|。接着，点击 :guilabel:`交货` 智能按钮。

在生成的操作列表中，点击由 :guilabel:`来源文件` 指示的逆向转移，来源文件应显示为 `退回 WH/OUT/XXXXX`。

这将打开退货表单。在此表单顶部，现在会显示一个 :guilabel:`维修订单` 智能按钮，将此退货与已完成的 |RO| 关联起来。

点击表单顶部的 :guilabel:`退货`。这将打开一个 :guilabel:`逆向转移` 弹出窗口。

.. image:: repair_orders/repair-orders-reverse-transfer.png
   :align: center
   :alt: 交货单表单上的逆向转移弹出窗口。

此弹出窗口列出了订单中的 :guilabel:`产品`、交付给客户的 :guilabel:`数量` 和产品的 :guilabel:`计量单位`。

如有必要，点击 :guilabel:`数量` 字段中的数值来更改要退回的产品数量。

如有需要，点击产品行最右侧的 :guilabel:`🗑️ (垃圾桶)` 图标将其从退货中删除。

准备就绪后，点击 :guilabel:`退货` 来确认退货。这样会为退回的产品创建新的交货单。

交货处理完毕，产品退还给客户后，点击 :guilabel:`验证` 来验证交货。

.. seealso::
   :doc:`../../sales/sales/products_prices/returns`
