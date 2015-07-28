################
HTML 表格类
################

表格类提供了一些方法用于根据数组或数据库结果集自动生成 HTML 的表格。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

*********************
使用表格类
*********************

初始化类
======================

跟 CodeIgniter 中的其他类一样，可以在你的控制器中使用 ``$this->load->library()`` 
方法加载表格类::

	$this->load->library('table');

一旦加载，表格类就可以像下面这样使用::

	$this->table

例子
========

下面这个例子向你演示了如何通过多维数组来创建一个表格。注意数组的第一行将会变成
表格的表头（或者你也可以通过下面介绍的 ``set_heading()`` 方法来设置你自己的表头）。

::

	$this->load->library('table');

	$data = array(
		array('Name', 'Color', 'Size'),
		array('Fred', 'Blue', 'Small'),
		array('Mary', 'Red', 'Large'),
		array('John', 'Green', 'Medium')	
	);

	echo $this->table->generate($data);

下面这个例子是通过数据库查询结果来创建一个表格。表格类将使用查询结果的列名自动生成表头
（或者你也可以通过下面介绍的 ``set_heading()`` 方法来设置你自己的表头）。

::

	$this->load->library('table');

	$query = $this->db->query('SELECT * FROM my_table');

	echo $this->table->generate($query);

下面这个例子演示了如何使用分开的参数来生成表格::

	$this->load->library('table');

	$this->table->set_heading('Name', 'Color', 'Size');

	$this->table->add_row('Fred', 'Blue', 'Small');
	$this->table->add_row('Mary', 'Red', 'Large');
	$this->table->add_row('John', 'Green', 'Medium');

	echo $this->table->generate();

下面这个例子和上面的一样，但是它不是使用分开的参数，而是使用了数组::

	$this->load->library('table');

	$this->table->set_heading(array('Name', 'Color', 'Size'));

	$this->table->add_row(array('Fred', 'Blue', 'Small'));
	$this->table->add_row(array('Mary', 'Red', 'Large'));
	$this->table->add_row(array('John', 'Green', 'Medium'));

	echo $this->table->generate();

修改表格样式
===============================

表格类可以允许你设置一个表格的模板，你可以通过它设计表格的样式，下面是模板的原型::

	$template = array(
		'table_open'		=> '<table border="0" cellpadding="4" cellspacing="0">',

		'thead_open'		=> '<thead>',
		'thead_close'		=> '</thead>',

		'heading_row_start'	=> '<tr>',
		'heading_row_end'	=> '</tr>',
		'heading_cell_start'	=> '<th>',
		'heading_cell_end'	=> '</th>',

		'tbody_open'		=> '<tbody>',
		'tbody_close'		=> '</tbody>',

		'row_start'		=> '<tr>',
		'row_end'		=> '</tr>',
		'cell_start'		=> '<td>',
		'cell_end'		=> '</td>',

		'row_alt_start'		=> '<tr>',
		'row_alt_end'		=> '</tr>',
		'cell_alt_start'	=> '<td>',
		'cell_alt_end'		=> '</td>',

		'table_close'		=> '</table>'
	);

	$this->table->set_template($template);

.. note:: 你会发现模板中有两个 "row" 代码块，它可以让你的表格每行使用交替的颜色，
	或者其他的这种隔行的设计元素。

你不用设置整个模板，只需要设置你想修改的部分即可。在下面这个例子中，只有 table 的起始标签需要修改::

	$template = array(
		'table_open' => '<table border="1" cellpadding="2" cellspacing="1" class="mytable">'
	);

	$this->table->set_template($template);
	
你也可以在配置文件中设置默认的模板。

***************
类参考
***************

.. php:class:: CI_Table

	.. attribute:: $function = NULL

		允许你指定一个原生的 PHP 函数或一个有效的函数数组对象，该函数会作用于所有的单元格数据。
		::

			$this->load->library('table');

			$this->table->set_heading('Name', 'Color', 'Size');
			$this->table->add_row('Fred', '<strong>Blue</strong>', 'Small');

			$this->table->function = 'htmlspecialchars';
			echo $this->table->generate();

		上例中，所有的单元格数据都会先通过 PHP 的 :php:func:`htmlspecialchars()` 函数，结果如下::

			<td>Fred</td><td>&lt;strong&gt;Blue&lt;/strong&gt;</td><td>Small</td>

	.. php:method:: generate([$table_data = NULL])

		:param	mixed	$table_data: Data to populate the table rows with
		:returns:	HTML table
		:rtype:	string

		返回生成的表格的字符串。 接受一个可选的参数，该参数可以是一个数组或是从数据库获取的结果对象。

	.. php:method:: set_caption($caption)

		:param	string	$caption: Table caption
		:returns:	CI_Table instance (method chaining)
		:rtype:	CI_Table

		允许你给表格添加一个标题。
		::

			$this->table->set_caption('Colors');

	.. php:method:: set_heading([$args = array()[, ...]])

		:param	mixed	$args: An array or multiple strings containing the table column titles
		:returns:	CI_Table instance (method chaining)
		:rtype:	CI_Table

		允许你设置表格的表头。你可以提交一个数组或分开的参数：

			$this->table->set_heading('Name', 'Color', 'Size');

			$this->table->set_heading(array('Name', 'Color', 'Size'));

	.. php:method:: add_row([$args = array()[, ...]])

		:param	mixed	$args: An array or multiple strings containing the row values
		:returns:	CI_Table instance (method chaining)
		:rtype:	CI_Table

		允许你在你的表格中添加一行。你可以提交一个数组或分开的参数：

			$this->table->add_row('Blue', 'Red', 'Green');

			$this->table->add_row(array('Blue', 'Red', 'Green'));

		如果你想要单独设置一个单元格的属性，你可以使用一个关联数组。关联数组的键名 **data** 定义了这个单元格的数据。
		其它的键值对 key => val 将会以 key='val' 的形式被添加为该单元格的属性里：

			$cell = array('data' => 'Blue', 'class' => 'highlight', 'colspan' => 2);
			$this->table->add_row($cell, 'Red', 'Green');

			// generates
			// <td class='highlight' colspan='2'>Blue</td><td>Red</td><td>Green</td>

	.. php:method:: make_columns([$array = array()[, $col_limit = 0]])

		:param	array	$array: An array containing multiple rows' data
		:param	int	$col_limit: Count of columns in the table
		:returns:	An array of HTML table columns
		:rtype:	array

		这个函数以一个一维数组为输入，创建一个多维数组，它的深度（译注：不是行数，而是每一行的元素个数）和列数一样。
		这个函数可以把一个含有多个元素的数组按指定列在表格中显示出来。参考下面的例子:

			$list = array('one', 'two', 'three', 'four', 'five', 'six', 'seven', 'eight', 'nine', 'ten', 'eleven', 'twelve');

			$new_list = $this->table->make_columns($list, 3);

			$this->table->generate($new_list);

			// Generates a table with this prototype

			<table border="0" cellpadding="4" cellspacing="0">
			<tr>
			<td>one</td><td>two</td><td>three</td>
			</tr><tr>
			<td>four</td><td>five</td><td>six</td>
			</tr><tr>
			<td>seven</td><td>eight</td><td>nine</td>
			</tr><tr>
			<td>ten</td><td>eleven</td><td>twelve</td></tr>
			</table>


	.. php:method:: set_template($template)

		:param	array	$template: An associative array containing template values
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		允许你设置你的模板。你可以提交整个模板或部分模板。
		::

			$template = array(
				'table_open'  => '<table border="1" cellpadding="2" cellspacing="1" class="mytable">'
			);
		
			$this->table->set_template($template);

	.. php:method:: set_empty($value)

		:param	mixed	$value: Value to put in empty cells
		:returns:	CI_Table instance (method chaining)
		:rtype:	CI_Table

		用于设置当表格中的单元格为空时要显示的默认值。例如，设置一个不换行空格（NBSP，non-breaking space）::

			$this->table->set_empty("&nbsp;");

	.. php:method:: clear()

		:returns:	CI_Table instance (method chaining)
		:rtype:	CI_Table

		使你能清除表格的表头和行中的数据。如果你需要显示多个有不同数据的表格，
		那么你需要在每个表格生成之后调用这个函数来清除之前表格的信息。例如：

			$this->load->library('table');

			$this->table->set_heading('Name', 'Color', 'Size');
			$this->table->add_row('Fred', 'Blue', 'Small');
			$this->table->add_row('Mary', 'Red', 'Large');
			$this->table->add_row('John', 'Green', 'Medium');

			echo $this->table->generate();

			$this->table->clear();

			$this->table->set_heading('Name', 'Day', 'Delivery');
			$this->table->add_row('Fred', 'Wednesday', 'Express');
			$this->table->add_row('Mary', 'Monday', 'Air');
			$this->table->add_row('John', 'Saturday', 'Overnight');

			echo $this->table->generate();
