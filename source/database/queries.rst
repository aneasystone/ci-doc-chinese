#######
查询
#######

************
基本查询
************

常规查询
===============

要提交一个查询，使用 **query** 函数::

	$this->db->query('YOUR QUERY HERE');

当你执行读类型的查询（如：SELECT）时，``query()`` 函数将以**对象**形式
返回一个结果集，参考这里来 :doc:`显示你的结果 <results>`。
当你执行写类型的查询（如：INSERT、DELETE、UPDATE）时，函数将简单的返回
TRUE 或 FALSE 来表示操作是否成功。
你可以将函数返回的结果赋值给一个变量，这样你就可以根据这个变量来获取
数据了，像下面这样::

	$query = $this->db->query('YOUR QUERY HERE');

简化查询
==================

**simple_query** 函数是 ``$this->db->query()`` 的简化版。它不会返回查询的
结果集，不会去设置查询计数器，不会去编译绑定的数据，不会去存储查询的调试信息。
它只是用于简单的提交一个查询，大多数用户并不会用到这个函数。

**simple_query** 函数直接返回数据库驱动器的 "execute" 方法的返回值。对于写类型的
查询（如：INSERT、DELETE、UPDATE），返回代表操作是否成功的 TRUE 或 FALSE；而
对于读类型的成功查询，则返回代表结果集的对象。

::

	if ($this->db->simple_query('YOUR QUERY'))
	{
		echo "Success!";
	}
	else
	{
		echo "Query failed!";
	}

.. note:: 对于所有的查询，如果成功执行的话，PostgreSQL 的 ``pg_exec()`` 函数
	都会返回一个结果集对象，就算是写类型的查询也是这样。如果你想判断查询执行是否
	成功或失败，请记住这一点。

***************************************
指定数据库前缀
***************************************

如果你配置了一个数据库前缀参数，想把它加上你的 SQL 语句里的表名前面，
你可以调用下面的方法::

	$this->db->dbprefix('tablename'); // outputs prefix_tablename

如果你想动态的修改这个前缀，而又不希望创建一个新的数据库连接，可以使用这个方法::

	$this->db->set_dbprefix('newprefix');
	$this->db->dbprefix('tablename'); // outputs newprefix_tablename


**********************
保护标识符
**********************

在很多数据库里，保护表名和字段名是可取的，例如在 MySQL 数据库里使用反引号。
**使用查询构造器会自动保护标识符**，尽管如此，你还是可以像下面这样手工来保护::

	$this->db->protect_identifiers('table_name');

.. important:: 尽管查询构造器会尽力保护好你输入的表名和字段名，但值得注意的是，
	它并不是被设计来处理任意用户输入的，所以，请不要传未处理的数据给它。

这个函数也可以为你的表名添加一个前缀，如果你在数据库配置文件中定义了 ``dbprefix`` 
参数，通过将这个函数的第二个参数设置为 TRUE 来启用前缀::

	$this->db->protect_identifiers('table_name', TRUE);


****************
转义查询
****************

在提交数据到你的数据库之前，确保先对其进行转义是个非常不错的做法。
CodeIgniter 有三个方法来帮你做到这一点：

#. **$this->db->escape()** 这个函数会检测数据类型，仅转义字符串类型的数据。
   它会自动用单引号将你的数据括起来，你不用手动添加：
   ::

	$sql = "INSERT INTO table (title) VALUES(".$this->db->escape($title).")";

#. **$this->db->escape_str()** 这个函数忽略数据类型，对传入的数据进行转义，
   这个方法并不常用，一般情况都是使用上面的那个方法。方法的使用代码如下：
   ::

	$sql = "INSERT INTO table (title) VALUES('".$this->db->escape_str($title)."')";

#. **$this->db->escape_like_str()** 这个函数用于处理 LIKE 语句中的字符串，
     这样，LIKE 通配符（'%', '_'）可以被正确的转义。

::

        $search = '20% raise'; 
        $sql = "SELECT id FROM table WHERE column LIKE '%" .
            $this->db->escape_like_str($search)."%'";


**************
查询绑定
**************

查询绑定可以简化你的查询语法，它通过系统自动的为你将各个查询组装在一起。
参考下面的例子::

	$sql = "SELECT * FROM some_table WHERE id = ? AND status = ? AND author = ?";
	$this->db->query($sql, array(3, 'live', 'Rick'));

查询语句中的问号将会自动被第二个参数位置的数组的相应的值替代。

也可以使用数组的数组进行绑定，里面的数组会被转换成 IN 语句的集合::

	$sql = "SELECT * FROM some_table WHERE id IN ? AND status = ? AND author = ?";
	$this->db->query($sql, array(array(3, 6), 'live', 'Rick'));

上面的例子会被转换为这样的查询::

	SELECT * FROM some_table WHERE id IN (3,6) AND status = 'live' AND author = 'Rick'

使用查询绑定的第二个好处是：所有的值会被自动转义，生成安全的查询语句。
你不再需要手工进行转义，系统会自动进行。

***************
错误处理
***************

**$this->db->error();**

要获取最近一次发生的错误，使用 ``error()`` 方法可以得到一个包含错误代码和错误消息的数组。
这里是一个简单例子::

	if ( ! $this->db->simple_query('SELECT `example_field` FROM `example_table`'))
	{
		$error = $this->db->error(); // Has keys 'code' and 'message'
	}

