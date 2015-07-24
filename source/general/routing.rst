###########
URI 路由
###########

一般情况下，一个 URL 字符串和它对应的控制器中类和方法是一一对应的关系。
URL 中的每一段通常遵循下面的规则::

	example.com/class/function/id/

但是有时候，你可能想改变这种映射关系，调用一个不同的类和方法，而不是 
URL 中对应的那样。

例如，假设你希望你的 URL 变成下面这样::

	example.com/product/1/
	example.com/product/2/
	example.com/product/3/
	example.com/product/4/

URL 的第二段通常表示方法的名称，但在上面的例子中，第二段是一个商品 ID ，
为了实现这一点，CodeIgniter 允许你重新定义 URL 的处理流程。

设置你自己的路由规则
==============================

路由规则定义在 *application/config/routes.php* 文件中，在这个文件中你会
发现一个名为 ``$route`` 的数组，利用它你可以设置你自己的路由规则。
在路由规则中你可以使用通配符或正则表达式。

通配符
=========

一个典型的使用通配符的路由规则如下::

	$route['product/:num'] = 'catalog/product_lookup';

在一个路由规则中，数组的键表示要匹配的 URI ，而数组的值表示要重定向的位置。
上面的例子中，如果 URL 的第一段是字符串 "product" ，第二段是个数字，那么，
将调用 "catalog" 类的 "product_lookup" 方法。

你可以使用纯字符串匹配，或者使用下面两种通配符：

**(:num)** 匹配只含有数字的一段。
**(:any)** 匹配含有任意字符的一段。（除了 '/' 字符，因为它是段与段之间的分隔符）

.. note:: 通配符实际上是正则表达式的别名，**:any** 会被转换为 **[^/]+** ，
	**:num** 会被转换为 **[0-9]+** 。

.. note:: 路由规则将按照它们定义的顺序执行，前面的规则优先级高于后面的规则。

.. note:: 路由规则并不是过滤器！设置一个这样的路由：'foo/bar/(:num)' ，
	 *Foo* 控制器的 *bar* 方法还是有可能会通过一个非数字的参数被调用
	 （如果这个路由也是合法的话）。

例子
========

这里是一些路由的例子::

	$route['journals'] = 'blogs';

URL 的第一段是单词 "journals" 时，将重定向到 "blogs" 类。

::

	$route['blog/joe'] = 'blogs/users/34';

URL 包含 blog/joe 的话，将重定向到 "blogs" 类和 "users" 方法。ID 参数设为 "34" 。

::

	$route['product/(:any)'] = 'catalog/product_lookup';

URL 的第一段是 "product" ，第二段是任意字符时，将重定向到 "catalog" 类的
"product_lookup" 方法。

::

	$route['product/(:num)'] = 'catalog/product_lookup_by_id/$1';

URL 的第一段是 "product" ，第二段是数字时，将重定向到 "catalog" 类的
"product_lookup_by_id" 方法，并将第二段的数字作为参数传递给它。

.. important:: 不要在前面或后面加反斜线（'/'）。

正则表达式
===================

如果你喜欢，你可以在路由规则中使用正则表达式。任何有效的正则表达式都是
允许的，包括逆向引用。

.. note:: 如果你使用逆向引用，你需要使用美元符号代替双斜线语法。

一个典型的使用正则表达式的路由规则看起来像下面这样::

	$route['products/([a-z]+)/(\d+)'] = '$1/id_$2';

上例中，一个类似于 products/shirts/123 这样的 URL 将会重定向到 "shirts"
控制器的 "id_123" 方法。

使用正则表达式，你还可以匹配含有反斜线字符（'/'）的段，它通常来说是
多个段之间的分隔符。

例如，当一个用户访问你的 Web 应用中的某个受密码保护的页面时，如果他没有
登陆，会先跳转到登陆页面，你希望在他们在成功登陆后重定向回刚才那个页面，
那么这个例子会很有用::

	$route['login/(.+)'] = 'auth/login/$1';

如果你还不知道正则表达式，可以访问 `regular-expressions.info <http://www.regular-expressions.info/>`
开始学习一下。

.. note:: 你也可以在你的路由规则中混用通配符和正则表达式。

回调函数
=========

如果你正在使用的 PHP 版本高于或等于 5.3 ，你还可以在路由规则中使用回调函数来处理逆向引用。
例如::

	$route['products/([a-zA-Z]+)/edit/(\d+)'] = function ($product_type, $id)
	{
		return 'catalog/product_edit/' . strtolower($product_type) . '/' . $id;
	};

在路由中使用 HTTP 动词
==========================

还可以在你的路由规则中使用 HTTP 动词（请求方法），当你在创建 RESTful 应用时特别有用。
你可以使用标准的 HTTP 动词（GET、PUT、POST、DELETE、PATCH），也可以使用自定义的动词
（例如：PURGE），不区分大小写。你需要做的就是在路由数组后面再加一个键，键名为 HTTP 
动词。例如::

	$route['products']['put'] = 'product/insert';

上例中，当发送 PUT 请求到 "products" 这个 URI 时，将会调用 ``Product::insert()`` 方法。

::

	$route['products/(:num)']['DELETE'] = 'product/delete/$1';

当发送 DELETE 请求到第一段为 "products" ，第二段为数字这个 URL时，将会调用
``Product::delete()`` 方法，并将数字作为第一个参数。

当然，使用 HTTP 动词是可选的。

保留路由
===============

有下面三个保留路由::

	$route['default_controller'] = 'welcome';

这个路由表示当用户不带任何参数直接访问你的网站时该加载哪个控制器，
上例中，将会加载 "welcome" 类。你应该永远都有个默认的路由，要不然
会显示 404 页面。

::

	$route['404_override'] = '';

这个路由表示当用户请求了一个不存在的页面时该加载哪个控制器，它将会覆盖
默认的 404 错误页面。``show_404()`` 函数不会受影响，它还是会继续加载
*application/views/errors/* 目录下的默认的 *error_404.php* 文件。


::

	$route['translate_uri_dashes'] = FALSE;

从它的布尔值就能看出来这其实并不是一个路由，这个选项可以自动的将 URL
中的控制器和方法中的连字符（'-'）转换为下划线（'_'），当你需要这样时，
它可以让你少写很多路由规则。由于连字符不是一个有效的类名或方法名，
如果你不使用它的话，将会引起一个严重错误。

.. important:: 保留的路由规则必须位于任何一般的通配符或正则路由的前面。