################
CodeIgniter URL
################

默认情况下，CodeIgniter 中的 URL 被设计成对搜索引擎和人类友好。
不同于使用标准的 “查询字符串” 方法，CodeIgniter 使用基于段的方法::

	example.com/news/article/my_article

.. note:: 在 CodeIgniter 中也可以使用查询字符串的方法，参见下文。

URI 分段
============

如果遵循模型-视图-控制器模式，那么 URI 中的每一段通常表示下面的含义::

	example.com/class/function/ID

#. 第一段表示要调用的控制器 **类** ；
#. 第二段表示要调用的类中的 **函数** 或 **方法** ；
#. 第三段以及后面的段代表传给控制器的参数，如 ID 或其他任何变量；

:doc:`URI 类 <../libraries/uri>` 和 :doc:`URL 辅助库 <../helpers/url_helper>`
包含了一些函数可以让你更容易的处理 URI 数据，另外，你的 URL 可以通过 
:doc:`URI 路由 <routing>` 进行重定向从而得到更大的灵活性。

移除 URL 中的 index.php
===========================

默认情况，你的 URL 中会包含 **index.php** 文件::

	example.com/index.php/news/article/my_article

如果你的 Apache 服务器启用了 *mod_rewrite* ，你可以简单的通过一个 .htaccess
文件再加上一些简单的规则就可以移除 index.php 了。下面是这个文件的一个例子，
其中使用了 "否定条件" 来排除某些不需要重定向的项目：

::
	
	RewriteEngine On
	RewriteCond %{REQUEST_FILENAME} !-f
	RewriteCond %{REQUEST_FILENAME} !-d
	RewriteRule ^(.*)$ index.php/$1 [L]

在上面的例子中，除已存在的目录和文件，其他的 HTTP 请求都会经过你的 index.php 文件。

.. note:: 这些规则并不是对所有 Web 服务器都有效。

.. note:: 确保使用上面的规则排除掉你希望能直接访问到的资源。

添加 URL 后缀
===================

在你的 **config/config.php** 文件中你可以指定一个后缀，CodeIgniter
生成 URL 时会自动添加上它。譬如，一个像这样的 URL::

	example.com/index.php/products/view/shoes

你可以添加一个后缀，如：**.html** ，这样页面看起来就是这个样子::

	example.com/index.php/products/view/shoes.html

启用查询字符串
======================

有些时候，你可能更喜欢使用查询字符串格式的 URL::

	index.php?c=products&m=view&id=345

CodeIgniter 也支持这个格式，你可以在 **application/config.php** 配置文件中启用它。
打开你的配置文件，查找下面这几项::

	$config['enable_query_strings'] = FALSE;
	$config['controller_trigger'] = 'c';
	$config['function_trigger'] = 'm';

你只要把 "enable_query_strings" 参数设为 TRUE 即可启用该功能。然后通过你设置的
trigger 关键字来访问你的控制器和方法::

	index.php?c=controller&m=method

.. note:: 如果你使用查询字符串格式的 URL，你就必须自己手工构造 URL 而不能使用 URL 
	辅助库了（以及其他生成 URL 相关的库，譬如表单辅助库），这是由于这些库只能处理
	分段格式的 URL 。