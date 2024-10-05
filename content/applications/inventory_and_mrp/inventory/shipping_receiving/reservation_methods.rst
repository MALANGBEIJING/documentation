:show-content:
:hide-page-toc:

===================
预留方法
===================

销售和交付产品的公司需要确保他们始终有现货库存，以便在新的销售订单确认时，能够按时交付产品。

在 Odoo 中，这可以通过 *预留方法* 来处理。预留方法控制交付订单（DO）中包含的产品应如何为交付预留，确保它们在正确的时间为正确的订单预留。

在 Odoo 中有三种不同的预留方法：*确认时*、*手动* 和 *在计划日期前*。

.. tabs::

   .. tab:: 确认时

      仅在销售订单确认时预留产品，**且** 只有在库存已经可用的情况下进行预留。

   .. tab:: 手动

      一旦确认报价，必须手动检查产品的可用性，且必须手动预留所需的数量。

   .. tab:: 在计划日期前

      可以选择具体的天数；这是产品应在计划交付日期 **之前** 预留的最长期限。

配置
=============

预留方法设置在单个操作类型上。要配置预留方法，请前往 :menuselection:`库存应用 --> 配置 --> 操作类型`。然后，选择所需的操作类型。或者，点击 :guilabel:`新建` 创建一个新的操作类型。

在操作类型表单的 :guilabel:`常规` 标签中，找到 :guilabel:`预留方法` 选项，并选择应为此操作类型使用的方法。

.. image:: reservation_methods/reservation-methods-operations-type-field.png
   :align: center
   :alt: 交付订单操作类型表单中的预留方法字段。

.. tip::
   如果选择了 :guilabel:`在计划日期前` 预留方法，表单下方会出现一个新的 :guilabel:`在计划日期前预留` 字段。在此字段中，可以将 :guilabel:`提前天数` 和 :guilabel:`收藏时提前天数` 从默认值 `0` 修改。

   更改 :guilabel:`提前天数` 值会更改产品应在计划日期前预留的最长天数。

   更改 :guilabel:`收藏时提前天数` 值会更改产品的收藏转移应在计划日期前预留的最长天数。

   .. image:: reservation_methods/reservation-methods-before-scheduled-date.png
      :align: center
      :alt: 选择“在计划日期前”预留方法时的“计划日期前预留”字段。

必需应用
=====================

使用预留方法时，必须安装的两个应用是 *销售* 和 *库存* 应用。

.. note::
   除了交付订单，预留方法还可以用于 *生产订单*、*补给分包商* 订单、*维修* 订单和 *内部转移*，如果需要的话。要启用此功能，请配置以下附加设置：

   - **对于生产订单**：通过前往 :menuselection:`应用` 应用程序，找到 *生产* 应用并点击 :guilabel:`安装` 来安装 *生产* 应用。
   - **对于补给分包商**：导航到 :menuselection:`生产应用 --> 配置 --> 设置`，在 :guilabel:`操作` 部分下，启用 :guilabel:`分包`。然后，点击 :guilabel:`保存`。
   - **对于维修**：通过前往 :menuselection:`应用` 应用程序，找到 *维修* 应用并点击 :guilabel:`安装` 来安装 *维修* 应用。
   - **对于内部转移**：导航到 :menuselection:`库存应用 --> 配置 --> 设置`，在 :guilabel:`仓库` 部分下，启用 :guilabel:`存储库位`。然后，点击 :guilabel:`保存`。

安装这些应用后，无需从设置中启用其他功能即可使用预留方法。它们将在某些操作类型中默认可用，并可以通过导航到 :menuselection:`库存应用 --> 配置 --> 操作类型`，然后点击特定的操作类型进行查看和更改。

.. note::
   当在 :guilabel:`操作类型` 表单中将 :guilabel:`操作类型` 更改为 :guilabel:`收货` 时，预留方法将 **不可用**。

.. image:: reservation_methods/reservation-methods-operations-type-menu.png
   :align: center
   :alt: 库存应用配置子菜单中的操作类型。

.. seealso::
   - :doc:`reservation_methods/at_confirmation`
   - :doc:`reservation_methods/manually`
   - :doc:`reservation_methods/before_scheduled_date`

.. toctree::
   :titlesonly:

   reservation_methods/at_confirmation
   reservation_methods/manually
   reservation_methods/before_scheduled_date
