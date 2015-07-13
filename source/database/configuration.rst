######################
数据库配置
######################

CodeIgniter 有一个配置文件用来保存数据库连接值（用户名、密码、数据库名等等），
这个配置文件位于 ``application/config/database.php`` 。
你也可以放置不同的 **database.php** 文件到特定的环境配置文件夹里
来设置 :doc:`特定环境 <../libraries/config>` 的数据库连接值。

配置存放在一个多维数组里，原型如下::

	$db['default'] = array(
		'dsn'	=> '',
		'hostname' => 'localhost',
		'username' => 'root',
		'password' => '',
		'database' => 'database_name',
		'dbdriver' => 'mysqli',
		'dbprefix' => '',
		'pconnect' => TRUE,
		'db_debug' => TRUE,
		'cache_on' => FALSE,
		'cachedir' => '',
		'char_set' => 'utf8',
		'dbcollat' => 'utf8_general_ci',
		'swap_pre' => '',
		'encrypt' => FALSE,
		'compress' => FALSE,
		'stricton' => FALSE,
		'failover' => array()
	);

有些数据库驱动（譬如：PDO，PostgreSQL，Oracle，ODBC）可能需要提供完整的 DSN 字符串。
在这种情况下，你需要使用 'dsn' 配置参数，就好像使用该驱动的 PHP 原生扩展一样。譬如::

	// PDO
	$db['default']['dsn'] = 'pgsql:host=localhost;port=5432;dbname=database_name';

	// Oracle
	$db['default']['dsn'] = '//localhost/XE';

.. note:: 如果你没有为需要 DSN 参数的驱动指定 DSN 字符串，CodeIgniter 将使用你提供的其他配置信息自动生成它。

.. note:: 如果你提供了一个 DSN 字符串，但是缺少了某些配置（譬如：数据库的字符集），
	如果该配置存在在其他的配置项中，CodeIgniter 将自动在 DSN 上附加上该配置。

当主数据库由于某些原因无法连接时，你还可以配置故障转移（failover）。
可以像下面这样为一个连接配置故障转移::

	$db['default']['failover'] = array(
			array(
				'hostname' => 'localhost1',
				'username' => '',
				'password' => '',
				'database' => '',
				'dbdriver' => 'mysqli',
				'dbprefix' => '',
				'pconnect' => TRUE,
				'db_debug' => TRUE,
				'cache_on' => FALSE,
				'cachedir' => '',
				'char_set' => 'utf8',
				'dbcollat' => 'utf8_general_ci',
				'swap_pre' => '',
				'encrypt' => FALSE,
				'compress' => FALSE,
				'stricton' => FALSE
			),
			array(
				'hostname' => 'localhost2',
				'username' => '',
				'password' => '',
				'database' => '',
				'dbdriver' => 'mysqli',
				'dbprefix' => '',
				'pconnect' => TRUE,
				'db_debug' => TRUE,
				'cache_on' => FALSE,
				'cachedir' => '',
				'char_set' => 'utf8',
				'dbcollat' => 'utf8_general_ci',
				'swap_pre' => '',
				'encrypt' => FALSE,
				'compress' => FALSE,
				'stricton' => FALSE
			)
		);

你可以指定任意多个故障转移。

我们使用多维数组的原因是为了让你随意的存储多个连接值的设置，
譬如：如果你有多个环境（开发、生产、测试 等等），
你能为每个环境建立独立的连接组，并在组之间进行切换。
举个例子，如果要设置一个 "test" 环境，你可以这样做::

	$db['test'] = array(
		'dsn'	=> '',
		'hostname' => 'localhost',
		'username' => 'root',
		'password' => '',
		'database' => 'database_name',
		'dbdriver' => 'mysqli',
		'dbprefix' => '',
		'pconnect' => TRUE,
		'db_debug' => TRUE,
		'cache_on' => FALSE,
		'cachedir' => '',
		'char_set' => 'utf8',
		'dbcollat' => 'utf8_general_ci',
		'swap_pre' => '',
		'compress' => FALSE,
		'encrypt' => FALSE,
		'stricton' => FALSE,
		'failover' => array()
	);

然后，设置位于配置文件中的 ``$active_group`` 变量，告诉系统要使用 "test" 组::

	$active_group = 'test';

.. note:: 分组的名称 "test" 是任意的，你可以取任意的名字。默认情况下，
	主连接使用 "default" 这个名称。当然，您可以基于您的项目为它起一个更有意义的名字。

查询构造器
-------------

可以通过数据库配置文件里的 ``$query_builder`` 变量对 :doc:`查询构造器类 <query_builder>` 
进行全局的设定（启用设成 TRUE，禁用设成 FALSE，默认是 TRUE）。
如果你不用这个类，那么你可以通过将这个变量值设置成 FALSE 来减少在数据库类初始化时对电脑资源的消耗。

::

	$query_builder = TRUE;

.. note:: 一些 CodeIgniter 的类，例如 Sessions，在执行一些函数的时候需要查询构造器的支持。

参数解释：
----------------------

======================  ==================================================================================================
 配置名                                   描述
======================  ==================================================================================================
**dsn**			DSN 连接字符串（该字符串包含了所有的数据库配置信息）
**hostname**			数据库的主机名，通常位于本机，可以表示为 "localhost"
**username**			需要连接到数据库的用户名
**password**			登陆数据库的密码
**database**			你需要连接的数据库名
**dbdriver**			数据库类型。如：mysql、postgres、odbc 等。必须为小写字母。
**dbprefix**			当使用 :doc:`查询构造器 <query_builder>` 查询时，可以选择性的为表加个前缀，
				它允许在一个数据库上安装多个 CodeIgniter 程序。
**pconnect**			TRUE/FALSE (boolean) - 是否使用持续连接
**db_debug**			TRUE/FALSE (boolean) - 是否显示数据库错误信息
**cache_on**			TRUE/FALSE (boolean) - 是否开启数据库查询缓存，
				详情请见 :doc:`数据库缓存类 <caching>`。
**cachedir**			数据库查询缓存目录所在的服务器绝对路径
**char_set**			与数据库通信时所使用的字符集
**dbcollat**			与数据库通信时所使用的字符规则

				.. note:: 只使用于 'mysql' 和 'mysqli' 数据库驱动

**swap_pre**			替换默认的 ``dbprefix`` 表前缀，该项设置对于分布式应用是非常有用的，
				你可以在查询中使用由最终用户定制的表前缀。
**schema**			数据库模式，默认为 'public'，用于 PostgreSQL 和 ODBC 驱动
**encrypt**			TRUE/FALSE (boolean) - 是否使用加密连接
**compress**			TRUE/FALSE (boolean) - 是否使用客户端压缩协议（只用于MySQL）
**stricton**			TRUE/FALSE (boolean) - 是否强制使用 "Strict Mode" 连接, 
				在开发程序时，使用 strict SQL 是一个好习惯。
**port**			数据库端口号，要使用这个值，你应该添加一行代码到数据库配置数组。
				::

					$db['default']['port'] = 5432;
======================  ==================================================================================================

.. note:: 根据你使用的数据库平台（MySQL, PostgreSQL 等），并不是所有的参数都是必须的。譬如，
	当你使用 SQLite 时，你无需指定用户名和密码，数据库名称直接是你的数据库文件的路径。
	以上内容假设你使用的是 MySQL 数据库。