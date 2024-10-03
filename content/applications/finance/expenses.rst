========
费用管理
========

Odoo **费用管理** 应用简化了费用管理流程。员工在 Odoo 提交费用后，管理和会计团队会进行审核。一旦批准，款项可以处理并发放给员工进行报销。

.. seealso::
   `Odoo 费用管理：产品页面 <https://www.odoo.com/app/expenses>`_

设置费用类别
============

追踪费用的第一步是为公司配置不同类型的费用（在 Odoo 中管理为 *费用类别*）。每个类别可以根据需要进行细化或概括。进入 :menuselection:`费用管理应用 --> 配置 --> 费用类别` 查看当前的可报销类别，它们以默认的列表视图显示。

.. image:: expenses/categories.png
   :align: center
   :alt: 设置费用类别。

要创建新的费用类别，点击 :guilabel:`新建`。将显示一个产品表单，描述字段标记为 :guilabel:`产品名称`。

.. note::
   费用类别在 Odoo 中像产品一样进行管理。费用类别表单遵循 Odoo 的标准产品表单，输入的信息类似。本文档中费用产品将称为费用类别，因为主菜单将这些称为 :guilabel:`费用类别`。

只需填写两个必填字段，分别是 :guilabel:`产品名称` 和 :guilabel:`计量单位`。在字段中输入 :guilabel:`产品名称`，并从下拉菜单中选择 :guilabel:`计量单位`（大多数产品将设置为 :guilabel:`单位`）。

.. tip::
   *销售* 应用是指定和编辑计量单位的地方（例如，单位、英里、夜晚等）。进入 :menuselection:`销售应用 --> 配置 --> 设置` 并确保在 `产品目录` 部分启用了 `计量单位`。点击 :guilabel:`计量单位` 内部链接以 :doc:`查看、创建和编辑计量单位 <../inventory_and_mrp/inventory/product_management/configure/uom>`。

.. image:: expenses/new-expense-product.png
   :align: center
   :alt: 在产品上设置费用。

产品表单上的 :guilabel:`成本` 字段默认为 `0.00`。如果某个特定费用应始终以固定价格报销，请在 :guilabel:`成本` 字段中输入该金额。否则，将 :guilabel:`成本` 保持为 `0.00`，员工在提交费用报告时会报告实际费用。

.. note::
   :guilabel:`成本` 字段始终在费用类别表单上显示，但只有在 :guilabel:`费用重计` 部分选择了 :guilabel:`销售价格` 后，:guilabel:`销售价格` 字段才会显示。否则，:guilabel:`销售价格` 字段将被隐藏。

.. example::
   以下是设置产品的特定 :guilabel:`成本` 与将 :guilabel:`成本` 设置为 `0.00` 的一些示例：

   - **餐饮**：将 :guilabel:`成本` 设置为 `0.00`。当员工记录餐饮费用时，他们输入账单的实际金额，并按该金额报销。餐饮费用为 $95.23，将报销 $95.23。
   - **里程**：将 :guilabel:`成本` 设置为 `0.30`。当员工记录 "里程" 费用时，他们在 :guilabel:`数量` 字段中输入行驶的英里数，每英里报销 0.30。他们记录 100 英里的费用，将报销 $30.00。
   - **月度停车**：将 :guilabel:`成本` 设置为 `75.00`。当员工记录 "月度停车" 费用时，报销金额为 $75.00。
   - **其他费用**：将 :guilabel:`成本` 设置为 `0.00`。当员工记录非餐饮、里程或月度停车的费用时，他们使用通用的 :guilabel:`费用` 产品。例如，员工购买了一台价格为 $350.00 的笔记本电脑，该费用将记录为 :guilabel:`费用` 产品，报销金额为 $350.00。

如果使用 Odoo *会计* 应用，请选择 :guilabel:`费用账户`。建议与会计部门确认以确定正确的账户，因为这将影响报告。

如果适用，在产品上设置 :guilabel:`供应商税` 和 :guilabel:`客户税`。通常建议使用已配置为 :ref:`价格中含税 <taxes/included-in-price>` 的税项。如果设置了此选项，税金将自动配置。

.. seealso::
   - :doc:`记录费用 <expenses/log_expenses>`
   - :doc:`费用报告 <expenses/expense_reports>`
   - :doc:`发布费用 <expenses/post_expenses>`
   - :doc:`报销费用 <expenses/reimburse>`
   - :doc:`费用重计 <expenses/reinvoice_expenses>`

.. toctree::
   :titlesonly:

   expenses/approve_expenses
   expenses/log_expenses
   expenses/expense_reports
   expenses/post_expenses
   expenses/reimburse
   expenses/reinvoice_expenses
