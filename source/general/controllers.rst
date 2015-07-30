###########
控制器
###########

控制器是你整个应用的核心，因为它们决定了 HTTP 请求将被如何处理。

.. contents:: Page Contents

什么是控制器？
=====================

**简单来说，控制器就是一个类文件，它的命名和 URI 有着一定的联系。**

考虑下面的 URL ::

	example.com/index.php/blog/

上例中，CodeIgniter 将会尝试查询一个名为 Blog.php 的控制器并加载它。

**当控制器的名称和 URI 的第一段匹配上时，它将会被加载。**

让我们试试看：Hello World！
============================

接下来你会看到如何创建一个简单的控制器，打开你的文本编辑器，新建一个文件 Blog.php ，
然后放入以下代码::

	<?php
	class Blog extends CI_Controller {

		public function index()
		{
			echo 'Hello World!';
		}
	}

然后将文件保存到 *application/controllers/* 目录下。

.. important:: 文件名必须是大写字母开头，如：'Blog.php' 。

现在使用类似下面的 URL 来访问你的站点::

	example.com/index.php/blog/

如果一切正常，你将看到：

	Hello World!

.. important:: 类名必须以大写字母开头。

这是有效的::

	<?php
	class Blog extends CI_Controller {

	}
	
这是无效的::

	<?php
	class blog extends CI_Controller {

	}

另外，一定要确保你的控制器继承了父控制器类，这样它才能使用父类的方法。

方法
=======

上例中，方法名为 ``index()`` 。"index" 方法总是在 URI 的 **第二段** 为空时被调用。
另一种显示 "Hello World" 消息的方法是::

	example.com/index.php/blog/index/

**URI 中的第二段用于决定调用控制器中的哪个方法。**

让我们试一下，向你的控制器添加一个新的方法::

	<?php
	class Blog extends CI_Controller {

		public function index()
		{
			echo 'Hello World!';
		}

		public function comments()
		{
			echo 'Look at this!';
		}
	}

现在，通过下面的 URL 来调用 comments 方法::

	example.com/index.php/blog/comments/

你应该能看到你的新消息了。

通过 URI 分段向你的方法传递参数
====================================

如果你的 URI 多于两个段，多余的段将作为参数传递到你的方法中。

例如，假设你的 URI 是这样::

	example.com/index.php/products/shoes/sandals/123

你的方法将会收到第三段和第四段两个参数（"sandals" 和 "123"）::

	<?php
	class Products extends CI_Controller {

		public function shoes($sandals, $id)
		{
			echo $sandals;
			echo $id;
		}
	}

.. important:: 如果你使用了 :doc:`URI 路由 <routing>` ，传递到你的方法的参数将是路由后的参数。

定义默认控制器
=============================

CodeIgniter 可以设置一个默认的控制器，当 URI 没有分段参数时加载，譬如当用户直接访问你网站的首页时。
打开 **application/config/routes.php** 文件，通过下面的参数指定一个默认的控制器::

	$route['default_controller'] = 'blog';

其中，Blog 是你想加载的控制器类名，如果你现在通过不带任何参数的 index.php 访问你的站点，你将看到你的
Hello World 消息。

重映射方法
======================

正如上文所说，URI 的第二段通常决定控制器的哪个方法被调用。CodeIgniter 允许你使用 ``_remap()`` 
方法来重写该规则::

	public function _remap()
	{
		// Some code here...
	}

.. important:: 如果你的控制包含一个 _remap() 方法，那么无论 URI 中包含什么参数时都会调用该方法。
	它允许你定义你自己的路由规则，重写默认的使用 URI 中的分段来决定调用哪个方法这种行为。

被重写的方法（通常是 URI 的第二段）将被作为参数传递到 ``_remap()`` 方法::

	public function _remap($method)
	{
		if ($method === 'some_method')
		{
			$this->$method();
		}
		else
		{
			$this->default_method();
		}
	}

方法名之后的所有其他段将作为 ``_remap()`` 方法的第二个参数，它是可选的。这个参数可以使用 PHP 的
`call_user_func_array() <http://php.net/call_user_func_array>`_ 函数来模拟 CodeIgniter 的默认行为。

例如::

	public function _remap($method, $params = array())
	{
		$method = 'process_'.$method;
		if (method_exists($this, $method))
		{
			return call_user_func_array(array($this, $method), $params);
		}
		show_404();
	}

处理输出
=================

CodeIgniter 有一个输出类，它可以自动的将最终数据发送到你的浏览器。更多信息可以阅读 
:doc:`视图 <views>` and :doc:`输出类 <../libraries/output>` 页面。但是，有时候，
你可能希望对最终的数据进行某种方式的后处理，然后你自己手工发送到浏览器。CodeIgniter
允许你向你的控制器中添加一个 ``_output()`` 方法，该方法可以接受最终的输出数据。

.. important:: 如果你的控制器含有一个 ``_output()`` 方法，输出类将会调用该方法来显示数据，
	而不是直接显示数据。该方法的第一个参数包含了最终输出的数据。

这里是个例子::

	public function _output($output)
	{
		echo $output;
	}

.. note::

	请注意，当数据传到 ``_output()`` 方法时，数据已经是最终状态。这时基准测试和计算内存占用都已经完成，
	缓存文件也已经写到文件（如果你开启缓存的话），HTTP 头也已经发送（如果用到了该 :doc:`特性 
	<../libraries/output>`）。为了使你的控制器能正确处理缓存，``_output()`` 可以这样写::

		if ($this->output->cache_expiration > 0)
		{
			$this->output->_write_cache($output);
		}

	如果你在使用 ``_output()`` 时，希望获取页面执行时间和内存占用情况，结果可能会不准确，
	因为并没有统计你后加的处理代码。另一个可选的方法是在所有最终输出 *之前* 来进行处理，
	请参阅 :doc:`输出类 <../libraries/output>` 。

私有方法
===============

有时候你可能希望某些方法不能被公开访问，要实现这点，只要简单的将方法声明为 private 或 protected ，
这样这个方法就不能被 URL 访问到了。例如，如果你有一个下面这个方法::

	private function _utility()
	{
		// some code
	}

使用下面的 URL 尝试访问它，你会发现是无法访问的::

	example.com/index.php/blog/_utility/

.. note:: 在方法名前加上一个下划线前缀也可以让该方法无法访问。这是个遗留特性，为实现向前兼容。

将控制器放入子目录中
================================================

如果你正在构建一个比较大的应用，那么将控制器放到子目录下进行组织可能会方便一点。CodeIgniter
也可以实现这一点。

你只需要简单的在 *application/controllers/* 目录下创建新的目录，并将控制器类放到子目录下。

.. note:: 当使用该功能时，URI 的第一段必须制定目录，例如，假设你在如下位置有一个控制器::

		application/controllers/products/Shoes.php

	为了调用该控制器，你的 URI 应该像下面这样::

		example.com/index.php/products/shoes/show/123

每个子目录下都应该包含一个默认控制器，这样当 URL 中只有子目录路径时将会调用它。你可以在
*application/config/routes.php* 文件中配置默认控制器。

你也可以使用 CodeIgniter 的 :doc:`URI 路由 <routing>` 功能 来重定向 URI 。

构造函数
==================

如果你打算在你的控制器中使用构造函数，你 **必须** 将下面这行代码放在里面::

	parent::__construct();

原因是你的构造函数将会覆盖父类的构造函数，所以我们要手工的调用它。

例如::

	<?php
	class Blog extends CI_Controller {

		public function __construct()
		{
			parent::__construct();
			// Your own constructor code
		}
	}

如果你需要在你的类被初始化时设置一些默认值，或者进行一些默认处理，构造函数将很有用。
构造函数没有返回值，但是可以执行一些默认操作。

保留方法名
=====================

因为你的控制器将继承主程序的控制器，在新建方法时你必须要小心不要使用和父类一样的方法名，
要不然你的方法将覆盖它们，参见 :doc:`保留名称 <reserved_names>` 。

.. important:: 另外，你也绝对不要新建一个和类名称一样的方法。如果你这样做了，而且你的控制器
	又没有一个 ``__construct()`` 构造函数，那么这个和类名同名的方法 ``Index::index()`` 
	将会作为类的构造函数被执行！这个是 PHP4 的向前兼容的一个特性。

就这样了！
==========

OK，总的来说，这就是关于控制器的所有内容了。
