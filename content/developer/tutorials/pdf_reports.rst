=================
生成 PDF 报告
=================

.. important::
   本教程是 :doc:`server_framework_101` 教程的扩展。确保您已经完成了该教程，并使用您构建的 `estate` 模块作为本教程中练习的基础。

我们之前 :doc:`介绍了 QWeb <server_framework_101/14_qwebintro>`，并将其用于构建看板视图。现在我们将扩展 QWeb 的另一个主要用途：创建 PDF 报告。常见的业务需求之一是生成文档，以发送给客户或供内部使用。这些报告可以用于汇总和展示信息，支持业务的各个方面。Odoo 还可以轻松地为我们的报告添加公司抬头和页脚，几乎不需要额外的努力。

与此主题相关的文档可在 :ref:`reference/qweb`、:ref:`reference/reports/report` 和操作参考的 :ref:`reference/actions/report` 部分找到。

文件结构
==============

PDF 报告的主体是其 QWeb 模板。通常，它还需要一个对应的 ``ir.actions.report``，以将报告纳入模块的业务逻辑中。文件名或其存放位置没有严格的规定，但这两个部分通常位于模块目录顶层的 ``report`` 文件夹中。如果一个模块包含多个较长的报告模板，它们通常按逻辑划分为不同的文件，并根据它们所包含的报告命名。所有报告的操作通常存储在同一个以 ``_reports.xml`` 结尾的文件中，无论包含多少报告。

因此，您的工作目录应如下所示：

.. code-block:: bash

  estate
  ├── models
  │   ├── *.py
  │   └── __init__.py
  ├── report
  │   ├── estate_property_templates.xml
  │   └── estate_property_reports.xml
  ├── security
  │   └── ir.model.access.csv
  ├── views
  │   └── *.xml
  ├── __init__.py
  └── __manifest__.py

不要忘记将您的模板和操作视图所在的文件添加到 ``__manifest__.py`` 中。在本例中，您需要将这些文件添加到 ``data`` 列表中，并记住，声明文件中的文件是按顺序加载的！

基础报告
============

.. note::

    **目标**: 在本节结束时，我们将能够打印显示某个房产所有报价的报告。

    .. image:: pdf_reports/simple_report.png
      :align: center
      :alt: 简单 PDF 报告

在我们的房地产示例中，我们可以创建许多有用的报告。我们可以创建的一个简单报告是显示房产所有报价的报告。

报告数据
-----------

在我们开始之前，首先需要一些数据来填充我们的报告，否则本教程将不会很有趣。创建报告时，您需要一些数据来测试您的报告代码，并检查生成的外观是否符合预期。最好使用可以覆盖大多数或所有预期使用案例的数据集。我们简单报告的良好代表集是：

* 至少 3 个房产，其中 1 个已“售出”，1 个“收到报价”，1 个“新建”。
* 对于“售出”和“收到报价”的房产，至少有 2-3 个报价。

如果您还没有这样的数据集，您可以：

* 完成 :doc:`define_module_data` 教程（如果还没有完成），并将这些额外的情况添加到您的演示数据中（可能需要创建一个新数据库来加载演示数据）。
* 手动在您的数据库中创建数据。
* 复制此 `数据文件 <https://github.com/odoo/technical-training-solutions/blob/{BRANCH}-J_reports/estate/data/estate_demo.xml>`_ 到您的 estate 模块的新目录（data）中，并将 `这些行 <https://github.com/odoo/technical-training-solutions/blob/{BRANCH}-J_reports/estate/__manifest__.py#L21-L23>`_ 复制到您的 __manifest__.py 文件中（可能需要创建一个新数据库来加载演示数据）。

在继续之前，请点击您的数据库中的数据，确保数据符合预期。当然，您也可以在编写报告代码后添加数据，但这样做会让您在编写代码时无法逐步测试代码的各个部分。对于复杂报告，长期来看，这可能会增加查找错误和调试代码的难度。

最小模板
----------------

在 "最小可行模板" 部分中可以查看最小可行模板的示例 :ref:`reference/reports/templates` 文档。我们可以修改该示例来构建我们房产报价的最小模板文件：

.. code-block:: xml

    <?xml version="1.0" encoding="UTF-8" ?>
    <odoo>
        <template id="report_property_offers">
            <t t-foreach="docs" t-as="property">
                <t t-call="web.html_container">
                    <t t-call="web.external_layout">
                        <div class="page">
                            <h2>
                                <span t-field="property.name"/>
                            </h2>
                            <div>
                                <strong>预期价格: </strong>
                                <span t-field="property.expected_price"/>
                            </div>
                            <table class="table">
                                <thead>
                                    <tr>
                                        <th>价格</th>
                                    </tr>
                                </thead>
                                <tbody>
                                    <t t-set="offers" t-value="property.mapped('offer_ids')"/>
                                    <tr t-foreach="offers" t-as="offer">
                                        <td>
                                            <span t-field="offer.price"/>
                                        </td>
                                    </tr>
                                </tbody>
                            </table>
                        </div>
                    </t>
                </t>
            </t>
        </template>
    </odoo>

文件中的大多数 Odoo 特定项（即非 HTML 项）都在最小可行模板部分中进行了说明。我们的模板中的一些其他功能包括：

* 使用 ``class="table"`` 属性，使我们的表格具有良好的格式。Twitter Bootstrap（我们在此使用其表格类）和 Font Awesome（用于添加图标）类可以在报告模板中使用。
* 使用 ``t-set``、``t-value``、``t-foreach`` 和 ``t-as``，以便我们可以循环遍历所有 ``offer_ids``。

如果您已经熟悉网站模板引擎，那么 QWeb 指令（即 `t-` 命令）可能不需要太多解释，您只需查看其 :ref:`文档 <reference/qweb>`，然后跳到下一小节。

否则，建议您阅读更多关于它们的信息（`Wikipedia <https://en.wikipedia.org/wiki/Template_processor>`__ 提供了一个很好的高级描述），但其一般思想是，QWeb 提供了根据 Odoo 数据和简单命令动态生成网页代码的能力。即 QWeb 可以访问记录集数据（和方法）并处理简单的编程操作，例如设置和访问临时变量。例如，在上述示例中：

* ``t-set`` 创建一个名为 "offers" 的临时变量，其值通过 ``t-value`` 设置为当前 ``estate.property`` 记录集的 ``offer_ids``。
* ``t-foreach`` 和 ``t-as`` 的用法相当于 Python 中的：

.. code-block:: Python

  for offer in offers:

报告操作
-------------

现在我们有了模板，我们需要通过 ``ir.actions.report`` 使其在应用中可访问。
``ir.actions.report`` 的一个实际示例可以在 `这里 <https://github.com/odoo/odoo/blob/0e12fa135882cd5095dbf15fe2f64231c6a84336/addons/event/report/event_event_reports.xml#L20-L30>`__ 查看，其对应的
`模板 <https://github.com/odoo/odoo/blob/0e12fa135882cd5095dbf15fe2f64231c6a84336/addons/event/report/event_event_templates.xml#L5>`__。
其内容在 :ref:`文档 <reference/actions/report>` 中进行了详细解释。

``ir.actions.report`` 主要通过模型视图的打印菜单使用。在实际示例中，``binding_model_id`` 指定了报告应显示在哪些模型视图中，Odoo 会自动为您添加它。报告操作的另一个常见用例是将其链接到按钮，如我们在 :doc:`server_framework_101/09_actions` 中所学的。这对于仅在特定条件下有意义的报告非常方便。例如，如果我们要创建一个“最终销售”报告，那么我们可以将其链接到一个“打印销售信息”按钮，该按钮仅在房产状态为“售出”时出现在表单视图中。

.. image:: pdf_reports/print_menu.png
    :align: center
    :alt: 打印菜单按钮

您可能已经注意到或想知道，为什么我们的报告模板会遍历一个记录集。当传递多个记录时，它可以为所有记录生成一个 PDF 报告。使用列表视图中的打印菜单并选择多个记录时，可以演示这一点。

生成报告
-------------

最后，您现在知道了在哪里创建文件以及文件的内容应如何编写。祝您报告生成愉快！

.. exercise:: 生成一个报告。

    - 将最小模板子节中的房产报价报告添加到房产视图的打印菜单中。

    - 通过添加更多数据来改进报告。请参阅本节的 **目标**，以了解您可以添加哪些其他数据，并随意添加更多。

    - 奖励: 通过添加一些逻辑生成一个更灵活的报告，这样当某个房产没有报价时，我们不会创建表格，而是写一些关于该房产尚无报价的信息。提示: 您需要使用 ``t-if`` 和 ``t-else``。

    请记住检查您的 PDF 报告与预期数据是否一致。


子模板
=============

.. note::

    **目标**: 在本节结束时，我们将拥有一个可在两个报告中使用的子模板。

    .. image:: pdf_reports/report_subtemplate.png
      :align: center
      :alt: 使用子模板的报告

使用子模板有两个主要原因。一是当处理非常长或复杂的模板时，使代码更易于阅读。另一个是尽可能重用代码。我们简单的房产报价报告很有用，但列出房产报价信息不仅对一个报告模板有用。一个例子是列出某个销售人员所有房产报价的报告。

请参阅 :ref:`文档 <reference/qweb/sub-templates>` 了解如何调用子模板，或者查看 `示例 <https://github.com/odoo/odoo/blob/0e12fa135882cd5095dbf15fe2f64231c6a84336/addons/portal/static/src/xml/portal_chatter.xml#L147-L160>`__（记住，QWeb 使用相同的控制流，无论是用于报告还是 Odoo 中的视图）。

.. exercise:: 创建并使用子模板。

    - 将报价表格部分拆分为一个独立的模板。请记住检查原始报告在修改后是否仍能正确打印。

    - 为 ``res.users`` 添加一个新报告，允许您打印在其表单视图中可见的所有房产（即在“设置”应用中）。在同一报告中包含这些销售人员的房产报价。提示: 由于 ``binding_model_id`` 在本例中不在 estate 模块内，您需要使用 ``ref="base.model_res_users"``。

      最终结果应类似于本节 **目标** 中的图片。

    请记住检查您的报告是否与预期数据一致！

报告继承
==================

.. note::

    **目标**: 在本节结束时，我们将继承 ``estate_account`` 模块中的房产报告。

    .. image:: pdf_reports/inherited_report.png
      :align: center
      :alt: 继承的报告

QWeb 中的继承使用与 :ref:`视图继承 <reference/view_records/inheritance>` 相同的 ``xpath`` 元素。然而，QWeb 模板引用其父模板的方式不同。实际上更简单，只需在 ``template`` 元素中添加 ``inherit_id`` 属性，并将其设置为 *module.parent_template_id*。

我们没有在 `estate_account` 中的任何 estate 模型中添加新字段，但我们仍然可以向现有的房产报告中添加信息。例如，我们知道任何“已售出”的房产都已经为其生成了发票，因此我们可以将该信息添加到报告中。

.. exercise:: 继承一个报告。

    - 扩展房产报告，包含一些关于发票的信息。您可以查看本节 **目标** 获取灵感（即在房产状态为“完成”时打印一行，否则不打印任何内容）。

    同样，请记住检查您的报告是否与预期数据一致！

其他功能
===================

所有以下额外功能都在 :ref:`reference/reports/report` 文档中有更详细的说明，包括如何实现每一个功能。

翻译
------------

众所周知，Odoo 可以通过自动和手动翻译在多个语言中使用。QWeb 报告也不例外！请注意，有时如果模板中的文本内容中存在不必要的空格，翻译可能无法正常工作，因此请尽量避免它们（尤其是前导空格）。

报告是网页
---------------------

您可能已经厌倦了听到 QWeb 创建 HTML 了，但我们再次强调！报告以 QWeb 编写的一个有趣功能是它们可以在 Web 浏览器中查看。这在您希望嵌入指向特定报告的超链接时非常有用。请注意，通常的安全检查仍将适用于防止未经授权的用户访问报告。

条形码
--------

Odoo 内置了一个条形码图像生成器，允许将条形码嵌入到报告中。查看对应的 `代码 <https://github.com/odoo/odoo/blob/0e12fa135882cd5095dbf15fe2f64231c6a84336/addons/web/controllers/main.py#L2044-L2046>`__，以了解所有支持的条形码类型。
