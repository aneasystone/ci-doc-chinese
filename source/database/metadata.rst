#################
数据库元数据
#################

**************
表元数据
**************

下面这些方法用于获取表信息：

列出数据库的所有表
================================

**$this->db->list_tables();**

该方法返回一个包含你当前连接的数据库的所有表名称的数组。例如::

	$tables = $this->db->list_tables();
	
	foreach ($tables as $table)
	{
		echo $table;
	}


检测表是否存在
===========================

**$this->db->table_exists();**

有时候，在对某个表执行操作之前先判断该表是否存在将是很有用的。
该函数返回一个布尔值：TRUE / FALSE。使用示例::

	if ($this->db->table_exists('table_name'))
	{
		// some code...
	}

.. note:: 使用你要查找的表名替换掉 *table_name*


**************
字段元数据
**************

列出表的所有列
==========================

**$this->db->list_fields()**

该方法返回一个包含字段名称的数组。有两种不同的调用方式：

1. 将表名陈作为参数传入 $this->db->list_fields()::

	$fields = $this->db->list_fields('table_name');
	
	foreach ($fields as $field)
	{
		echo $field;
	}

2. 你可以从任何查询结果对象上调用该方法，获取查询返回的所有字段::

	$query = $this->db->query('SELECT * FROM some_table');
	
	foreach ($query->list_fields() as $field)
	{
		echo $field;
	}


检测表中是否存在某字段
==========================================

**$this->db->field_exists()**

有时候，在执行一个操作之前先确定某个字段是否存在将会有很用。
该方法返回一个布尔值：TRUE / FALSE。使用示例::

	if ($this->db->field_exists('field_name', 'table_name'))
	{
		// some code...
	}

.. note:: 使用你要查找的字段名替换掉 *field_name* ，然后使用
	你要查找的表名替换掉 *table_name* 。


获取字段的元数据
=======================

**$this->db->field_data()**

该方法返回一个包含了字段信息的对象数组。

获取字段名称或相关的元数据，如数据类型，最大长度等等，
在有些时候也是非常有用的。

.. note:: 并不是所有的数据库都支持元数据。

使用示例::

	$fields = $this->db->field_data('table_name');
	
	foreach ($fields as $field)
	{
		echo $field->name;
		echo $field->type;
		echo $field->max_length;
		echo $field->primary_key;
	}

如果你已经执行了一个查询，你也可以在查询结果对象上调用该方法获取
返回结果中的所有字段的元数据::

	$query = $this->db->query("YOUR QUERY");
	$fields = $query->field_data();

如果你的数据库支持，该函数获取的字段信息将包括下面这些：

-  name - 列名称
-  max_length - 列的最大长度
-  primary_key - 等于1的话表示此列是主键
-  type - 列的数据类型
