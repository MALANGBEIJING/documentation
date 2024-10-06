=================
使用 LDAP 登录
=================

- 在常规设置中安装轻量级目录访问协议 (LDAP) 模块。

- 点击 **创建** 以设置 :abbr:`LDAP (轻量级目录访问协议)` 服务器。

.. image:: ldap/ldap01.png
    :align: center
    :alt: Odoo 集成设置中高亮显示的 LDAP 认证复选框。

.. image:: ldap/ldap02.png
    :align: center
    :alt: 在 LDAP 服务器设置中高亮显示的创建按钮。

- 选择使用 LDAP 的公司。

.. image:: ldap/ldap03.png
    :align: center
    :alt: 在 LDAP 设置中高亮显示的选择公司下拉菜单。

- 在 **服务器信息** 中，输入服务器的 IP 地址以及其监听的端口。

- 如果服务器兼容，请勾选 **使用 TLS**。

.. image:: ldap/ldap04.png
    :align: center
    :alt: Odoo 中 LDAP 服务器设置中的高亮显示。

- 在 **登录信息** 中，输入用于查询服务器的账户 ID 和密码。如果留空，服务器将以匿名方式查询。

.. image:: ldap/ldap05.png
    :align: center
    :alt: Odoo 中 LDAP 服务器设置中的登录信息高亮显示。

- 在 **处理参数** 中，以 :abbr:`LDAP (轻量级目录访问协议)` 的命名规则输入 LDAP 服务器的域名（例如 ``dc=example,dc=com``）。

- 在 **LDAP 过滤器** 中，输入 ``uid=%s``。

.. image:: ldap/ldap06.png
    :align: center
    :alt: Odoo 中 LDAP 服务器设置中的处理参数高亮显示。

- 在 **用户信息** 中，如果 Odoo 应该在第一次使用 :abbr:`LDAP (轻量级目录访问协议)` 登录时创建用户配置文件，请勾选 *创建用户*。

- 在 **模板用户** 中，为新创建的配置文件指定一个模板。如果留空，将使用管理员配置文件作为模板。

.. image:: ldap/ldap07.png
    :align: center
    :alt: Odoo 中 LDAP 服务器设置中的用户信息高亮显示。
