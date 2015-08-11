############
加载静态内容
############

**Note:** 这篇教程假设你已经下载好 CodeIgniter ，并将其 :doc:`安装 <../installation/index>` 
到你的开发环境。

你要做的第一件事情是新建一个 **控制器** 来处理静态页面，控制器就是一个简单的类，
用来完成你的工作，它是你整个 Web 应用程序的 “粘合剂” 。

例如，当访问下面这个 URL 时:

	http://example.com/news/latest/10

通过这个 URL 我们就可以推测出来，有一个叫做 "news" 的控制器，被调用的方法为 "latest" ，
这个方法的作用应该是查询 10 条新闻条目并显示在页面上。在 MVC 模式里，你会经常看到下面
格式的 URL ：

	http://example.com/[controller-class]/[controller-method]/[arguments]

在正式环境下 URL 的格式可能会更复杂，但是现在，我们只需要关心这些就够了。

新建一个文件 application/controllers/Pages.php ，然后添加如下代码。

::

	<?php 
	class Pages extends CI_Controller { 

		public function view($page = 'home') 
		{
	    }
	}

你刚刚创建了一个 ``Pages`` 类，有一个方法 view 并可接受一个 $page 参数。
``Pages`` 类继承自 ``CI_Controller`` 类，这意味着它可以访问 ``CI_Controller``
类（ *system/core/Controller.php* ）中定义的方法和变量。

控制器将会成为你的 Web 应用程序中的处理请求的核心，在关于 CodeIgniter 
的技术讨论中，这有时候被称作 **超级对象** 。和其他的 PHP 类一样，可以在
你的控制器中使用 ``$this`` 来访问它，通过 ``$this`` 你就可以加载类库、
视图、以及针对框架的一般性操作。

现在，你已经创建了你的第一个方法，是时候创建一些基本的页面模板了，我们将
新建两个视图（页面模板）分别作为我们的页脚和页头。

新建页头文件 *application/views/templates/header.php* 并添加以下代码：

::

	<html>
		<head>
			<title>CodeIgniter Tutorial</title>
		</head>
		<body>

			<h1><?php echo $title; ?></h1>

页头包含了一些基本的 HTML 代码，用于显示页面的主视图之前的内容。
另外，它还打印出了 ``$title`` 变量，这个我们后面讲控制器的时候再讲。
现在，再新建个页脚文件 *application/views/templates/footer.php* ，然后添加以下代码：

::

			<em>&copy; 2015</em>
		</body>
	</html>

在控制器中添加逻辑
------------------------------

你刚刚新建了一个控制器，里面有一个 ``view()`` 方法，这个方法接受一个参数
用于指定要加载的页面，静态页面模板位于 *application/views/pages/* 目录。

在该目录中，再新建两个文件 *home.php* 和 *about.php* ，在每个文件里随便
写点东西然后保存它们。如果你没什么好写的，就写 "Hello World!" 吧。

为了加载这些页面，你需要先检查下请求的页面是否存在：

::

	public function view($page = 'home')
	{
	    if ( ! file_exists(APPPATH.'/views/pages/'.$page.'.php'))
		{
			// Whoops, we don't have a page for that!
			show_404();
		}

		$data['title'] = ucfirst($page); // Capitalize the first letter

		$this->load->view('templates/header', $data);
		$this->load->view('pages/'.$page, $data);
		$this->load->view('templates/footer', $data);
	}

当请求的页面存在，将包括页面和页脚一起被加载并显示给用户，如果不存在，
会显示一个 "404 Page not found" 错误。

第一行检查页面是否存在，``file_exists()`` 是个原生的 PHP 函数，用于检查某个
文件是否存在，``show_404()`` 是个 CodeIgniter 内置的函数，用来显示一个默认的
错误页面。

在页头文件中，``$title`` 变量用来自定义页面的标题，它是在这个方法中赋值的，
但是注意的是并不是直接赋值给 title 变量，而是赋值给一个 ``$data`` 数组的
title 元素。

最后要做的是按顺序加载所需的视图，``view()`` 方法的第二个参数用于向视图传递参数，
``$data`` 数组中的每一项将被赋值给一个变量，这个变量的名字就是数组的键值。
所以控制器中 ``$data['title']`` 的值，就等于视图中的 ``$title`` 的值。

路由
-------

控制器现在开始工作了！在你的浏览器中输入 ``[your-site-url]index.php/pages/view``
来查看你的页面。当你访问 ``index.php/pages/view/about`` 时你将看到 about 页面，
包括页头和页脚。

使用自定义的路由规则，你可以将任意的 URI 映射到任意的控制器和方法上，从而打破
默认的规则：

``http://example.com/[controller-class]/[controller-method]/[arguments]``

让我们来试试。打开文件 *application/config/routes.php* 然后添加如下两行代码，
并删除掉其他对 ``$route`` 数组赋值的代码。

::

	$route['default_controller'] = 'pages/view';
	$route['(:any)'] = 'pages/view/$1';

CodeIgniter 从上到下读取路由规则并将请求映射到第一个匹配的规则，每一个规则都是
一个正则表达式（左侧）映射到 一个控制器和方法（右侧）。当有请求到来时，CodeIgniter
首先查找能匹配的第一条规则，然后调用相应的控制器和方法，可能还带有参数。

你可以在关于 :doc:`URI 路由的文档 <../general/routing>` 中找到更多信息。

这里，第二条规则中 ``$routes`` 数组使用了通配符 ``(:any)`` 可以匹配所有的请求，
然后将参数传递给 ``Pages`` 类的 ``view()`` 方法。

现在访问 ``index.php/about`` 。路由规则是不是正确的将你带到了控制器中的 ``view()``
方法？实在是太棒了！
