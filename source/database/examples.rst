##################################
数据库快速入门: 示例代码
##################################

这个页面包含的示例代码将简单介绍如何使用数据库类。更详细的信息请参考每个函数单独的介绍页面。

初始化数据库类
===============================

下面的代码将根据你的 :doc:`数据库配置 <configuration>` 加载并初始化数据库类::

	$this->load->database();

数据库类一旦载入，你就可以像下面介绍的那样使用它。

注意：如果你所有的页面都需要连接数据库，你可以让其自动加载。参见 :doc:`数据库连接 <connecting>`。

多结果标准查询（对象形式）
=====================================================

::

	$query = $this->db->query('SELECT name, title, email FROM my_table');
	
	foreach ($query->result() as $row)
	{
		echo $row->title;
		echo $row->name;
		echo $row->email;
	}
	
	echo 'Total Results: ' . $query->num_rows();

上面的 ``result()`` 函数返回一个**对象数组**。譬如：``$row->title``

多结果标准查询（数组形式）
====================================================

::

	$query = $this->db->query('SELECT name, title, email FROM my_table');
	
	foreach ($query->result_array() as $row)
	{
		echo $row['title'];
		echo $row['name'];
		echo $row['email'];
	}

上面的 ``result_array()`` 函数返回一个**数组的数组**。譬如：``$row['title']``

测试查询结果
===================

如果你的查询不返回结果，建议使用函数 ``num_rows()`` 来测试::

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

单结果标准查询（对象形式）
=================================

::

	$query = $this->db->query('SELECT name FROM my_table LIMIT 1'); 
	$row = $query->row();
	echo $row->name;

上面的 ``row()`` 函数返回一个**对象**。譬如：``$row->name``

单结果标准查询（数组形式）
=================================================

::

	$query = $this->db->query('SELECT name FROM my_table LIMIT 1');
	$row = $query->row_array();
	echo $row['name'];

上面的 ``row_array()`` 函数返回一个**数组**。譬如：``$row['name']``

标准插入
===============

::

	$sql = "INSERT INTO mytable (title, name) VALUES (".$this->db->escape($title).", ".$this->db->escape($name).")";
	$this->db->query($sql);
	echo $this->db->affected_rows();

使用查询构造器查询数据
===========================

:doc:`查询构造器模式 <query_builder>` 提供给我们一种简单的查询数据的途径::

	$query = $this->db->get('table_name');
	
	foreach ($query->result() as $row)
	{
		echo $row->title;
	}

上面的 ``get()`` 函数从给定的表中查询出所有的结果。:doc:`查询构造器 <query_builder>` 提供了所有数据库操作的快捷函数。

使用查询构造器插入数据
===========================

::

	$data = array(
		'title' => $title,
		'name' => $name,
		'date' => $date
	);
	
	//
	// 生成这样的SQL代码: 
	//   INSERT INTO mytable (title, name, date) VALUES ('{$title}', '{$name}', '{$date}')
	//
	$this->db->insert('mytable', $data);

