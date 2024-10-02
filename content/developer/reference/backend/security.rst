================
Odoo中的安全性
================

除了通过自定义代码手动管理访问权限外，Odoo还提供了两种主要的数据驱动机制来管理或限制对数据的访问。

这两种机制通过*组*与特定用户相关联：用户属于任意数量的组，并且安全机制与组相关联，从而将安全机制应用于用户。

.. class:: res.groups

    .. attribute:: name

        作为用户可读的组标识（明确组的角色/目的）

    .. attribute:: category_id

        *模块类别*，用于将组与Odoo应用程序（~一组相关的业务模型）关联起来，并将它们转换为用户表单中的独占选择。

        .. todo:: 更清晰地阐明和记录组与类别之间的特殊情况和关系

    .. attribute:: implied_ids

        与此组一起在用户上设置的其他组。这是一种便利的伪继承关系：可以显式地从用户中删除暗示组，而无需删除暗示者。

    .. attribute:: comment

        关于组的附加说明，例如：

访问权限
=============

*授予*对给定操作的一整个模型的访问权限。如果没有访问权限与用户（通过他们的组）在模型上的操作匹配，则该用户将无法访问。

访问权限是累加的，用户的访问权限是通过所有组获得的访问权限的并集，例如，假设一个用户属于授予读取和创建访问权限的组 A，以及授予更新访问权限的组 B，则该用户将具有创建、读取和更新这三种权限。

.. class:: ir.model.access

    .. attribute:: name

        组的目的或角色。

    .. attribute:: model_id

        ACL 控制的模型。

    .. attribute:: group_id

        授予访问权限的 :class:`res.groups`，空的 :attr:`group_id` 意味着 ACL 授予*每个用户*（例如门户或公共用户的非员工）。

    :samp:`perm_{method}` 属性在设置时授予相应的 CRUD 访问权限，默认情况下它们都是未设置的。

    .. attribute:: perm_create
    .. attribute:: perm_read
    .. attribute:: perm_write
    .. attribute:: perm_unlink

记录规则
============

记录规则是*条件*，必须满足才能允许某个操作。记录规则逐条记录进行评估，遵循访问权限。

记录规则是默认允许的：如果访问权限授予访问并且没有规则适用于该用户的操作和模型，则授予访问权限。

.. class:: ir.rule

    .. attribute:: name

        规则的描述。

    .. attribute:: model_id

        规则适用的模型。

    .. attribute:: groups

        授予（或不授予）访问权限的 :class:`res.groups`。可以指定多个组。如果未指定组，则该规则是*全局的*，其处理方式与“组”规则（见下文）不同。

    .. attribute:: global

        基于 :attr:`groups` 计算，提供对规则全局状态（或非全局状态）的便捷访问。

    .. attribute:: domain_force

        作为 :ref:`domain <reference/orm/domains>` 指定的谓词，如果域与记录匹配，则允许所选操作，否则禁止。

        域是一个*python 表达式*，可以使用以下变量：

        ``time``
            Python的 :mod:`python:time` 模块。
        ``user``
            当前用户，作为单例记录集。
        ``company_id``
            当前用户当前选择的公司，作为单个公司ID（而不是记录集）。
        ``company_ids``
            当前用户可以访问的所有公司，作为公司ID的列表（而不是记录集），有关更多详细信息，请参见 :ref:`howto/company/security`。

    :samp:`perm_{method}` 的语义与 :class:`ir.model.access` 完全不同：对于规则，它们指定规则*适用于*的操作。如果未选择某个操作，则该规则不进行检查，就像该规则不存在一样。

    默认情况下，所有操作都被选择。

    .. attribute:: perm_create
    .. attribute:: perm_read
    .. attribute:: perm_write
    .. attribute:: perm_unlink

全局规则与组规则
-------------------------------

全局规则和组规则在组合和组合方式上存在很大差异：

* 全局规则*交集*，如果两个全局规则适用，则*必须*同时满足才能授予访问权限，这意味着添加全局规则会进一步限制访问权限。
* 组规则*联合*，如果两个组规则适用，则*任一*都可以满足才能授予访问权限。这意味着添加组规则可以扩展访问权限，但不能超出全局规则定义的范围。
* 全局规则集和组规则集*交集*，这意味着将第一组规则添加到给定的全局规则集中将限制访问权限。

.. danger::

    创建多个全局规则是有风险的，因为可能会创建
    不重叠的规则集，从而删除所有访问权限。

字段访问
============

ORM :class:`~odoo.fields.Field` 可以具有 ``groups`` 属性，提供一个组列表（作为逗号分隔的 :term:`external identifiers` 字符串）。

如果当前用户不在列出的组中，他将无法访问该字段：

* 受限字段会自动从请求的视图中删除
* 受限字段会从 :meth:`~odoo.models.Model.fields_get` 响应中删除
* 尝试（显式）读取或写入受限字段会导致访问错误

.. todo::

    字段访问组在 fields_get 中适用于超级用户，但在读取/写入中不适用...

安全隐患
=================

作为开发者，了解安全机制并避免导致不安全代码的常见错误非常重要。

不安全的公共方法
---------------------

任何公共方法都可以通过 :ref:`RPC 调用 <api/external_api/calling_methods>` 与所选参数一起执行。以 ``_`` 开头的方法不能通过操作按钮或外部 API 调用。

在公共方法上，执行该方法的记录和参数无法被信任，ACL 仅在 CRUD 操作期间进行验证。

.. code-block:: python

    # 该方法是公共的，无法信任其参数
    def action_done(self):
        if self.state == "draft" and self.user_has_groups('base.manager'):
            self._set_state("done")

    # 该方法是私有的，只能从其他 Python 方法调用
    def _set_state(self, new_state):
        self.sudo().write({"state": new_state})

将方法设为私有显然不够，必须谨慎使用它。

绕过 ORM
-----------------

在 ORM 可以做同样事情的情况下，您绝对不应直接使用数据库游标！这样做会绕过所有 ORM 功能，可能会影响自动行为，如翻译、字段失效、``active`` 、访问权限等。

而且您还可能使代码更难以阅读，并可能降低安全性。

.. code-block:: python

    # 非常非常错误
    self.env.cr.execute('SELECT id FROM auction_lots WHERE auction_id in (' + ','.join(map(str, ids))+') AND state=%s AND obj_price > 0', ('draft',))
    auction_lots_ids = [x[0] for x in self.env.cr.fetchall()]

    # 没有注入，但仍然错误
    self.env.cr.execute('SELECT id FROM auction_lots WHERE auction_id in %s '\
               'AND state=%s AND obj_price > 0', (tuple(ids), 'draft',))
    auction_lots_ids = [x[0] for x in self.env.cr.fetchall()]

    # 更好
    auction_lots_ids = self.search([('auction_id','in',ids), ('state','=','draft'), ('obj_price','>',0)])


SQL 注入
~~~~~~~~~~~~~~

在使用手动 SQL 查询时，必须小心不要引入 SQL 注入漏洞。该漏洞在用户输入未被正确过滤或错误引用时存在，允许攻击者在 SQL 查询中引入不必要的子句（例如绕过过滤器或执行 ``UPDATE`` 或 ``DELETE`` 命令）。

安全的最好方法是绝对不要使用 Python 字符串连接 (+) 或字符串参数插值 (%) 将变量传递给 SQL 查询字符串。

第二个原因（几乎同样重要）是，数据库抽象层（psycopg2）的工作是决定如何格式化查询参数，而不是您的工作！例如，psycopg2 知道当您传递值列表时，它需要将它们格式化为逗号分隔的列表，并用括号括起来！

.. code-block:: python

    # 以下是非常糟糕的：
    #   - 这是 SQL 注入漏洞
    #   - 它不易读
    #   - 格式化 ID 列表不是您的工作
    self.env.cr.execute('SELECT distinct child_id FROM account_account_consol_rel ' +
               'WHERE parent_id IN ('+','.join(map(str, ids))+')')

    # 更好
    self.env.cr.execute('SELECT DISTINCT child_id '\
               'FROM account_account_consol_rel '\
               'WHERE parent_id IN %s',
               (tuple(ids),))

这非常重要，因此在重构时请小心，最重要的是，不要复制这些模式！

这是一个难忘的例子，可以帮助您记住问题的所在（但不要复制那里的代码）。在继续之前，请确保阅读 psycopg2 的在线文档以了解如何正确使用它：

- `查询参数的问题 <http://initd.org/psycopg/docs/usage.html#the-problem-with-the-query-parameters>`_
- `如何使用 psycopg2 传递参数 <http://initd.org/psycopg/docs/usage.html#passing-parameters-to-sql-queries>`_
- `高级参数类型 <http://initd.org/psycopg/docs/usage.html#adaptation-of-python-values-to-sql-types>`_
- `Psycopg 文档 <https://www.psycopg.org/docs/sql.html>`_
未转义的字段内容
-----------------------

在使用 JavaScript 和 XML 渲染内容时，可能会倾向于使用 ``t-raw`` 来显示富文本内容。应避免这样做，因为这是一种常见的 `XSS <https://en.wikipedia.org/wiki/Cross-site_scripting>`_ 向量。

从计算到最终在浏览器 DOM 中集成，控制数据的完整性是非常困难的。在引入时正确转义的 ``t-raw`` 在下一个 bug 修复或重构时可能不再安全。

.. code-block:: javascript

    QWeb.render('insecure_template', {
        info_message: "You have an <strong>important</strong> notification",
    })

.. code-block:: xml

    <div t-name="insecure_template">
        <div id="information-bar"><t t-raw="info_message" /></div>
    </div>

上述代码可能看起来安全，因为消息内容是可控的，但这是不好的做法，可能会导致意外的安全漏洞，尤其是当这段代码在未来演变时。

.. code-block:: javascript

    // 使用未转义的用户提供内容可能发生 XSS！
    QWeb.render('insecure_template', {
        info_message: "You have an <strong>important</strong> notification on " \
            + "the product <strong>" + product.name + "</strong>",
    })

改变模板格式可以防止这种漏洞。

.. code-block:: javascript

    QWeb.render('secure_template', {
        message: "You have an important notification on the product:",
        subject: product.name
    })

.. code-block:: xml

    <div t-name="secure_template">
        <div id="information-bar">
            <div class="info"><t t-esc="message" /></div>
            <div class="subject"><t t-esc="subject" /></div>
        </div>
    </div>

.. code-block:: css

    .subject {
        font-weight: bold;
    }

使用 :class:`~markupsafe.Markup` 创建安全内容
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

请参阅 `官方文档 <https://markupsafe.palletsprojects.com/>`_ 以获取解释，但 :class:`~markupsafe.Markup` 的最大优点是它是一种非常丰富的类型，重写了 :class:`str` 操作，以*自动转义参数*。

这意味着通过对字符串文字使用 :class:`~markupsafe.Markup` 并“格式化”用户提供（因此可能不安全）内容，可以轻松创建*安全*的 HTML 片段：

.. code-block:: pycon

    >>> Markup('<em>Hello</em> ') + '<foo>'
    Markup('<em>Hello</em> &lt;foo&gt;')
    >>> Markup('<em>Hello</em> %s') % '<foo>'
    Markup('<em>Hello</em> &lt;foo&gt;')

尽管这很好，但请注意，效果有时可能会很奇怪：

.. code-block:: pycon

    >>> Markup('<a>').replace('>', 'x')
    Markup('<a>')
    >>> Markup('<a>').replace(Markup('>'), 'x')
    Markup('<ax')
    >>> Markup('<a&gt;').replace('>', 'x')
    Markup('<ax')
    >>> Markup('<a&gt;').replace('>', '&')
    Markup('<a&amp;')

.. tip:: 大多数内容安全 API 实际上返回一个 :class:`~markupsafe.Markup`，这意味着所有的隐含内容。

:class:`~markupsafe.escape` 方法（及其别名 :class:`~odoo.tools.misc.html_escape`）将 `str` 转换为 :class:`~markupsafe.Markup` 并转义其内容。它不会转义 :class:`~markupsafe.Markup` 对象的内容。

.. code-block:: python

    def get_name(self, to_html=False):
        if to_html:
            return Markup("<strong>%s</strong>") % self.name  # 转义名称
        else:
            return self.name

    >>> record.name = "<R&D>"
    >>> escape(record.get_name())
    Markup("&lt;R&amp;D&gt;")
    >>> escape(record.get_name(True))
    Markup("<strong>&lt;R&amp;D&gt;</strong>")  # HTML 被保留

在生成 HTML 代码时，重要的是将结构（标签）与内容（文本）分开。

.. code-block:: pycon

    >>> Markup("<p>") + "Hello <R&D>" + Markup("</p>")
    Markup('<p>Hello &lt;R&amp;D&gt;</p>')
    >>> Markup("%s <br/> %s") % ("<R&D>", Markup("<p>Hello</p>"))
    Markup('&lt;R&amp;D&gt; <br/> <p>Hello</p>')
    >>> escape("<R&D>")
    Markup('&lt;R&amp;D&gt;')
    >>> _("List of Tasks on project %s: %s",
    ...     project.name,
    ...     Markup("<ul>%s</ul>") % Markup().join(Markup("<li>%s</li>") % t.name for t in project.task_ids)
    ... )
    Markup('Liste de tâches pour le projet &lt;R&amp;D&gt;: <ul><li>First &lt;R&amp;D&gt; task</li></ul>')

    >>> Markup("<p>Foo %</p>" % bar)  # 不好，bar 未转义
    >>> Markup("<p>Foo %</p>") % bar  # 好，bar 在文本中被转义并在标记中保留

    >>> link = Markup("<a>%s</a>") % self.name
    >>> message = "Click %s" % link  # 不好，消息是文本且 Markup 没有任何作用
    >>> message = escape("Click %s") % link  # 好，将两个标记对象一起格式化

    >>> Markup(f"<p>Foo {self.bar}</p>")  # 不好，bar 在转义之前插入
    >>> Markup("<p>Foo {bar}</p>").format(bar=self.bar)  # 好，不好意思，没有 fstring

在处理翻译时，将 HTML 与文本分开尤其重要。翻译方法接受 :class:`~markupsafe.Markup` 参数，如果收到至少一个参数，则会转义翻译。

.. code-block:: pycon

    >>> Markup("<p>%s</p>") % _("Hello <R&D>")
    Markup('<p>Bonjour &lt;R&amp;D&gt;</p>')
    >>> _("Order %s has been confirmed", Markup("<a>%s</a>") % order.name)
    Markup('Order <a>SO42</a> has been confirmed')
    >>> _("Message received from %(name)s <%(email)s>",
    ...   name=self.name,
    ...   email=Markup("<a href='mailto:%s'>%s</a>") % (self.email, self.email)
    Markup('Message received from Georges &lt;<a href=mailto:george@abitbol.example>george@abitbol.example</a>&gt;')


转义与清理
----------------------

.. important::

    当混合数据和代码时，转义始终是 100% 强制性的，无论数据多么安全

**转义** 将 *TEXT* 转换为 *CODE*。每次将 *DATA/TEXT* 与 *CODE* 混合时，绝对有必要这样做（例如，生成 HTML 或在 `safe_eval` 内评估的 Python 代码），因为 *CODE* 始终需要对 *TEXT* 进行编码。这对安全至关重要，但这也是一个正确性问题。即使没有安全风险（因为文本 100% 确保安全或可信），仍然需要这样做（例如，避免在生成的 HTML 中破坏布局）。

转义永远不会破坏任何功能，只要开发人员能够识别出哪个变量包含 *TEXT*，哪个变量包含 *CODE*。

.. code-block:: python

    >>> from odoo.tools import html_escape, html_sanitize
    >>> data = "<R&D>" # `data` 是来自某处的某些 TEXT

    # 转义将其变为 CODE，好的！
    >>> code = html_escape(data)
    >>> code
    Markup('&lt;R&amp;D&gt;')

    # 现在可以与其他代码混合...
    >>> self.website_description = Markup("<strong>%s</strong>") % code

**清理** 将 *CODE* 转换为 *更安全的 CODE*（但不一定是 *安全* 的代码）。它不适用于 *TEXT*。清理仅在 *CODE* 不可信时是必要的，因为它完全或部分来自某些用户提供的数据。如果用户提供的数据以 *TEXT* 形式存在（例如，来自用户填写的表单的内容），并且在放入 *CODE* 之前该数据已正确转义，则清理是无用的（但仍然可以执行）。然而，如果用户提供的数据**未转义**，那么清理将**无法**按预期工作。

.. code-block:: python

    # 在未转义的情况下清理是损坏的：数据被破坏！
    >>> html_sanitize(data)
    Markup('')

    # 在转义后清理是好的！
    >>> html_sanitize(code)
    Markup('<p>&lt;R&amp;D&gt;</p>')

清理可能会破坏功能，具体取决于 *CODE* 是否被期望包含不安全的模式。这就是 `fields.Html` 和 `tools.html_sanitize()` 有选项可以微调样式等清理级别的原因。这些选项必须仔细考虑，具体取决于数据来源以及所需功能。清理安全性与清理损坏之间的平衡：清理越安全，越有可能破坏功能。

.. code-block:: python

    >>> code = "<p class='text-warning'>Important Information</p>"
    # 这将移除样式，可能会破坏功能
    # 但如果源不受信任，这是必要的
    >>> html_sanitize(code, strip_classes=True)
    Markup('<p>Important Information</p>')
评估内容
------------------

有些人可能希望使用 ``eval`` 来解析用户提供的内容。使用 ``eval`` 应该避免不使用。可以使用更安全、沙箱化的方法 :class:`~odoo.tools.safe_eval`，但这仍然赋予运行它的用户巨大的能力，必须仅保留给受信任的特权用户，因为它打破了代码和数据之间的界限。

.. code-block:: python

    # 非常糟糕
    domain = eval(self.filter_domain)
    return self.search(domain)

    # 更好，但仍不推荐
    from odoo.tools import safe_eval
    domain = safe_eval(self.filter_domain)
    return self.search(domain)

    # 好
    from ast import literal_eval
    domain = literal_eval(self.filter_domain)
    return self.search(domain)

解析内容不需要 ``eval``

==========  ==================  ================================
语言         数据类型             适用解析器
==========  ==================  ================================
Python      int, float, etc.    int(), float()
Javascript  int, float, etc.    parseInt(), parseFloat()
Python      dict                json.loads(), ast.literal_eval()
Javascript  object, list, etc.  JSON.parse()
==========  ==================  ================================

访问对象属性
---------------------------

如果需要动态检索或修改记录的值，可以使用 ``getattr`` 和 ``setattr`` 方法。

.. code-block:: python

    # 不安全的字段值检索
    def _get_state_value(self, res_id, state_field):
        record = self.sudo().browse(res_id)
        return getattr(record, state_field, False)

然而，这段代码并不安全，因为它允许访问记录的任何属性，包括私有属性或方法。

记录集的 ``__getitem__`` 已被定义，可以轻松安全地访问动态字段值：

.. code-block:: python

    # 更好的字段值检索
    def _get_state_value(self, res_id, state_field):
        record = self.sudo().browse(res_id)
        return record[state_field]

上述方法显然仍然过于乐观，必须对记录 ID 和字段值进行额外验证。
