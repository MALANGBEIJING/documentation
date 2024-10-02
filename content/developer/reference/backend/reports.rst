.. highlight:: xml

.. _reference/reports:

.. _reference/reports/report:

============
QWeb 报告
============

报告采用 HTML/QWeb 编写，类似于 Odoo 中的网站视图。您可以使用
常规的 :ref:`QWeb 控制流工具 <reference/qweb>`。PDF 渲染本身由 wkhtmltopdf_ 执行。

报告通过 :ref:`报告操作 <reference/actions/report>` 声明，并为操作使用 :ref:`reference/reports/templates`。

如果有用或必要，可以为报告指定 :ref:`reference/reports/paper_formats`。

.. _reference/reports/templates:

报告模板
===============

报告模板将始终提供以下变量：

``time`` 
    引用 Python 标准库中的 :mod:`python:time`
``user`` 
    打印报告的用户的 ``res.user`` 记录
``res_company`` 
    当前 ``user`` 的公司的记录
``website`` 
    当前网站对象（如果有，此项可能存在但为 ``None``）
``web_base_url`` 
    Web 服务器的基本 URL
``context_timestamp`` 
    一个函数，接受 UTC 中的 :class:`python:datetime.datetime` \ [#unzoned]_ 并将其转换为打印报告的用户的时区

最小可行模板
-----------------------

一个最小的模板看起来像这样::

    <template id="report_invoice">
        <t t-call="web.html_container">
            <t t-foreach="docs" t-as="o">
                <t t-call="web.external_layout">
                    <div class="page">
                        <h2>报告标题</h2>
                        <p>该对象的名称是 <span t-field="o.name"/></p>
                    </div>
                </t>
            </t>
        </t>
    </template>

调用 ``external_layout`` 将在报告中添加默认的页眉和页脚。PDF 的主体将是 ``<div
class="page">`` 内的内容。模板的 ``id`` 必须是报告声明中指定的名称；例如，上述报告的 ``account.report_invoice``。由于这是一个 QWeb 模板，您可以访问模板接收到的 ``docs`` 对象的所有字段。

默认情况下，渲染上下文还将暴露以下项目：

``docs`` 
    当前报告的记录
``doc_ids`` 
    ``docs`` 记录的 ID 列表
``doc_model`` 
    ``docs`` 记录的模型

如果您希望在模板中访问其他记录/模型，您需要 :ref:`自定义报告 <reference/reports/custom_reports>`，但在这种情况下，如果需要，您必须提供上述项目。

可翻译模板
----------------------

如果您希望将报告翻译为（例如）合作伙伴的语言，则需要定义两个模板：

* 主报告模板
* 可翻译文档

然后，您可以在主模板中调用可翻译文档，并将属性 ``t-lang`` 设置为语言代码（例如 ``fr`` 或 ``en_US``）或记录字段。
如果您使用的是可翻译的字段（如国家名称、销售条件等），则还需要使用适当的上下文重新浏览相关记录。

.. warning::

    如果您的报告模板未使用可翻译的记录字段，则在另一种语言中重新浏览记录 *不是* 必要的，这将影响性能。

例如，让我们看一下销售模块中的销售订单报告::

    <!-- 主模板 -->
    <template id="report_saleorder">
        <t t-call="web.html_container">
            <t t-foreach="docs" t-as="doc">
                <t t-call="sale.report_saleorder_document" t-lang="doc.partner_id.lang"/>
            </t>
        </t>
    </template>

    <!-- 可翻译模板 -->
    <template id="report_saleorder_document">
        <!-- 使用合作伙伴语言重新浏览记录 -->
        <t t-set="doc" t-value="doc.with_context(lang=doc.partner_id.lang)" />
        <t t-call="web.external_layout">
            <div class="page">
                <div class="oe_structure"/>
                <div class="row">
                    <div class="col-6">
                        <strong t-if="doc.partner_shipping_id == doc.partner_invoice_id">发票和送货地址：</strong>
                        <strong t-if="doc.partner_shipping_id != doc.partner_invoice_id">发票地址：</strong>
                        <div t-field="doc.partner_invoice_id" t-options="{&quot;no_marker&quot;: True}"/>
                    <...>
                <div class="oe_structure"/>
            </div>
        </t>
    </template>


主模板通过 ``doc.partner_id.lang`` 作为 ``t-lang`` 参数调用可翻译模板，因此它将以合作伙伴的语言呈现。这样，每个销售订单将在相应客户的语言中打印。如果您希望仅翻译文档的主体，但保持页眉和页脚为默认语言，则可以这样调用报告的外部布局::

    <t t-call="web.external_layout" t-lang="en_US">

.. tip::

    请注意，这仅在调用外部模板时有效，您将无法通过在 XML 节点上设置 ``t-lang`` 属性来翻译文档的一部分，除了 ``t-call``。如果您希望翻译模板的一部分，可以创建一个外部模板并调用主模板，并使用 ``t-lang`` 属性。

条形码
--------

条形码是由控制器返回的图像，可以通过 QWeb 语法轻松嵌入报告中（例如，参见 :ref:`reference/qweb/attributes`）：

.. code-block:: html

    <img t-att-src="'/report/barcode/QR/%s' % '我的二维码文本'"/>

更多参数可以作为查询字符串传递

.. code-block:: html

    <img t-att-src="'/report/barcode/?
        barcode_type=%s&amp;value=%s&amp;width=%s&amp;height=%s'%('QR', 'text', 200, 200)"/>


有用的备注
--------------

* Twitter Bootstrap 和 FontAwesome 类可以在您的报告模板中使用
* 本地 CSS 可以直接放入模板中
* 全局 CSS 可以通过继承其模板并插入您的 CSS 来插入主报告布局::

    <template id="report_saleorder_style" inherit_id="report.style">
      <xpath expr=".">
        <t>
          .example-css-class {
            background-color: red;
          }
        </t>
      </xpath>
    </template>

* 如果您的 PDF 报告缺少样式，请检查 :ref:`这些说明 <reference/backend/reporting/printed-reports/pdf-without-styles>`。

.. _reference/reports/paper_formats:

纸张格式
============

纸张格式是 ``report.paperformat`` 的记录，可以包含以下属性：

``name`` (必填)
    在某种列表中查找报告时，仅用于助记符/描述
``description`` 
    您格式的小描述
``format`` 
    预定义格式（A0 至 A9，B0 至 B10，Legal，Letter，Tabloid，...）或 ``custom``；默认情况下为 A4。如果您定义页面尺寸，则不能使用非自定义格式。
``dpi`` 
    输出 DPI；默认值为 90
``margin_top`` , ``margin_bottom`` , ``margin_left`` , ``margin_right`` 
    边距大小（以毫米为单位）
``page_height`` , ``page_width`` 
    页面尺寸（以毫米为单位）
``orientation`` 
    横向或纵向
``header_line`` 
    布尔值以显示页眉线
``header_spacing`` 
    页眉间距（以毫米为单位）

示例::

    <record id="paperformat_frenchcheck" model="report.paperformat">
        <field name="name">法国银行支票</field>
        <field name="default" eval="True"/>
        <field name="format">custom</field>
        <field name="page_height">80</field>
        <field name="page_width">175</field>
        <field name="orientation">纵向</field>
        <field name="margin_top">3</field>
        <field name="margin_bottom">3</field>
        <field name="margin_left">3</field>
        <field name="margin_right">3</field>
        <field name="header_line" eval="False"/>
        <field name="header_spacing">3</field>
        <field name="dpi">80</field>
    </record>

.. _reference/reports/custom_reports:
纸张格式
============

纸张格式是 ``report.paperformat`` 的记录，可以包含以下属性：

``name`` (必填)
    在某种列表中查找报告时，仅用于助记符/描述
``description`` 
    您格式的小描述
``format`` 
    预定义格式（A0 至 A9，B0 至 B10，Legal，Letter，Tabloid，...）或 ``custom``；默认情况下为 A4。如果您定义页面尺寸，则不能使用非自定义格式。
``dpi`` 
    输出 DPI；默认值为 90
``margin_top`` , ``margin_bottom`` , ``margin_left`` , ``margin_right`` 
    边距大小（以毫米为单位）
``page_height`` , ``page_width`` 
    页面尺寸（以毫米为单位）
``orientation`` 
    横向或纵向
``header_line`` 
    布尔值以显示页眉线
``header_spacing`` 
    页眉间距（以毫米为单位）

示例::

    <record id="paperformat_frenchcheck" model="report.paperformat">
        <field name="name">法国银行支票</field>
        <field name="default" eval="True"/>
        <field name="format">custom</field>
        <field name="page_height">80</field>
        <field name="page_width">175</field>
        <field name="orientation">纵向</field>
        <field name="margin_top">3</field>
        <field name="margin_bottom">3</field>
        <field name="margin_left">3</field>
        <field name="margin_right">3</field>
        <field name="header_line" eval="False"/>
        <field name="header_spacing">3</field>
        <field name="dpi">80</field>
    </record>

自定义报告
==============

默认情况下，报告系统根据通过 ``model`` 字段指定的目标模型构建渲染值。

但是，它首先会查找名为
:samp:`report.{module.report_name}` 的模型，并调用该模型的
``_get_report_values(doc_ids, data)`` 以准备模板的渲染数据。

这可以用于包含在渲染模板时使用或显示的任意项目，例如来自其他模型的数据：

.. code-block:: python

    from odoo import api, models

    class ParticularReport(models.AbstractModel):
        _name = 'report.module.report_name'

        def _get_report_values(self, docids, data=None):
            # 获取报告操作，因为我们将需要其数据
            report = self.env['ir.actions.report']._get_report_from_name('module.report_name')
            # 获取为此报告的渲染所选的记录
            obj = self.env[report.model].browse(docids)
            # 返回自定义渲染上下文
            return {
                'lines': docids.get_lines()
            }

.. warning::

    使用自定义报告时，“默认”与文档相关的项目
    ( ``doc_ids`` 、 ``doc_model`` 和 ``docs``) 将 *不会* 被包含。如果您
    想要它们，您需要自己包含它们。

    在上面的示例中，渲染上下文将包含“全局”值
    以及我们放入的 ``lines``，但没有其他内容。

自定义字体
============

如果您想使用自定义字体，则需要将自定义字体及相关的 less/CSS 添加到 ``web.reports_assets_common`` 资源包中。
将自定义字体添加到 ``web.assets_common`` 或 ``web.assets_backend`` 不会使您的字体在 QWeb 报告中可用。

示例::

    <template id="report_assets_common_custom_fonts" name="自定义 QWeb 字体" inherit_id="web.report_assets_common">
        <xpath expr="." position="inside">
            <link href="/your_module/static/src/less/fonts.less" rel="stylesheet" type="text/less"/>
        </xpath>
    </template>

您需要在此 less 文件中定义您的 ``@font-face``，即使您已在其他资产包中使用（与 ``web.reports_assets_common`` 不同）。

示例::

    @font-face {
        font-family: 'MonixBold';
        src: local('MonixBold'), local('MonixBold'), url(/your_module/static/fonts/MonixBold-Regular.otf) format('opentype');
    }

    .h1-title-big {
        font-family: MonixBold;
        font-size: 60px;
        color: #3399cc;
    }

在将 less 添加到您的资产包后，您可以在自定义 QWeb 报告中使用这些类 - 在此示例中为 ``h1-title-big``。

报告是网页
=====================

报告由报告模块动态生成，可以通过 URL 直接访问：

例如，您可以通过访问
\http://<server-address>/report/html/sale.report_saleorder/38 以 html 模式访问销售订单报告。

或者，您可以通过访问
\http://<server-address>/report/pdf/sale.report_saleorder/38 来访问 PDF 版本。

.. [#unzoned] 它实际上不关心 :class:`python:datetime`
              对象处于什么时区（包括没有时区），其时区将
              无条件设置为 UTC，然后调整为用户的时区。

.. _wkhtmltopdf: https://wkhtmltopdf.org
