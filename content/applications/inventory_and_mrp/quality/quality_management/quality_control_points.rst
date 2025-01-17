======================
质量控制点
======================

.. _quality/quality_management/quality-control-points:
.. |MO| replace:: :abbr:`MO (生产订单)`
.. |MOs| replace:: :abbr:`MOs (生产订单)`
.. |QCP| replace:: :abbr:`QCP (质量控制点)`
.. |QCPs| replace:: :abbr:`QCPs (质量控制点)`

在 Odoo 中，*质量控制点* (QCP) 用于在预定的间隔内自动创建 :doc:`质量检查 <quality_checks>`。|QCPs| 可以配置为为特定操作（生产、发货等）以及这些操作中的特定产品创建质量检查。

使用 |QCPs| 允许质量团队确保产品定期接受缺陷和其他问题的检查。

配置质量控制点
================

要创建新的 |QCP|，请导航到 :menuselection:`质量 --> 质量控制 --> 控制点`，然后点击 :guilabel:`新建`。

开始填写新的 |QCP| 时，首先输入一个唯一的 :guilabel:`标题`，以使 |QCP| 易于识别。

在 :guilabel:`产品` 字段中，选择一个或多个 |QCP| 应适用的产品。如果 |QCP| 应适用于整个产品类别，请在 :guilabel:`产品类别` 字段中选择它。

在 :guilabel:`操作` 字段中，选择应触发 |QCP| 的操作。例如，在 :guilabel:`操作` 字段中选择 :guilabel:`生产` 选项会为新的生产订单 (MOs) 创建质量检查。

.. note::
   创建新的 |QCP| 时，至少需要在 :guilabel:`操作` 字段中列出一个操作。然而，:guilabel:`产品` 和 :guilabel:`产品类别` 字段可以留空。如果它们留空，|QCP| 将为指定操作的每个实例生成质量检查。

如果在 :guilabel:`操作` 字段中选择了 :guilabel:`生产` 操作，则会在其下方出现一个新的字段，标题为 :guilabel:`工单操作`。从此字段中选择一个特定的工单，以为该操作生成质量检查，而不是为整个生产操作生成检查。

例如，可以配置一个 |QCP| 为 `咖啡桌` 产品的 `组装` 工单创建质量检查。那么，如果为 `咖啡桌` 确认了新的 |MO|，|QCP| 将专门为 `组装` 操作创建质量检查。

:guilabel:`控制方式` 字段设置为三种选项之一，以确定何时创建新的质量检查：

- :guilabel:`操作`：对指定的整个操作请求一次检查。
- :guilabel:`产品`：对指定操作中包含的每个 *唯一* 产品请求一次检查。例如，一个包含一张桌子和四把椅子的发货操作会生成两个检查，因为操作中包含了两个 *唯一* 产品。
- :guilabel:`数量`：请求对指定操作中的某个百分比的产品进行检查。通过启用 :guilabel:`部分转移测试` 复选框并在其下方的 :guilabel:`百分比` 字段中输入一个数值来设置此百分比。如果未启用复选框，则为全部数量创建一次质量检查。

:guilabel:`控制频率` 字段设置为三种选项之一，以确定多长时间创建一次质量检查：

- :guilabel:`全部`：每次满足 |QCP| 条件时都会请求一次质量检查。
- :guilabel:`随机`：随机请求对某个百分比的操作进行质量检查，可以在其下方的 :guilabel:`每 #% 次转移` 字段中指定该百分比。
- :guilabel:`定期`：每隔一段时间请求一次质量检查，通过在下方的字段中输入数值，并选择 :guilabel:`天`、:guilabel:`周` 或 :guilabel:`月` 作为所需的时间间隔来指定该周期。

在 :guilabel:`类型` 字段中，指定应执行的质量检查类型。|QCP| 创建的质量检查的处理方法取决于所选择的质量检查类型：

- :guilabel:`说明` 检查提供有关如何完成质量检查的具体说明。
- :guilabel:`拍照` 检查要求上传产品的照片，以供分配的质量团队稍后审核。
- :guilabel:`登记生产` 检查提示生产员工确认在生产操作期间生产的产品数量。
- :guilabel:`通过-失败` 检查指定产品必须满足的标准才能通过检查。
- :guilabel:`测量` 检查提示员工记录产品的测量值，该值必须在标准值的公差范围内才能通过检查。
- :guilabel:`工作表` 检查提供一个交互式工作表，处理检查的员工必须填写该工作表。

.. important::
   *说明* 检查与生产订单 (MO) 的工单步骤相同。

   添加步骤到工单时，Odoo 会将其存储在质量应用中作为一个 |QCP|。可以手动创建一个具有 *说明* 检查类型的 |QCP|，甚至将其分配给生产以外的操作（如收货）。

   然而，专门为质量控制目的创建控制点时，使用不同的检查类型可能更有效。

在 :guilabel:`团队` 字段中，指定负责管理 |QCP| 及其创建的质量检查的质量团队。如果某个特定质量团队成员负责此 |QCP|，请在 :guilabel:`负责人` 字段中选择他们。

:guilabel:`步骤文档` 字段有两个选项，指定详细说明如何完成 |QCP| 创建的质量检查的说明文件的位置。

选择 :guilabel:`操作工作表的特定页面`，如果文档包含在工单的说明工作表中，则在其下方的 :guilabel:`工作表页面` 字段中输入页码。

选择 :guilabel:`自定义`，如果文档应包含在 |QCP| 表单底部的 :guilabel:`说明` 标签页中。

在表单底部的 :guilabel:`说明` 标签页中，输入完成 |QCP| 创建的质量检查的说明。

如果上面的 :guilabel:`步骤文档` 字段中选择了 :guilabel:`自定义` 选项，可以在此标签中附加文档。为此，选择 :guilabel:`上传文件` 按钮以打开设备的文件管理器，然后选择文件，或在 :guilabel:`Google Slide 链接` 字段中添加 Google 幻灯片文档的链接。

在 :guilabel:`失败消息` 标签中，包含质量检查失败时应采取的操作说明。例如，指示处理质量检查的员工创建一个 :doc:`质量警报 <quality_alerts>`。

:guilabel:`备注` 标签用于提供有关 |QCP| 的其他信息，例如创建它的原因。此标签中输入的信息不会显示给处理 |QCP| 创建的质量检查的员工。

.. image:: quality_control_points/qcp-form.png
   :align: center
   :alt: 配置为为工单操作创建通过-失败检查的 QCP。
