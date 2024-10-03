====================
财务本地化
====================

.. _fiscal_localizations/packages:

财务本地化包
============================

**财务本地化包** 是针对特定国家的模块，安装在您的数据库中，包含预配置的税收、财务位置、会计科目表和法律声明等内容。根据您的财务管理需求，一些额外功能（如特定证书的配置）也会添加到您的会计应用中。

.. note::
   Odoo 持续增加新的本地化，并改进现有的包。

配置
-------------

Odoo 根据在创建数据库时选择的国家，自动安装适当的本地化包。

.. important::
   只要您还没有发布任何条目，您仍然可以选择其他包。

要为您的公司选择财务本地化包，进入 :menuselection:`会计 --> 配置 --> 设置 --> 财务本地化`，从列表中选择适当的包并保存。

.. image:: fiscal_localizations/package-selection.png
   :alt: 在 Odoo 会计中选择您的国家财务本地化包。

使用
---

这些包需要您根据需求微调您的会计科目表，激活所使用的税种，并配置国家特定的报表和证书。

.. seealso::
   - :doc:`会计科目表 <accounting/get_started/chart_of_accounts>`
   - :doc:`税收 <accounting/taxes>`

.. _fiscal_localizations/countries-list:

支持国家列表
===========================

Odoo 会计支持在许多国家使用，只需安装适当的模块。以下是所有可以在 Odoo 中使用的 :ref:`财务本地化包 <fiscal_localizations/packages>` 列表。

- 阿尔及利亚 - 会计
- :doc:`阿根廷 - 会计 <fiscal_localizations/argentina>`
- :doc:`澳大利亚 - 会计 <fiscal_localizations/australia>`
- :doc:`奥地利 - 会计 <fiscal_localizations/austria>`
- :doc:`比利时 - 会计 <fiscal_localizations/belgium>`
- 玻利维亚 - 会计
- :doc:`巴西 - 会计 <fiscal_localizations/brazil>`
- :doc:`加拿大 - 会计 <fiscal_localizations/canada>`
- :doc:`智利 - 会计 <fiscal_localizations/chile>`
- 中国 - 会计
- :doc:`哥伦比亚 - 会计 <fiscal_localizations/colombia>`
- 哥斯达黎加 - 会计
- 克罗地亚 - 会计 (RRIF 2012)
- 捷克 - 会计
- 丹麦 - 会计
- 多米尼加共和国 - 会计
- :doc:`厄瓜多尔 - 会计 <fiscal_localizations/ecuador>`
- :doc:`埃及 - 会计 <fiscal_localizations/egypt>`
- 埃塞俄比亚 - 会计
- 芬兰 - 本地化
- :doc:`法国 - 会计 <fiscal_localizations/france>`
- 通用 - 会计
- :doc:`德国 <fiscal_localizations/germany>`

  - 德国 SKR03 - 会计
  - 德国 SKR04 - 会计

- 希腊 - 会计
- 危地马拉 - 会计
- 洪都拉斯 - 会计
- :doc:`香港 - 会计 <fiscal_localizations/hong_kong>`
- 匈牙利 - 会计
- :doc:`印度 - 会计 <fiscal_localizations/india>`
- :doc:`印度尼西亚 - 会计 <fiscal_localizations/indonesia>`
- 爱尔兰 - 会计
- 以色列 - 会计
- :doc:`意大利 - 会计 <fiscal_localizations/italy>`
- 日本 - 会计
- :doc:`肯尼亚 - 会计 <fiscal_localizations/kenya>`
- 立陶宛 - 会计
- :doc:`卢森堡 - 会计 <fiscal_localizations/luxembourg>`
- 摩洛哥 - 会计
- :doc:`墨西哥 - 会计 <fiscal_localizations/mexico>`
- 蒙古 - 会计
- :doc:`荷兰 - 会计 <fiscal_localizations/netherlands>`
- 新西兰 - 会计
- 挪威 - 会计
- OHADA - 会计
- 巴基斯坦 - 会计
- 巴拿马 - 会计
- :doc:`秘鲁 - 会计 <fiscal_localizations/peru>`
- :doc:`菲律宾 - 会计 <fiscal_localizations/philippines>`
- 波兰 - 会计
- 葡萄牙 - 会计
- :doc:`罗马尼亚 - 会计 <fiscal_localizations/romania>`
- :doc:`沙特阿拉伯 - 会计 <fiscal_localizations/saudi_arabia>`
- :doc:`新加坡 - 会计 <fiscal_localizations/singapore>`
- 斯洛伐克 - 会计
- 斯洛文尼亚 - 会计
- 南非 - 会计
- :doc:`西班牙 - 会计 (PGCE 2008) <fiscal_localizations/spain>`
- 瑞典 - 会计
- :doc:`瑞士 - 会计 <fiscal_localizations/switzerland>`
- 台湾 - 会计
- :doc:`泰国 - 会计 <fiscal_localizations/thailand>`
- 土耳其 - 会计
- :doc:`阿联酋 - 会计 <fiscal_localizations/united_arab_emirates>`
- :doc:`英国 - 会计 <fiscal_localizations/united_kingdom>`
- 乌克兰 - 会计
- :doc:`美国 - 会计 <fiscal_localizations/united_states>`
- :doc:`乌拉圭 - 会计 <fiscal_localizations/uruguay>`
- 委内瑞拉 - 会计
- :doc:`越南 - 会计 <fiscal_localizations/vietnam>`

.. toctree::
   :titlesonly:

   fiscal_localizations/argentina
   fiscal_localizations/australia
   fiscal_localizations/austria
   fiscal_localizations/belgium
   fiscal_localizations/brazil
   fiscal_localizations/canada
   fiscal_localizations/chile
   fiscal_localizations/colombia
   fiscal_localizations/ecuador
   fiscal_localizations/egypt
   fiscal_localizations/france
   fiscal_localizations/germany
   fiscal_localizations/hong_kong
   fiscal_localizations/india
   fiscal_localizations/indonesia
   fiscal_localizations/italy
   fiscal_localizations/kenya
   fiscal_localizations/luxembourg
   fiscal_localizations/mexico
   fiscal_localizations/netherlands
   fiscal_localizations/romania
   fiscal_localizations/peru
   fiscal_localizations/philippines
   fiscal_localizations/saudi_arabia
   fiscal_localizations/singapore
   fiscal_localizations/spain
   fiscal_localizations/switzerland
   fiscal_localizations/thailand
   fiscal_localizations/vietnam
   fiscal_localizations/united_arab_emirates
   fiscal_localizations/united_kingdom
   fiscal_localizations/united_states
   fiscal_localizations/uruguay
