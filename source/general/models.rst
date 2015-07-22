######
模型
######

模型对于那些想使用更传统的 MVC 模式的人来说是可选的。

.. contents:: 本页内容

什么是模型？
================

模型是专门用来和数据库打交道的 PHP 类。例如，假设你使用 CodeIgniter
管理一个博客，那么你应该会有一个用于插入、更新以及获取博客数据的模型类。
这里是一个模型类的例子::

	class Blog_model extends CI_Model {

		public $title;
		public $content;
		public $date;

		public function __construct()
		{
			// Call the CI_Model constructor
			parent::__construct();
		}

		public function get_last_ten_entries()
		{
			$query = $this->db->get('entries', 10);
			return $query->result();
		}

		public function insert_entry()
		{
			$this->title	= $_POST['title']; // please read the below note
			$this->content	= $_POST['content'];
			$this->date	= time();

			$this->db->insert('entries', $this);
		}

		public function update_entry()
		{
			$this->title	= $_POST['title'];
			$this->content	= $_POST['content'];
			$this->date	= time();

			$this->db->update('entries', $this, array('id' => $_POST['id']));
		}

	}

.. note:: 上面的例子中使用了 :doc:`查询构造器 <../database/query_builder>` 数据库方法。

.. note:: 为了保证简单，我们在这个例子中直接使用了 ``$_POST`` 数据，这其实是个不好的实践，
	一个更通用的做法是使用 :doc:`输入库 <../libraries/input>`` 的 ``$this->input->post('title')``。

剖析模型
==================

模型类位于你的 **application/models/** 目录下，如果你愿意，也可以在里面创建子目录。

模型类的基本原型如下::

	class Model_name extends CI_Model {

		public function __construct()
		{
			parent::__construct();
		}

	}

其中，**Model_name** 是类的名字，类名的第一个字母 **必须** 大写，其余部分小写。确保你的类
继承 CI_Model 基类。

文件名和类名应该一致，例如，如果你的类是这样::

	class User_model extends CI_Model {

		public function __construct()
		{
			parent::__construct();
		}

	}

那么你的文件名应该是这样::

	application/models/User_model.php

加载模型
===============

你的模型一般会在你的 :doc:`控制器 <controllers>` 的方法中加载并调用，
你可以使用下面的方法来加载模型::

	$this->load->model('model_name');

如果你的模型位于一个子目录下，那么加载时要带上你的模型所在目录的相对路径，
譬如，如果你的模型位于 *application/models/blog/Queries.php* ，
你可以这样加载它::

	$this->load->model('blog/queries');

加载之后，你就可以通过一个和你的类同名的对象访问模型中的方法。
::

	$this->load->model('model_name');

	$this->model_name->method();

如果你想将你的模型对象赋值给一个不同名字的对象，你可以使用 ``$this->load->model()``
方法的第二个参数::

	$this->load->model('model_name', 'foobar');

	$this->foobar->method();

这里是一个例子，该控制器加载一个模型，并处理一个视图::

	class Blog_controller extends CI_Controller {

		public function blog()
		{
			$this->load->model('blog');

			$data['query'] = $this->blog->get_last_ten_entries();

			$this->load->view('blog', $data);
		}
	}
	

模型的自动加载
===================

如果你发现你有一个模型需要在整个应用程序中使用，你可以让 CodeIgniter
在系统初始化时自动加载它。打开 **application/config/autoload.php** 文件，
并将该模型添加到 autoload 数组中。

连接数据库
===========================

当模型加载之后，它 **并不会** 自动去连接你的数据库，下面是一些关于
数据库连接的选项：

-  你可以在控制器或模型中使用 :doc:`标准的数据库方法 <../database/connecting>` 连接数据库。
-  你可以设置第三个参数为 TRUE 让模型在加载时自动连接数据库，会使用你的数据库配置文件中的配置::

	$this->load->model('model_name', '', TRUE);

-  你还可以通过第三个参数传一个数据库连接配置::

	$config['hostname'] = 'localhost';
	$config['username'] = 'myusername';
	$config['password'] = 'mypassword';
	$config['database'] = 'mydatabase';
	$config['dbdriver'] = 'mysqli';
	$config['dbprefix'] = '';
	$config['pconnect'] = FALSE;
	$config['db_debug'] = TRUE;

	$this->load->model('model_name', '', $config);