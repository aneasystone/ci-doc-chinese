#####################
自定义函数调用
#####################

$this->db->call_function();
============================

这个方法用于执行一些 CodeIgniter 中没有定义的 PHP 数据库函数，而且
使用了一种平台独立的方式。举个例子，假设你要调用 mysql_get_client_info()
函数，这个函数 CodeIgniter 并不是原生支持的，你可以这样做::

	$this->db->call_function('get_client_info');

你必须提供一个不带 mysql\_ 前缀的函数名来作为第一个参数，这个前缀
会根据当前所使用的数据库驱动自动添加。这让你可以在不同的数据库平台
执行相同的函数。但是很显然，并不是所有的数据库平台函数都是一样的，
所以就可移植性而言，它的作用非常有限。

任何你需要的其它参数都放在第一个参数后面。

::

	$this->db->call_function('some_function', $param1, $param2, etc..);

经常的，你会需要提供一个数据库的 connection ID 或是一个 result ID，
connection ID 可以这样来获得::

	$this->db->conn_id;

result ID 可以从查询返回的结果对象获取，像这样::

	$query = $this->db->query("SOME QUERY");
	
	$query->result_id;