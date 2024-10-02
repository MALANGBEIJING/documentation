.. _reference/mobile:

=================
移动JavaScript
=================

简介
============

在Odoo 10.0中，我们发布了一款移动应用，允许您访问所有**Odoo应用**（甚至是您自定义的模块）。

该应用是**Odoo Web**和**本机移动组件**的结合。换句话说，它是一个在本机移动WebView容器中加载的Odoo Web实例。

本页面文档说明如何通过Odoo Web（通过JavaScript）访问移动原生组件，如相机、振动、通知和Toast。您无需成为移动开发人员，只需了解Odoo JavaScript API即可访问所有可用的移动功能。

.. warning::
   这些功能仅适用于**Odoo Enterprise 10.0+**

它是如何工作的？
=================

移动应用的内部工作原理：

.. image:: mobile/mobile_working.jpg

当然，它是一个加载在移动原生Web容器上的网页。但它的集成方式使您可以从Web JavaScript访问原生资源。

WebPages（Odoo Web）位于每层的顶部，第二层是Odoo Web（JS）与本机移动组件之间的桥接。

当JavaScript中的任何调用被触发时，它会通过桥接传递，桥接将其传递给原生调用者以执行该操作。

当原生组件完成其工作后，它会再次传递给桥接，您将获得JavaScript中的输出。

原生组件所花费的处理时间取决于您从原生资源请求的内容。例如，相机或GPS位置。

如何使用它？
==============

与Odoo Web框架一样，移动API可以通过获取**web_mobile.rpc**中的对象在任何地方使用。

.. image:: mobile/odoo_mobile_api.png

移动RPC对象提供了一系列可用的方法（这仅适用于移动应用）。

检查方法是否可用，然后执行它。

方法
-------

.. note:: 每个方法返回一个jQuery Deferred对象，该对象返回数据JSON字典。

在设备上显示Toast
~~~~~~~~~~~~~~~~~~~~

.. js:function:: showToast

    :param object args: **message** 要显示的文本

Toast提供有关操作的简单反馈，显示在小弹出窗口中。它只填充消息所需的空间，当前活动保持可见和交互。

.. code-block:: javascript

    mobile.methods.showToast({'message': '消息已发送'});

.. image:: mobile/toast.png

振动设备
~~~~~~~~~~~~~~~~

.. js:function:: vibrate

    :param object args: 按指定的时间（以毫秒为单位）持续振动。

用给定的持续时间振动移动设备。

.. code-block:: javascript

    mobile.methods.vibrate({'duration': 100});

显示带有操作的Snackbar
~~~~~~~~~~~~~~~~~~~~~~~~~

.. js:function:: showSnackBar

    :param object args: (*必需*) **Message** 在Snackbar中显示的消息和操作 **button label** （可选）
    :returns:   ``True``  如果用户点击操作按钮，``False`` 如果Snackbar在一段时间后自动消失。

Snackbars提供有关操作的轻量级反馈。它们在移动设备的屏幕底部或较大设备的左下角显示简短消息。Snackbars显示在屏幕上的所有其他元素之上，并且一次只能显示一个。

.. code-block:: javascript

    mobile.methods.showSnackBar({'message': '消息已删除', 'btn_text': '撤销'}).then(function(result){
        if(result){
            // 执行撤销操作
        }else{
            // Snackbar已消失
        }
    });

.. image:: mobile/snackbar.png

显示通知
~~~~~~~~~~~~~~~~~~~~

.. js:function:: showNotification

    :param object args: **title** （第一行）通知的标题，**message** （第二行）通知的消息，以标准通知的形式。

通知是您可以在应用程序的正常用户界面之外显示给用户的消息。当您告诉系统发布通知时，它首先作为图标出现在通知区域。要查看通知的详细信息，用户打开通知抽屉。通知区域和通知抽屉都是系统控制的区域，用户可以随时查看。

.. code-block:: javascript

    mobile.showNotification({'title': '简单通知', 'message': '这是一个简单通知的测试'})

.. image:: mobile/mobile_notification.png

在设备中创建联系人
~~~~~~~~~~~~~~~~~~~~~~~~

.. js:function:: addContact

    :param object args: 包含联系人的详细信息的字典。可能的键（name、mobile、phone、fax、email、website、street、street2、country_id、state_id、city、zip、parent_id、function 和 image）

使用给定的联系信息创建新的设备联系人。

.. code-block:: javascript

    var contact = {
        'name': 'Michel Fletcher',
        'mobile': '9999999999',
        'phone': '7954856587',
        'fax': '765898745',
        'email': 'michel.fletcher@agrolait.example.com',
        'website': 'http://www.agrolait.com',
        'street': '69 rue de Namur',
        'street2': false,
        'country_id': [21, '比利时'],
        'state_id': false,
        'city': 'Wavre',
        'zip': '1300',
        'parent_id': [8, 'Agrolait'],
        'function': '分析师',
        'image': '<<BASE 64 图像数据>>'
    }

    mobile.methods.addContact(contact);

.. image:: mobile/mobile_contact_create.png

扫描条形码
~~~~~~~~~~~~~~~~~

.. js:function:: scanBarcode

    :returns: 从任何条形码扫描的 ``code``

条形码API实时检测设备上的条形码，无论方向如何。

条形码API可以读取以下条形码格式：

* 1D条形码：EAN-13、EAN-8、UPC-A、UPC-E、Code-39、Code-93、Code-128、ITF、Codabar
* 2D条形码：QR码、数据矩阵、PDF-417、AZTEC

.. code-block:: javascript

    mobile.methods.scanBarcode().then(function(code){
        if(code){
            // 使用扫描的代码执行操作
        }
    });

在设备中切换帐户
~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. js:function:: switchAccount

使用switchAccount在设备上从一个帐户切换到另一个帐户。

.. code-block:: javascript

    mobile.methods.switchAccount();

.. image:: mobile/mobile_switch_account.png
