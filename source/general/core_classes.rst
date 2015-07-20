############################
创建核心系统类
############################

每次 CodeIgniter 运行时，都有一些基础类伴随着核心框架自动的被初始化。
但你也可以使用你自己类来替代这些核心类或者扩展这些核心类。

***大多数用户一般不会有这种需求，但对于那些想较大幅度的改变
CodeIgniter 的人来说，我们依然提供了替换和扩展核心类的选择。*

.. note:: 改变系统核心类会产生很大影响，所以在你做之前必须清楚地
	知道自己正在做什么。

系统类清单
=================

以下是系统核心文件的清单，它们在每次 CodeIgniter 启动时被调用：

-  Benchmark
-  Config
-  Controller
-  Exceptions
-  Hooks
-  Input
-  Language
-  Loader
-  Log
-  Output
-  Router
-  Security
-  URI
-  Utf8

替换核心类
======================

要使用你自己的系统类替换默认的系统类只需简单的将你自己的文件
放入目录 *application/core* 下::

	application/core/some_class.php

如果这个目录不存在，你可以创建一个。

任何一个和上面清单中同名的文件将被替换成核心类。

要注意的是，你的类名必须以 CI 开头，譬如，你的文件是 Input.php ，
那么类应该命名为::

	class CI_Input {

	}

扩展核心类
====================

如果你只是想往现有类中添加一些功能，譬如增加一两个方法，这时替换
整个类感觉就有点杀鸡用牛刀了。在这种情况下，最好是使用扩展类的方法。
扩展一个类和替换一个类的做法几乎是一样的，除了要注意以下几点：

-  你定义的类必须继承自父类。
-  你的类名和文件名必须以 MY\_ 开头。（这是可配置的，见下文）

举个例子，要扩展原始的 Input 类，你需要新建一个文件
application/core/MY_Input.php，然后像下面这样定义你的类::

	class MY_Input extends CI_Input {

	}

.. note:: 如果在你的类中需要使用构造函数，记得要调用父类的构造函数::

		class MY_Input extends CI_Input {

			public function __construct()
			{
				parent::__construct();
			}
		}

**提示：** 任何和父类同名的方法将会取代父类中的方法（这又被称作 “方法覆盖”），
这让你可以充分的利用并修改 CodeIgniter 的核心。

如果你扩展了 控制器 核心类，那么记得在你的应用程序控制器里继承你
扩展的新类。

::

	class Welcome extends MY_Controller {

		public function __construct()
		{
			parent::__construct();
		}

		public function index()
		{
			$this->load->view('welcome_message');
		}
	}

自定义前缀
-----------------------

要想自定义你自己的类的前缀，打开文件 *application/config/config.php*
然后找到这项::

	$config['subclass_prefix'] = 'MY_';

请注意所有原始的 CodeIgniter 类库都以 CI\_ 开头，所以请不要使用这个
作为你的自定义前缀。