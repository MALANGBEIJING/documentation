.. _reference/orm/changelog:

=========
变更日志
=========

Odoo 版本 17.0
=================

- 引入一个 :class:`~odoo.tools.SQL` 包装对象，以便更轻松和安全地进行 SQL 组合，避免 SQL 注入。ORM 的方法现在在内部使用它。由 `#134677 <https://github.com/odoo/odoo/pull/134677>`_ 引入。

Odoo Online 版本 16.4
========================

- 方法 :meth:`~odoo.models.Model.name_get` 已被弃用，参见 `#122085 <https://github.com/odoo/odoo/pull/122085>`_。
  请改为读取字段 `display_name`。

Odoo Online 版本 16.3
========================

- 方法 :meth:`~odoo.models.Model._read_group` 具有新的签名，参见 `#110737 <https://github.com/odoo/odoo/pull/110737>`_。

Odoo Online 版本 16.2
========================

- 对搜索和读取方法的实现进行了重构，以便能够在最少的 SQL 查询中结合两者。引入了两个新方法 :meth:`~odoo.models.Model.search_fetch` 和 :meth:`~odoo.models.Model.fetch`，利用这种结合。更多细节见拉取请求 `#112126 <https://github.com/odoo/odoo/pull/112126>`_。

Odoo 版本 16.0
=================

- 翻译字段的翻译以 JSONB 值形式存储，参见 `#97692 <https://github.com/odoo/odoo/pull/97692>`_ 和 `#101115 <https://github.com/odoo/odoo/pull/101115>`_。
  代码翻译不再存储到数据库中。它们变为静态，并在需要时从 PO 文件中提取。
- :meth:`~odoo.models.Model.search_count` 考虑了 :attr:`limit` 参数，参见 `#95589 <https://github.com/odoo/odoo/pull/95589>`_。
  它限制了计数的记录数量，当部分结果可以接受时提高了性能。

Odoo Online 版本 15.4
========================

- 新 API 用于刷新数据库并使缓存失效，参见 `#87527 <https://github.com/odoo/odoo/pull/87527>`_。
  在 `odoo.models.Model` 和 `odoo.api.Environment` 中添加了新方法，使其在实际操作中更清晰。
  详见 :ref:`SQL 执行 <reference/orm/sql>` 部分。

Odoo Online 版本 15.3
========================

- 参数 `args` 被重命名为 `domain`，适用于 :meth:`~odoo.models.Model.search`、:meth:`~odoo.models.Model.search_count` 和 :meth:`~odoo.models.Model._search`，参见 `#83687 <https://github.com/odoo/odoo/pull/83687>`_。
- :meth:`~odoo.models.Model.filtered_domain` 保留当前记录集的顺序，参见 `#83687 <https://github.com/odoo/odoo/pull/83687>`_。
- :meth:`~odoo.models.Model.browse` 不接受 :class:`str` 作为 `ids`，参见 `#83687 <https://github.com/odoo/odoo/pull/83687>`_。
- 方法 :meth:`~odoo.models.Model.fields_get_keys` 和 :meth:`~odoo.models.Model.get_xml_id` 在 :class:`~odoo.models.Model` 中被弃用，参见 `#83687 <https://github.com/odoo/odoo/pull/83687>`_。
- 方法 :meth:`~odoo.models.Model._mapped_cache` 被移除，参见 `#83687 <https://github.com/odoo/odoo/pull/83687>`_。
- 移除 :attr:`limit` 属性的 :class:`~odoo.fields.One2many` 和 :class:`~odoo.fields.Many2many`，参见 `#83687 <https://github.com/odoo/odoo/pull/83687>`_。

Odoo Online 版本 15.2
========================

- 字段的特定索引类型：通过 `#83274 <https://github.com/odoo/odoo/pull/83274>`_ 和 `#83015 <https://github.com/odoo/odoo/pull/83015>`_，开发人员现在可以定义字段上可以使用的索引类型。请参见 :ref:`索引属性 <reference/fields>`。
- 移除 :attr:`_sequence` 属性的 :class:`~odoo.models.Model`。Odoo 让 PostgreSQL 使用主键的默认序列，参见 `#82727 <https://github.com/odoo/odoo/pull/82727>`_。
- 方法 :meth:`~odoo.models.Model._write` 不会对不存在的记录引发错误，参见 `#82727 <https://github.com/odoo/odoo/pull/82727>`_。
- 移除 :attr:`column_format` 和 :attr:`deprecated` 属性的 :class:`~odoo.fields.Field`，参见 `#82727 <https://github.com/odoo/odoo/pull/82727>`_。
