===============
连接电子秤
===============

可以通过几个简单的步骤将电子秤连接到 Odoo 数据库中的 :abbr:`IoT (物联网)` 盒子。设置完成后，*销售点 (PoS)* 应用可以用于称量产品，这对于基于重量计算价格的产品非常有帮助。

.. important::
   - 在欧盟成员国，使用电子秤作为集成设备时，`认证是法律要求 <https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=uriserv%3AOJ.L_.2014.096.01.0107.01.ENG>`_。
   - Odoo 在包括法国、德国和瑞士在内的多个国家尚未获得认证。如果您居住在这些国家，仍然可以使用电子秤，但不能将其与 Odoo 数据库集成。
   - 作为替代方案，您可以选择购买*非集成*的认证秤，这些秤可以打印认证标签，之后可以将标签扫描到您的 Odoo 数据库中。

.. seealso::
   `欧洲议会指令 2014/31/EU <https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=uriserv%3AOJ.L_.2014.096.01.0107.01.ENG>`_

连接
==========

要将电子秤连接到 :abbr:`IoT (物联网)` 盒子，请使用 :abbr:`USB (通用串行总线)` 电缆进行连接。

.. note::
   在某些情况下，可能需要使用串口转 :abbr:`USB (通用串行总线)` 适配器。

如果电子秤 `兼容 Odoo IoT 盒子 <https://www.odoo.com/page/iot-hardware>`_，则无需进行任何设置，因为连接后会自动检测到。

.. image:: scale/iot-choice.png
   :align: center
   :alt: IOT 盒子自动检测。

在某些情况下，可能需要重新启动 :abbr:`IoT (物联网)` 盒子，并且可能需要下载电子秤的驱动程序到盒子中。要更新驱动程序，请访问 :abbr:`IoT (物联网)` 盒子主页并点击 :guilabel:`驱动程序列表`，然后点击 :guilabel:`加载驱动程序`。

.. image:: scale/driver-list.png
   :align: center
   :alt: 查看 IoT 盒子设置和驱动程序列表。

如果加载驱动程序后仍无法正常使用电子秤，则可能该电子秤与 Odoo :abbr:`IoT (物联网)` 盒子不兼容。在这种情况下，需要更换其他电子秤。

在销售点 (PoS) 系统中使用电子秤
===========================================

要在 *销售点 (PoS) 应用* 中使用电子秤，导航到 :menuselection:`PoS 应用 --> PoS 的三点菜单 --> 设置`，然后启用 :abbr:`IoT (物联网)` 盒子功能。完成后，可以设置电子秤设备。

从 :guilabel:`电子秤` 下拉菜单中选择电子秤。然后点击 :guilabel:`保存` 以保存更改（如有需要）。

.. image:: scale/electronic-scale-feature.png
   :align: center
   :alt: PoS 和 IoT 盒子中可使用的外部工具列表。

现在，电子秤可以在所有 :abbr:`PoS (销售点)` 会话中使用。如果某个产品设置了按重量计价，点击 PoS 屏幕上的该产品将打开秤重屏幕，收银员可以称量产品并将正确的价格添加到购物车中。

.. image:: scale/scale-view.png
   :align: center
   :alt: 电子秤在未称量商品时的仪表板视图。
