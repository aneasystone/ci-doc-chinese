################
辅助函数
################

辅助函数，顾名思义，是帮助我们完成特定任务的函数。每个辅助函数文件都是某一类
函数的集合。例如， **URL 辅助函数** 帮助我们创建链接，**表单辅助函数**帮助
我们创建表单元素，**本文辅助函数** 帮助我们处理文本的格式化，**Cookie 辅助函数**
帮助我们读取或设置 Cookie ，**文件辅助库** 帮助我们处理文件，等等等等。

不同于 CodeIgniter 中的大多数系统，辅助函数没有使用面向对象的方式来实现的。
它们是简单的过程式函数，每个函数处理一个特定的任务，不依赖于其他的函数。

CodeIgniter 默认不会自己加载辅助库，所以使用辅助函数的第一步就是加载它。
一旦加载了，它就可以在你的 :doc:`控制器 <../general/controllers>` 和
:doc:`视图 <../general/views>` 中全局访问了。

一般情况下，辅助函数位于 **system/helpers** 或者 **application/helpers** 目录
目录下。CodeIgniter 首先会查找 **application/helpers** 目录，如果该目录不存在，
或者你加载的辅助库没有在该目录下找到，CodeIgniter 就会去 *system/helpers/* 目录查找。

加载辅助库
================

可以使用下面的方法简单的加载辅助库::

	$this->load->helper('name');

**name** 参数为辅助库的文件名，去掉 .php 文件后缀以及 _helper 部分。

例如，要加载 **URL 辅助库** ，它的文件名为 **url_helper.php** ，你可以这样加载它::

	$this->load->helper('url');

辅助库可以在你的控制器方法的任何地方加载（甚至可以在你的视图文件中加载，尽管这不是
个好的实践），只要确保在使用之前加载它就可以了。你可以在你的控制器的构造函数中加载它，
这样就可以在该控制器的任何方法中使用它，你也可以在某个需要它的函数中单独加载它。

.. note:: 上面的加载辅助库的方法没有返回值，所以不要将它赋值给变量，直接调用就好了。

加载多个辅助库
========================

如果你需要加载多个辅助库，你可以使用一个数组，像下面这样::

	$this->load->helper(
		array('helper1', 'helper2', 'helper3')
	);

自动加载辅助库
====================

如果你需要在你的整个应用程序中使用某个辅助库，你可以将其设置为在 CodeIgniter 初始化时
自动加载它。打开 **application/config/autoload.php** 文件然后将你想加载的辅助库添加到
autoload 数组中。

使用辅助库
==============

一旦你想要使用的辅助库被加载，你就可以像使用标准的 PHP 函数一样使用它们。

譬如，要在你的视图文件中使用 ``anchor()`` 函数创建一个链接，你可以这样做::

	<?php echo anchor('blog/comments', 'Click Here');?>

其中，"Click Here" 是链接的名称，"blog/comments" 是你希望链接到 
controller/method 的 URI 。

扩展辅助库
===================

为了扩展辅助库，你需要在 **application/helpers/** 目录下新建一个文件，
文件名和已存在的辅助库文件名一样，但是要加上 **MY\_** 前缀（这个可以配置，
见下文）。

如果你只是想往现有类中添加一些功能，譬如增加一两个方法，或者修改辅助库中的
某个函数，这时替换整个类感觉就有点杀鸡用牛刀了。在这种情况下，最好的方法是
扩展类。

.. note:: “扩展”一词在这里可能不是很恰当，因为辅助库函数都是过程式的独立函数，
	在传统编程中并不能被扩展。不过在 CodeIgniter 中，你可以向辅助库中添加函数，
	或者使用你自己的函数替代辅助库中的函数。

譬如，要扩展原始的 **数组辅助库** ，首先你要创建一个文件 **application/helpers/MY_array_helper.php** ，
然后像下面这样添加或重写函数::

	// any_in_array() is not in the Array Helper, so it defines a new function
	function any_in_array($needle, $haystack)
	{
		$needle = is_array($needle) ? $needle : array($needle);

		foreach ($needle as $item)
		{
			if (in_array($item, $haystack))
			{
				return TRUE;
			}
	        }

		return FALSE;
	}

	// random_element() is included in Array Helper, so it overrides the native function
	function random_element($array)
	{
		shuffle($array);
		return array_pop($array);
	}

设置自定义前缀
-----------------------

用于扩展辅助库的文件名前缀和扩展类库和核心类是一样的。要自定义这个前缀，你可以打开
**application/config/config.php** 文件然后找到这项::

	$config['subclass_prefix'] = 'MY_';

请注意所有原始的 CodeIgniter 类库都以 **CI\_** 开头，所以请不要使用这个
作为你的自定义前缀。

然后？
=========

在目录里你可以找到所有的辅助库清单，你可以浏览下它们看看它们都是做什么的。