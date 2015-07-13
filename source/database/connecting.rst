###########################
连接你的数据库
###########################

有两种方法连接数据库：

自动连接
========================

“自动连接” 特性将在每一个页面加载时自动实例化数据库类。要启用“自动连接”，
可在 ``application/config/autoload.php`` 中的 library 数组里添加 database::

$autoload['libraries'] = array('database');

手动连接
===================

如果你只有一部分页面需要数据库连接，你可以在那些有需要的函数里手工添加
如下代码来连接数据库，或者写在类的构造函数里，让整个类都可以访问：

::

	$this->load->database();

如果 ``database()`` 函数没有指定第一个参数，它将使用数据库配置文件中
指定的组连接数据库。对大多数人而言，这是首选方案。

可用的参数
--------------------

#. 数据库连接值，用数组或DSN字符串传递；
#. TRUE/FALSE (boolean) - 是否返回连接ID（参考下文的“连接多数据库”）；
#. TRUE/FALSE (boolean) - 是否启用查询构造器类，默认为 TRUE 。

手动连接到数据库
---------------------------------

这个函数的第一个参数是**可选的**，被用来从你的配置文件中
指定一个特定的数据库组，甚至可以使用没有在配置文件中定义的
数据库连接值。下面是例子：

从你的配置文件中选择一个特定分组::

	$this->load->database('group_name');

其中 ``group_name`` 是你的配置文件中连接组的名字。

连接一个完全手动指定的数据库，可以传一个数组参数::

	$config['hostname'] = 'localhost';
	$config['username'] = 'myusername';
	$config['password'] = 'mypassword';
	$config['database'] = 'mydatabase';
	$config['dbdriver'] = 'mysqli';
	$config['dbprefix'] = '';
	$config['pconnect'] = FALSE;
	$config['db_debug'] = TRUE;
	$config['cache_on'] = FALSE;
	$config['cachedir'] = '';
	$config['char_set'] = 'utf8';
	$config['dbcollat'] = 'utf8_general_ci';
	$this->load->database($config);

这些值的详细信息请参考 :doc: `数据库配置 <configuration>` 页面。

.. note:: 对于 PDO 驱动，你应该使用 ``$config['dsn']`` 取代 'hostname' 和 'database' 参数：

	|
	| $config['dsn'] = 'mysql:host=localhost;dbname=mydatabase';

或者你可以使用数据源名称（DSN，Data Source Name）作为参数，DSN 的格式必须类似于下面这样::

	$dsn = 'dbdriver://username:password@hostname/database';  
	$this->load->database($dsn);

当用 DSN 字符串连接时，要覆盖默认配置，可以像添加查询字符串一样添加配置变量。

::

	$dsn = 'dbdriver://username:password@hostname/database?char_set=utf8&dbcollat=utf8_general_ci&cache_on=true&cachedir=/path/to/cache';  
	$this->load->database($dsn);

连接到多个数据库
================================

如果你需要同时连接到多个不同的数据库，可以这样::

	$DB1 = $this->load->database('group_one', TRUE); 
	$DB2 = $this->load->database('group_two', TRUE);

注意：将 "group_one" 和 "group_two" 修改为你要连接的组名称
（或者像上面介绍的那样传入连接值数组）

第二个参数 TRUE 表示函数将返回数据库对象。

.. note:: 当你使用这种方式连接数据库时，你将通过你的对象名来执行数据库命令，
	而不再是通过这份指南中通篇介绍的，就像下面这样的语法了：
	
	|
	| $this->db->query();
	| $this->db->result();
	| etc...
	|
	| 取而代之的，你将这样执行数据库命令：
	|
	| $DB1->query();
	| $DB1->result();
	| etc...

.. note:: 如果你只是需要切换到同一个连接的另一个不同的数据库，你没必要创建
	独立的数据库配置，你可以像下面这样切换到另一个数据库：

	| $this->db->db_select($database2_name);

重新连接 / 保持连接有效
===========================================

当你在处理一些重量级的 PHP 操作时（例如处理图片），如果超过了数据库的超时值，
你应该考虑在执行后续查询之前先调用 ``reconnect()`` 方法向数据库发送 ping 命令，
这样可以优雅的保持连接有效或者重新建立起连接。

::

	$this->db->reconnect();

手动关闭连接
===============================

虽然 CodeIgniter 可以智能的管理并自动关闭数据库连接，你仍可以用下面的方法显式的关闭连接：

::

	$this->db->close();