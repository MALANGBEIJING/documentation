===========
Odoo 编辑器
===========

Odoo 编辑器是 Odoo 自己的富文本编辑器。其源代码可以在
`odoo-editor 目录
<{GITHUB_PATH}/addons/web_editor/static/src/js/editor/odoo-editor>`_ 中找到。

Powerbox
========

Powerbox 是一个包含
:ref:`命令 <reference/frontend/odoo_editor/powerbox/command>` 的用户界面部分，这些命令被组织在
:ref:`类别 <reference/frontend/odoo_editor/powerbox/category>` 中。当在编辑器中输入 `/` 时，它会出现。用户输入文本时可以过滤命令，并且可以使用方向键进行导航。

.. image:: odoo_editor/powerbox.png
   :align: center
   :alt: 输入 "/" 后打开的 Powerbox。

修改 Powerbox
-------------

同一时间只能实例化一个 Powerbox，编辑器负责此工作。其 Powerbox 实例可以在其 `powerbox` 实例变量中找到。
要更改 Powerbox 的内容和选项，需要在实例化编辑器之前更改传递给编辑器的选项。

.. important::
   永远不要自己实例化 Powerbox。始终使用当前编辑器的实例。

.. example::
    假设我们想为 `mass_mailing` 模块向 Powerbox 添加一个新命令 `Document`。我们希望将其添加到一个名为
    `Documentation` 的新类别中，并且我们希望它位于 Powerbox 的最上方。

    `mass_mailing` 扩展了
    <{GITHUB_PATH}/addons/mass_mailing/static/src/js/wysiwyg.js>`_
    `web_editor` 的 `Wysiwyg 类
    <{GITHUB_PATH}/addons/web_editor/static/src/js/wysiwyg/wysiwyg.js>`_，其中在其 `start` 方法中实例化了编辑器。在此之前，它调用了自己的 `_getPowerboxOptions` 方法，我们可以方便地覆盖它来添加我们的新命令。

    由于 `mass_mailing` 已经覆盖了 `_getPowerboxOptions`，我们只需向其添加我们的新命令：

    .. code-block:: javascript

      _getPowerboxOptions: function () {
          const options = this._super();
          // (返回语句之前的现有代码)
          options.categories.push({
              name: _t('Documentation'),
              priority: 300,
          });
          options.commands.push({
              name: _t('Document'),
              category: _t('Documentation'),
              description: _t("将此文本添加到邮件的文档中"),
              fontawesome: 'fa-book',
              priority: 1, // 这是该类别中唯一的命令。
          });
          return options;
      }

    .. important::
      为了使命令和类别的名称和描述支持翻译，请确保将它们包裹在 `_t` 函数中。

    .. tip::
      为了避免不受控制的扩展，请不要为优先级使用随机数：查看现有优先级并相应选择值（就像处理 `z-index` 一样）。

打开自定义 Powerbox
-------------------

可以使用自定义的类别和命令打开 Powerbox，绕过所有预先存在的命令。为此，调用 Powerbox 的 `open` 方法并传递自定义命令和类别。

.. image:: odoo_editor/powerbox-custom.png
   :align: center
   :alt: 粘贴图片 URL 时，Powerbox 使用自定义类别和命令打开。

.. example::
    我们需要当前 Powerbox 实例，可以在当前编辑器中找到。在 `Wysiwyg 类
    <{GITHUB_PATH}/addons/web_editor/static/src/js/wysiwyg/wysiwyg.js>`_ 中，可以通过 `this.odooEditor.powerbox` 获取。

    现在，用我们的自定义 "Document" 命令在自定义 "Documentation" 类别中打开它：

    .. code-block:: javascript

      this.odooEditor.powerbox.open(
          [{
              name: _t('Document'),
              category: _t('Documentation'),
              description: _t("将此文本添加到邮件的文档中"),
              fontawesome: 'fa-book',
              priority: 1, // 这是该类别中唯一的命令。
          }],
          [{
              name: _t('Documentation'),
              priority: 300,
          }]
      );

过滤命令
--------

有三种方法可以过滤命令：

#. 通过 `powerboxFilters`
   :ref:`Powerbox 选项 <reference/frontend/odoo_editor/powerbox/options>`。
#. 通过给定命令的
   :ref:`isDisabled 条目 <reference/frontend/odoo_editor/powerbox/command>`。
#. 用户可以在打开 Powerbox 后输入文本来过滤命令。它将对文本与类别和命令的名称进行模糊匹配。

.. image:: odoo_editor/powerbox-filtered.png
   :align: center
   :alt: 使用单词 "head" 过滤后的 Powerbox 命令。

参考
-----

.. _reference/frontend/odoo_editor/powerbox/category:

类别
~~~~

.. list-table::
    :widths: 20 20 60
    :header-rows: 1

    * - 名称
      - 类型
      - 描述
    * - `name`
      - `string`
      - 类别的名称
    * - `priority`
      - `number`
      - 用于对类别进行排序：优先级较高的类别会显示在 Powerbox 更靠上的位置（具有相同优先级的类别按字母顺序排列）

.. note::
    如果有多个类别名称相同，它们将被合并为一个。其优先级将以最后声明的类别的优先级为准。

.. _reference/frontend/odoo_editor/powerbox/command:

命令
~~~~~

.. list-table::
    :widths: 20 20 60
    :header-rows: 1

    * - 名称
      - 类型
      - 描述
    * - `name`
      - `string`
      - 命令的名称
    * - `category`
      - `string`
      - 命令所属的类别名称
    * - `description`
      - `string`
      - 描述命令的简短文本
    * - `fontawesome`
      - `string`
      - 用作命令图标的 *Font Awesome* 名称
    * - `priority`
      - `number`
      - 用于对命令进行排序：优先级较高的命令显示在 Powerbox 更靠上位置（具有相同优先级的命令按字母顺序排列）
    * - `callback`
      - `function` (`() => void`)
      - 命令被选中时执行的函数（可以是异步的）
    * - `isDisabled` (可选)
      - `function` (`() => void`)
      - 在某些条件下用于禁用命令的函数（当返回 `true` 时，命令将被禁用）

.. note::
    如果命令指向尚不存在的类别，则该类别将被创建并追加到 Powerbox 的末尾。

.. _reference/frontend/odoo_editor/powerbox/options:

选项
~~~~~

以下选项可以传递给 OdooEditor，然后传递给 Powerbox 实例：

.. list-table::
    :widths: 20 20 60
    :header-rows: 1

    * - 名称
      - 类型
      - 描述
    * - `commands`
      - `命令数组`
      - 要添加到编辑器默认定义的命令
    * - `categories`
      - `类别数组`
      - 要添加到编辑器默认定义的类别
    * - `powerboxFilters`
      - `函数数组` (`commands => commands`)
      - 用于过滤 Powerbox 中显示的命令的函数
    * - `getContextFromParentRect`
      - `function` (`() => DOMRect`)
      - 一个返回编辑器祖先的 `DOMRect` 的函数（当编辑器在 iframe 中时可能有用）
