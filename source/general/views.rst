#####
视图
#####

简单来说，一个视图其实就是一个 Web 页面，或者页面的一部分，像页头、页脚、侧边栏等。
实际上，视图可以很灵活的嵌在另一个视图里，然后这个视图再嵌在另一个视图里，等等，
如果你想使用这种层次结构的话，可以这样做。

视图不是直接被调用的，它必须通过 :doc:`控制器 <controllers>` 来加载。在 MVC 框架里，
控制器扮演着类似于交警的角色，它专门负责读取特定的视图。如果你还没有读过
:doc:`控制器 <controllers>` 页面，你应该先看下这个。

使用在 :doc:`控制器 <controllers>` 页面中创建的控制器例子，让我们再添加一个视图。

创建视图
===============

使用你的文本编辑器，创建一个 blogview.php 文件，代码如下::

	<html>
	<head>
		<title>My Blog</title>
	</head>
	<body>
		<h1>Welcome to my Blog!</h1>
	</body>
	</html>
	
然后保存到你的 *application/views/* 目录下。

加载视图
==============

使用下面的方法来加载指定的视图::

	$this->load->view('name');

name 参数为你的视图文件名。

.. note:: 文件的扩展名 .php 可以省略，除非你使用了其他的扩展名。

现在，打开你之前创建的控制器文件 Blog.php ，然后将 echo 语句替换成
加载视图的代码::

	<?php
	class Blog extends CI_Controller {

		public function index()
		{
			$this->load->view('blogview');
		}
	}

跟之前一样，通过类似于下面的 URL 来访问你的网站，你将看到新的页面::

	example.com/index.php/blog/

加载多个视图
======================

CodeIgniter 可以智能的处理在控制器中多次调用 ``$this->load->view()`` 方法。
如果出现了多次调用，视图会被合并到一起。例如，你可能希望有一个页头视图、
一个菜单视图，一个内容视图 以及 一个页脚视图。代码看起来应该这样::

	<?php

	class Page extends CI_Controller {

		public function index()
		{
			$data['page_title'] = 'Your title';
			$this->load->view('header');
			$this->load->view('menu');
			$this->load->view('content', $data);
			$this->load->view('footer');
		}

	}

在上面的例子中，我们使用了 "添加动态数据" ，我们会在后面讲到。

在子目录中存储视图
====================================

如果你喜欢的话，你的视图文件可以放到子目录下组织存储，当你这样做，
加载视图时需要包含子目录的名字，例如::

	$this->load->view('directory_name/file_name');

向视图添加动态数据
===============================

通过视图加载方法的第二个参数可以从控制器中动态的向视图传入数据，
这个参数可以是一个 **数组** 或者一个 **对象** 。这里是使用数组的例子::

	$data = array(
		'title' => 'My Title',
		'heading' => 'My Heading',
		'message' => 'My Message'
	);

	$this->load->view('blogview', $data);

这里是使用对象的例子::

	$data = new Someclass();
	$this->load->view('blogview', $data);

.. note:: 当你使用对象时，对象中的变量会转换为数组元素。

让我们在你的控制器文件中尝试一下，添加如下代码::

	<?php
	class Blog extends CI_Controller {

		public function index()
		{
			$data['title'] = "My Real Title";
			$data['heading'] = "My Real Heading";

			$this->load->view('blogview', $data);
		}
	}

再打开你的视图文件，将文本修改为传入的数组对应的变量::

	<html>
	<head>
		<title><?php echo $title;?></title>
	</head>
	<body>
		<h1><?php echo $heading;?></h1>
	</body>
	</html>

然后通过刚刚的 URL 重新加载页面，你应该可以看到变量被替换了。

使用循环
==============

传入视图文件的数据不仅仅限制为普通的变量，你还可以传入多维数组，
这样你就可以在视图中生成多行了。例如，如果你从数据库中获取数据，
一般情况下数据都是一个多维数组。

这里是个简单的例子，将它添加到你的控制器中::

	<?php
	class Blog extends CI_Controller {

		public function index()
		{
			$data['todo_list'] = array('Clean House', 'Call Mom', 'Run Errands');

			$data['title'] = "My Real Title";
			$data['heading'] = "My Real Heading";

			$this->load->view('blogview', $data);
		}
	}

然后打开你的视图文件，创建一个循环::

	<html>
	<head>
		<title><?php echo $title;?></title>
	</head>
	<body>
		<h1><?php echo $heading;?></h1>
	
		<h3>My Todo List</h3>

		<ul>
		<?php foreach ($todo_list as $item):?>
	
			<li><?php echo $item;?></li>
	
		<?php endforeach;?>
		</ul>

	</body>
	</html>

.. note:: 你会发现在上例中，我们使用了 PHP 的替代语法，如果你对其还不熟悉，可以阅读
	:doc:`这里 <alternative_php>` 。

将视图作为数据返回
=======================

加载视图方法有一个可选的第三个参数可以让你修改它的默认行为，它让视图作为字符串返回
而不是显示到浏览器中，这在你想对视图数据做某些处理时很有用。如果你将该参数设置为 TRUE ，
该方法返回字符串，默认情况下为 FALSE ，视图将显示到浏览器。如果你需要返回的数据，
记住将它赋值给一个变量::

	$string = $this->load->view('myfile', '', TRUE);