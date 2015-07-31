####################
数据库工厂类
####################

数据库工厂类提供了一些方法来帮助你管理你的数据库。

.. contents:: Table of Contents
    :depth: 3

****************************
初始化数据库工厂类
****************************

.. important:: 由于数据库工厂类依赖于数据库驱动器，为了初始化该类，你的数据库驱动器必须已经运行。

加载数据库工厂类的代码如下::

	$this->load->dbforge()

如果你想管理的不是你正在使用的数据库，你还可以传另一个数据库对象到数据库工具类的加载方法::

	$this->myforge = $this->load->dbforge($this->other_db, TRUE);

上例中，我们通过第一个参数传递了一个自定义的数据库对象，第二个参数表示方法将返回 dbforge 对象，
而不是直接赋值给 ``$this->dbforge`` 。

.. note:: 两个参数都可以独立使用，如果你只想传第二个参数，可以将第一个参数置空。

一旦初始化结束，你就可以使用 ``$this->dbforge`` 对象来访问它的方法::

	$this->dbforge->some_method();

*******************************
创建和删除数据库
*******************************

**$this->dbforge->create_database('db_name')**

用于创建指定数据库，根据成败返回 TRUE 或 FALSE ::

	if ($this->dbforge->create_database('my_db'))
	{
		echo 'Database created!';
	}

**$this->dbforge->drop_database('db_name')**

用于删除指定数据库，根据成败返回 TRUE 或 FALSE ::

	if ($this->dbforge->drop_database('my_db'))
	{
		echo 'Database deleted!';
	}


****************************
创建和删除数据表
****************************

创建表涉及到这样几件事：添加字段、添加键、修改字段。CodeIgniter 提供了这几个方法。

添加字段
=============

字段通过一个关联数组来创建，数组中必须包含一个 'type' 索引，代表字段的数据类型。
例如，INT、VARCHAR、TEXT 等，有些数据类型（例如 VARCHAR）还需要加一个 'constraint' 索引。

::

	$fields = array(
		'users' => array(
			'type' => 'VARCHAR',
			'constraint' => '100',
		),
	);
	// will translate to "users VARCHAR(100)" when the field is added.


另外，还可以使用下面的键值对：

-  unsigned/true : 在字段定义中生成 "UNSIGNED" 
-  default/value : 在字段定义中生成一个默认值
-  null/true : 在字段定义中生成 "NULL" ，如果没有这个，字段默认为 "NOT NULL"
-  auto_increment/true : 在字段定义中生成自增标识，注意数据类型必须支持这个，譬如整型

::

	$fields = array(
		'blog_id' => array(
			'type' => 'INT',
			'constraint' => 5,
			'unsigned' => TRUE,
			'auto_increment' => TRUE
		),
		'blog_title' => array(
			'type' => 'VARCHAR',
			'constraint' => '100',
		),
		'blog_author' => array(
			'type' =>'VARCHAR',
			'constraint' => '100',
			'default' => 'King of Town',
		),
		'blog_description' => array(
			'type' => 'TEXT',
			'null' => TRUE,
		),
	);


字段定义好了之后，就可以在调用 ``create_table()`` 方法的后面使用
``$this->dbforge->add_field($fields);`` 方法来添加字段了。

**$this->dbforge->add_field()**

添加字段方法的参数就是上面介绍的数组。


使用字符串参数添加字段
---------------------------------

如果你非常清楚的知道你要添加的字段，你可以使用字段的定义字符串来传给 add_field() 方法

::

	$this->dbforge->add_field("label varchar(100) NOT NULL DEFAULT 'default label'");


.. note:: 多次调用 add_field() 将会累积

创建 id 字段
--------------------

创建 id 字段和创建其他字段非常不一样，id 字段将会自动定义成类型为 INT(9)  的自增主键。

::

	$this->dbforge->add_field('id');
	// gives id INT(9) NOT NULL AUTO_INCREMENT


添加键
===========

通常来说，表都会有键。这可以使用 $this->dbforge->add_key('field') 方法来实现。
第二个参数可选，可以将其设置为主键。注意 add_key() 方法必须紧跟在 create_table() 方法的后面。

包含多列的非主键必须使用数组来添加，下面是 MySQL 的例子。

::

	$this->dbforge->add_key('blog_id', TRUE);
	// gives PRIMARY KEY `blog_id` (`blog_id`)
	
	$this->dbforge->add_key('blog_id', TRUE);
	$this->dbforge->add_key('site_id', TRUE);
	// gives PRIMARY KEY `blog_id_site_id` (`blog_id`, `site_id`)
	
	$this->dbforge->add_key('blog_name');
	// gives KEY `blog_name` (`blog_name`)
	
	$this->dbforge->add_key(array('blog_name', 'blog_label'));
	// gives KEY `blog_name_blog_label` (`blog_name`, `blog_label`)


创建表
================

字段和键都定义好了之后，你可以使用下面的方法来创建表::

	$this->dbforge->create_table('table_name');
	// gives CREATE TABLE table_name

第二个参数设置为 TRUE ，可以在定义中添加 "IF NOT EXISTS" 子句。

::

	$this->dbforge->create_table('table_name', TRUE);
	// gives CREATE TABLE IF NOT EXISTS table_name

你还可以指定表的属性，譬如 MySQL 的 ``ENGINE`` ::

	$attributes = array('ENGINE' => 'InnoDB');
	$this->dbforge->create_table('table_name', FALSE, $attributes);
	// produces: CREATE TABLE `table_name` (...) ENGINE = InnoDB DEFAULT CHARACTER SET utf8 COLLATE utf8_general_ci

.. note:: 除非你指定了 ``CHARACTER SET`` 或 ``COLLATE`` 属性，``create_table()`` 方法
	默认会使用配置文件中 *char_set* 和 *dbcollat* 的值（仅针对 MySQL）。

删除表
================

执行一个 DROP TABLE 语句，可以选择添加 IF EXISTS 子句。

::

	// Produces: DROP TABLE table_name
	$this->dbforge->drop_table('table_name');

	// Produces: DROP TABLE IF EXISTS table_name
	$this->dbforge->drop_table('table_name',TRUE);


重命名表
================

执行一个重命名表语句。

::

	$this->dbforge->rename_table('old_table_name', 'new_table_name');
	// gives ALTER TABLE old_table_name RENAME TO new_table_name


****************
修改表
****************

给表添加列
==========================

**$this->dbforge->add_column()**

``add_column()`` 方法用于对现有数据表进行修改，它的参数和上面介绍的
字段数组一样。

::

	$fields = array(
		'preferences' => array('type' => 'TEXT')
	);
	$this->dbforge->add_column('table_name', $fields); 
	// Executes: ALTER TABLE table_name ADD preferences TEXT

如果你使用 MySQL 或 CUBIRD ，你可以使用 AFTER 和 FIRST 语句来为新添加的列指定位置。

例如::

	// Will place the new column after the `another_field` column:
	$fields = array(
		'preferences' => array('type' => 'TEXT', 'after' => 'another_field')
	);

	// Will place the new column at the start of the table definition:
	$fields = array(
		'preferences' => array('type' => 'TEXT', 'first' => TRUE)
	);

从表中删除列
==============================

**$this->dbforge->drop_column()**

用于从表中删除指定列。

::

	$this->dbforge->drop_column('table_name', 'column_to_drop');


修改表中的某个列
=============================

**$this->dbforge->modify_column()**

该方法的用法和 ``add_column()`` 一样，只是它用于对现有的列进行修改，而不是添加新列。
如果要修改列的名称，你可以在列的定义数组中添加一个 "name" 索引。

::

	$fields = array(
		'old_name' => array(
			'name' => 'new_name',
			'type' => 'TEXT',
		),
	);
	$this->dbforge->modify_column('table_name', $fields);
	// gives ALTER TABLE table_name CHANGE old_name new_name TEXT


***************
类参考
***************

.. php:class:: CI_DB_forge

	.. php:method:: add_column($table[, $field = array()[, $_after = NULL]])

		:param	string	$table: Table name to add the column to
		:param	array	$field: Column definition(s)
		:param	string	$_after: Column for AFTER clause (deprecated)
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		给表添加列。用法参见 `给表添加列`_ 。

	.. php:method:: add_field($field)

		:param	array	$field: Field definition to add
		:returns:	CI_DB_forge instance (method chaining)
		:rtype:	CI_DB_forge
                
                	添加字段到集合，用于创建一个表。用法参见 `添加字段`_ 。

	.. php:method:: add_key($key[, $primary = FALSE])

		:param	array	$key: Name of a key field
		:param	bool	$primary: Set to TRUE if it should be a primary key or a regular one
		:returns:	CI_DB_forge instance (method chaining)
		:rtype:	CI_DB_forge

		添加键到集合，用于创建一个表。用法参见：`添加键`_ 。

	.. php:method:: create_database($db_name)

		:param	string	$db_name: Name of the database to create
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		创建数据库。用法参见：`创建和删除数据库`_ 。

	.. php:method:: create_table($table[, $if_not_exists = FALSE[, array $attributes = array()]])

		:param	string	$table: Name of the table to create
		:param	string	$if_not_exists: Set to TRUE to add an 'IF NOT EXISTS' clause
		:param	string	$attributes: An associative array of table attributes
		:returns:  TRUE on success, FALSE on failure
		:rtype:	bool

		创建表。用法参见：`创建表`_ 。

	.. php:method:: drop_column($table, $column_name)

		:param	string	$table: Table name
		:param	array	$column_name: The column name to drop
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		删除某个表的字段。用法参见：`从表中删除列`_ 。

	.. php:method:: drop_database($db_name)

		:param	string	$db_name: Name of the database to drop
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		删除数据库。用法参见：`创建和删除数据库`_ 。

	.. php:method:: drop_table($table_name[, $if_exists = FALSE])

		:param	string	$table: Name of the table to drop
		:param	string	$if_exists: Set to TRUE to add an 'IF EXISTS' clause
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		删除表。用法参见：`删除表`_ 。

	.. php:method:: modify_column($table, $field)

		:param	string	$table: Table name
		:param	array	$field: Column definition(s)
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		修改表的某个列。用法参见：`修改表中的某个列`_ 。

	.. php:method:: rename_table($table_name, $new_table_name)

		:param	string	$table: Current of the table
		:param	string	$new_table_name: New name of the table
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		重命名表。用法参见：`重命名表`_ 。
