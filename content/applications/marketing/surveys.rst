=======
调查
=======

企业通过调查从客户和员工那里收集有价值的见解，帮助做出明智的决策。

借助 Odoo 的 *Surveys* 应用程序，用户可以创建各种调查、问卷、认证、评估等。这些可以用来收集反馈，评估最近活动的成功与否，并衡量客户和/或员工的满意度。这个过程为了解市场趋势的变化提供了宝贵的见解。

.. seealso::
   `Odoo 教程: 调查 <https://www.odoo.com/slides/surveys-62>`_

仪表板
=========

打开 *Surveys* 应用程序后，Odoo 会显示 *Surveys* 应用程序的主仪表板，也称为 :guilabel:`Surveys` 页面。

.. image:: surveys/surveys-dashboard.png
   :align: center
   :alt: Odoo Surveys 应用程序默认看板视图下的仪表板。

.. tip::
   可以通过点击标题菜单中的 :menuselection:`Surveys`，随时访问 *Surveys* 仪表板。

在左上角，有一个 :guilabel:`新建` 按钮。点击后，Odoo 会显示一个空白的调查表单，用于创建调查。

在仪表板上，数据库中创建的所有调查都显示在默认的看板视图中。

从左到右，继调查名称、负责人和创建月份之后，每行 *Surveys* 仪表板上显示以下信息：

- 该特定调查的 :guilabel:`问题` 数量
- 调查的 :guilabel:`平均时长`（通常参与者完成的时间）
- 调查的 :guilabel:`注册` 参与者数量
- 该特定调查的 :guilabel:`完成` 次数
- 展示多少人 :guilabel:`通过` 或获得 :guilabel:`认证` 的百分比和数据条

  .. note::
     当为该特定调查配置了 *必需分数* 时，才会出现 :guilabel:`通过` 百分比和条形图。

     当该特定调查启用了 *是认证* 选项时，才会显示 :guilabel:`认证` 百分比和条形图。

     如果没有显示 :guilabel:`通过` 或 :guilabel:`认证`，则表明该调查没有 *必需分数*，也没有启用 *是认证* 选项。

- 与该调查相关的 :guilabel:`课程` 数量，这仅在为单个调查创建并附加了多个课程时才会出现。

.. note::
   在调查名称后方的半奖杯背景图片表明该调查为 *认证*。

在调查行的最右侧，*Surveys* 应用程序仪表板上有一组按钮。

这些按钮如下：

- :guilabel:`分享`：点击以显示一个 :guilabel:`分享调查` 弹出表单，用于邀请潜在参与者参与调查——包含一个 :guilabel:`调查链接`，可复制并发送给潜在参与者，还带有一个 :guilabel:`通过电子邮件发送` 开关。

  当 :guilabel:`通过电子邮件发送` 开关处于活动状态（绿色开关）时，会出现额外的字段，其中可以添加 :guilabel:`收件人`、:guilabel:`附加电子邮件` 和 :guilabel:`主题`。

  下面会出现一个动态电子邮件模板，其中包含一个 :guilabel:`开始认证` 按钮，如果需要，也可以进行修改。

  也可以将 :guilabel:`附件` 添加到电子邮件中，还可以设置 :guilabel:`答案截止日期`。

  完成修改后，点击 :guilabel:`发送` 将该电子邮件邀请发送给 :guilabel:`收件人` 字段中列出的所有电子邮件地址/联系人。

  .. image:: surveys/share-survey-popup-email-toggle.png
     :align: center
     :alt: Odoo Surveys 中的“分享调查”弹出窗口，开启了通过电子邮件发送选项。

  .. tip::
     默认的调查邀请 :guilabel:`邮件模板` 可以通过导航到 :menuselection:`设置 --> 技术 --> 电子邮件模板` 并搜索 `Survey: Invite` 进行编辑。

  .. note::
     当调查行没有任何问题时，不会出现 :guilabel:`通过电子邮件发送` 开关。

     只有当调查的 *访问模式* 设置为 *有链接的任何人* 时，才会出现 :guilabel:`调查链接`。

     只有当调查的 *需要登录* 字段未激活时，才会出现 :guilabel:`附加电子邮件` 字段。

- :guilabel:`测试`：点击在新标签页中以参与者的视角测试调查，以检查是否存在错误或不一致之处。
- :guilabel:`查看结果`：点击显示一个新标签页，展示所有调查参与者、问题和回答的详细指标和图表表示，以便进行深入分析。
- :guilabel:`开始现场会话`：点击启动 *现场会话* 调查，并在新标签页中显示会话管理器窗口。此按钮不会出现在启用了 *是认证* 选项的调查上。
- :guilabel:`结束现场会话`：点击结束已正式开始的 *现场会话* 调查。此按钮仅出现在先前启动了现场会话的调查行上。

在调查行最右侧的按钮上方，当光标悬停在特定行时，会出现一个 :guilabel:`⋮（三个点）` 图标。点击该 :guilabel:`⋮（三个点）` 图标时，会出现一个配置相关选项的下拉菜单：

这些选项是：

- :guilabel:`编辑调查`：点击后，Odoo 会显示该特定调查的调查表单，用户可以以多种不同方式对其进行修改。
- :guilabel:`分享`：点击后，Odoo 会显示 :guilabel:`分享调查` 弹出表单，用户可以用它来邀请潜在的调查参与者。
- :guilabel:`删除`：点击后，Odoo 会显示一个弹出窗口，用户必须确认他们希望完全删除调查，可以通过点击弹出窗口底部的 :guilabel:`删除` 按钮来执行此操作。
- :guilabel:`颜色`：用户可以选择为仪表板上的调查行添加颜色，以便于组织（如果需要）。

.. image:: surveys/three-dot-dropdown.png
   :align: center
   :alt: Odoo Surveys 仪表板上显示的三个点下拉菜单选项。

在调查行最右侧的按钮下方，有一个 *活动* 按钮，表示为 :guilabel:`🕘（时钟）` 图标。点击后，会弹出一个迷你弹出窗口，可以在其中调度和自定义与特定调查相关的活动。

.. image:: surveys/schedule-activities-dropdown.png
   :align: center
   :alt: Odoo Surveys 仪表板上显示的调度活动下拉菜单。

列表视图
---------

*Surveys* 仪表板默认显示为看板视图，但在右上角还有一个列表视图选项，表示为 :guilabel:`≣（条形）` 图标。

点击 :guilabel:`≣（条形）` 图标后，调查相关数据将显示为列表视图。

.. image:: surveys/list-view.png
   :align: center
   :alt: Odoo Surveys 应用程序仪表板上的列表视图选项。

在列表视图下，*Surveys* 应用程序仪表板上显示的列如下：

- :guilabel:`调查标题`
- :guilabel:`负责人`
- :guilabel:`平均时长`
- :guilabel:`注册`
- :guilabel:`成功率 (%)`
- :guilabel:`平均分数 (%)`

.. tip::
   在列表视图下，可以通过点击位于列标题最右侧的 *附加选项* 下拉菜单（表示为 :guilabel:`（带两个点的滑块）` 图标）向 *Surveys* 应用程序仪表板添加其他列。

活动视图
---------------

要让 *Surveys* 应用程序仪表板仅显示数据库中与调查相关的活动，请点击位于其他视图选项最右侧的 :guilabel:`🕘（时钟）` 图标。

.. image:: surveys/activities-view.png
   :align: center
   :alt: Odoo Surveys 仪表板上的活动视图选项。

点击后，会显示一个包含行和列的表格。行显示数据库中的不同调查，列显示各种活动类型。

.. note::
   在此视图中无法创建新调查，因为它仅用于创建和查看已调度的活动。

.. toctree::
   :titlesonly:

   surveys/create
   surveys/scoring
   surveys/questions
   surveys/live_session
   surveys/analysis
