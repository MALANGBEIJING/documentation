:orphan: true

========
术语表
========

.. glossary::

    外部ID
    外部标识符
    外部标识符
        存储在 ``ir.model.data`` 中的字符串标识符，可以在数据导入或导出/导入往返过程中用来引用记录，而不考虑其数据库标识符。

        外部标识符的格式为 :samp:`{module}.{id}`（例如：``account.invoice_graph`` ）。在模块内部，:samp:`{module}.` 前缀可以省略。

        有时被称为 "xml id" 或 ``xml_id``，因为基于XML的 :ref:`reference/data` 大量使用它们。

    格式字符串
        受 `jinja变量`_ 启发，格式字符串允许更容易地混合文本内容和计算内容（表达式）：``{{`` 和 ``}}`` 之间的内容被解释为表达式并进行评估，其他内容被解释为文本字符串并按原样显示。

    GIS
    地理信息系统
        任何捕获、存储、操作、分析、管理或展示空间和地理数据的计算机系统或子系统。

    缩小化
    缩小化
        删除文件中不必要部分（如注释、空白）并可能使用等效但更短的结构（例如使用 `三元运算符`_ 代替 ``if/else``）重新编译，以减少网络流量的过程。

.. _jinja变量: http://jinja.pocoo.org/docs/dev/templates/#variables
.. _三元运算符: http://en.wikipedia.org/wiki/%3F:
