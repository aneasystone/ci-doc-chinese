##################
创建类库
##################

当我们使用 “类库” 这个词的时候，通常我们指的是位于 libraries 这个目录下的那些类，
在我们这份用户手册的类库参考部分有详细的介绍。但是在这篇文章中，我们将介绍
如何在 application/libraries 目录下创建你自己的类库，和全局的框架类库独立开来。

另外，如果你希望在现有的类库中添加某些额外功能，CodeIgniter 允许你扩展原生的类，
或者你甚至可以在你的 *application/libraries* 目录下放置一个和原生的类库同名的文件
完全替代它。

总结起来：

-  你可以创建一个全新的类库，
-  你可以扩展原生的类库，
-  你可以替换掉原生的类库。

下面将详细讲述这三点。

.. note:: 除了数据库类不能被扩展或被你的类替换外，其他的类都可以。

存储位置
=========

你的类库文件应该放置在 *application/libraries* 目录下，当你初始化类时，CodeIgniter 
会在这个目录下寻找这些类。

命名约定
==================

-  文件名首字母必须大写，例如：Myclass.php
-  类名定义首字母必须大写，例如：class Myclass
-  类名和文件名必须一致

类文件
==============

类应该定义成如下原型::

	<?php
	defined('BASEPATH') OR exit('No direct script access allowed'); 

	class Someclass {

		public function some_method()
		{
		}
	}

.. note:: 这里的 Someclass 名字只是个例子。

使用你的类
================

在你的 :doc:`控制器 <controllers>` 的任何方法中使用如下代码初始化你的类::

	$this->load->library('someclass');

其中，*someclass* 为文件名，不包括 .php 文件扩展名。文件名可以写成首字母大写，
也可以写成全小写，CodeIgniter 都可以识别。

一旦加载，你就可以使用小写字母名称来访问你的类::

	$this->someclass->some_method();  // Object instances will always be lower case

初始化类时传入参数
===============================================

在加载类库的时候，你可以通过第二个参数动态的传递一个数组数据，该数组将被传到
你的类的构造函数中::

	$params = array('type' => 'large', 'color' => 'red');

	$this->load->library('someclass', $params);

如果你使用了该功能，你必须在定义类的构造函数时加上参数::

	<?php defined('BASEPATH') OR exit('No direct script access allowed');

	class Someclass {

		public function __construct($params)
		{
			// Do something with $params
		}
	}

你也可以将参数保存在配置文件中来传递，只需简单的创建一个和类文件同名的配置文件，
并保存到你的 *application/config/* 目录下。要注意的是，如果你使用了上面介绍的方法
动态的传递参数，配置文件将不可用。

在你的类库中使用 CodeIgniter 资源
===================================================

在你的类库中使用 ``get_instance()`` 函数来访问 CodeIgniter 的原生资源，这个函数返回
CodeIgniter 超级对象。

通常情况下，在你的控制器方法中你会使用 ``$this`` 来调用所有可用的 CodeIgniter 方法::

	$this->load->helper('url');
	$this->load->library('session');
	$this->config->item('base_url');
	// etc.

但是 ``$this`` 只能在你的控制器、模型或视图中直接使用，如果你想在你自己的类中使用 
CodeIgniter 类，你可以像下面这样做：

首先，将 CodeIgniter 对象赋值给一个变量::

	$CI =& get_instance();

一旦你把 CodeIgniter 对象赋值给一个变量之后，你就可以使用这个变量来 *代替* ``$this`` ::

	$CI =& get_instance();

	$CI->load->helper('url');
	$CI->load->library('session');
	$CI->config->item('base_url');
	// etc.

.. note:: 你会看到上面的 ``get_instance()`` 函数通过引用来传递::
	
		$CI =& get_instance();

	这是非常重要的，引用赋值允许你使用原始的 CodeIgniter 对象，而不是创建一个副本。

既然类库是一个类，那么我们最好充分的使用 OOP 原则，所以，为了让类中的所有方法都能使用
CodeIgniter 超级对象，建议将其赋值给一个属性::

	class Example_library {

		protected $CI;

		// We'll use a constructor, as you can't directly call a function
		// from a property definition.
		public function __construct()
		{
			// Assign the CodeIgniter super-object
			$this->CI =& get_instance();
		}

		public function foo()
		{
			$this->CI->load->helper('url');
			redirect();
		}

		public function bar()
		{
			echo $this->CI->config->item('base_url');
		}

	}

使用你自己的类库替换原生类库
=============================================

简单的将你的类文件名改为和原生的类库文件一致，CodeIgniter 就会使用它替换掉原生的类库。
要使用该功能，你必须将你的类库文件和类定义改成和原生的类库完全一样，例如，
要替换掉原生的 Email 类的话，你要新建一个 *application/libraries/Email.php* 文件，
然后定义定义你的类::

	class CI_Email {
	
	}

注意大多数原生类都以 CI\_ 开头。

要加载你的类库，和标准的方法一样::

	$this->load->library('email');

.. note:: 注意数据库类不能被你自己的类替换掉。

扩展原生类库
==========================

如果你只是想往现有的类库中添加一些功能，譬如增加一两个方法，
这时替换整个类感觉就有点杀鸡用牛刀了。在这种情况下，最好的方法是
扩展类库。扩展一个类和替换一个类差不多，除了以下几点：

-  类在定义时必须继承自父类。
-  你的新类名和文件名必须以 MY\_ 为前缀（这个可配置，见下文）

例如，要扩展原生的 Email 类你需要新建一个文件命名为 *application/libraries/MY_Email.php* ，
然后定义你的类::

	class MY_Email extends CI_Email {

	}

如果你需要在你的类中使用构造函数，确保你调用了父类的构造函数::

	class MY_Email extends CI_Email {

		public function __construct($config = array())
		{
			parent::__construct($config);
		}

	}

.. note:: 并不是所有的类库构造函数的参数都是一样的，在对类库扩展之前
	先看看它是怎么实现的。

加载你的扩展类
----------------------

要加载你的扩展类，还是使用和通常一样的语法。不用包含前缀。例如，
要加载上例中你扩展的 Email 类，你可以使用::

	$this->load->library('email');

一旦加载，你还是和通常一样使用类变量来访问你扩展的类，以 email 类为例，
访问它的方法如下::

	$this->email->some_method();

设置自定义前缀
-----------------------

要设置你自己的类的前缀，你可以打开 *application/config/config.php* 文件，
找到下面这项::

	$config['subclass_prefix'] = 'MY_';

请注意所有原始的 CodeIgniter 类库都以 **CI\_** 开头，所以请不要使用这个
作为你的自定义前缀。
