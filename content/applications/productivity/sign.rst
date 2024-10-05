====
签名
====

**Odoo Sign** 允许您在线发送、签署和批准文件，使用电子签名。

**电子签名** 表示签署人对文件内容的同意。就像手写签名一样，电子签名也代表签署文件的条款具有法律约束力。

使用 Sign，您可以上传任何 PDF 文件并在其上添加字段。这些字段可以自动填充用户在数据库中的详细信息。

.. seealso::
   - `Odoo Sign: 产品页面 <https://www.odoo.com/app/sign>`_
   - `Odoo 教程: 电子签名 [视频] <https://www.odoo.com/slides/sign-61>`_

电子签名的有效性
=================

通过 Sign 应用签署的文件在欧盟和美国是有效的电子签名。它们还符合大多数国家对电子签名的要求。Odoo 生成的电子签名的法律效力取决于您所在国家的法律。跨国企业还应考虑其他国家的电子签名法律。

.. important::
   以下信息不具有法律效力，仅供一般参考。由于电子签名法律迅速变化，我们无法保证所有信息都是最新的。我们建议您联系本地律师，了解有关电子签名合规性和有效性的法律建议。

欧盟
----

`eIDAS 法规 <http://data.europa.eu/eli/reg/2014/910/oj>`_ 建立了电子签名在 `欧盟 27 个成员国
<https://europa.eu/european-union/about-eu/countries_en>`_ 的框架。它区分了三种类型的电子签名：

#. 简单电子签名
#. 高级电子签名
#. 合格的电子签名

Odoo 生成的属于第一种类型，**简单电子签名**；根据 eIDAS 法规，这些签名在欧盟是法律上有效的。

电子签名可能不会自动被视为有效。您可能需要提供支持证据来证明签名的有效性。虽然 Sign 应用提供了简单的电子签名，但签名过程中会自动收集一些支持证据，例如：

#. 电子邮件和短信验证（如果启用）
#. 通过 itsme® 提供强身份验证（适用于比利时和荷兰）
#. 带时间戳的 IP 地址和地理位置可追踪的文件访问日志及其相关签名
#. 文件的可追溯性和不可更改性（对已签署文件的任何修改都会被 Odoo 通过加密证明检测到）

美国
----

`ESIGN 法案 (全球与国家商务中的电子签名法案)
<https://www.fdic.gov/regulations/compliance/manual/10/X-3.1.pdf>`_，适用于州际和国际层面，`UETA (统一电子交易法)
<https://www.uniformlaws.org/committees/community-home/librarydocuments?communitykey=2c04b76c-2b7d-4399-977e-d5876ba7e034&tab=librarydocuments>`_ 适用于州一级。请注意，`伊利诺伊州
<https://www.ilga.gov/legislation/ilcs/ilcs5.asp?ActID=89&>`_ 和 `纽约州
<https://its.ny.gov/electronic-signatures-and-records-act-esra>`_ 尚未采用 UETA，但采用了类似的法律。

总的来说，要被认定为有效，电子签名必须满足五个标准：

#. 签署人必须明确表示有 **签署意图**。例如，使用鼠标绘制签名可以显示签署意图。签署人还必须有机会选择退出电子文件。
#. 签署人必须首先明确或隐含其 **同意以电子方式处理业务**。
#. **签名必须明确归属**。在 Odoo 中，签名会添加元数据，例如签署人的 IP 地址，这些元数据可以用作支持证据。
#. **签名必须与签署的文件关联**，例如，保留一份记录，详细说明如何捕获签名。
#. 电子签署的文件需要 **由所有相关方保留和存储**；例如，通过向签署人提供完整执行的副本或下载副本的可能性。

其他国家
--------

- :doc:`阿尔及利亚 <sign/algeria>`
- :doc:`安哥拉 <sign/angola>`
- :doc:`阿根廷 <sign/argentina>`
- :doc:`澳大利亚 <sign/australia>`
- :doc:`阿塞拜疆 <sign/azerbaijan>`
- :doc:`孟加拉国 <sign/bangladesh>`
- :doc:`巴西 <sign/brazil>`
- :doc:`加拿大 <sign/canada>`
- :doc:`智利 <sign/chile>`
- :doc:`中国 <sign/china>`
- :doc:`哥伦比亚 <sign/colombia>`
- :doc:`多米尼加共和国 <sign/dominican_republic>`
- :doc:`厄瓜多尔 <sign/ecuador>`
- :doc:`埃及 <sign/egypt>`
- :doc:`埃塞俄比亚 <sign/ethiopia>`
- :doc:`危地马拉 <sign/guatemala>`
- :doc:`香港 <sign/hong_kong>`
- :doc:`印度 <sign/india>`
- :doc:`印度尼西亚 <sign/indonesia>`
- :doc:`伊朗 <sign/iran>`
- :doc:`伊拉克 <sign/iraq>`
- :doc:`以色列 <sign/israel>`
- :doc:`日本 <sign/japan>`
- :doc:`哈萨克斯坦 <sign/kazakhstan>`
- :doc:`肯尼亚 <sign/kenya>`
- :doc:`科威特 <sign/kuwait>`
- :doc:`马来西亚 <sign/malaysia>`
- :doc:`墨西哥 <sign/mexico>`
- :doc:`摩洛哥 <sign/morocco>`
- :doc:`新西兰 <sign/new_zealand>`
- :doc:`尼日利亚 <sign/nigeria>`
- :doc:`挪威 <sign/norway>`
- :doc:`阿曼 <sign/oman>`
- :doc:`巴基斯坦 <sign/pakistan>`
- :doc:`秘鲁 <sign/peru>`
- :doc:`菲律宾 <sign/philippines>`
- :doc:`卡塔尔 <sign/qatar>`
- :doc:`俄罗斯 <sign/russia>`
- :doc:`沙特阿拉伯 <sign/saudi_arabia>`
- :doc:`新加坡 <sign/singapore>`
- :doc:`南非 <sign/south_africa>`
- :doc:`韩国 <sign/south_korea>`
- :doc:`瑞士 <sign/switzerland>`
- :doc:`泰国 <sign/thailand>`
- :doc:`土耳其 <sign/turkey>`
- :doc:`乌克兰 <sign/ukraine>`
- :doc:`阿联酋 <sign/united_arab_emirates>`
- :doc:`英国 <sign/united_kingdom>`
- :doc:`乌兹别克斯坦 <sign/uzbekistan>`
- :doc:`越南 <sign/vietnam>`

发送需要签名的文件
=================

一次性签名
----------

您可以通过仪表板点击 :guilabel:`上传 PDF 进行签名` 进行一次性签名。选择您的文件，打开它，然后拖放所需的 :ref:`字段 <sign/fields>` 到文件中。您可以通过点击字段并选择您想要的角色来修改字段的 :ref:`角色 <sign/role>`。

准备就绪后，点击 :guilabel:`发送`，填写必要的字段。发送后，您的文件仍然可用。前往 :menuselection:`文档 --> 所有文档` 查看您的文件和签名状态。

.. image:: sign/signature-status.png
   :alt: 签名状态

有效期和提醒
~~~~~~~~~~~~~~

您可以在有限期限的协议文件上设置 **有效期** 或发送 **自动电子邮件提醒** 以按时获得签名。通过仪表板，点击文件上的 :guilabel:`发送`。在新页面中，前往 :guilabel:`选项` 部分并填写 :guilabel:`有效期至` 和 :guilabel:`提醒` 字段。

模板
----
您可以创建文档模板，以便在需要多次发送相同文档时使用。在您的仪表板上，点击 :guilabel:`上传 PDF 模板`。选择文档并添加所需的 :ref:`字段 <sign/fields>`。您可以通过点击字段并选择所需的角色来修改字段的 :ref:`角色 <sign/role>`。

点击 :guilabel:`模板属性` 以向模板添加 :guilabel:`标签`，定义 :guilabel:`签署文档的工作区`，添加 :guilabel:`签署文档标签`，设置 :guilabel:`重定向链接`（签署人完成签署后可在签署确认消息中看到），或定义 :guilabel:`授权用户`，以限制模板的使用范围。

默认情况下，您的模板会显示在仪表板上。您可以点击 :guilabel:`发送` 快速将文档模板发送给签署人，或点击 :guilabel:`立即签署` 以立刻开始签署文档。

.. tip::
   您可以从之前发送的文档中 **创建模板**。为此，前往 :menuselection:`文档 --> 所有文档`，找到您想要的文档，点击垂直省略号 (:guilabel:`⋮`)，然后点击 :guilabel:`模板`。再次点击垂直省略号 (:guilabel:`⋮`)，然后点击 :guilabel:`恢复`。现在，该文档会出现在仪表板中的模板列表中。

.. _sign/role:

角色
=====

在 Sign 文档中，每个字段都与一个特定人的角色相关。当文档需要签署时，分配该角色的人必须填写他们分配的字段并签署文档。

可以通过进入 :menuselection:`Sign --> 配置 --> 角色` 查看所有角色。

您可以更新现有的角色或点击 :guilabel:`新建` 来创建新角色。为角色选择一个 :guilabel:`角色名称`，添加一个 :guilabel:`额外的身份验证步骤` 以确认签署人的身份，如果文档可以重新分配给其他联系人，请选择 :guilabel:`允许更改`。您还可以为角色选择一个 :guilabel:`颜色`，在配置模板时，该颜色有助于了解哪个角色负责哪个字段。

安全身份验证
------------

作为文档的所有者，您可以通过 :ref:`短信验证 <sign/sms>` 或 :ref:`Itsme® <sign/itsme>`（适用于比利时和荷兰）请求 :guilabel:`额外的身份验证步骤`。两种身份验证选项都需要使用 :ref:`积分 <iap/buying_credits>`。如果您没有积分，身份验证步骤将被跳过。

.. seealso::
   - :doc:`应用内购买 (IAP) <../essentials/in_app_purchase>`
   - :doc:`短信定价和常见问题 <../marketing/sms_marketing/pricing_and_faq>`

.. _sign/sms:

短信验证
~~~~~~~~

前往 :menuselection:`Sign --> 配置 --> 角色`，在 :guilabel:`额外身份验证步骤` 列中点击，然后选择 :guilabel:`通过短信发送验证码`。

.. note::
   在能够发送短信之前，您需要注册您的电话号码。为此，前往 :menuselection:`Sign --> 配置 --> 设置` 并在 :guilabel:`通过短信验证身份` 下点击 :guilabel:`购买积分`。

前往要签署的文档，添加需要短信验证的字段，例如 :guilabel:`签名` 字段，然后点击 :guilabel:`发送`。在新页面上，选择 :guilabel:`客户`，然后点击 :guilabel:`发送`。

签署人填写 :guilabel:`签名` 字段后，点击 :guilabel:`签署` 并点击 :guilabel:`验证并发送已完成文档`。弹出一个 :guilabel:`最终验证` 页面，要求输入他们的电话号码。验证码将通过短信发送。

.. image:: sign/sms-verification.png
   :alt: 向文档添加哈希值

.. note::
   - 该功能默认启用。
   - 只要角色启用了 :guilabel:`额外身份验证步骤`，任何分配给该角色的字段都会要求验证步骤。

.. _sign/itsme:

Itsme®
~~~~~~
Itsme® 验证可以用于允许签署人通过 Itsme® 提供他们的身份验证。此功能仅在 **比利时** 和 **荷兰** 可用。

可以在 :guilabel:`签署设置` 中启用该功能，并自动应用于 :guilabel:`客户（使用 Itsme® 认证）` 角色。要为其他角色启用该功能，请前往 :menuselection:`签署 --> 配置 --> 角色`。在 :guilabel:`额外身份验证步骤` 列中点击并选择 :guilabel:`通过 Itsme®`。

进入需要签署的文档并添加 :guilabel:`签名` 字段。切换到任何已配置使用该功能的角色，点击 :guilabel:`验证` 并 :guilabel:`发送`。

.. image:: sign/itsme-identification.png
   :alt: 选择使用 Itsme® 验证的客户

在签署文档时，签署人完成 :guilabel:`签名` 字段后，点击 :guilabel:`验证并发送已完成的文档`，触发一个 :guilabel:`最终验证` 页面，要求通过 Itsme® 进行身份验证。

签署人哈希
==========

每当有人签署文档时，会生成一个 **哈希值**，这是一种独特的数字签名，用于确保可追溯性、完整性和不可篡改性。此过程确保在签署后对文档所做的任何更改都能被轻松检测到，从而维护文档的真实性和安全性。

一个显示哈希值开头的可视安全框被添加到签名中。内部用户可以通过在签署文档时启用或禁用 :guilabel:`框架` 选项来隐藏或显示它。

.. image:: sign/sign-hash.png
   :alt: 向签名添加可视安全框

.. _sign/field-types:

标签
====

标签可以用于对文档进行分类和组织，允许用户根据特定条件快速搜索和筛选文档。

您可以通过进入 :menuselection:`配置 --> 标签` 来管理标签。要创建一个标签，点击 :guilabel:`新建`。在新行中，添加 :guilabel:`标签名称` 并为您的标签选择一个 :guilabel:`颜色索引`。

要将标签应用于文档，请使用文档中可用的下拉列表。

.. note::
   您可以通过前往 :menuselection:`文档 --> 所有文档`，点击文档上的垂直省略号 (:guilabel:`⋮`)，然后点击 :guilabel:`详细信息` 来修改已签署文档的标签。

签署顺序
==========

当需要由不同方签署文档时，签署顺序让您可以控制收件人接收签署请求的顺序。

上传一个包含至少两个签名字段和两个不同角色的 PDF 并点击 :guilabel:`发送` 后，切换 :guilabel:`指定签署顺序` 开关，搜索签署人的姓名或电子邮件信息并将其添加。您可以通过在第一列中输入 **1** 或 **2** 来决定签署顺序。

.. image:: sign/specify-signing-order.png
   :alt: 切换开关以指定签署顺序

每个收件人只有在前一个收件人完成其操作后才会收到签署请求通知。

.. _sign/fields:

字段类型
===========

字段用于文档中，以指示签署人需要填写哪些信息。您可以简单地通过将左栏的字段拖放到文档中来添加字段。

多种字段类型可用于签署文档（占位符、自动填充等）。通过配置自己的字段类型，也称为签名项类型，可以使您的客户、合作伙伴和员工的签署过程更加快捷。

要创建和编辑字段类型，请前往 :menuselection:`签署 --> 配置 --> 设置 --> 编辑字段类型`。

您可以通过点击选择现有字段，或者点击 :guilabel:`创建` 来创建一个新字段。首先编辑 :guilabel:`字段名称`，然后选择一个 :guilabel:`字段类型`：

- :guilabel:`签名`: 要求用户输入签名，方式可以是手动绘制、基于他们的姓名自动生成签名，或者上传本地文件（通常为图像）。随后的每个 :guilabel:`签名` 字段类型将重复使用在第一个字段中输入的数据。
- :guilabel:`首字母`: 要求用户输入首字母，与 :guilabel:`签名` 字段类似。
- :guilabel:`文本`: 用户输入单行文本。
- :guilabel:`多行文本`: 用户输入多行文本。
- :guilabel:`复选框`: 用户可以勾选复选框（例如，标记他们的批准或同意）。
- :guilabel:`选择`: 用户从多个选项中选择一个。

:guilabel:`自动填充合作伙伴字段` 设置用于在签署过程中自动填充字段。它使用签署人联系人 (`res.partner`) 模型中的一个字段值。为此，输入联系人模型字段的技术名称。

.. tip::
   要了解字段的技术名称，请启用开发者模式，并将鼠标悬停在字段旁边的问号上。

.. note::
   自动填充的值只是建议，签署人可以根据需要修改这些值。
字段的大小也可以通过编辑 :guilabel:`默认宽度` 和 :guilabel:`默认高度` 来更改。这两个大小均以整个页面的百分比形式表示为小数，其中 1 表示整个页面的宽度或高度。默认情况下，您创建的新字段的宽度设置为页面宽度的 15% (0.150)，而高度设置为页面高度的 1.5% (0.015)。

接下来，编写一个 :guilabel:`提示`。提示在签署过程中显示在用户屏幕左侧的箭头内，帮助用户理解步骤的具体要求（例如，“在此签名”或“填写您的出生日期”）。您还可以使用 :guilabel:`占位符` 文本，在字段填写前显示。

.. image:: sign/tip-placeholder.png
   :alt: Odoo Sign 中的提示和占位符示例
