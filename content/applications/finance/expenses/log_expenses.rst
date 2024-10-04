============
记录费用
============

在费用报销之前，每笔单独的费用都需要记录在数据库中。费用记录可以通过三种不同方式创建：:ref:`手动输入费用记录 <expenses/manual_expense>`，:ref:`上传收据 <expenses/upload_receipt>`，或将收据 :ref:`发送电子邮件 <expenses/email_expense>` 到预配置的电子邮件地址。

.. _expenses/manual_expense:

手动输入费用
================

要记录新的费用，打开 :menuselection:`费用应用`，默认显示 :guilabel:`我的费用` 页面。

.. tip::
   此视图也可以通过 :menuselection:`费用应用 --> 我的费用 --> 我的费用` 访问。

然后，点击 :guilabel:`新建`，并填写表单上的以下字段：

- :guilabel:`描述`: 输入简短的费用描述。应简明扼要，例如 `与客户午餐` 或 `会议酒店`。
- :guilabel:`类别`: 从下拉菜单中选择最符合费用的类别。
- :guilabel:`总计`: 以两种方式之一输入费用的总金额：

  #. 如果费用是单项物品/费用，并且所选类别为单项费用，则在 :guilabel:`总计` 字段中输入金额（隐藏 :guilabel:`数量` 字段）。
  #. 如果费用是相同物品/费用的多个项，且价格固定，则显示 :guilabel:`单价` 字段。在 :guilabel:`数量` 字段中输入数量，总金额将自动更新为正确的总计。总金额显示在 :guilabel:`数量` 下方。

     .. example::
        如果费用为里程费用，:guilabel:`单价` 为每英里费用。设置 :guilabel:`数量` 为行驶的英里数，总金额将被计算出来。

- :guilabel:`含税`: 如果费用类别已配置税费，则在输入 :guilabel:`总计` 或 :guilabel:`数量` 后，税率和金额会自动显示。

  .. note::
     如果费用类别配置了税费，:guilabel:`含税` 值会在实时更新，因为 :guilabel:`总计` 或 :guilabel:`数量` 会更新。

- :guilabel:`员工`: 从下拉菜单中选择该费用所对应的员工。
- :guilabel:`支付方`: 点击单选按钮以指明谁支付了费用并应该被报销。选择 :guilabel:`员工（待报销）` 或 :guilabel:`公司`。根据所选费用类别，可能不会显示此字段。
- :guilabel:`费用日期`: 使用点击该字段时弹出的日历窗口，输入费用发生的日期。
- :guilabel:`账户`: 从下拉菜单中选择应记录该费用的费用账户。
- :guilabel:`客户可重计费`: 如果该费用应由客户支付，从下拉菜单中选择应为该费用开具发票的 :abbr:`SO (销售订单)` 和客户。下拉菜单中的所有销售订单都会同时列出 :abbr:`SO (销售订单)` 和所写公司的名称。保存费用后，客户名称会消失，只有 :abbr:`SO (销售订单)` 在费用中可见。

  .. example::
     一位客户希望在现场会议中设计并安装一个定制花园，并同意支付相关费用（如差旅、酒店、餐饮等）。与该会议相关的所有费用都会标明定制花园的销售订单（该订单也会引用客户）作为 :guilabel:`客户可重计费`。

- :guilabel:`分析分配`: 从下拉菜单中选择费用应计入的账户，选择 :guilabel:`项目`、:guilabel:`部门` 或两者。根据需要可以为每个类别列出多个账户。通过输入每个账户旁边的百分比值来调整每个分析账户的百分比。
- :guilabel:`公司`: 如果设置了多个公司，从下拉菜单中选择该费用应归档的公司。当前公司会自动填充此字段。
- :guilabel:`备注`: 如果需要澄清费用的备注，请在备注字段中输入。

.. image:: log_expenses/expense-filled-in.png
   :align: center
   :alt: 填写了客户午餐的费用表单。

附加收据
---------------

费用记录创建后，下一步是附加收据。点击 :guilabel:`附加收据` 按钮，会弹出一个文件浏览器。导航到要附加的收据，并点击 :guilabel:`打开`。

新的收据会记录在 *chatter* 中，收据数量会显示在 :icon:`fa-paperclip` :guilabel:`(回形针)` 图标旁。根据需要，可以为每条费用记录附加多张收据。

.. image:: log_expenses/receipt-icon.png
   :align: center
   :alt: 附加收据并显示在chatter中。

.. _expenses/upload_receipt:

上传费用
===============

可以通过上传PDF收据自动创建费用记录。此功能需要启用设置，并购买 :abbr:`IAP (应用内购买)` 积分。

数字化设置
-----------------------

要启用收据扫描，导航到 :menuselection:`费用应用 --> 配置 --> 设置`，勾选 :guilabel:`费用数字化 (OCR)` 选项旁的复选框。然后，点击 :guilabel:`保存`。启用后，会出现附加选项。点击相应的单选按钮，选择以下选项之一：

- :guilabel:`不进行数字化`: 关闭收据数字化。
- :guilabel:`仅按需数字化`: 仅在请求时数字化收据。费用记录上会出现一个 :guilabel:`数字化文档` 按钮。点击后，收据会被扫描，费用记录会更新。
- :guilabel:`自动数字化`: 上传收据时自动对所有收据进行数字化。

这些选项下方有两个附加链接。点击 :icon:`fa-arrow-right` :guilabel:`购买积分` 链接以购买收据数字化的积分。点击 :icon:`fa-arrow-right` :guilabel:`查看我的服务` 链接以查看所有当前服务及其剩余的积分余额。

有关文档数字化和 :abbr:`IAP (应用内购买)` 的更多信息，请参阅 :doc:`应用内购买 (IAP) <../../essentials/in_app_purchase>` 文档。

.. note::
   启用 :guilabel:`费用数字化 (OCR)` 选项时，会安装一个必要的模块，以便可以扫描收据。禁用此选项将卸载该模块。

   如果某个时候希望暂时停止数字化收据，可以选择 :guilabel:`不进行数字化` 选项。提供此选项的原因是，模块不会被卸载，可以通过选择其他两个选项之一在未来重新启用数字化。

上传收据
---------------

打开 :guilabel:`费用应用`，从 :guilabel:`我的费用` 仪表盘，点击 :guilabel:`上传`，弹出一个文件浏览器。导航到所需的收据，选择它，然后点击 :guilabel:`打开`。

.. image:: log_expenses/upload.png
   :align: center
   :alt: 通过扫描收据创建费用。点击仪表盘视图顶部的扫描按钮。

收据将被扫描，并创建一个新的费用记录。:guilabel:`费用日期` 字段自动填充为当天日期，以及根据扫描数据填充其他字段，如 :guilabel:`总计`。

点击新条目以打开单独的费用表单，并根据需要进行更改。扫描的收据显示在 *chatter* 中。

.. _expenses/email_expense:

通过电子邮件发送费用
=======================

除了在 **费用** 应用中单独创建每笔费用外，还可以通过发送电子邮件到指定的电子邮件别名来自动创建费用。

为此，必须首先配置电子邮件别名。导航到 :menuselection:`费用应用 --> 配置 --> 设置`。确保勾选 :guilabel:`接收电子邮件` 旁的复选框。默认的电子邮件别名是 *expense@(域名).com*。在 :guilabel:`别名` 字段右侧输入所需的电子邮件地址以更改电子邮件别名。然后点击 :guilabel:`保存`。

.. image:: log_expenses/alias-email.png
   :align: center
   :alt: 费用电子邮件别名的默认电子邮件地址。

.. note::
   如果需要设置域名别名，:icon:`fa-arrow-right` :guilabel:`设置域名别名` 链接会显示在 :guilabel:`接收电子邮件` 复选框下方，而不是电子邮件地址字段。

   .. image:: log_expenses/email-alias.png
      :align: center
      :alt: 通过点击链接创建域名别名。

   有关设置和更多信息，请参阅 :doc:`/applications/websites/website/configuration/domain_names` 文档。

   设置域名别名后，电子邮件地址字段在 **费用** 应用的 :guilabel:`设置` 页面上的 :guilabel:`接收电子邮件` 功能下可见。

输入电子邮件地址后，发送到该别名的电子邮件将创建新的费用记录，无需进入Odoo数据库。

通过电子邮件提交费用时，创建一封新电子邮件，并在电子邮件的主题中输入产品的 *内部参考* 代码（如有）和费用金额。接下来，将收据附加到电子邮件中。Odoo 会根据电子邮件主题中的信息结合收据创建费用。

要检查费用类别的内部参考，请导航到 :menuselection:`费用应用 --> 配置 --> 费用类别`。如果费用类别有内部参考，列表中的 :guilabel:`内部参考` 列将列出这些参考。

.. image:: log_expenses/ref.png
   :align: center
   :alt: 内部参考编号列在主要费用类别视图中。

要在费用类别上添加内部参考，点击该类别以打开费用类别表单。在相应字段中输入 :guilabel:`内部参考`。在 :guilabel:`内部参考` 字段下方会出现以下句子：:guilabel:`使用此参考作为提交电子邮件时的主题前缀`。

.. image:: log_expenses/mileage-internal-reference.png
   :align: center
   :alt: 里程费用的内部参考编号列在费用产品视图中。

.. example::
   如果通过电子邮件提交工作旅行中的一顿$25.00的餐费，电子邮件主题将是 `FOOD $25.00`。

   解释：

   - 费用类别 `餐费` 的 :guilabel:`内部参考` 是 `FOOD`
   - 费用的 :guilabel:`成本` 是 `$25.00`

.. note::
   出于安全原因，只有经过身份验证的员工电子邮件才被Odoo接受用于通过电子邮件创建费用。要确认员工电子邮件的身份验证状态，请转到 :menuselection:`员工应用` 中的员工卡片，并参考 :guilabel:`工作电子邮件` 字段。

   .. image:: log_expenses/authenticated-email-address.png
      :align: center
      :alt: 通过点击链接创建域名别名。
