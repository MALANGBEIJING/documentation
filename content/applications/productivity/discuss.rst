=======
讨论
=======

Odoo *讨论* 应用是一个内部沟通工具，允许用户通过消息、笔记和文件共享进行连接，可以通过跨应用程序的持久聊天窗口，或通过专用的 *讨论* 仪表板进行沟通。

.. _discuss_app/notification_preferences:

选择通知偏好
===============================

通过导航到 :menuselection:`设置应用程序 --> 用户 --> 用户 --> 偏好选项卡`，访问 *讨论* 应用的用户特定偏好。

.. image:: discuss/preferences-user.png
   :alt: Odoo Discuss 偏好选项卡视图。

默认情况下，:guilabel:`通知` 字段设置为 :guilabel:`通过电子邮件处理`。启用此设置后，每当从聊天框发送消息，使用 `@` 提及发送笔记（从聊天框），或发送用户关注的记录的通知时，Odoo 都会发送通知电子邮件。触发通知的操作包括更改阶段（例如，如果任务设置为 :guilabel:`完成`，并配置发送电子邮件）。

通过选择 :guilabel:`在 Odoo 中处理`，上述通知将显示在 *讨论* 应用的 *收件箱* 中。消息可以执行以下操作：点击 :guilabel:`添加反应` 以响应表情符号，或点击 :guilabel:`回复` 以回复消息。其他操作可能包括通过点击 :guilabel:`标记为待办` 加星标消息，或选择 :guilabel:`固定` 以固定消息，甚至选择 :guilabel:`标记为未读`。

.. image:: discuss/reactions-discuss.png
   :alt: Odoo Discuss 收件箱消息及其操作选项视图。

点击 :guilabel:`标记为待办` 会使消息出现在 :guilabel:`已加星标` 页面，而点击 :guilabel:`标记为已读` 会将消息移到 :guilabel:`历史记录`。

.. image:: discuss/starred-messages.png
   :alt: Odoo Discuss 中标记为待办的消息视图。

开始聊天
==============

用户首次登录他们的账户时，OdooBot 会发送一条消息，询问是否允许发送聊天桌面通知。如果接受，用户将收到桌面消息推送通知，无论用户在 Odoo 的哪个位置。

.. image:: discuss/odoobot-push.png
   :alt: Odoo Discuss 消息菜单中的请求推送通知视图。

.. tip::
   若要停止接收桌面通知，请重置浏览器的通知设置。

要开始聊天，请转到 :menuselection:`讨论` 应用，并点击仪表板左侧菜单中 :guilabel:`直接消息` 或 :guilabel:`频道` 旁的 :guilabel:`+ (加号)` 图标。

.. image:: discuss/channels-direct-messages.png
   :height: 400
   :alt: Odoo Discuss 中的频道和直接消息标题视图。

公司还可以轻松创建 :doc:`公共和私人频道 <discuss/team_communication>`。

在聊天和聊天框中提及
---------------------------------------

要在聊天或聊天框中提及用户，输入 `@用户名`；要提及频道，输入 `#频道名`。提及的用户将根据其通信设置在 *收件箱* 中或通过电子邮件收到通知。

.. image:: discuss/chat-windows.png
   :alt: Odoo Discuss 中的几个聊天窗口消息视图。

.. tip::
   当提及用户时，搜索列表（姓名列表）首先基于任务的关注者推荐值，其次基于员工。如果搜索的记录既不匹配关注者也不匹配员工，搜索范围将扩展到所有合作伙伴。

用户状态
-----------

通过检查同事的 *状态*，可以了解他们的动态以及回复消息的速度。状态显示在联系人姓名的左侧，位于 :guilabel:`讨论` 侧边栏、*消息菜单* 和 *聊天框* 中列出时。

- 绿色 = 在线
- 橙色 = 离开
- 白色 = 离线
- 飞机图标 = 离开办公室

.. image:: discuss/status.png
   :height: 300
   :alt: Odoo Discuss 中的联系人状态视图。

.. seealso::
   - :doc:`discuss/team_communication`
   - :doc:`/applications/essentials/activities`

.. toctree::
   :titlesonly:

   discuss/team_communication
   discuss/ice_servers
   discuss/chatter
