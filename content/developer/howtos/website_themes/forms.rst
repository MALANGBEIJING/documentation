=====
表单
=====

Odoo 的表单非常强大。它们与其他应用程序直接集成，可以用于多种不同的目的。

在本章中，您将了解如何：

- 在自定义主题中添加表单。
- 更改表单的操作。
- 通过 Bootstrap 变量对表单进行样式化。

默认表单
============

要将表单添加到您的页面中，您可以简单地复制并粘贴由网站构建器在视图中生成的代码。

它看起来应该像下面的示例。

.. code-block:: xml

   <form action="/website/form/" method="post" enctype="multipart/form-data" class="o_mark_required" data-mark="*" data-pre-fill="true" data-success-mode="redirect" data-success-page="/contactus-thank-you" data-model_name="mail.mail">
        <div class="s_website_form_rows row s_col_no_bgcolor">
             <div class="form-group s_website_form_field col-12    s_website_form_dnone" data-name="Field">
                  <!-- 表单字段 -->
              </div>
        </div>
   </form>

操作
=======

表单标签中有一个 `data-model_name` 属性。它使您可以为表单定义不同的操作。

发送电子邮件（这是默认的操作）。

.. code-block:: xml

   <form data-model_name="mail.mail">

申请工作。

.. code-block:: xml

   <form data-model_name="hr.applicant">

创建客户。

.. code-block:: xml

   <form data-model_name="res.partner">

创建工单。

.. code-block:: xml

   <form data-model_name="helpdesk.ticket">

创建机会。

.. code-block:: xml

   <form data-model_name="crm.lead">

创建任务。

.. code-block:: xml

   <form data-model_name="project.task">

成功提交
=======

您还可以通过 `data-success-mode` 定义表单提交后发生的情况。

将用户重定向到 `data-success-page` 中定义的页面。

.. code-block:: xml

   <form data-success-mode="redirect" data-success-page="/contactus-thank-you">

在同一页面上显示消息。

.. code-block:: xml

   <form data-success-mode="message">

您可以直接在表单标签下添加成功消息。始终添加 `d-none` 类，以确保如果表单未提交，成功消息将被隐藏。

.. code-block:: xml

   <div class="s_website_form_end_message d-none">
        <div class="oe_structure">
             <section class="s_text_block pt64 pb64" data-snippet="s_text_block">
                  <div class="container">
                        <h2 class="text-center">这是成功的！</h2>
                  </div>
             </section>
        </div>
   </div>

Bootstrap 变量
===================

正如您已经知道的，网站构建器基于 Bootstrap 创建内容。这对表单同样适用。以下是一些 Bootstrap 变量的选择，或者您可以查看 `变量的完整列表 <https://github.com/twbs/bootstrap/blob/main/scss/_variables.scss>`_。

.. code-block:: scss
   :caption: ``/website_airproof/static/src/scss/bootstrap_overridden.scss``

   $input-padding-y:                       $input-btn-padding-y !default;
   $input-padding-x:                       $input-btn-padding-x !default;

   $input-font-family:                     $input-btn-font-family !default;
   $input-font-size:                       $input-btn-font-size !default;
   $input-font-weight:                     $font-weight-base !default;
   $input-line-height:                     $input-btn-line-height !default;

   $input-color:                           $gray-700 !default;
   $input-border-color:                    $gray-400 !default;
   $input-border-width:                    $input-btn-border-width !default;
   $input-box-shadow:                      inset 0 1px 1px rgba($black, .075) !default;
   $input-border-radius:                   $border-radius !default;

   $input-focus-bg:                        $input-bg !default;
   $input-focus-border-color:              lighten($component-active-bg, 25%) !default;
   $input-focus-color:                     $input-color !default;
   $input-focus-width:                     $input-btn-focus-width !default;
   $input-focus-box-shadow:                $input-btn-focus-box-shadow !default;
