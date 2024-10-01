================
Odoo 移动应用
================

Odoo 移动应用有两种类型：渐进式 Web 应用（PWA）和商店应用。推荐使用 PWA。

渐进式 Web 应用 (PWA)
=========================

PWA 是基于 Web 的应用程序，旨在跨不同设备和平台运行，利用 Web 浏览器提供类似于原生应用的用户体验。

Odoo PWA 的功能包括：

- 通过将 PWA 添加到设备的主屏幕快速访问
- 无缝和无边界的导航体验
- 推送通知
- SSO 认证

要安装 Odoo PWA，请启动支持 PWA 的浏览器并登录到 Odoo 数据库。安装 PWA 的说明取决于所使用的平台和浏览器。

.. tabs::

   .. tab:: Android

      **Chrome**: 打开 Chrome 菜单 (:guilabel:`⋮`)，选择 :guilabel:`安装应用`，然后点击 :guilabel:`安装`。

      **Firefox**: 打开 Firefox 菜单 (:guilabel:`⋮`)，选择 :guilabel:`安装`，然后长按 Odoo 图标或点击 :guilabel:`自动添加`。

      也可以使用 **Samsung Internet**、**Edge** 和 **Opera** 安装 PWA。

   .. tab:: iOS

      **Safari**: 点击带有向上箭头的方形图标以打开 **分享** 菜单，选择 :guilabel:`添加到主屏幕`，根据需要编辑 PWA 详细信息，然后点击 :guilabel:`添加`。

      在 iOS 16.4 及以上版本中，还可以使用 **Chrome**、**Firefox** 和 **Edge** 安装 PWA。

   .. tab:: 桌面

      **Chrome** 和 **Edge**: 点击地址栏右侧的安装图标，然后点击 :guilabel:`安装`。

.. seealso::
   - `Google Chrome 帮助：使用渐进式 Web 应用
     <https://support.google.com/chrome/answer/9658361>`_
   - `MDN Web 文档：安装和卸载 Web 应用
     <https://developer.mozilla.org/en-US/docs/Web/Progressive_web_apps/Guides/Installing>`_
   - `Microsoft 支持：在 Microsoft Edge 中安装、管理或卸载应用 <https://support.microsoft.com/en-us/topic/install-manage-or-uninstall-apps-in-microsoft-edge-0c156575-a94a-45e4-a54f-3a84846f6113>`_

商店应用
==========

Odoo 移动应用可在 `Google Play 商店 <https://play.google.com/store/apps/details?id=com.odoo.mobile>`_ 和 `Apple App 商店 <https://apps.apple.com/app/odoo/id1272543640>`_ 下载。

.. important::
   iOS 应用无法更新，未来某个时候将被弃用。

尽管商店应用支持多账户，它们不兼容 SSO 认证。
