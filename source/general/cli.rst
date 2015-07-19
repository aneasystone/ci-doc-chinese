###################
以 CLI 方式运行
###################

除了从浏览器中通过 URL 来调用程序的 :doc:`控制器 <./controllers>` 之外，
你也可以通过 CLI （命令行界面）的方式来调用。

.. contents:: 页面内容

什么是 CLI ？
================

CLI （命令行界面）是一种基于文本的和计算机交互的方式。 更多信息，
请查看 `维基百科 <http://en.wikipedia.org/wiki/Command-line_interface>`_ 。

为什么使用命令行？
=============================

虽然不是很明显，但是有很多情况下我们需要使用命令行来运行 CodeIgniter。

-  使用 cron 定时运行任务，而不需要使用 *wget* 或 *curl*
-  通过函数 :php:func:`is_cli()` 的返回值来让你的 cron 页面不能通过 URL 访问到
-  制作交互式的任务，譬如：设置权限，清除缓存，备份等等
-  与其他语言进行集成，譬如可以通过 C++ 调用一条指令来运行你模型中的代码。

让我们试一试：Hello World！
=============================

让我们先创建一个简单的控制器，打开你的文本编辑器，新建一个文件并命名为
Tools.php，然后输入如下的代码::

	<?php
	class Tools extends CI_Controller {

		public function message($to = 'World')
		{
			echo "Hello {$to}!".PHP_EOL;
		}
	}

然后将文件保存到 *application/controllers/* 目录下。

现在你可以通过类似下面的 URL 来访问它::

	example.com/index.php/tools/message/to

或者，我们可以通过 CLI 来访问。在 Mac/Linux 下你可以打开一个终端，在 Windows
下你可以打开 “运行”，然后输入 "cmd"，进入 CodeIgniter 项目所在的目录。

.. code-block:: bash

	$ cd /path/to/project;
	$ php index.php tools message

如果你操作正确，你应该会看到 *Hello World!* 。

.. code-block:: bash

	$ php index.php tools message "John Smith"

这里我们传一个参数给它，这和使用 URL 参数是一样的。"John Smith" 
被作为参数传入并显示出::

	Hello John Smith!

就这么简单！
============

简单来说，这就是你需要知道的关于如何在命令行中使用控制器的所有事情了。
记住，这只是一个普通的控制器，所以路由和 _remap 也照样工作。