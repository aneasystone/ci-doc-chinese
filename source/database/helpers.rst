####################
查询辅助函数
####################

关于执行查询的信息
==================================

**$this->db->insert_id()**

当执行 INSERT 语句时，这个方法返回新插入行的ID。

.. note:: 如果你使用的是 PostgreSQL 的 PDO 驱动器, 或者 Interbase 驱动器，
	这个方法需要一个 ``$name`` 参数来指定合适的顺序。（什么意思？）

**$this->db->affected_rows()**

当执行 INSERT、UPDATE 等写类型的语句时，这个方法返回受影响的行数。

.. note:: 在 MySQL 中执行 "DELETE FROM TABLE" 语句返回受影响的行数为 0 。
	为了让这个方法返回正确的受影响行数，数据库类对此做了一点小 hack。
	默认情况下，这个 hack 是启用的，你可以在数据库驱动文件中关闭它。

**$this->db->last_query()**

该方法返回上一次执行的查询语句（是查询语句，不是结果）。
举例::

	$str = $this->db->last_query();
	
	// Produces:  SELECT * FROM sometable....


.. note:: 将数据库配置文件中的 **save_queries** 设置为 FALSE 可以让这个方法无效。

关于数据库的信息
===============================

**$this->db->count_all()**

该方法用于获取数据表的总行数，第一个参数为表名，例如::

	echo $this->db->count_all('my_table');
	
	// Produces an integer, like 25

**$this->db->platform()**

该方法输出你正在使用的数据库平台（MySQL，MS SQL，Postgres 等）::

	echo $this->db->platform();

**$this->db->version()**

该方法输出你正在使用的数据库版本::

	echo $this->db->version();

让你的查询更简单
==========================

**$this->db->insert_string()**

这个方法简化了 INSERT 语句的书写，它返回一个正确格式化的 INSERT 语句。
举例::

	$data = array('name' => $name, 'email' => $email, 'url' => $url);
	
	$str = $this->db->insert_string('table_name', $data);

第一个参数为表名，第二个参数是一个关联数组，表示待插入的数据。
上面的例子生成的 SQL 语句如下::

	INSERT INTO table_name (name, email, url) VALUES ('Rick', 'rick@example.com', 'example.com')

.. note:: 所有的值自动被转义，生成安全的查询语句。

**$this->db->update_string()**

这个方法简化了 UPDATE 语句的书写，它返回一个正确格式化的 UPDATE 语句。
举例::

	$data = array('name' => $name, 'email' => $email, 'url' => $url);
	
	$where = "author_id = 1 AND status = 'active'";
	
	$str = $this->db->update_string('table_name', $data, $where);

第一个参数是表名，第二个参数是一个关联数组，表示待更新的数据，第三个参数
是个 WHERE 子句。上面的例子生成的 SQL 语句如下::

	 UPDATE table_name SET name = 'Rick', email = 'rick@example.com', url = 'example.com' WHERE author_id = 1 AND status = 'active'

.. note:: 所有的值自动被转义，生成安全的查询语句。