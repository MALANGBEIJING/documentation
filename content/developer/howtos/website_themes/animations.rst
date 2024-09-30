==========
动画效果
==========

引人注目的动画效果可以让您的网站更加生动。

出现在视窗时的动画
===================

在标准配置中，您可以使用网站构建器在元素出现在视窗中时为列添加动画。Odoo 会检测到元素何时出现在视窗中，并启动动画。可供选择的动画效果包括：

- 淡入
- 弹入
- 旋转进入
- 缩放进入
- …

您可以在自定义主题中轻松地为列定义动画。您需要添加两个类：`o_animate` 和 `o_anim_fade_in`。第二个类根据您想使用的动画类型而变化。

添加 `o_animate_both_scroll` 类以确保每次该列出现在屏幕上时都启动动画。默认情况下，动画仅启动一次。

您还可以直接在 `style` 属性中定义 `animation-duration` 和 `animation-delay`。

**用法**

.. code-block:: xml

   <div class="col-lg-6 o_animate o_anim_fade_in o_animate_both_scroll" style="animation-duration: 2s !important; animation-delay: 1s !important;">
       <h2>部分副标题</h2>
       <p>撰写一到两段文字描述您的产品或服务。</p>
   </div>

.. image:: animations/animations.png
   :alt: 动画选项

.. seealso::
   `网站动画
   <https://github.com/odoo/odoo/blob/34c0c9c1ae00aba391932129d4cefd027a9c6bbd/addons/website/static/src/scss/website.scss#L1638>`_
