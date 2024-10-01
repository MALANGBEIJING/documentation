===========
提取 API
===========

Odoo 提供了一项服务，自动处理 **发票**、**费用** 或 **简历** 类型的文档。

该服务使用 :abbr:`OCR (光学字符识别)` 引擎扫描文档，然后使用基于 :abbr:`AI(人工智能)` 的算法提取感兴趣的字段，例如发票的总额、到期日或发票行，费用的总额、日期，或简历的姓名、电子邮件、电话号码。

该服务为付费服务。每处理一份文档将消耗一个积分。积分可以在 `iap.odoo.com <https://iap.odoo.com/iap/in-app-services/259?sortby=date>`_ 上购买。

您可以直接在会计、费用或招聘应用中使用此服务，也可以通过 API 使用。提取 API 的详细信息将在下一节中介绍，允许您将我们的服务直接集成到您自己的项目中。

概述
========

提取 API 使用 JSON-RPC2_ 协议；其端点路由位于 `https://extract.api.odoo.com`。

.. _extract_api/version:

版本
-------

提取 API 的版本在路由中指定。

最新版本为：
    - 发票：122
    - 费用：132
    - 申请人：102

流程
----

每种文档类型的流程相同。

#. | 调用 :ref:`/parse <extract_api/parse>` 提交您的文档（每个文档一个调用）。成功后，您将在响应中收到 `document_token`。
#. | 然后，您必须定期轮询 :ref:`/get_result <extract_api/get_result>` 以获取文档的解析状态。
   | 或者，您可以在调用 :ref:`/parse <extract_api/parse>` 时提供 `webhook_url`，在结果准备好时将通过 POST 请求通知您。

所有请求均应使用 HTTP POST 方法。关于发票的完整流程的 Python 实现可以在 :download:`此处 <extract_api/implementation.py>` 找到，集成测试的令牌在
:ref:`集成测试部分 <latestextract_api/integration_testing>` 中提供。

解析
=====

请求 OCR 处理文档。该路由将返回一个 `document_token`，您可以使用它来获取请求的结果。

.. _extract_api/parse:

路由
------

    - /api/extract/invoice/2/parse
    - /api/extract/expense/2/parse
    - /api/extract/applicant/2/parse

请求
-------

.. rst-class:: o-definition-list

``jsonrpc`` (必需)
    参见 JSON-RPC2_
``method`` (必需)
    参见 JSON-RPC2_
``id`` (必需)
    参见 JSON-RPC2_
``params``
    .. rst-class:: o-definition-list

    ``account_token`` (必需)
        将扣除积分的帐户令牌。每次成功调用将消耗一个令牌。
    ``version`` (必需)
        版本将决定请求的格式和服务器响应的格式。
        您应使用 :ref:`最新可用版本 <extract_api/version>`。
    ``documents`` (必需)
        文档必须以 ASCII 编码的字符串提供。列表应仅包含一个字符串。如果提供多个字符串，仅处理第一个对应于 pdf 的字符串。如果没有找到 pdf，则处理第一个字符串。该字段仅出于兼容性原因是列表。支持的扩展名为 *pdf*、*png*、*jpg* 和 *bmp*。
    ``dbuuid`` (可选)
        Odoo 数据库的唯一标识符。
    ``webhook_url`` (可选)
        可以提供 webhook URL。结果准备好时，将向 ``webhook_url/document_token`` 发送一个空的 POST 请求。
    ``user_infos`` (可选)
        有关发送文档到提取服务的人的信息。它可以是客户或供应商（具体取决于 ``perspective``）。此信息在服务工作时并不是必需的，但可以大大提高结果的质量。

        .. rst-class:: o-definition-list

        ``user_company_vat`` (可选)
            用户的增值税号码。
        ``user_company_name`` (可选)
            用户公司名称。
        ``user_company_country_code`` (可选)
            用户的国家代码。格式：
            `ISO3166 alpha-2 <https://www.iban.com/country-codes>`_。
        ``user_lang`` (可选)
            用户语言。格式：*language_code + _ + locale*（例如 fr_FR，en_US）。
        ``user_email`` (可选)
            用户电子邮件。
        ``purchase_order_regex`` (可选)
            采购订单识别的正则表达式。如果未提供，则默认为 Odoo PO 格式。
        ``perspective`` (可选)
            .. rst-class:: o-definition-list

            可以是 ``client`` 或 ``supplier``。此字段仅对发票有用。
            ``client`` 表示提供的信息与发票的客户相关。
            ``supplier`` 表示它与供应商相关。
            如果未提供，将使用 client。

.. code-block:: js

    {
        "jsonrpc": "2.0",
        "method": "call",
        "params": {
            "account_token": string,
            "version": int,
            "documents": [string],
            "dbuuid": string,
            "webhook_url": string,
            "user_infos": {
                "user_company_vat": string,
                "user_company_name": string,
                "user_company_country_code": string,
                "user_lang": string,
                "user_email": string,
                "purchase_order_regex": string,
                "perspective": string,
            },
        },
        "id": string,
    }

.. note::
    ``user_infos`` 参数是可选的，但可以大大提高结果的质量，
    尤其是对于发票。您提供的信息越多，结果越好。

响应
--------

.. rst-class:: o-definition-list

``jsonrpc``
    参见 JSON-RPC2_
``id``
    参见 JSON-RPC2_
``result``
    .. rst-class:: o-definition-list

    ``status``
        表示请求状态的代码。见下表。
    ``status_msg``
        字符串，提供有关请求状态的详细信息。
    ``document_token``
        仅在请求成功时存在。

===========================  ==============================================================
状态                        状态消息
===========================  ==============================================================
`success`                    成功
`error_unsupported_version`  不支持的版本
`error_internal`             发生错误
`error_no_credit`            您的积分不足
`error_unsupported_format`   不支持的文件格式
`error_maintenance`          服务器当前正在维护，请稍后再试
===========================  ==============================================================

.. code-block:: js

    {
        "jsonrpc": "2.0",
        "id": string,
        "result": {
            "status": string,
            "status_msg": string,
            "document_token": string,
        }
    }

.. note::
    API 实际上并不使用 JSON-RPC 错误方案。相反，API 在成功的 JSON-RPC 结果中捆绑了自己的错误方案。

获取结果
===========

.. _extract_api/get_result:

路由
------

    - /api/extract/invoice/2/get_result
    - /api/extract/expense/2/get_result
    - /api/extract/applicant/2/get_result

请求
-------

.. rst-class:: o-definition-list

``jsonrpc`` (必需)
    参见 JSON-RPC2_
``method`` (必需)
    参见 JSON-RPC2_
``id`` (必需)
    参见 JSON-RPC2_
``params``
    .. rst-class:: o-definition-list

    ``version`` (必需)
        版本应与传递给 :ref:`/parse <extract_api/parse>` 请求的版本匹配。
    ``document_token`` (必需)
        您要获取当前解析状态的 ``document_token``。
    ``account_token`` (必需)
        用于提交文档的帐户的令牌。

.. code-block:: js

    {
        "jsonrpc": "2.0",
        "method": "call",
        "params": {
            "version": int,
            "document_token": int,
            "account_token": string,
        },
        "id": string,
    }

响应
--------

从解析中获取结果时，检测到的字段因文档类型而异。每个响应都是一个字典列表，每个字典代表一个文档。字典的键是字段的名称，值是字段的值。

.. rst-class:: o-definition-list

``jsonrpc``
    参见 JSON-RPC2_
``id``
    参见 JSON-RPC2_
``result``
    .. rst-class:: o-definition-list

    ``status``
        表示请求状态的代码。见下表。
    ``status_msg``
        字符串，提供有关请求状态的详细信息。
    ``results``
        仅在请求成功时存在。

        .. rst-class:: o-definition-list

        ``full_text_annotation``
            包含文档的未处理完整结果的 OCR。

================================  =============================================================
状态                            状态消息
================================  =============================================================
`success`                         成功
`error_unsupported_version`       不支持的版本
`error_internal`                  发生错误
`error_maintenance`               服务器当前正在维护，请稍后再试
`error_document_not_found`        找不到文档
`error_unsupported_size`          文档被拒绝，因为它太小
`error_no_page_count`             无法获取 PDF 文件的页面数
`error_pdf_conversion_to_images`  无法将 PDF 转换为图像
`error_password_protected`        PDF 文件受密码保护
`error_too_many_pages`            文档包含太多页面
================================  =============================================================

.. code-block:: js

    {
        "jsonrpc": "2.0",
        "id": string,
        "result": {
            "status": string,
            "status_msg": string,
            "results": [
                {
                    "full_text_annotation": string,
                    "feature_1_name": feature_1_result,
                    "feature_2_name": feature_2_result,
                    ...
                },
                ...
            ]
        }
    }

公共字段
~~~~~~~~~~~~~

.. _latestextract_api/get_result/feature_result:

``feature_result``
******************

我们希望从文档中提取的每个感兴趣的字段，如总额或到期日，也称为 **特征**。提取与文档类型相关的所有特征的详尽列表可以在下面的部分找到。

对于每个特征，我们返回一组候选项，并突出显示我们的模型预测为该特征的最佳匹配候选项。

.. rst-class:: o-definition-list

``selected_value`` (可选)
    该特征的最佳候选项。
``selected_values`` (可选)
    该特征的最佳候选项。
``candidates`` (可选)
    该特征的所有候选项列表，按信心得分降序排列。

.. code-block:: js

   "feature_name": {
       "selected_value": candidate_12,
       "candidates": [candidate_12, candidate_3, candidate_4, ...]
   }

候选项
*********

对于每个候选项，我们给出其表示和在文档中的位置。候选项按适用性降序排序。

.. rst-class:: o-definition-list

``content``
    候选项的表示。
``coords``
    .. rst-class:: o-definition-list

    ``[center_x, center_y, width, height, rotation_angle]``。位置和尺寸相对于页面的大小，因此在 0 到 1 之间。
    角度是顺时针旋转，以度为单位测量。
``page``
    候选项所在的原始文档的页码（从 0 开始）。

.. code-block:: js

    "candidate": [
        {
            "content": string|float,
            "coords": [float, float, float, float, float],
            "page": int
        },
        ...
    ]


发票
~~~~~~~~

发票比较复杂，可以有很多不同的字段。以下表格提供了我们可以从发票中提取的所有字段的详尽列表。

+-------------------------+------------------------------------------------------------------------+
| 特征名称                | 特殊说明                                                            |
+=========================+========================================================================+
| ``SWIFT_code``          | ``content`` 是以字典编码的字符串。                                   |
|                         |                                                                        |
|                         | 它包含关于检测到的 SWIFT 代码的信息                                  |
|                         | （或 `BIC <https://www.iso9362.org/isobic/overview.html>`_）。          |
|                         |                                                                        |
|                         | 键：                                                                  |
|                         |                                                                        |
|                         | .. rst-class:: o-definition-list                                       |
|                         |                                                                        |
|                         | ``bic``                                                                |
|                         |     检测到的 BIC（字符串）。                                           |
|                         | ``name`` (可选)                                                        |
|                         |     银行名称（字符串）。                                              |
|                         | ``country_code``                                                       |
|                         |     银行的 ISO3166 alpha-2 国家代码（字符串）。                        |
|                         | ``city`` (可选)                                                        |
|                         |     银行所在城市（字符串）。                                          |
|                         | ``verified_bic``                                                       |
|                         |     如果在我们的数据库中找到 BIC，则为 True（布尔值）。              |
|                         |                                                                        |
|                         | 名称和城市仅在 verified_bic 为 true 时存在。                         |
+-------------------------+------------------------------------------------------------------------+
| ``iban``                | ``content`` 是字符串                                                  |
+-------------------------+------------------------------------------------------------------------+
| ``aba``                 | ``content`` 是字符串                                                  |
+-------------------------+------------------------------------------------------------------------+
| ``VAT_Number``          | ``content`` 是字符串                                                  |
|                         |                                                                        |
|                         | 根据用户信息中的 perspective 值，这将是供应商或客户的增值税号码。     |
|                         | 如果 perspective 为 client，则为供应商的增值税号码。如果为 supplier，  |
|                         | 则为客户的增值税号码。                                               |
+-------------------------+------------------------------------------------------------------------+
| ``qr-bill``             | ``content`` 是字符串                                                  |
+-------------------------+------------------------------------------------------------------------+
| ``payment_ref``         | ``content`` 是字符串                                                  |
+-------------------------+------------------------------------------------------------------------+
| ``purchase_order``      | ``content`` 是字符串                                                  |
|                         |                                                                        |
|                         | 使用 ``selected_values`` 而不是 ``selected_value``                    |
+-------------------------+------------------------------------------------------------------------+
| ``country``             | ``content`` 是字符串                                                  |
+-------------------------+------------------------------------------------------------------------+
| ``currency``            | ``content`` 是字符串                                                  |
+-------------------------+------------------------------------------------------------------------+
| ``date``                | ``content`` 是字符串                                                  |
|                         |                                                                        |
|                         | 格式： *YYYY-MM-DD*                                                  |
+-------------------------+------------------------------------------------------------------------+
| ``due_date``            | 同 ``date``                                                         |
+-------------------------+------------------------------------------------------------------------+
| ``total_tax_amount``    | ``content`` 是浮动值                                                 |
+-------------------------+------------------------------------------------------------------------+
| ``invoice_id``          | ``content`` 是字符串                                                  |
+-------------------------+------------------------------------------------------------------------+
| ``subtotal``            | ``content`` 是浮动值                                                 |
+-------------------------+------------------------------------------------------------------------+
| ``total``               | ``content`` 是浮动值                                                 |
+-------------------------+------------------------------------------------------------------------+
| ``supplier``            | ``content`` 是字符串                                                  |
+-------------------------+------------------------------------------------------------------------+
| ``client``              | ``content`` 是字符串                                                  |
+-------------------------+------------------------------------------------------------------------+
| ``email``               | ``content`` 是字符串                                                  |
+-------------------------+------------------------------------------------------------------------+
| ``website``             | ``content`` 是字符串                                                  |
+-------------------------+------------------------------------------------------------------------+


``feature_result`` 对于 ``invoice_lines`` 特征
****************************************************

它遵循更具体的结构。它基本上是一个字典列表，每个字典代表一条发票行。每个值遵循
:ref:`latestextract_api/get_result/feature_result` 结构。

.. code-block:: js

    "invoice_lines": [
        {
            "description": feature_result,
            "discount": feature_result,
            "product": feature_result,
            "quantity": feature_result,
            "subtotal": feature_result,
            "total": feature_result,
            "taxes": feature_result,
            "total": feature_result,
            "unit": feature_result,
            "unit_price": feature_result
        },
        ...
    ]


费用
~~~~~~~

费用的复杂程度低于发票。以下表格提供了我们可以从费用报告中提取的所有字段的详尽列表。

+-------------------------+------------------------------------------------------------------------+
| 特征名称                | 特殊说明                                                            |
+=========================+========================================================================+
| ``description``         | ``content`` 是字符串                                                  |
+-------------------------+------------------------------------------------------------------------+
| ``country``             | ``content`` 是字符串                                                  |
+-------------------------+------------------------------------------------------------------------+
| ``date``                | ``content`` 是字符串                                                  |
+-------------------------+------------------------------------------------------------------------+
| ``total``               | ``content`` 是浮动值                                                 |
+-------------------------+------------------------------------------------------------------------+
| ``currency``            | ``content`` 是字符串                                                  |
+-------------------------+------------------------------------------------------------------------+

申请人
~~~~~~~~~

第三种文档类型用于处理简历。以下表格提供了我们可以从简历中提取的所有字段的详尽列表。

+-------------------------+------------------------------------------------------------------------+
| 特征名称                | 特殊说明                                                            |
+=========================+========================================================================+
| ``name``                | ``content`` 是字符串                                                  |
+-------------------------+------------------------------------------------------------------------+
| ``email``               | ``content`` 是字符串                                                  |
+-------------------------+------------------------------------------------------------------------+
| ``phone``               | ``content`` 是字符串                                                  |
+-------------------------+------------------------------------------------------------------------+
| ``mobile``              | ``content`` 是字符串                                                  |
+-------------------------+------------------------------------------------------------------------+

.. _latestextract_api/integration_testing:

集成测试
===================

您可以通过在 :ref:`/parse <extract_api/parse>` 请求中使用 *integration_token* 作为 ``account_token`` 来测试您的集成。

使用此令牌将您置于测试模式，并允许您模拟整个流程，而无需真正解析文档，并且在每次成功 **文档** 解析时不会被收取一个积分。

测试模式中的唯一技术差异是您发送的文档不会被系统解析，而您从
:ref:`/get_result <extract_api/get_result>` 获取的响应是硬编码的。

有关发票完整流程的 Python 实现可以在 :download:`此处 <extract_api/implementation.py>` 找到。

.. _JSON-RPC2: https://www.jsonrpc.org/specification

.. |ss| raw:: html

    <strike>

.. |se| raw:: html

    </strike>
