##################
基准测试类
##################

CodeIgniter 有一个一直都是启用状态的基准测试类，用于计算两个标记点之间的时间差。

.. note:: 该类是由系统自动加载，无需手动加载。

另外，基准测试总是在框架被调用的那一刻开始，在输出类向浏览器发送最终的视图之前结束。
这样可以显示出整个系统执行的精确时间。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

*************************
使用基准测试类
*************************

基准测试类可以在你的 :doc:`控制器 </general/controllers>`、:doc:`视图 </general/views>`
以及 :doc:`模型 </general/models>` 中使用。

使用流程如下：

#. 标记一个起始点
#. 标记一个结束点
#. 使用 elapsed_time 函数计算时间差。

这里是个真实的代码示例::

	$this->benchmark->mark('code_start');

	// Some code happens here

	$this->benchmark->mark('code_end');

	echo $this->benchmark->elapsed_time('code_start', 'code_end');

.. note:: "code_start" 和 "code_end" 这两个单词是随意的，它们只是两个用于标记
	的单词而已，你可以任意使用其他你想使用的单词，另外，你也可以设置多个标记点。
	看如下示例::

		$this->benchmark->mark('dog');

		// Some code happens here

		$this->benchmark->mark('cat');

		// More code happens here

		$this->benchmark->mark('bird');

		echo $this->benchmark->elapsed_time('dog', 'cat');
		echo $this->benchmark->elapsed_time('cat', 'bird');
		echo $this->benchmark->elapsed_time('dog', 'bird');


在 性能分析器 中使用基准测试点
====================================

如果你希望你的基准测试数据显示在 :doc:`性能分析器 </general/profiling>` 中，
那么你的标记点就需要成对出现，而且标记点名称需要以 _start 和 _end 结束，
每一对的标记点名称应该一致。例如::

	$this->benchmark->mark('my_mark_start');

	// Some code happens here...

	$this->benchmark->mark('my_mark_end');

	$this->benchmark->mark('another_mark_start');

	// Some more code happens here...

	$this->benchmark->mark('another_mark_end');

阅读 :doc:`性能分析器 </general/profiling>` 页面了解更多信息。

显示总执行时间
===============================

如果你想显示从 CodeIgniter 运行开始到最终结果输出到浏览器之间花费的总时间，
只需简单的将下面这行代码放入你的视图文件中::

	<?php echo $this->benchmark->elapsed_time();?>

你大概也注意到了，这个方法和上面例子中的介绍的那个计算两个标记点之间时间差的方法是一样的，
只是不带任何参数。当不设参数时，CodeIgniter 在向浏览器输出最终结果之前不会停止计时，所以
无论你在哪里使用该方法，输出的计时结果都是总执行时间。

如果你不喜欢纯 PHP 语法的话，也可以在你的视图中使用另一种伪变量的方式来显示总执行时间::

	{elapsed_time}

.. note:: 如果你想在你的控制器方法中进行基准测试，你需要设置你自己的标记起始点和结束点。

显示内存占用
=============================

如果你的 PHP 在安装时使用了 --enable-memory-limit 参数进行编译，你就可以在你的视图文件中
使用下面这行代码来显示整个系统所占用的内存大小::

	<?php echo $this->benchmark->memory_usage();?>

.. note:: 这个方法只能在视图文件中使用，显示的结果代表整个应用所占用的内存大小。

如果你不喜欢纯 PHP 语法的话，也可以在你的视图中使用另一种伪变量的方式来显示占用的内存大小::

	{memory_usage}


***************
类参考
***************

.. php:class:: CI_Benchmark

	.. php:method:: mark($name)

		:param	string	$name: the name you wish to assign to your marker
		:rtype:	void

		设置一个基准测试的标记点。

	.. php:method:: elapsed_time([$point1 = ''[, $point2 = ''[, $decimals = 4]]])

		:param	string	$point1: a particular marked point
		:param	string	$point2: a particular marked point
		:param	int	$decimals: number of decimal places for precision
		:returns:	Elapsed time
		:rtype:	string

		计算并返回两个标记点之间的时间差。

		如果第一个参数为空，方法将返回 ``{elapsed_time}`` 伪变量。这用于在视图中
		显示整个系统的执行时间，输出类将在最终输出时使用真实的总执行时间替换掉这个伪变量。


	.. php:method:: memory_usage()

		:returns:	Memory usage info
		:rtype:	string

		只是简单的返回 ``{memory_usage}`` 伪变量。

		该方法可以在视图的任意位置使用，直到最终输出页面时 :doc:`输出类 <output>`
		才会将真实的值替换掉这个伪变量。