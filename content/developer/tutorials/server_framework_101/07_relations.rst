===================================
第7章：模型之间的关系
===================================

在 :doc:`上一章 <06_basicviews>` 中，我们讨论了为包含基本字段的模型创建自定义视图。然而，在任何实际的业务场景中，我们需要的不止一个模型。此外，模型之间的链接是必要的。可以很容易地想象一个模型包含客户，另一个模型包含用户列表。您可能需要在任何现有的业务模型中引用客户或用户。

在我们的房地产模块中，我们希望获取物业的以下信息：

- 购买物业的客户
- 卖出物业的房地产代理
- 物业类型：房屋、公寓、顶层公寓、城堡...
- 表征物业的标签列表：舒适、翻新...
- 收到的报价列表

Many2one
========

**参考**：有关此主题的文档可以在 :class:`~odoo.fields.Many2one` 中找到。

.. 注意::

    **目标**：在本节结束时：

    - 应创建一个新的 ``estate.property.type`` 模型，并具有相应的菜单、操作和视图。

    .. image:: 07_relations/property_type.png
        :align: center
        :alt: 物业类型

    - 应向 ``estate.property`` 模型添加三个 Many2one 字段：物业类型、买家和卖家。

    .. image:: 07_relations/property_many2one.png
        :align: center
        :alt: 物业

在我们的房地产模块中，我们希望定义物业类型的概念。物业类型是例如房屋或公寓。根据物业类型对其进行分类是标准的业务需求，特别是为了精细化过滤。

一处物业可以有 **一个** 类型，但同一种类型可以分配给 **多个** 物业。这是 **many2one** 概念所支持的。

Many2one 是对另一个对象的简单链接。例如，为了在我们的测试模型中定义与 ``res.partner`` 的链接，我们可以写：

.. code-block::

    partner_id = fields.Many2one("res.partner", string="合作伙伴")

按照约定，many2one 字段有 ``_id`` 后缀。然后可以轻松访问合作伙伴的数据：

.. code-block::

    print(my_test_object.partner_id.name)

.. 另见::

    `外键 <https://www.postgresql.org/docs/12/tutorial-fk.html>`_

在实践中，many2one 可以在表单视图中视为下拉列表。

.. 练习:: 添加房地产物业类型表。

    - 创建 ``estate.property.type`` 模型，并添加以下字段：

    ========================= ========================= =========================
    字段                     类型                      属性
    ========================= ========================= =========================
    name                      Char                      required
    ========================= ========================= =========================

    - 添加如本节 **目标** 中所示的菜单
    - 将字段 ``property_type_id`` 添加到您的 ``estate.property`` 模型及其表单、树和搜索视图中

    该练习是前几章的良好回顾：您需要创建一个 :doc:`模型 <03_basicmodel>`，设置 :doc:`模型 <04_securityintro>`，添加一个 :doc:`操作和菜单 <05_firstui>`，并 :doc:`创建视图 <06_basicviews>`。

    提示：不要忘记在 ``__init__.py`` 中导入任何新的 Python 文件，或在 ``__manifest__.py`` 中添加新的数据文件，或者添加访问权限 ;-)

再次，重新启动服务器并刷新以查看结果！

在房地产模块中，我们仍然缺少两个想要在物业上获取的信息：买家和销售人员。买家可以是任何个人，但销售人员必须是房地产代理的员工（即 Odoo 用户）。

在 Odoo 中，我们通常提到两个模型：

- ``res.partner``：合作伙伴是一个自然人或法人。它可以是公司、个人或甚至是联系地址。
- ``res.users``：系统的用户。用户可以是“内部”的，即他们可以访问 Odoo 后台。或者他们可以是“门户”用户，即他们无法访问后台，只能访问前端（例如，访问他们在电子商务中的先前订单）。

.. 练习:: 添加买家和销售人员。

    使用上述两个常见模型向 ``estate.property`` 模型添加买家和销售人员。它们应在表单视图的新选项卡中添加，如本节 **目标** 中所示。

    销售人员的默认值必须为当前用户。买家应不可复制。

    提示：要获取默认值，请查看下面的注释或查看一个示例
    `这里 <https://github.com/odoo/odoo/blob/5bb8b927524d062be32f92eb326ef64091301de1/addons/crm/models/crm_lead.py#L92>`__。

.. 注意::

    对象 ``self.env`` 提供对请求参数和其他有用信息的访问：

    - ``self.env.cr`` 或 ``self._cr`` 是数据库 *游标* 对象；用于查询数据库
    - ``self.env.uid`` 或 ``self._uid`` 是当前用户的数据库 ID
    - ``self.env.user`` 是当前用户的记录
    - ``self.env.context`` 或 ``self._context`` 是上下文字典
    - ``self.env.ref(xml_id)`` 返回与 XML ID 相对应的记录
    - ``self.env[model_name]`` 返回给定模型的实例

现在，让我们看看其他类型的链接。

Many2many
=========

**参考**：有关此主题的文档可以在 :class:`~odoo.fields.Many2many` 中找到。

.. 注意::

    **目标**：在本节结束时：

    - 应创建一个新的 ``estate.property.tag`` 模型，并具有相应的菜单和操作。

    .. image:: 07_relations/property_tag.png
        :align: center
        :alt: 物业标签

    - 应向 ``estate.property`` 模型添加标签：

    .. image:: 07_relations/property_many2many.png
        :align: center
        :alt: 物业

在我们的房地产模块中，我们希望定义物业标签的概念。物业标签是例如，物业是“舒适”或“翻新”的。

一处物业可以有 **多个** 标签，而一个标签也可以分配给 **多个** 物业。这是 **many2many** 概念所支持的。

Many2many 是双向的多重关系：一侧的任何记录可以与另一侧的任意数量的记录相关联。例如，为了在我们的测试模型中定义与 ``account.tax`` 模型的链接，我们可以写：

.. code-block::

    tax_ids = fields.Many2many("account.tax", string="税收")

按照约定，many2many 字段有 ``_ids`` 后缀。这意味着可以将多个税收添加到我们的测试模型中。它表现得像一个记录列表，这意味着必须在循环中访问数据：

.. code-block::

    for tax in my_test_object.tax_ids:
        print(tax.name)

记录列表称为 *记录集*，即有序的记录集合。它支持对集合的标准 Python 操作，如 ``len()`` 和 ``iter()`，以及额外的集合操作，如 ``recs1 | recs2``。

.. 练习:: 添加房地产物业标签表。

    - 创建 ``estate.property.tag`` 模型，并添加以下字段：

    ========================= ========================= =========================
    字段                     类型                      属性
    ========================= ========================= =========================
    name                      Char                      required
    ========================= ========================= =========================

    - 添加如本节 **目标** 中所示的菜单
    - 将字段 ``tag_ids`` 添加到您的 ``estate.property`` 模型及其表单和树视图中

    提示：在视图中，使用 ``widget="many2many_tags"`` 属性，如在
    `这里 <https://github.com/odoo/odoo/blob/5bb8b927524d062be32f92eb326ef64091301de1/addons/crm_iap_lead_website/views/crm_reveal_views.xml#L36>`__ 所演示的。
    ``widget`` 属性将在培训的 :doc:`后续章节 <11_sprinkles>` 中详细解释。现在，您可以尝试添加和删除它，看看结果 ;-)

One2many
========

**参考**：有关此主题的文档可以在 :class:`~odoo.fields.One2many` 中找到。

.. 注意::

    **目标**：在本节结束时：

    - 应创建一个新的 ``estate.property.offer`` 模型，并具有相应的表单和树视图。
    - 应向 ``estate.property`` 模型添加报价：

    .. image:: 07_relations/property_offer.png
        :align: center
        :alt: 物业报价

在我们的房地产模块中，我们希望定义物业报价的概念。物业报价是潜在买家向卖家提供的金额。报价可以低于或高于预期价格。

一份报价适用于 **一个** 物业，但同一物业可以有 **多个** 报价。**many2one** 概念再次出现。然而，在这种情况下，我们希望显示给定物业的报价列表，因此我们将使用 **one2many** 概念。

One2many 是 many2one 的反向关系。例如，我们在测试模型上定义了与 ``res.partner`` 模型的链接，借助于字段 ``partner_id``。我们可以定义反向关系，即与我们的合作伙伴链接的测试模型列表：

.. code-block::

    test_ids = fields.One2many("test_model", "partner_id", string="测试")

第一个参数称为 ``comodel``，第二个参数是我们想要反向的字段。

按照约定，one2many 字段有 ``_ids`` 后缀。它们表现得像一个记录列表，这意味着必须在循环中访问数据：

.. code-block::

    for test in partner.test_ids:
        print(test.name)

.. 危险::

    因为 :class:`~odoo.fields.One2many` 是一种虚拟关系，
    必须在 co模型中定义一个 :class:`~odoo.fields.Many2one` 字段。

.. 练习:: 添加房地产物业报价表。

    - 创建 ``estate.property.offer`` 模型，并添加以下字段：

    ========================= ================================ ============= =================
    字段                     类型                             属性        值
    ========================= ================================ ============= =================
    price                     Float
    status                    Selection                        no copy       Accepted, Refused
    partner_id                Many2one (``res.partner``)       required
    property_id               Many2one (``estate.property``)   required
    ========================= ================================ ============= =================

    - 创建一个树视图和一个表单视图，包含 ``price``、``partner_id`` 和 ``status`` 字段。不需要创建操作或菜单。
    - 将字段 ``offer_ids`` 添加到您的 ``estate.property`` 模型及其表单视图，如本节 **目标** 中所示。

这里有几个重要的事情需要注意。首先，我们不需要为所有模型创建操作或菜单。有些模型的设计仅用于通过另一个模型访问。这正是我们练习中的情况：报价总是通过物业访问。

其次，尽管 ``property_id`` 字段是必需的，但我们并没有将其包含在视图中。Odoo 如何知道我们的报价与哪个物业相关？这就是使用 Odoo 框架的魔力的一部分：有时事情是隐式定义的。当我们通过 one2many 字段创建记录时，相应的 many2one 会自动填充，以便于使用。

还好吗？这一章绝对不是最简单的。它引入了一些新概念，同时依赖于之前介绍的所有内容。 :doc:`下一章 <08_compute_onchange>` 将更轻松，请不要担心 ;-) 
