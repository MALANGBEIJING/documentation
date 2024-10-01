.. _reference/controllers:

===============
Web 控制器
===============

控制器
===========

控制器需要提供可扩展性，类似于 :class:`~odoo.models.Model`，但不能使用相同的机制，因为前置条件（加载模块的数据库）可能尚未可用（例如尚未创建数据库，或未选择数据库）。

因此，控制器提供了与模型分离的扩展机制：

控制器通过继承 :class:`~odoo.http.Controller` 创建。路由通过使用 :func:`~odoo.http.route` 装饰的方法定义::

    class MyController(odoo.http.Controller):
        @route('/some_url', auth='public')
        def handler(self):
            return stuff()

要*重写*控制器，继承其类并重写相关方法，必要时重新公开它们::

    class Extension(MyController):
        @route()
        def handler(self):
            do_before()
            return super(Extension, self).handler()

* 使用 :func:`~odoo.http.route` 装饰是保持方法（及其路由）可见的必要步骤：如果重新定义方法时未装饰，它将“取消发布”。
* 所有方法的装饰器会组合在一起，如果重写方法的装饰器没有参数，则保留之前的所有装饰器，任何提供的参数将覆盖先前定义的参数，例如::

    class Restrict(MyController):
        @route(auth='user')
        def handler(self):
            return super(Restrict, self).handler()

  这将把 ``/some_url`` 的身份验证从公开更改为用户（需要登录）。

API
===

.. _reference/http/routing:

路由
-------

.. autodecorator:: odoo.http.route

.. _reference/http/request:

请求
-------

请求对象会在请求开始时自动设置为 :data:`odoo.http.request`。

.. autoclass:: odoo.http.Request
    :members:
    :member-order: bysource

.. autoclass:: odoo.http.JsonRPCDispatcher
    :members:
    :member-order: bysource
.. autoclass:: odoo.http.HttpDispatcher
    :members:
    :member-order: bysource

响应
--------

.. autoclass:: odoo.http.Response
    :members:
    :member-order: bysource

    .. 也许可以设置为记录 Werkzeug 的 Response 对象上的所有方法？（它确实有效）
       :inherited-members:
