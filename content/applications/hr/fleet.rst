:show-content:

=====
车辆管理
=====

本文档概述了 *Fleet* 应用程序的配置和设置，包括 :ref:`settings <fleet/settings>` 和 :ref:`manufacturers <fleet/manufacturers>`。

.. _fleet/settings:

设置
====

要访问设置菜单，请转到 :menuselection:`Fleet app --> Configuration --> Settings`。 需要配置的设置只有两个：:guilabel:`End Date Contract Alert` 和 :guilabel:`New Vehicle Request`。

.. image:: fleet/fleet-settings.png
   :align: center
   :alt: Fleet 应用程序的设置界面。

合同结束日期提醒
-------------------

:guilabel:`End Date Contract Alert` 字段设置在车辆合同结束前多少天发送提醒。 负责人员将收到一封电子邮件，告知他们车辆合同将在所定义的天数内到期。

.. note::
   要确定合同的负责人，打开单个合同。 在合同的 :guilabel:`Contract Information` 部分中列为 :guilabel:`Responsible` 的人将收到提醒。

   要访问所有合同，请导航到 :menuselection:`Fleet app --> Fleet --> Contracts`，所有合同将显示在列表中。 点击 :guilabel:`Contract` 以查看它。

   还可以通过导航到 :menuselection:`Fleet app --> Fleet --> Fleet` 并点击单个车辆来找到单个合同。 在车辆表单上，点击页面顶部的 :guilabel:`Contracts` 智能按钮。 仅此车辆关联的合同将显示在列表中。 点击单个合同以打开它。 合同中列出了 :guilabel:`Responsible` 人员。

新车辆请求
------------

:guilabel:`New Vehicle Request` 字段根据车队可用性设置请求新车辆的限制。 填写薪资配置表（在获得职位后）的员工，如果现有车辆数量大于 :guilabel:`New Vehicle Request` 字段中指定的车辆数量，则将无法请求新车。 在此字段中输入现有可用车辆的具体数量限制。

.. example::
   如果 :guilabel:`New Vehicle Request` 限制设置为 20 辆车辆，而有 25 辆车辆可用，则员工将无法请求新车辆。 如果仅有 10 辆车可用，则员工可以请求新车辆。

.. _fleet/manufacturers:

制造商
====

Odoo *Fleet* 已预先配置了数据库中常用的六十六个汽车和自行车制造商及其徽标。 要查看预加载的制造商，请转到 :menuselection:`Fleet app --> Configuration --> Manufacturers`。

制造商以字母顺序显示。 每个制造商的卡片列出为特定制造商配置的具体车型数量。 Odoo 预配置了来自四大汽车制造商和一家主要自行车制造商的四十六个 :doc:`models <fleet/models>`：奥迪、宝马、梅赛德斯、欧宝（汽车）和埃迪·默克斯（自行车）。

.. image:: fleet/manufacturer.png
   :align: center
   :alt: 列出了车型数量的制造商卡片。

添加制造商
------------

要将新制造商添加到数据库，请点击 :guilabel:`Create`。 将加载一个制造商表单。 只需要两项信息：制造商的 :guilabel:`Name` 和徽标。 在名称字段中输入制造商的名称，并选择要上传的徽标图像。 输入信息后，点击 :guilabel:`Save`。

.. seealso::
   - :doc:`fleet/models`
   - :doc:`fleet/new_vehicle`
   - :doc:`fleet/service`
   - :doc:`fleet/accidents`

.. toctree::
   :titlesonly:

   fleet/models
   fleet/new_vehicle
   fleet/service
   fleet/accidents
