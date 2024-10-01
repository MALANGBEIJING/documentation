
.. _reference/mixins:

=========================
Mixins 和实用类
=========================

Odoo 实现了一些实用的类和 mixins，使您能够轻松地为对象添加常用行为。本指南将详细介绍其中的大部分内容，并提供示例和用例。

.. _reference/mixins/mail:

消息功能
==================

.. _reference/mixins/mail/chatter:

消息集成
---------------------

基础消息系统
~~~~~~~~~~~~~~~~~~~~~~

将消息功能集成到模型中非常简单。只需继承 ``mail.thread`` 模型，并将消息字段（及其相应的小部件）添加到表单视图中，即可快速启用。

.. 例子::

    让我们创建一个简单的模型，表示商务旅行。由于组织这种类型的旅行通常涉及很多人和讨论，因此我们在模型中添加消息交换支持。

    .. code-block:: python

        class BusinessTrip(models.Model):
            _name = 'business.trip'
            _inherit = ['mail.thread']
            _description = '商务旅行'

            name = fields.Char()
            partner_id = fields.Many2one('res.partner', '负责人')
            guest_ids = fields.Many2many('res.partner', '参与者')

    在表单视图中：

    .. code-block:: xml

        <record id="business_trip_form" model="ir.ui.view">
            <field name="name">business.trip.form</field>
            <field name="model">business.trip</field>
            <field name="arch" type="xml">
                <form string="商务旅行">
                    <!-- 您的常规表单视图在此 -->
                    ...
                    然后是 chatter 集成 -->
                    <div class="oe_chatter">
                        <field name="message_follower_ids" widget="mail_followers"/>
                        <field name="message_ids" widget="mail_thread"/>
                    </div>
                </form>
            </field>
        </record>

添加 Chatter 支持后，用户可以轻松在模型的任何记录上添加消息或内部备注；每条消息都会发送通知（消息发送给所有关注者，内部备注发送给员工用户）。如果您的邮件网关和 catchall 地址配置正确，这些通知将通过电子邮件发送，并且可以直接从邮件客户端回复；自动路由系统将回复路由到正确的线程。

服务器端提供了一些辅助函数，帮助您轻松发送消息和管理记录的关注者：

.. rubric:: 发布消息

.. method:: message_post(self, body='', subject=None, message_type='notification', subtype=None, parent_id=False, attachments=None, **kwargs)

    在现有线程中发布新消息，返回新创建的 ``mail.message`` ID。

    :param str | Markup body: 消息内容。如果是 `str`，将会进行转义。使用 :class:`~markupsafe.Markup` 对象来发送 HTML 内容。
    :param str message_type: 参见 mail_message.message_type 字段
    :param int parent_id: 如果是私密对话，通过添加父合作伙伴来处理回复之前的消息
    :param list(tuple(str,str)) attachments: 附件列表，形式为 ``(name,content)``，其中内容不需 base64 编码
    :param bool body_is_html: 指示是否将 `body` 视为 HTML，即使是 `str`。
    :param `\**kwargs`: 额外的关键字参数将用作新 mail.message 记录的默认列值
    :return: 新创建的 mail.message ID
    :rtype: int

.. method:: message_post_with_view(views_or_xmlid, **kwargs):

    使用 ``view_id`` 通过 ir.qweb 引擎渲染发送邮件/发布消息的辅助方法。此方法是独立的，因为模板和撰写器中没有允许批量处理视图的功能。当模板处理 ir ui 视图时，该方法可能会消失。

    :param str or ``ir.ui.view`` record: 要发送的视图的外部 ID 或记录

.. method:: message_post_with_template(template_id, **kwargs)

    使用模板发送邮件的辅助方法

    :param template_id: 要渲染以创建消息内容的模板 ID
    :param `\**kwargs`: 用于创建 mail.compose.message 向导的参数（继承自 mail.message）

.. rubric:: 接收消息

这些方法在邮件网关处理新电子邮件时调用。这些电子邮件可以是新线程（如果通过 :ref:`alias <reference/mixins/mail/alias>` 到达），也可以是现有线程的回复。重写这些方法可以根据电子邮件本身的某些值为线程的记录设置值（例如更新日期或电子邮件地址、添加 CC 地址作为关注者等）。

.. method:: message_new(msg_dict, custom_values=None)

    当为给定线程模型接收到新消息时，由 ``message_process`` 调用，如果该消息不属于现有线程。

    默认行为是根据从消息中提取的一些基本信息创建相应模型的新记录。可以通过重写此方法实现附加行为。

    :param dict msg_dict: 包含电子邮件详细信息和附件的映射。有关详细信息，请参阅 ``message_process`` 和 ``mail.message.parse``。
    :param dict custom_values: 传递给 create() 方法的附加字段值的可选字典；请注意，这些值可能会覆盖消息中的其他值
    :rtype: int
    :return: 新创建的线程对象 ID

.. method:: message_update(msg_dict, update_vals=None)

    当接收到现有线程的新消息时，由 ``message_process`` 调用。默认行为是使用来自传入邮件的 ``update_vals`` 更新记录。

    可以通过重写此方法实现附加行为。

    :param dict msg_dict: 包含电子邮件详细信息和附件的映射；请参阅 ``message_process`` 和 ``mail.message.parse()`` 了解详细信息。
    :param dict update_vals: 包含要更新记录值的字典；如果字典为 None 或为空，则不执行写入操作。
    :return: True

.. rubric:: 关注者管理

.. method:: message_subscribe(partner_ids=None, channel_ids=None, subtype_ids=None, force=True)

    将合作伙伴添加为记录的关注者。

    :param list(int) partner_ids: 将订阅记录的合作伙伴 ID 列表
    :param list(int) channel_ids: 将订阅记录的频道 ID 列表
    :param list(int) subtype_ids: 渠道/合作伙伴将订阅的子类型 ID（如果为 ``None``，则默认为默认子类型）
    :param force: 如果为 True，则在使用参数中给定的子类型创建新关注者之前删除现有关注者
    :return: 成功/失败
    :rtype: bool

.. method:: message_unsubscribe(partner_ids=None, channel_ids=None)

    从记录的关注者中移除合作伙伴。

    :param list(int) partner_ids: 将取消订阅记录的合作伙伴 ID 列表
    :param list(int) channel_ids: 将取消订阅记录的频道 ID 列表
    :return: True
    :rtype: bool

.. method:: message_unsubscribe_users(user_ids=None)

    通过用户进行 ``message_subscribe`` 的包装方法。

    :param list(int) user_ids: 将取消订阅记录的用户 ID 列表；如果为 None，则取消订阅当前用户。
    :return: True
    :rtype: bool

记录变更日志
~~~~~~~~~~~~~~

``mail`` 模块为字段添加了强大的跟踪系统，允许您在记录的 Chatter 中记录特定字段的更改。要跟踪字段的更改，只需将 tracking 属性设置为 True。

.. 例子::

    让我们跟踪商务旅行的名称和负责人的更改：

    .. code-block:: python

        class BusinessTrip(models.Model):
            _name = 'business.trip'
            _inherit = ['mail.thread']
            _description = '商务旅行'

            name = fields.Char(tracking=True)
            partner_id = fields.Many2one('res.partner', '负责人', tracking=True)
            guest_ids = fields.Many2many('res.partner', '参与者')

    从现在开始，对旅行名称或负责人的每一次更改都会在记录中记录一条备注。``name`` 字段将显示在通知中，即使名称没有更改，它也会提供更多的上下文信息。

子类型
~~~~~~~~

子类型使您能够对消息进行更细粒度的控制。子类型作为通知的分类系统，允许文档的订阅者自定义他们希望接收的通知子类型。

子类型作为数据在模块中创建；该模型具有以下字段：

``name``（必填）- :class:`~odoo.fields.Char`
    子类型名称，将显示在通知自定义弹出窗口中
``description`` - :class:`~odoo.fields.Char`
    将添加到该子类型发布的消息中的描述。如果为空，则添加名称
``internal`` - :class:`~odoo.fields.Boolean`
    具有内部子类型的消息仅对员工可见，即 ``base.group_user`` 组的成员
``parent_id`` - :class:`~odoo.fields.Many2one`
    链接子类型以实现自动订阅；例如项目子类型通过此链接连接到任务子类型。当某人订阅项目时，他将自动订阅所有任务，其子类型通过父子类型查找
``relation_field`` - :class:`~odoo.fields.Char`
    例如，在链接项目和任务子类型时，relation_field 是任务的 project_id 字段
``res_model`` - :class:`~odoo.fields.Char`
    子类型适用的模型；如果为 False，则该子类型适用于所有模型
``default`` - :class:`~odoo.fields.Boolean`
    订阅时是否默认激活该子类型
``sequence`` - :class:`~odoo.fields.Integer`
    用于在通知自定义弹出窗口中对子类型进行排序
``hidden`` - :class:`~odoo.fields.Boolean`
    子类型是否隐藏在通知自定义弹出窗口中

通过将子类型与字段跟踪接口相结合，允许用户根据自己的兴趣订阅不同类型的通知。要实现此功能，您可以重写 ``_track_subtype()`` 函数：

.. method:: _track_subtype(init_values)

    根据记录中的更改，返回触发的子类型。

    :param dict init_values: 记录的原始值；仅包含已修改的字段。
    :returns: 触发的子类型的完整外部ID，若未触发子类型则返回False。


.. 示例::

    我们在示例类中添加一个 ``state`` 字段，并在该字段更改时触发一个特定的子类型通知。

    首先，定义我们的子类型：

    .. code-block:: xml

        <record id="mt_state_change" model="mail.message.subtype">
            <field name="name">旅行确认</field>
            <field name="res_model">business.trip</field>
            <field name="default" eval="True"/>
            <field name="description">商务旅行已确认！</field>
        </record>

    然后，我们需要重写 ``track_subtype()`` 函数。该函数由跟踪系统调用，根据当前应用的更改来决定应使用哪个子类型。在此示例中，我们希望当 ``state`` 字段从 *draft* 更改为 *confirmed* 时，使用新定义的子类型：

    .. code-block:: python

        class BusinessTrip(models.Model):
            _name = 'business.trip'
            _inherit = ['mail.thread']
            _description = '商务旅行'

            name = fields.Char(tracking=True)
            partner_id = fields.Many2one('res.partner', '负责人', tracking=True)
            guest_ids = fields.Many2many('res.partner', '参与者')
            state = fields.Selection([('draft', '新建'), ('confirmed', '已确认')],
                                     tracking=True)

            def _track_subtype(self, init_values):
                # init_values 包含更改前字段的原始值
                #
                # 应用的值可以通过缓存中的记录进行访问
                self.ensure_one()
                if 'state' in init_values and self.state == 'confirmed':
                    return self.env.ref('my_module.mt_state_change')
                return super(BusinessTrip, self)._track_subtype(init_values)

自定义通知
~~~~~~~~~~~~~~~~~~~~~~~~~

在向关注者发送通知时，在模板中添加按钮以允许用户直接从电子邮件中执行快速操作是非常有用的。即使是一个简单的按钮，直接链接到记录的表单视图也能提高效率；然而，在大多数情况下，您不希望这些按钮显示给门户用户。

通知系统允许以以下方式自定义通知模板：

- 显示 *访问按钮*：这些按钮显示在通知电子邮件的顶部，允许收件人直接访问记录的表单视图。
- 显示 *关注按钮*：这些按钮允许收件人直接快速地从记录中订阅。
- 显示 *取消关注按钮*：这些按钮允许收件人直接快速取消订阅。
- 显示 *自定义操作按钮*：这些按钮通过调用特定的路由，允许用户直接从电子邮件中执行某些有用的操作（例如将潜在客户转换为机会、为费用经理验证费用报表等）。

这些按钮设置可以应用于不同的组，您可以通过重写函数 ``_notify_get_groups`` 来自定义这些组。

.. method:: _notify_get_groups(message, groups)

    根据记录中的更改，返回触发的子类型。

    :param ``record`` message: 当前正在发送的 ``mail.message`` 记录
    :param list(tuple) groups: 组的元组列表，格式为 (group_name, group_func, group_data)，其中：

        group_name
          仅用于覆盖和操作组的标识符。默认组为 ``user``（链接到员工用户的收件人），``portal``（链接到门户用户的收件人），和 ``customer``（未链接到任何用户的收件人）。例如，可以添加一个链接到像 Hr Officers 这样的 ``res.groups`` 组，以为他们设置特定的操作按钮。
        group_func
          是一个函数指针，接受一个合伙人记录作为参数。该方法将应用于收件人，以判断他们是否属于某个组。只保留第一个匹配的组。评估顺序为列表顺序。
        group_data
          是一个包含通知电子邮件参数的字典，其中可能的键 - 值如下：

          - has_button_access
              是否在电子邮件中显示“访问<文档>”按钮。对于新组默认设置为True，对于门户/客户为False。
          - button_access
              包含按钮的URL和标题的字典。
          - has_button_follow
              是否在电子邮件中显示“关注”按钮（如果收件人当前未关注该线程）。对于新组默认设置为True，对于门户/客户为False。
          - button_follow
              包含按钮的URL和标题的字典。
          - has_button_unfollow
              是否在电子邮件中显示“取消关注”按钮（如果收件人当前已关注该线程）。对于新组默认设置为True，对于门户/客户为False。
          - button_unfollow
              包含按钮的URL和标题的字典。
          - actions
              要在通知电子邮件中显示的操作按钮列表。每个操作都是一个包含按钮的URL和标题的字典。

    :returns: 触发的子类型的完整外部ID，若未触发子类型则返回False。

这些操作按钮的URL可以通过调用 ``_notify_get_action_link()`` 函数自动生成：

.. method:: _notify_get_action_link(self, link_type, **kwargs)

    为当前记录生成给定类型的链接（或在设置了kwargs ``model`` 和 ``res_id`` 时为特定记录生成链接）。

    :param str link_type: 要生成的链接类型，可以是以下任意值：

        ``view``
          链接到记录的表单视图。
        ``assign``
          将登录用户分配给记录的 ``user_id`` 字段（如果存在）。
        ``follow``
          自解释。
        ``unfollow``
          自解释。
        ``method``
          调用记录上的方法；方法名应作为kwarg ``method`` 提供。
        ``new``
          打开一个空白的表单视图用于新记录；您可以通过kwarg ``action_id`` 指定一个特定的操作（数据库ID或完全解析的外部ID）。

    :returns: 为记录生成的链接类型。
    :rtype: str

.. 示例::

    让我们在商务旅行状态更改通知中添加一个自定义按钮；该按钮将把状态重置为“草稿”，并且仅对属于（假设的）旅行经理组（``business.group_trip_manager``）的成员可见。

    .. code-block:: python

        class BusinessTrip(models.Model):
            _name = 'business.trip'
            _inherit = ['mail.thread', 'mail.alias.mixin']
            _description = '商务旅行'

            # 之前的代码

            def action_cancel(self):
                self.write({'state': 'draft'})

            def _notify_get_groups(self, message, groups):
                """ 处理旅行经理收件人，他们可以在最后一刻取消旅行，破坏所有的乐趣。 """
                groups = super(BusinessTrip, self)._notify_get_groups(message, groups)

                self.ensure_one()
                if self.state == 'confirmed':
                    app_action = self._notify_get_action_link('method',
                                        method='action_cancel')
                    trip_actions = [{'url': app_action, 'title': _('取消')}]

                new_group = (
                    'group_trip_manager',
                    lambda partner: bool(partner.user_ids) and
                    any(user.has_group('business.group_trip_manager')
                    for user in partner.user_ids),
                    {
                        'actions': trip_actions,
                    })

                return [new_group] + groups

    请注意，我本可以在此方法外部定义我的评估函数，并定义一个全局函数来代替lambda函数，但为了使这些文档文件更简洁和不那么冗长，我选择了前者而不是后者。

重写默认值
~~~~~~~~~~~~~~~~~~~

有几种方式可以自定义 ``mail.thread`` 模型的行为，包括但不限于：

``_mail_post_access`` - :class:`~odoo.models.Model` 属性
    在模型上发布消息所需的访问权限；默认情况下需要 ``write`` 访问权限，也可以设置为 ``read``。

上下文键：
    这些上下文键可以在调用 ``create()`` 或 ``write()`` （或任何其他可能有用的方法）时，控制 ``mail.thread`` 功能，如自动订阅或字段跟踪。

    - ``mail_create_nosubscribe``: 在创建或 ``message_post`` 时，不会将当前用户订阅到记录线程。
    - ``mail_create_nolog``: 在创建时，不会记录自动的“<文档> 已创建”消息。
    - ``mail_notrack``: 在创建和写入时，不会执行创建消息的值跟踪。
    - ``tracking_disable``: 在创建和写入时，不执行 MailThread 功能（自动订阅、跟踪、发布等）。
    - ``mail_auto_delete``: 自动删除邮件通知；默认值为 True。
    - ``mail_notify_force_send``: 如果要发送的电子邮件通知少于 50 封，则直接发送，而不是使用队列；默认值为 True。
    - ``mail_notify_user_signature``: 在电子邮件通知中添加当前用户的签名；默认值为 True。

.. _reference/mixins/mail/alias:

邮件别名
----------

别名是可配置的电子邮件地址，链接到特定的记录（通常继承 ``mail.alias.mixin`` 模型），在通过电子邮件联系时会创建新记录。它们是让系统从外部访问的便捷方式，允许用户或客户快速在数据库中创建记录，而无需直接连接到 Odoo。

别名与传入邮件网关
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

有些人使用传入邮件网关实现同样的目的。您仍然需要正确配置的邮件网关来使用别名，但单一的 catchall 域名就足够了，因为所有路由都会在 Odoo 内部完成。别名相较于邮件网关有以下优点：

* 更易配置
    * 一个单一的传入网关可以用于多个别名；这样避免了在您的域名上配置多个电子邮件地址（所有配置都在 Odoo 内完成）。
    * 配置别名不需要系统访问权限。
* 更加连贯
    * 在相关记录上进行配置，而不是在设置子菜单中。
* 更容易在服务器端覆盖
    * Mixin 模型从一开始就构建为可扩展，允许您比使用邮件网关更容易地从传入的电子邮件中提取有用的数据。

别名支持集成
~~~~~~~~~~~~~~~~~~~~~~~~~

别名通常配置在父模型上，当通过电子邮件联系时，会创建特定的记录。例如，项目可以通过别名创建任务或问题，销售团队可以通过别名生成潜在客户。

.. note:: 别名将创建的模型 **必须** 继承 ``mail_thread`` 模型。

通过继承 ``mail.alias.mixin`` 添加别名支持；该混合模式会为父类中的每条记录生成一个新的 ``mail.alias`` 记录（例如，每个 ``project.project`` 记录在创建时都会初始化其 ``mail.alias`` 记录）。

.. note:: 别名也可以手动创建，并通过一个简单的 :class:`~odoo.fields.Many2one` 字段支持。本指南假设您希望通过自动创建别名、记录特定的默认值等方式进行更完整的集成。

与 ``mail.thread`` 继承不同，``mail.alias.mixin`` **需要** 一些特定的覆盖才能正常工作。这些覆盖将指定创建的别名的值，如必须创建的记录类型以及根据父对象可能具有的一些默认值：

.. method:: _get_alias_model_name(vals)

    返回别名的模型名称。传入的电子邮件如果不是对现有记录的回复，将会导致创建别名模型的新记录。该值可能依赖于 ``vals``，即创建该模型记录时传递的值字典。

    :param vals dict: 新创建的记录的值，这些记录将持有别名。
    :return: 模型名称。
    :rtype: str

.. method:: _get_alias_values()

    返回用于创建别名的值，或在别名创建后写入的值。虽然不是完全强制的，但通常需要确保新创建的记录将链接到别名的父记录（即，通过设置别名的 ``alias_defaults`` 字段中的默认值，确保在正确的项目中创建任务）。

    :return: 写入新别名的值的字典。
    :rtype: dict

``_get_alias_values()`` 覆盖特别有趣，因为它允许您轻松修改别名的行为。在别名上可以设置的字段中，以下内容尤为重要：

``alias_name`` - :class:`~odoo.fields.Char`
    电子邮件别名的名称，例如 'jobs'，如果您希望接收 <jobs@example.odoo.com> 的电子邮件。
``alias_user_id`` - :class:`~odoo.fields.Many2one` (``res.users``)
    当别名接收到电子邮件时创建的记录的所有者；如果未设置此字段，系统将尝试根据发件人（发件人）地址查找正确的所有者，或者如果找不到该地址的系统用户，将使用管理员帐户。
``alias_defaults`` - :class:`~odoo.fields.Text`
    一个 Python 字典，当为该别名创建新记录时，将计算并提供默认值。
``alias_force_thread_id`` - :class:`~odoo.fields.Integer`
    可选的线程（记录）ID，所有传入消息都将附加到该线程，即使它们没有回复它；如果设置了此字段，将完全禁用新记录的创建。
``alias_contact`` - :class:`~odoo.fields.Selection`
    使用邮件网关在文档上发布消息的策略。

    - *everyone*: 所有人都可以发布消息。
    - *partners*: 只有经过认证的合作伙伴可以发布消息。
    - *followers*: 只有相关文档的关注者或跟随频道的成员可以发布消息。

注意，别名使用了 :ref:`delegation inheritance <reference/orm/inheritance>`，这意味着虽然别名存储在另一个表中，但您可以从父对象直接访问所有这些字段。这允许您从记录的表单视图中轻松配置别名。

.. 示例::

    让我们为商务旅行类添加别名，以通过电子邮件即时创建费用记录。

    .. code-block:: python

        class BusinessTrip(models.Model):
            _name = 'business.trip'
            _inherit = ['mail.thread', 'mail.alias.mixin']
            _description = '商务旅行'

            name = fields.Char(tracking=True)
            partner_id = fields.Many2one('res.partner', '负责人', tracking=True)
            guest_ids = fields.Many2many('res.partner', '参与者')
            state = fields.Selection([('draft', '新建'), ('confirmed', '已确认')],
                                     tracking=True)
            expense_ids = fields.One2many('business.expense', 'trip_id', '费用')
            alias_id = fields.Many2one('mail.alias', string='别名', ondelete="restrict",
                                       required=True)

            def _get_alias_model_name(self, vals):
            """ 指定当别名接收到消息时将创建的模型 """
                return 'business.expense'

            def _get_alias_values(self):
            """ 指定在创建别名时将设置的一些默认值 """
                values = super(BusinessTrip, self)._get_alias_values()
                # alias_defaults 包含将写入所有由该别名创建的记录的字典
                #
                # 在本例中，我们希望所有发送到旅行别名的费用记录都链接到相应的商务旅行
                values['alias_defaults'] = {'trip_id': self.id}
                # 默认情况下，我们只希望旅行的关注者能够发布费用
                values['alias_contact'] = 'followers'
                return values

        class BusinessExpense(models.Model):
            _name = 'business.expense'
            _inherit = ['mail.thread']
            _description = '商务费用'

            name = fields.Char()
            amount = fields.Float('金额')
            trip_id = fields.Many2one('business.trip', '商务旅行')
            partner_id = fields.Many2one('res.partner', '创建者')

    我们希望别名可以轻松地从商务旅行的表单视图进行配置，因此让我们将以下内容添加到表单视图中：

    .. code-block:: xml

        <page string="电子邮件">
            <group name="group_alias">
                <label for="alias_name" string="电子邮件别名"/>
                <div name="alias_def">
                    <!-- 在查看模式下显示链接，在编辑模式下显示可配置字段 -->
                    <field name="alias_id" class="oe_read_only oe_inline"
                            string="电子邮件别名" required="0"/>
                    <div class="oe_edit_only oe_inline" name="edit_alias"
                         style="display: inline;" >
                        <field name="alias_name" class="oe_inline"/>
                        @
                        <field name="alias_domain" class="oe_inline" readonly="1"/>
                    </div>
                </div>
                <field name="alias_contact" class="oe_inline"
                        string="接受邮件来自"/>
            </group>
        </page>

    现在，我们可以直接从表单视图更改别名地址，并更改谁可以向别名发送电子邮件。

    然后我们可以在费用模型上覆盖 ``message_new()``，以便在创建费用时从电子邮件中获取值：

    .. code-block:: python

        class BusinessExpense(models.Model):
            # 之前的代码
            # ...

            def message_new(self, msg, custom_values=None):
                """ 重写以根据电子邮件设置值。

                在这个简单的示例中，我们只使用电子邮件标题作为费用名称，尝试找到具有该电子邮件地址的合作伙伴，并通过正则表达式匹配找到费用金额。"""
                name = msg_dict.get('subject', '新费用')
                # 匹配字符串中最后出现的浮点数
                # 例如： '50.3 bar 34.5' 变为 '34.5'。这可能是要在费用中编码的价格。如果没有，默认取 1.0
                amount_pattern = '(\d+(\.\d*)?|\.\d+)'
                expense_price = re.findall(amount_pattern, name)
                price = expense_price and float(expense_price[-1][0]) or 1.0
                # 根据电子邮件查找合伙人
                partner = self.env['res.partner'].search([('email', 'ilike', email_address)],
                                                         limit=1)
                defaults = {
                    'name': name,
                    'amount': price,
                    'partner_id': partner.id
                }
                defaults.update(custom_values or {})
                res = super(BusinessExpense, self).message_new(msg, custom_values=defaults)
                return res

.. _reference/mixins/mail/activities:
活动追踪
-------------------

活动是用户在文档上需要执行的操作，如打电话或组织会议。活动与邮件模块集成在 Chatter 中，但 *不与 mail.thread 捆绑*。活动是 ``mail.activity`` 类的记录，具有类型（``mail.activity.type``）、名称、描述、计划时间（等等）。未完成的活动显示在 Chatter 小部件中的消息历史上方。

您可以通过在对象上使用 ``mail.activity.mixin`` 类并在表单视图和看板视图中显示特定的小部件（通过 ``activity_ids`` 字段）来集成活动（分别为 ``mail_activity`` 和 ``kanban_activity`` 小部件）。

.. 示例::

    组织商务旅行是一个繁琐的过程，跟踪所需的活动（如订机票或叫车去机场）可能很有用。为此，我们将在模型上添加活动混合类，并在旅行的消息历史中显示下一个计划活动。

    .. code-block:: python

        class BusinessTrip(models.Model):
            _name = 'business.trip'
            _inherit = ['mail.thread', 'mail.activity.mixin']
            _description = '商务旅行'

            name = fields.Char()
            # [...]

    我们修改了旅行的表单视图，以显示下一个活动：

    .. code-block:: xml

        <record id="business_trip_form" model="ir.ui.view">
            <field name="name">business.trip.form</field>
            <field name="model">business.trip</field>
            <field name="arch" type="xml">
                <form string="商务旅行">
                    <!-- 您的表单视图通常放在这里 -->
                    <div class="oe_chatter">
                        <field name="message_follower_ids" widget="mail_followers"/>
                        <field name="activity_ids" widget="mail_activity"/>
                        <field name="message_ids" widget="mail_thread"/>
                    </div>
                </form>
            </field>
        </record>

您可以在以下模型中找到具体的集成示例：

* ``crm.lead`` 在 CRM (*crm*) 应用中
* ``sale.order`` 在销售 (*sale*) 应用中
* ``project.task`` 在项目 (*project*) 应用中


.. _reference/mixins/website:

网站功能
================

.. _reference/mixins/website/utm:

访客追踪
----------------

``utm.mixin`` 类可用于通过指定资源的链接参数跟踪在线营销/沟通活动。该混合类向您的模型添加了 3 个字段：

* ``campaign_id``: :class:`~odoo.fields.Many2one` 字段，指向 ``utm.campaign`` 对象（例如 Christmas_Special、Fall_Collection 等）。
* ``source_id``: :class:`~odoo.fields.Many2one` 字段，指向 ``utm.source`` 对象（例如搜索引擎、邮件列表等）。
* ``medium_id``: :class:`~odoo.fields.Many2one` 字段，指向 ``utm.medium`` 对象（例如邮寄、电子邮件、社交网络更新等）。

这些模型只有一个 ``name`` 字段（即它们只是用于区分活动，但没有任何特定行为）。

当客户访问您的网站并在网址中设置了这些参数（例如 https://www.odoo.com/?campaign_id=mixin_talk&source_id=www.odoo.com&medium_id=website），系统会在访客的浏览器中为这些参数设置三个 cookie。一旦从网站创建了继承了 utm.mixin 的对象（例如线索表单、工作申请等），utm.mixin 代码将开始运行，并从 cookie 中获取值设置到新记录中。完成后，您可以像定义其他字段一样使用活动/来源/媒介字段来定义报表和视图（分组等）。

要扩展此行为，只需向简单模型添加一个关系字段（该模型应支持 *快速创建*（即通过一个 ``name`` 值调用 ``create()``）并扩展 ``tracking_fields()`` 函数）：

.. code-block:: python

    class UtmMyTrack(models.Model):
        _name = 'my_module.my_track'
        _description = '我的追踪对象'

        name = fields.Char(string='名称', required=True)


    class MyModel(models.Models):
        _name = 'my_module.my_model'
        _inherit = ['utm.mixin']
        _description = '我的被追踪对象'

        my_field = fields.Many2one('my_module.my_track', '我的字段')

        @api.model
        def tracking_fields(self):
            result = super(MyModel, self).tracking_fields()
            result.append([
            # ("URL_PARAMETER", "FIELD_NAME_MIXIN", "NAME_IN_COOKIES")
                ('my_field', 'my_field', 'odoo_utm_my_field')
            ])
            return result

这将告诉系统创建一个名为 *odoo_utm_my_field* 的 cookie，并将网址参数 ``my_field`` 中找到的值存储在其中；一旦通过网站表单创建了此模型的新记录，utm.mixin 的通用重写 ``create()`` 方法将从 cookie 中获取该字段的默认值（如果 ``my_module.my_track`` 记录尚不存在，将会自动创建）。

您可以在以下模型中找到具体的集成示例：

* ``crm.lead`` 在 CRM (*crm*) 应用中
* ``hr.applicant`` 在招聘流程 (*hr_recruitment*) 应用中
* ``helpdesk.ticket`` 在帮助台 (*helpdesk* - 仅限 Odoo 企业版) 应用中

.. _reference/mixins/website/published:

网站可见性
------------------

您可以非常轻松地为任何记录添加网站可见性切换。虽然此混合类的实现相对简单，但它是仅次于 ``mail.thread`` 继承后使用最频繁的功能之一，这证明了它的实用性。此混合类的典型用例是任何具有前端页面的对象；能够控制页面的可见性允许您在编辑页面时花时间处理，只有在满意时才发布。

要包括此功能，您只需继承 ``website.published.mixin``：

.. code-block:: python

    class BlogPost(models.Model):
        _name = "blog.post"
        _description = "博客文章"
        _inherit = ['website.published.mixin']

此混合类会在您的模型中添加两个字段：

* ``website_published``: :class:`~odoo.fields.Boolean` 字段，表示发布状态。
* ``website_url``: :class:`~odoo.fields.Char` 字段，表示通过该对象访问的 URL。

请注意，最后一个字段是一个计算字段，必须为您的类实现：

.. code-block:: python

    def _compute_website_url(self):
        for blog_post in self:
            blog_post.website_url = "/blog/%s" % (log_post.blog_id)

机制就绪后，您只需调整前端和后端视图，使其可访问。在后端，通常在按钮框中添加一个按钮：

.. code-block:: xml

    <button class="oe_stat_button" name="website_publish_button"
        type="object" icon="fa-globe">
        <field name="website_published" widget="website_button"/>
    </button>

在前端，为了避免网站访客看到“编辑”按钮，需要进行一些安全检查：

.. code-block:: xml

    <div id="website_published_button" class="float-right"
         groups="base.group_website_publisher"> <!-- 或任何其他有意义的组 -->
        <t t-call="website.publish_management">
          <t t-set="object" t-value="blog_post"/>
          <t t-set="publish_edit" t-value="True"/>
          <t t-set="action" t-value="'blog.blog_post_action'"/>
        </t>
    </div>

请注意，您必须将对象作为变量 ``object`` 传递给模板；在本例中，``blog.post`` 记录作为 ``blog_post`` 变量传递给 ``qweb`` 渲染引擎，必须在发布管理模板中指定这一点。 ``publish_edit`` 变量允许前端按钮链接到后端（允许您轻松地在前端和后端之间切换）；如果设置，您必须在 ``action`` 变量中指定要在后端调用的操作的完整外部 ID（请注意，模型必须存在一个表单视图）。

``website_publish_button`` 操作是在混合类中定义的，并根据您的对象调整其行为：如果类有一个有效的 ``website_url`` 计算函数，当用户点击按钮时会重定向到前端；然后用户可以直接从前端发布页面。这确保不会意外发生在线发布。如果没有计算函数，只需触发 ``website_published`` 布尔值。

.. _reference/mixins/website/seo:
网站元数据
----------------

这个简单的混合类可以让您轻松地在前端页面中注入元数据。

.. code-block:: python

    class BlogPost(models.Model):
        _name = "blog.post"
        _description = "博客文章"
        _inherit = ['website.seo.metadata', 'website.published.mixin']

这个混合类会在您的模型中添加三个字段：

* ``website_meta_title``: :class:`~odoo.fields.Char` 字段，允许您为页面设置一个附加的标题
* ``website_meta_description``: :class:`~odoo.fields.Char` 字段，包含页面的简短描述（有时会在搜索引擎结果中使用）
* ``website_meta_keywords``: :class:`~odoo.fields.Char` 字段，包含一些关键词，帮助您的页面被搜索引擎更精准地分类；"推广"工具将帮助您轻松选择词汇相关的关键词

这些字段可以通过编辑器工具栏中的"推广"工具在前端进行编辑。设置这些字段可以帮助搜索引擎更好地索引您的页面。请注意，搜索引擎并不仅仅基于这些元数据生成结果；最好的 SEO 实践仍然是通过可靠的来源获得引用。

.. _reference/mixins/misc:

其他功能
======

.. _reference/mixins/misc/rating:

客户评价
---------------

评价混合类允许发送电子邮件请求客户评价，自动转换看板流程中的状态，并聚合您的评分统计数据。

在模型中添加评价功能
~~~~~~~~~~~~~~~~~~~~~~~~~~~

要添加评价支持，只需继承 ``rating.mixin`` 模型：

.. code-block:: python

    class MyModel(models.Models):
        _name = 'my_module.my_model'
        _inherit = ['rating.mixin', 'mail.thread']

        user_id = fields.Many2one('res.users', '负责人')
        partner_id = fields.Many2one('res.partner', '客户')

该混合类的行为会根据您的模型自动适应：

* ``rating.rating`` 记录将会与您的模型的 ``partner_id`` 字段关联（如果字段存在）。

  - 如果您使用的不是 ``partner_id`` 字段，该行为可以通过函数 ``rating_get_partner_id()`` 覆盖

* ``rating.rating`` 记录将会与您的模型的 ``user_id`` 字段关联的合伙人（即被评价的合伙人）。

  - 如果您使用的不是 ``user_id`` 字段，该行为可以通过函数 ``rating_get_rated_partner_id()`` 覆盖（请注意，函数必须返回一个 ``res.partner``，对于 ``user_id`` 系统会自动获取用户的合伙人）

* 如果您的模型继承自 ``mail.thread``，Chatter 历史将显示评分事件。

通过电子邮件发送评价请求
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

如果您希望通过电子邮件发送评价请求，只需生成一封包含评价对象链接的电子邮件。一个非常基础的电子邮件模板可能如下所示：

.. code-block:: xml

    <record id="rating_my_model_email_template" model="mail.template">
                <field name="name">我的模型：评价请求</field>
                <field name="email_from">${object.rating_get_rated_partner_id().email or '' | safe}</field>
                <field name="subject">服务评价请求</field>
                <field name="model_id" ref="my_module.model_my_model"/>
                <field name="partner_to">${object.rating_get_partner_id().id}</field>
                <field name="auto_delete" eval="True"/>
                <field name="body_html"><![CDATA[
    % set access_token = object.rating_get_access_token()
    <p>您好,</p>
    <p>您对我们的服务有多满意？</p>
    <ul>
        <li><a href="/rate/${access_token}/5">满意</a></li>
        <li><a href="/rate/${access_token}/3">一般</a></li>
        <li><a href="/rate/${access_token}/1">不满意</a></li>
    </ul>
    ]]></field>
    </record>

您的客户将会收到一封包含链接到一个简单网页的电子邮件，允许他们提供对与您用户交互的反馈（包括自由文本的反馈消息）。

然后，您可以通过为评价定义一个动作，轻松地将评价集成到您的表单视图中：

.. code-block:: xml

    <record id="rating_rating_action_my_model" model="ir.actions.act_window">
        <field name="name">客户评价</field>
        <field name="res_model">rating.rating</field>
        <field name="view_mode">kanban,pivot,graph</field>
        <field name="domain">[('res_model', '=', 'my_module.my_model'), ('res_id', '=', active_id), ('consumed', '=', True)]</field>
    </record>

    <record id="my_module_my_model_view_form_inherit_rating" model="ir.ui.view">
        <field name="name">my_module.my_model.view.form.inherit.rating</field>
        <field name="model">my_module.my_model</field>
        <field name="inherit_id" ref="my_module.my_model_view_form"/>
        <field name="arch" type="xml">
            <xpath expr="//div[@name='button_box']" position="inside">
                <button name="%(rating_rating_action_my_model)d" type="action"
                        class="oe_stat_button" icon="fa-smile-o">
                    <field name="rating_count" string="评分" widget="statinfo"/>
                </button>
            </xpath>
        </field>
    </record>

请注意，系统为评价提供了默认的视图（kanban, pivot, graph），这使您可以快速概览客户评分。

您可以在以下模型中找到具体的集成示例：

* ``project.task`` 在项目 (*rating_project*) 应用中
* ``helpdesk.ticket`` 在帮助台 (*helpdesk* - 仅限 Odoo 企业版) 应用中
