.. _reference/translations:

===================
翻译模块
===================

本节介绍如何为您的模块提供翻译功能。

.. note:: 如果您想为 Odoo 的翻译做贡献，请参阅 `Odoo Wiki 页面 <https://github.com/odoo/odoo/wiki/Translations>`_。

导出可翻译术语
===========================

模块中的一些术语是隐式可翻译的。因此，即使您没有针对翻译做任何具体工作，您也可以导出模块的可翻译术语，并且可能会找到可用的内容。

.. todo:: 需要技术功能

可以通过管理界面导出翻译，登录后打开 :menuselection:`设置 --> 翻译 --> 导入/导出 --> 导出翻译`

* 保持语言为默认值（新语言/空模板）
* 选择 `PO 文件`_ 格式
* 选择您的模块
* 点击 :guilabel:`导出` 并下载文件

.. image:: translations/po-export.png
    :align: center
    :width: 75%

这将生成一个名为 :file:`{yourmodule}.pot` 的文件，您应该将其移动到 :file:`{yourmodule}/i18n/` 目录。该文件是一个 *PO 模板*，它仅列出了可翻译的字符串，可以从中创建实际的翻译 (PO 文件)。可以使用 msginit_、专用的翻译工具如 POEdit_，或通过简单复制模板创建名为 :file:`{language}.po` 的新文件来创建 PO 文件。翻译文件应放在 :file:`{yourmodule}/i18n/` 中，放在 :file:`{yourmodule}.pot` 旁边，当安装相应语言时（通过 :menuselection:`设置 --> 翻译 --> 语言`），Odoo 会自动加载这些文件。

.. note:: 当安装或更新模块时，也会安装或更新所有已加载语言的翻译。

隐式导出
================

Odoo 会自动导出 "数据" 类型内容中的可翻译字符串：

* 在非 QWeb 视图中，所有文本节点以及 ``string``、``help``、``sum``、``confirm`` 和 ``placeholder`` 属性的内容都会被导出
* 在 QWeb 模板中（包括服务器端和客户端模板），除 ``t-translation="off"`` 块内的内容外，所有文本节点都会导出，同时也导出 ``title``、``alt``、``label`` 和 ``placeholder`` 属性的内容
* 对于 :class:`~odoo.fields.Field`，除非它们的模型被标记为 ``_translate = False``：

  * 它们的 ``string`` 和 ``help`` 属性会被导出
  * 如果 ``selection`` 是列表（或元组），它会被导出
  * 如果它们的 ``translate`` 属性设置为 ``True``，那么它们现有的所有值（跨所有记录）都会被导出
* :attr:`~odoo.models.Model._constraints` 和 :attr:`~odoo.models.Model._sql_constraints` 的帮助/错误信息会被导出

显式导出
================

在 Python 代码或 JavaScript 代码中，当遇到更具 "指令性" 的情况时，Odoo 无法自动导出可翻译的术语，因此必须明确标记它们进行导出。为此，需要将字符串字面量用函数调用包装。

在 Python 中，包装函数是 :func:`odoo._`::

    title = _("银行账户")

在 JavaScript 中，包装函数通常是 :js:func:`odoo.web._t`:

.. code-block:: javascript

    title = _t("银行账户");

.. warning::

    只有字符串字面量可以标记为导出，表达式或变量不能标记为导出。在格式化字符串的情况下，必须标记格式字符串，而不是格式化后的字符串。

`_` 和 `_t` 的延迟版本分别是 Python 中的 :func:`odoo._lt` 和 JavaScript 中的 :js:func:`odoo.web._lt`。翻译查找仅在渲染时执行，可用于在类方法或全局变量中声明可翻译的属性。

.. note::

    默认情况下，模块的翻译**不会**暴露给前端，因此无法从 JavaScript 访问这些翻译。要实现这一点，模块名称必须以 `website` 为前缀（如 `website_sale`、`website_event` 等），或通过为 `ir.http` 模型实现 :func:`_get_translation_frontend_modules_name` 来显式注册。

    这可以如下实现::

        from odoo import models

        class IrHttp(models.AbstractModel):
            _inherit = 'ir.http'

            @classmethod
            def _get_translation_frontend_modules_name(cls):
                modules = super()._get_translation_frontend_modules_name()
                return modules + ['your_module']

变量
---------

**不要**这样做，提取可能有效，但不会正确翻译文本::

    _("与 %s 的预定会议" % invitee.name)

**要**将动态变量设置为翻译查找的参数（如果翻译中缺少占位符，它将回退到源语言）::

    _("与 %s 的预定会议", invitee.name)


块
------

**不要**将翻译分成多个块或多行::

    # 不好，尾随空格，块上下文不连贯
    _("您有 ") + len(invoices) + _(" 份待处理发票")
    _t("您有 ") + invoices.length + _t(" 份待处理发票");

    # 不好，多次小翻译
    _("生成此销售订单请求的文档参考") + \
    _("。")

**要**保持在一个块中，给翻译人员完整的上下文::

    # 好，允许在翻译中改变数字的位置
    _("您有 %s 份待处理发票") % len(invoices)
    _.str.sprintf(_t("您有 %s 份待处理发票"), invoices.length);

    # 好，整句意思明确
    _("生成此销售订单请求的文档参考。")

复数
------

**不要**按英语的方式复数化术语::

    msg = _("您有 %(count)s 份发票", count=invoice_count)
    if invoice_count > 1:
      msg += _("s")

**要**记住每种语言有不同的复数形式::

    if invoice_count > 1:
      msg = _("您有 %(count)s 份发票", count=invoice_count)
    else:
      msg = _("您有一份发票")

读取与运行时
----------------

**不要**在服务器启动时调用翻译查找::

    ERROR_MESSAGE = {
      # 不好，在服务器启动时评估，没有用户语言
      'access_error': _('访问错误'),
      'missing_error': _('缺失记录'),
    }

    class Record(models.Model):

      def _raise_error(self, code):
        raise UserError(ERROR_MESSAGE[code])

**不要**在 JavaScript 文件读取时调用翻译查找::

    # 不好，js _t 太早执行
    var core = require('web.core');
    var _t = core._t;
    var map_title = {
        access_error: _t('访问错误'),
        missing_error: _t('缺失记录'),
    };

**要**使用延迟翻译查找方法::

    ERROR_MESSAGE = {
      'access_error': _lt('访问错误'),
      'missing_error': _lt('缺失记录'),
    }

    class Record(models.Model):

      def _raise_error(self, code):
        # 错误渲染时执行翻译查找
        raise UserError(ERROR_MESSAGE[code])


或 **要**动态评估可翻译的内容::

    # 好，运行时评估
    def _get_error_message(self):
      return {
        access_error: _('访问错误'),
        missing_error: _('缺失记录'),
      }

**要**当 JS 文件*读取*时进行翻译查找，使用 `_lt` 而不是 `_t`，以便在*使用*时翻译术语::

    # 好，js _lt 延迟评估
    var core = require('web.core');
    var _lt = core._lt;
    var map_title = {
        access_error: _lt('访问错误'),
        missing_error: _lt('缺失记录'),
    };


.. _PO 文件: https://en.wikipedia.org/wiki/Gettext#Translating
.. _msginit: https://www.gnu.org/software/gettext/manual/gettext.html#Creating
.. _POEdit: https://poedit.net/
