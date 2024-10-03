:show-content:
=====
午餐
=====

Odoo 的 *午餐* 应用程序为用户提供了一种方便的方式，可以直接从数据库中订餐并支付餐费。

在员工可以使用 *午餐* 应用程序之前，需要进行一些配置，包括设置、供应商、位置、产品、产品类别和提醒。完成这些配置后，员工可以查看菜单并订餐。

设置
====

在 *午餐* 应用程序中，只需要配置两个设置：透支设置和通知。要访问设置，请导航至 :menuselection:`Lunch app --> Configuration: Settings`。

配置以下内容：

- :guilabel:`午餐透支`: 输入员工的最大透支额度。货币格式由公司本地化设置决定。
- :guilabel:`接待通知`: 设置用户通过 *Discuss* 应用程序收到的食物送达通知信息。默认信息 `您的午餐已送达。请享用！` 会自动填充此字段，但可以根据需要进行修改。

.. tip::
   如果数据库中安装了多种语言，*午餐* 应用程序的许多表单提供了为不同字段输入翻译的选项。

   如果可以配置翻译，则表单中的可翻译字段旁边会显示语言代码。要为该字段添加翻译，请点击两位字母的语言代码（例如，:guilabel:`EN` 表示英语），会出现一个翻译弹出窗口。

   以下是 *设置* 菜单中 :guilabel:`接待通知` 字段的示例：

   导航至 :menuselection:`Lunch app --> Configuration: Settings`。点击 :guilabel:`EN`，位于 :guilabel:`接待通知` 部分文本框的右上角。加载一个 :guilabel:`Translate: company_lunch_notify_message` 弹出窗口，提供为数据库使用的其他语言输入翻译的选项。

   第一列按字母顺序列出了不同语言，当前选择的语言以粗体显示。第二列显示了每个语言列中当前配置的消息。最右侧的最后一列提供了文本框，可以为每种语言输入翻译。

   输入每种语言应该显示的文本，然后点击 :guilabel:`保存`。

  .. image:: lunch/translation.png
     :align: center
     :alt: 翻译文本框，当前语言突出显示，阿拉伯语翻译字段已突出显示。

位置
====

默认情况下，当 *午餐* 应用程序安装时，Odoo 会创建一个 `总部办公室` 位置。如果公司有多个位置，则需要进行配置。

要添加位置，请导航至 :menuselection:`Lunch app --> Configuration: Locations`。当前配置的位置以列表视图显示。点击左上角的 :guilabel:`新建` 按钮，列表中的最后一个位置下方会出现一个空白行。

在字段中输入位置名称。接着，点击名称右侧的 :guilabel:`地址` 字段，并输入该位置的地址。地址字段中可以输入多行信息。

重复此步骤，添加所有需要的位置。

.. image:: lunch/locations.png
   :align: center
   :alt: 位置的列表视图，新建按钮已突出显示。

提醒
====

可以设置提醒，这些提醒可以显示在 *午餐* 应用程序中，也可以通过 *Discuss* 应用程序发送给特定员工。

默认情况下没有预先配置的提醒。要设置提醒，请导航至 :menuselection:`Lunch app --> Configuration: Alerts`。点击左上角的 :guilabel:`新建` 按钮，会加载一个空白的午餐提醒表单。按表单输入以下信息：

- :guilabel:`提醒名称`: 输入提醒的名称，建议简短且具描述性，例如 `新供应商` 或 `请在11点前订购`。此字段是**必填项**。
- :guilabel:`显示`: 选择提醒是在 *午餐* 应用程序中可见 (:guilabel:`应用内提醒`) 还是通过 *Discuss* 应用程序在聊天窗口中发送给员工 (:guilabel:`聊天通知`)。

  - :guilabel:`接收人`: 仅在为 :guilabel:`显示` 选项选择了 :guilabel:`聊天通知` 时此字段才会出现。选择谁接收聊天提醒。可选项包括：:guilabel:`所有人`，:guilabel:`上周订购的员工`，:guilabel:`上个月订购的员工`，或 :guilabel:`去年订购的员工`。

- :guilabel:`位置`: 从下拉菜单中选择提醒应显示的位置。可以选择多个位置。此字段是**必填项**，因此，如果提醒适用于所有位置，请从下拉菜单中选择所有位置。
- :guilabel:`显示截止日期`: 如果提醒需要在特定日期过期，请从日历选择器中选择日期。
- :guilabel:`激活`: 默认情况下，此选项处于开启状态（显示为绿色）。要关闭提醒，请点击切换按钮，使其不再显示为绿色。
- :guilabel:`消息`: 在此字段中输入提醒消息。此字段是**必填项**。
- :guilabel:`通知时间`: 选择应发送提醒的星期几。默认情况下，所有七天都处于激活状态。点击复选框可以将设置从激活更改为不激活状态。

  如果为 :guilabel:`显示` 选项选择了 :guilabel:`聊天通知`，则还会出现一个 :guilabel:`时间` 字段。输入应发送聊天消息的时间。接下来，使用 :guilabel:`时间` 字段右侧的下拉菜单选择该时间是 :guilabel:`上午` 还是 :guilabel:`下午`。

.. image:: lunch/alert.png
   :align: center
   :alt: 填写完整的提醒表单，设置为聊天提醒，时间为上午10:30，提示员工在上午11:30前提交订单。

.. seealso::
   - :doc:`lunch/vendors`
   - :doc:`lunch/products`
   - :doc:`lunch/orders`
   - :doc:`lunch/user-accounts`
   - :doc:`lunch/management`

.. toctree::
   :titlesonly:

   lunch/vendors
   lunch/products
   lunch/orders
   lunch/user-accounts
   lunch/management
