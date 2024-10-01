===========
本地部署
===========

注册数据库
===================

要注册您的数据库，请在应用程序仪表板中的横幅中输入您的订阅码。如果注册成功，横幅将变为绿色并显示数据库的到期日期。

.. tip::
   到期日期也会显示在设置页面的底部。

.. _on-premise/duplicate:

复制数据库
====================

通过访问服务器上的数据库管理器 (`<odoo-server>/web/database/manager`) 复制数据库。通常，您需要将生产数据库复制为中性化的测试数据库。可以通过在提示时勾选中性化选项来实现，这会为每个已安装的模块执行所有 :file:`neutralize.sql` 脚本。

常见错误消息及解决方案
===================================

注册错误
------------------

如果出现注册错误，应显示以下消息。

.. image:: on_premise/error-message-sub-code.png
   :alt: 数据库注册错误消息

解决问题的步骤：

- 通过验证订阅详情是否带有 :guilabel:`进行中` 标签，检查您的 **Odoo 企业版订阅的有效性**，或联系您的客户经理。

- 确保 **没有其他数据库与该订阅码关联**，因为每个订阅只能链接一个数据库。

  .. tip::
     如果需要测试或开发数据库，可以 :ref:`复制数据库 <on-premise/duplicate>`。

- 通过打开您的 `Odoo 合同 <https://accounts.odoo.com/my/subscription>`_，验证 **没有数据库共享相同的 UUID**（全局唯一标识符）。如果两个或多个数据库共享相同的 UUID，其名称将显示。

  .. image:: on_premise/unlink-db-name-collision.png
     :alt: 数据库 UUID 错误消息

  如果出现这种情况，请手动更改数据库的 UUID 或 `发送支持工单 <https://www.odoo.com/help>`_。

- 由于更新通知必须能够访问 Odoo 的订阅验证服务器，请确保您的 **网络和防火墙设置** 允许 Odoo 服务器向以下地址发出出站连接：

  - `services.odoo.com` 端口 `443`（或 `80`）
  - 对于旧的部署，`services.openerp.com` 端口 `443`（或 `80`）

  即使在注册数据库之后，这些端口也必须保持开放，因为更新通知每周运行一次。

用户过多错误
--------------------

如果本地数据库中的用户数量超过了您的 Odoo 企业版订阅中预留的用户数量，将显示以下消息。

.. image:: on_premise/add-more-users.png
   :alt: 数据库用户过多错误消息

当消息出现时，您有 30 天的时间来采取行动，数据库到期前每天都会更新倒计时。

解决问题的步骤：

- **添加更多用户** 到您的订阅中，点击消息中显示的 :guilabel:`升级您的订阅` 链接，验证增购报价并支付额外用户的费用。
- :ref:`停用用户 <users/deactivate>` 并**拒绝**增购报价。

.. important::
   如果您使用的是月度订阅计划，数据库将自动更新以反映新增用户。如果您使用的是年度或多年订阅计划，数据库中将出现到期横幅。您可以通过点击横幅生成增购报价以更新订阅，或者 `发送支持工单 <https://www.odoo.com/help>`_ 以解决问题。

一旦数据库的用户数量正确，过期消息将在几天后自动消失，当下次验证发生时。

数据库到期错误
----------------------

如果您的数据库在续订订阅之前到期，将显示以下消息。

.. image:: on_premise/database-expired.png
   :alt: 数据库到期错误消息

如果您未能在 30 天倒计时结束前采取行动，将会出现此消息。

解决问题的步骤：

- 点击消息中显示的 :guilabel:`续订您的订阅` 链接并完成过程。如果您通过银行转账支付，您的订阅将在款项到达时续订，这可能需要几天时间。信用卡支付会立即处理。
- `发送支持工单 <https://www.odoo.com/help>`_。

.. toctree::

   on_premise/packages
   on_premise/source
   on_premise/update
   on_premise/deploy
   on_premise/email_gateway
   on_premise/geo_ip
   on_premise/community_to_enterprise
