============
翻译
============

使用 Odoo，您可以将网站翻译成不同的语言。

在本章中，您将学习如何：

- 翻译模块的内容。
- 导入和导出翻译。
- 将翻译集成到模块中。

前端
========

要使用网站构建器翻译您的页面，请访问您的网站并点击语言选择器切换语言。如果您的网站从未翻译成目标语言，请点击 :guilabel:`添加语言...`，在弹出窗口中选择语言，然后点击 :guilabel:`添加`。

点击 :guilabel:`翻译` 开始翻译。根据语言的不同，有些文本会自动翻译并以绿色高亮显示，而所有需要手动翻译的部分会以黄色高亮显示。

.. image:: translations/translate-button.png
   :alt: 翻译按钮
   :width: 570

后端
=======

直接从后端翻译页面，允许您同时翻译多种语言。为此，请转到 :menuselection:`设置 --> 技术 --> 用户界面：视图`，搜索您要翻译的页面名称，然后点击 :guilabel:`编辑翻译` 按钮。

.. image:: translations/edit-translations.png
   :alt: 编辑翻译
   :width: 718

导出
======

完成翻译后，您需要导出翻译以将其集成到模块中。要一次导出所有内容，请打开数据库，激活 :ref:`开发者模式 <developer-mode>`，然后转到 :menuselection:`设置 --> 翻译 --> 导出翻译`。选择您翻译的 :guilabel:`语言`，在 :guilabel:`文件格式` 中选择 *PO 文件*，并选择 *website_airproof* 作为 :guilabel:`导出的应用程序`。

下载文件并将其移至 :file:`i18n` 文件夹。如果需要，您可以手动编辑此后的 :file:`.po` 文件。

PO 文件
=======

您可以通过编辑 :file:`.po` 文件直接进行翻译，或者自己创建该文件。请查看 :doc:`翻译模块文档 <../translations>` 以编写您的翻译内容。

.. code-block:: po
   :caption: ``/website_coconuts/i18n/fr_BE.po``

   #. module: website_airproof
   #: model_terms:ir.ui.view,arch_db:website_airproof.s_custom_snippet
   msgid "..."
   msgstr "..."

导入
======

要将您的翻译文件导入到 Odoo 中，请转到 :menuselection:`设置 --> 翻译 --> 导入翻译` 并上传文件。
