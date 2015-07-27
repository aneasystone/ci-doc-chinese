##################
单元测试类
##################

单元测试是一种为你的应用程序中的每个函数编写测试的软件开发方法。如果你还不熟悉这个概念，
你应该先去 Google 一下。

CodeIgniter 的单元测试类非常简单，由一个测试方法和两个显示结果的方法组成。
它没打算成为一个完整的测试套件，只是提供一个简单的机制来测试你的代码是否
生成了正确的数据类型和结果。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

******************************
使用单元测试类库
******************************

初始化类
======================

正如 CodeIgniter 中的其他类一样，在你的控制器中使用 ``$this->load->library()``
方法来初始化单元测试类::

	$this->load->library('unit_test');

初始化之后，单元测试类的对象就可以这样访问::

	$this->unit

运行测试
=============

要运行一个测试用例，需要提供一个测试和一个期望结果，像下面这样::

	$this->unit->run('test', 'expected result', 'test name', 'notes');

其中，test 是你希望测试的代码的结果，expected result 是期望返回的结果，test name 是可选的，
你可以为你的测试取一个名字，notes 是可选的，可以填些备注信息。例如::

	$test = 1 + 1;

	$expected_result = 2;

	$test_name = 'Adds one plus one';

	$this->unit->run($test, $expected_result, $test_name);

期望的结果可以是字面量匹配（a literal match），也可以是数据类型匹配（a data type match）。
下面是字面量匹配的例子::

	$this->unit->run('Foo', 'Foo');

下面是数据类型匹配的例子::

	$this->unit->run('Foo', 'is_string');

注意第二个参数 "is_string" ，这让方法测试返回的结果是否是字符串类型。以下是可用的数据类型的列表：

-  is_object
-  is_string
-  is_bool
-  is_true
-  is_false
-  is_int
-  is_numeric
-  is_float
-  is_double
-  is_array
-  is_null
-  is_resource

生成报告
==================

你可以在每个测试之后显示出测试的结果，也可以先运行几个测试，然后在最后生成一份测试结果的报告。
要简单的显示出测试结果，可以直接在 run 方法的前面使用 echo::

	echo $this->unit->run($test, $expected_result);

要显示一份所有测试的完整报告，使用如下代码::

	echo $this->unit->report();

这份报告会以 HTML 的表格形式显示出来，如果你喜欢获取原始的数据，可以通过下面的代码得到一个数组::

	echo $this->unit->result();

严格模式
===========

默认情况下，单元测试类在字面量匹配时是松散的类型匹配。看下面这个例子::

	$this->unit->run(1, TRUE);

正在测试的结果是一个数字，期望的结果是一个布尔型。但是，由于 PHP 的松散数据类型，
如果使用常规的比较操作符的话，上面的测试结果将会是 TRUE 。

	if (1 == TRUE) echo 'This evaluates as true';

如果愿意的话，你可以将单元测试设置为严格模式，它不仅会比较两个数据的值，
而且还会比较两个数据的数据类型::

	if (1 === TRUE) echo 'This evaluates as FALSE';

使用如下代码启用严格模式::

	$this->unit->use_strict(TRUE);

启用/禁用单元测试
===============================

如果你希望在你的代码中保留一些测试，只在需要的时候才被执行，可以使用下面的代码禁用单元测试::

	$this->unit->active(FALSE);

单元测试结果显示
=================

单元测试的结果默认显示如下几项：

-  Test Name (test_name)
-  Test Datatype (test_datatype)
-  Expected Datatype (res_datatype)
-  Result (result)
-  File Name (file)
-  Line Number (line)
-  Any notes you entered for the test (notes)

你可以使用 $this->unit->set_test_items() 方法自定义要显示哪些结果，例如，
你只想显示出测试名和测试的结果：

自定义显示测试结果
---------------------------

::

	$this->unit->set_test_items(array('test_name', 'result'));

创建模板
-------------------

如果你想让你的测试结果以不同于默认的格式显示出来，你可以设置你自己的模板，
这里是一个简单的模板例子，注意那些必须的伪变量::

	$str = '
	<table border="0" cellpadding="4" cellspacing="1">
	{rows}
		<tr>
			<td>{item}</td>
			<td>{result}</td>
		</tr>
	{/rows}
	</table>';

	$this->unit->set_template($str);

.. note:: 你的模板必须在运行测试 **之前** 被定义。

***************
类参考
***************

.. php:class:: CI_Unit_test

	.. php:method:: set_test_items($items)

		:param array $items: List of visible test items
		:returns: void

		设置要在测试的结果中显示哪些项，有效的选项有：

		  - test_name
		  - test_datatype
		  - res_datatype
		  - result
		  - file
		  - line
		  - notes

	.. php:method:: run($test[, $expected = TRUE[, $test_name = 'undefined'[, $notes = '']]])

		:param	mixed	$test: Test data
		:param	mixed	$expected: Expected result
		:param	string	$test_name: Test name
		:param	string	$notes: Any notes to be attached to the test
		:returns:	Test report
		:rtype:	string

		运行单元测试。

	.. php:method:: report([$result = array()])

		:param	array	$result: Array containing tests results
		:returns:	Test report
		:rtype:	string

		根据已运行的测试生成一份测试结果的报告。

	.. php:method:: use_strict([$state = TRUE])

		:param	bool	$state: Strict state flag
		:rtype:	void

		在测试中启用或禁用严格比较模式。

	.. php:method:: active([$state = TRUE])

		:param	bool	$state: Whether to enable testing
		:rtype:	void

		启用或禁用单元测试。

	.. php:method:: result([$results = array()])

		:param	array	$results: Tests results list
		:returns:	Array of raw result data
		:rtype:	array

		返回原始的测试结果数据。

	.. php:method:: set_template($template)

		:param	string	$template: Test result template
		:rtype:	void

		设置显示测试结果数据的模板。
