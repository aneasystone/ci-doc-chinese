########################
生成查询结果
########################

有几种不同方法可以生成查询结果：

.. contents::
    :local:
    :depth: 2

*************
结果数组
*************

**result()** 方法

该方法以**对象数组**形式返回查询结果，如果查询失败返回**空数组**。
一般情况下，你会像下面这样在一个 foreach 循环中使用它::

	$query = $this->db->query("YOUR QUERY");
	
	foreach ($query->result() as $row)
	{
		echo $row->title;
		echo $row->name;
		echo $row->body;
	}

该方法是 ``result_object()`` 方法的别名。

如果你的查询可能会没有结果，推荐在处理结果之前，先使用方法
``num_rows()`` 检验一下::

	$query = $this->db->query("YOUR QUERY");
	
	if ($query->num_rows() > 0)
	{
		foreach ($query->result() as $row)
		{
			echo $row->title;
			echo $row->name;
			echo $row->body;
		}
	}

你还可以传一个字符串参数给 ``result()`` 方法，这个字符串参数代表
你想要把每个结果转换成某个类的类名（这个类必须已经加载）

::

	$query = $this->db->query("SELECT * FROM users;");

	foreach ($query->result('User') as $user)
	{
		echo $user->name; // access attributes
		echo $user->reverse_name(); // or methods defined on the 'User' class
	}

**result_array()** 方法

这个方法以**一个纯粹的数组**形式返回查询结果，如果无结果，则返回一个空数组。
一般情况下，你会像下面这样在一个 foreach 循环中使用它::

	$query = $this->db->query("YOUR QUERY");
	
	foreach ($query->result_array() as $row)
	{
		echo $row['title'];
		echo $row['name'];
		echo $row['body'];
	}

***********
结果行
***********

**row()** 方法

这个方法返回单独一行结果。如果你的查询不止一行结果，它只返回第一行。
返回的结果是**对象**形式，这里是用例::

	$query = $this->db->query("YOUR QUERY");
	
	if ($query->num_rows() > 0)
	{
		$row = $query->row();
		
		echo $row->title;
		echo $row->name;
		echo $row->body;
	}

如果你要返回特定行的数据，你可以将行号作为第一个参数传给这个方法::

	$row = $query->row(5);

你还可以加上第二个参数，该参数为字符串类型，代表你想要把结果转换成某个类的类名::

	$query = $this->db->query("SELECT * FROM users LIMIT 1;");
	$row = $query->row(0, 'User');
	
	echo $row->name; // access attributes
	echo $row->reverse_name(); // or methods defined on the 'User' class

**row_array()** 方法

这个方法除了返回结果是一个数组而不是一个对象之外，其他的和上面的 ``row()`` 方法完全一样。
举例::

	$query = $this->db->query("YOUR QUERY");
	
	if ($query->num_rows() > 0)
	{
		$row = $query->row_array();
		
		echo $row['title'];
		echo $row['name'];
		echo $row['body'];
	}

如果你要返回特定行的数据，你可以将行号作为第一个参数传给这个方法::

	$row = $query->row_array(5);

另外，你可以使用下面这些方法从你的结果集中获取前一个、后一个、
第一个或者最后一个结果：

	| **$row = $query->first_row()**
	| **$row = $query->last_row()**
	| **$row = $query->next_row()**
	| **$row = $query->previous_row()**

这些方法默认返回对象，如果需要返回数组形式，将单词 "array" 作为参数传入方法即可：

	| **$row = $query->first_row('array')**
	| **$row = $query->last_row('array')**
	| **$row = $query->next_row('array')**
	| **$row = $query->previous_row('array')**

.. note:: 上面所有的这些方法都会把所有的结果加载到内存里（预读取），
	当处理大结果集时最好使用 ``unbuffered_row()`` 方法。

**unbuffered_row()** 方法

这个方法和 ``row()`` 方法一样返回单独一行结果，但是它不会预读取所有的结果数据到内存中。
如果你的查询结果不止一行，它将返回当前一行，并通过内部实现的指针来移动到下一行。

::

	$query = $this->db->query("YOUR QUERY");
	
	while ($row = $query->unbuffered_row())
	{	
		echo $row->title;
		echo $row->name;
		echo $row->body;
	}

为了指定返回值的类型，可以传一个字符串参数 'object'（默认值） 或者 'array' 给这个方法::

	$query->unbuffered_row();		// object
	$query->unbuffered_row('object');	// object
	$query->unbuffered_row('array');	// associative array

*********************
结果辅助方法
*********************

**num_rows()** 方法

该方法返回查询结果的行数。注意：在这个例子中，``$query`` 变量为查询结果对象::

	$query = $this->db->query('SELECT * FROM my_table');
	
	echo $query->num_rows();

.. note:: 并不是所有的数据库驱动器都有原生的方法来获取查询结果的总行数。
	当遇到这种情况时，所有的数据会被预读取到内存中，并调用 ``count()`` 函数
	来取得总行数。
	
**num_fields()** 方法

该方法返回查询结果的字段数（列数）。在你的查询结果对象上调用该方法::

	$query = $this->db->query('SELECT * FROM my_table');
	
	echo $query->num_fields();

**free_result()** 方法

该方法释放掉查询结果所占的内存，并删除结果的资源标识。通常来说，
PHP 会在脚本执行结束后自动释放内存。但是，如果你在某个脚本中执行大量的查询，
你可能需要在每次查询之后释放掉查询结果，以此来降低内存消耗。

举例::

	$query = $this->db->query('SELECT title FROM my_table');
	
	foreach ($query->result() as $row)
	{
		echo $row->title;
	}

	$query->free_result();  // The $query result object will no longer be available

	$query2 = $this->db->query('SELECT name FROM some_table');

	$row = $query2->row();
	echo $row->name;
	$query2->free_result(); // The $query2 result object will no longer be available

**data_seek()** 方法

这个方法用来设置下一个结果行的内部指针，它只有在和 ``unbuffered_row()`` 方法一起使用才有效果。

它接受一个正整数参数（默认值为0）表示想要读取的下一行，返回值为 TRUE 或 FALSE 表示成功或失败。

::

	$query = $this->db->query('SELECT `field_name` FROM `table_name`');
	$query->data_seek(5); // Skip the first 5 rows
	$row = $query->unbuffered_row();

.. note:: 并不是所有的数据库驱动器都支持这一特性，调用这个方法将会返回 FALSE，
	譬如你无法在 PDO 上使用它。

***************
Class Reference
***************

.. php:class:: CI_DB_result

	.. php:method:: result([$type = 'object'])

		:param	string	$type: Type of requested results - array, object, or class name
		:returns:	Array containing the fetched rows
		:rtype:	array

		A wrapper for the ``result_array()``, ``result_object()``
		and ``custom_result_object()`` methods.

		Usage: see `结果数组`_.

	.. php:method:: result_array()

		:returns:	Array containing the fetched rows
		:rtype:	array

		Returns the query results as an array of rows, where each
		row is itself an associative array.

		Usage: see `结果数组`_.

	.. php:method:: result_object()

		:returns:	Array containing the fetched rows
		:rtype:	array

		Returns the query results as an array of rows, where each
		row is an object of type ``stdClass``.

		Usage: see `结果数组`_.

	.. php:method:: custom_result_object($class_name)

		:param	string	$class_name: Class name for the resulting rows
		:returns:	Array containing the fetched rows
		:rtype:	array

		Returns the query results as an array of rows, where each
		row is an instance of the specified class.

	.. php:method:: row([$n = 0[, $type = 'object']])

		:param	int	$n: Index of the query results row to be returned
		:param	string	$type: Type of the requested result - array, object, or class name
		:returns:	The requested row or NULL if it doesn't exist
		:rtype:	mixed

		A wrapper for the ``row_array()``, ``row_object() and 
		``custom_row_object()`` methods.

		Usage: see `结果行`_.

	.. php:method:: unbuffered_row([$type = 'object'])

		:param	string	$type: Type of the requested result - array, object, or class name
		:returns:	Next row from the result set or NULL if it doesn't exist
		:rtype:	mixed

		Fetches the next result row and returns it in the
		requested form.

		Usage: see `结果行`_.

	.. php:method:: row_array([$n = 0])

		:param	int	$n: Index of the query results row to be returned
		:returns:	The requested row or NULL if it doesn't exist
		:rtype:	array

		Returns the requested result row as an associative array.

		Usage: see `结果行`_.

	.. php:method:: row_object([$n = 0])

		:param	int	$n: Index of the query results row to be returned
                :returns:	The requested row or NULL if it doesn't exist
		:rtype:	stdClass

		Returns the requested result row as an object of type
		``stdClass``.

		Usage: see `结果行`_.

	.. php:method:: custom_row_object($n, $type)

		:param	int	$n: Index of the results row to return
		:param	string	$class_name: Class name for the resulting row
		:returns:	The requested row or NULL if it doesn't exist
		:rtype:	$type

		Returns the requested result row as an instance of the
		requested class.

	.. php:method:: data_seek([$n = 0])

		:param	int	$n: Index of the results row to be returned next
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		Moves the internal results row pointer to the desired offset.

		Usage: see `结果辅助方法`_.

	.. php:method:: set_row($key[, $value = NULL])

		:param	mixed	$key: Column name or array of key/value pairs
		:param	mixed	$value: Value to assign to the column, $key is a single field name
		:rtype:	void

		Assigns a value to a particular column.

	.. php:method:: next_row([$type = 'object'])

		:param	string	$type: Type of the requested result - array, object, or class name
		:returns:	Next row of result set, or NULL if it doesn't exist
		:rtype:	mixed

		Returns the next row from the result set.

	.. php:method:: previous_row([$type = 'object'])

		:param	string	$type: Type of the requested result - array, object, or class name
		:returns:	Previous row of result set, or NULL if it doesn't exist
		:rtype:	mixed

		Returns the previous row from the result set.

	.. php:method:: first_row([$type = 'object'])

		:param	string	$type: Type of the requested result - array, object, or class name
		:returns:	First row of result set, or NULL if it doesn't exist
		:rtype:	mixed

		Returns the first row from the result set.

	.. php:method:: last_row([$type = 'object'])

		:param	string	$type: Type of the requested result - array, object, or class name
		:returns:	Last row of result set, or NULL if it doesn't exist
		:rtype:	mixed

		Returns the last row from the result set.

	.. php:method:: num_rows()

		:returns:	Number of rows in the result set
		:rtype:	int

		Returns the number of rows in the result set.

		Usage: see `结果辅助方法`_.

	.. php:method:: num_fields()

		:returns:	Number of fields in the result set
		:rtype:	int

		Returns the number of fields in the result set.

		Usage: see `结果辅助方法`_.

	.. php:method:: field_data()

		:returns:	Array containing field meta-data
		:rtype:	array

		Generates an array of ``stdClass`` objects containing
		field meta-data.

	.. php:method:: free_result()

		:rtype:	void

		Frees a result set.

		Usage: see `结果辅助方法`_.

	.. php:method:: list_fields()

		:returns:	Array of column names
		:rtype:	array

		Returns an array containing the field names in the
		result set.
