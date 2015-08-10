###############
Session 类
###############

Session（会话）类可以让你保持一个用户的 "状态" ，并跟踪他在浏览你的网站时的活动。

CodeIgniter 自带了几个存储 session 的驱动：

  - 文件（默认的，基于文件系统）
  - 数据库
  - Redis
  - Memcached

另外，你也可以基于其他的存储机制来创建你自己的自定义 session 存储驱动，
使用自定义的驱动，同样也可以使用 Session 类提供的那些功能。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

***********************
使用 Session 类
***********************

初始化 Session 类
======================

Session 通常会在每个页面载入的时候全局运行，所以 Session 类必须首先被初始化。
您可以在 :doc:`控制器 <../general/controllers>` 的构造函数中初始化它，
也可以在系统中 :doc:`自动加载 <../general/autoloader>`。Session 类基本上都是在后台运行，
你不会注意到。所以当初始化 session 之后，系统会自动读取、创建和更新 session 数据 。

要手动初始化 Session 类，你可以在控制器的构造函数中使用 ``$this->load->library()``
方法::

	$this->load->library('session');

初始化之后，就可以使用下面的方法来访问 Session 对象了::

	$this->session

.. important:: 由于 :doc:`加载类 </libraries/loader>` 是在 CodeIgniter 的控制器基类中实例化的，
	所以如果要在你的控制器构造函数中加载类库的话，确保先调用 ``parent::__construct()`` 方法。

Session 是如何工作的？
=======================

当页面载入后，Session 类就会检查用户的 cookie 中是否存在有效的 session 数据。
如果 session 数据不存在（或者与服务端不匹配，或者已经过期），
那么就会创建一个新的 session 并保存起来。

如果 session 数据存在并且有效，那么就会更新 session 的信息。
根据你的配置，每一次更新都会生成一个新的 Session ID 。

有一点非常重要，你需要了解一下，Session 类一旦被初始化，它就会自动运行。
上面所说的那些，你完全不用做任何操作。正如接下来你将看到的那样，
你可以正常的使用 session 数据，至于读、写和更新 session 的操作都是自动完成的。

.. note:: 在 CLI 模式下，Session 类将自动关闭，这种做法完全是基于 HTTP 协议的。

关于并发的注意事项
----------------------------------

如果你开发的网站并不是大量的使用 AJAX 技术，那么你可以跳过这一节。
如果你的网站是大量的使用了 AJAX，并且遇到了性能问题，那么下面的注意事项，
可能正是你需要的。

在 CodeIgniter 之前的版本中，Session 类并没有实现锁机制，这也就意味着，
两个 HTTP 请求可能会同时使用同一个 session 。说的更专业点就是，
请求是非阻塞的。（requests were non-blocking）

在处理 session 时使用非阻塞的请求同样意味着不安全，因为在一个请求中修改 session 
数据（或重新生成 Session ID）会对并发的第二个请求造成影响。这是导致很多问题的根源，
同时也是为什么 CodeIgniter 3.0 对 Session 类完全重写的原因。

那么为什么要告诉你这些呢？这是因为在你查找性能问题的原因时，
可能会发现加锁机制正是导致性能问题的罪魁祸首，因此就想着如何去掉锁 ...

**请不要这样做！** 去掉加锁机制是完全错误的，它会给你带来更多的问题！

锁并不是问题，它是一种解决方案。你的问题是当 session 已经处理完毕不再需要时，
你还将 session 保持是打开的状态。所以，你需要做的其实是，当结束当前请求时，
将不再需要的 session 关闭掉。

简单来说就是：当你不再需要使用某个 session 变量时，就使用 ``session_write_close()`` 方法来关闭它。

什么是 Session 数据？
=====================

Session 数据是个简单的数组，带有一个特定的 session ID （cookie）。

如果你之前在 PHP 里使用过 session ，你应该对 PHP 的 `$_SESSION 全局变量 <http://php.net/manual/en/reserved.variables.session.php>`_
很熟悉（如果没有，请阅读下链接中的内容）。

CodeIgniter 使用了相同的方式来访问 session 数据，同时使用了 PHP 自带的 session 处理机制，
使用 session 数据和操作 ``$_SESSION`` 数组一样简单（包括读取，设置，取消设置）。

另外，CodeIgniter 还提供了两种特殊类型的 session 数据：flashdata 和 tempdata ，在下面将有介绍。

.. note:: 在之前的 CodeIgniter 版本中，常规的 session 数据被称之为 'userdata' ，当文档中出现这个词时请记住这一点。
	大部分都是用于解释自定义 'userdata' 方法是如何工作的。

获取 Session 数据
=======================

session 数组中的任何信息都可以通过 ``$_SESSION`` 全局变量获取::

	$_SESSION['item']

或使用下面的方法（magic getter）::

	$this->session->item

同时，为了和之前的版本兼容，也可以使用 ``userdata()`` 方法::

	$this->session->userdata('item');

其中，item 是你想获取的数组的键值。例如，将 'name' 键值对应的项赋值给 ``$name`` 变量，
你可以这样::

	$name = $_SESSION['name'];

	// or:

	$name = $this->session->name

	// or:

	$name = $this->session->userdata('name');

.. note:: 如果你访问的项不存在，``userdata()`` 方法返回 NULL 。

如果你想获取所有已存在的 userdata ，你可以忽略 item 参数::

	$_SESSION

	// or:

	$this->session->userdata();

添加 Session 数据
===================

假设某个用户访问你的网站，当他完成认证之后，你可以将他的用户名和 email 地址添加到 session 中，
这样当你需要的时候你就可以直接访问这些数据，而无法查询数据库了。

你可以简单的将数据赋值给 ``$_SESSION`` 数组，或赋值给 ``$this->session`` 的某个属性。

同时，老版本中的通过 "userdata" 来赋值的方法也还可以用，只不过是需要传递一个包含你的数据的数组
给 ``set_userdata()`` 方法::

	$this->session->set_userdata($array);

其中，``$array`` 是包含新增数据的一个关联数组，下面是个例子::

	$newdata = array(
		'username'  => 'johndoe',
		'email'     => 'johndoe@some-site.com',
		'logged_in' => TRUE
	);

	$this->session->set_userdata($newdata);

如果你想一次只添加一个值，``set_userdata()`` 也支持这种语法::

	$this->session->set_userdata('some_name', 'some_value');

如果你想检查某个 session 值是否存在，可以使用 ``isset()``::

	// returns FALSE if the 'some_name' item doesn't exist or is NULL,
	// TRUE otherwise:
	isset($_SESSION['some_name'])

或者，你也可以使用 ``has_userdata()``::

	$this->session->has_userdata('some_name');

删除 Session 数据
=====================

和其他的变量一样，可以使用 ``unset()`` 方法来删除 ``$_SESSION`` 数组中的某个值::

	unset($_SESSION['some_name']);

	// or multiple values:

	unset(
		$_SESSION['some_name'],
		$_SESSION['another_name']
	);

同时，正如 ``set_userdata()`` 方法可用于向 session 中添加数据，``unset_userdata()`` 
方法可用于删除指定键值的数据。例如，如果你想从你的 session 数组中删除 'some_name'::

	$this->session->unset_userdata('some_name');

这个方法也可以使用一个数组来同时删除多个值::

	$array_items = array('username', 'email');

	$this->session->unset_userdata($array_items);

.. note:: 在 CodeIgniter 之前的版本中，``unset_userdata()`` 方法接受一个关联数组，
	包含 ``key => 'dummy value'`` 这样的键值对，这种方式不再支持。

Flashdata
=========

CodeIgniter 支持 "flashdata" ，它指的是一种只对下一次请求有效的 session 数据，
之后将会自动被清除。

这用于一次性的信息时特别有用，例如错误或状态信息（诸如 "第二条记录删除成功" 这样的信息）。

要注意的是，flashdata 就是常规的 session 变量，只不过以特殊的方式保存在 '__ci_vars' 键下
（警告：请不要乱动这个值）。

将已有的值标记为 "flashdata"::

	$this->session->mark_as_flash('item');

通过传一个数组，同时标记多个值为 flashdata::

	$this->session->mark_as_flash(array('item', 'item2'));

使用下面的方法来添加 flashdata::

	$_SESSION['item'] = 'value';
	$this->session->mark_as_flash('item');

或者，也可以使用 ``set_flashdata()`` 方法::

	$this->session->set_flashdata('item', 'value');

你还可以传一个数组给 ``set_flashdata()`` 方法，和 ``set_userdata()`` 方法一样。

读取 flashdata 和读取常规的 session 数据一样，通过 ``$_SESSION`` 数组::

	$_SESSION['item']

.. important:: ``userdata()`` 方法不会返回 flashdata 数据。

如果你要确保你读取的就是 "flashdata" 数据，而不是其他类型的数据，可以使用 ``flashdata()`` 方法::

	$this->session->flashdata('item');

或者不传参数，直接返回所有的 flashdata 数组::

	$this->session->flashdata();

.. note:: 如果读取的值不存在，``flashdata()`` 方法返回 NULL 。

如果你需要在另一个请求中还继续保持 flashdata 变量，你可以使用 ``keep_flashdata()`` 方法。
可以传一个值，或包含多个值的一个数组。

::

	$this->session->keep_flashdata('item');
	$this->session->keep_flashdata(array('item1', 'item2', 'item3'));

Tempdata
========

CodeIgniter 还支持 "tempdata" ，它指的是一种带有有效时间的 session 数据，
当它的有效时间已过期，或在有效时间内被删除，都会自动被清除。

和 flashdata 一样， tempdata 也是常规的 session 变量，只不过以特殊的方式保存在 '__ci_vars' 键下
（再次警告：请不要乱动这个值）。

将已有的值标记为 "tempdata" ，只需简单的将要标记的键值和过期时间（单位为秒）传给 
``mark_as_temp()`` 方法即可::

	// 'item' will be erased after 300 seconds
	$this->session->mark_as_temp('item', 300);

你也可以同时标记多个值为 tempdata ，有下面两种不同的方式，
这取决于你是否要将所有的值都设置成相同的过期时间::

	// Both 'item' and 'item2' will expire after 300 seconds
	$this->session->mark_as_temp(array('item', 'item2'), 300);

	// 'item' will be erased after 300 seconds, while 'item2'
	// will do so after only 240 seconds
	$this->session->mark_as_temp(array(
		'item'	=> 300,
		'item2'	=> 240
	));

使用下面的方法来添加 tempdata::

	$_SESSION['item'] = 'value';
	$this->session->mark_as_temp('item', 300); // Expire in 5 minutes

或者，也可以使用 ``set_tempdata()`` 方法::

	$this->session->set_tempdata('item', 'value', 300);

你还可以传一个数组给 ``set_tempdata()`` 方法::

	$tempdata = array('newuser' => TRUE, 'message' => 'Thanks for joining!');

	$this->session->set_tempdata($tempdata, NULL, $expire);

.. note:: 如果没有设置 expiration 参数，或者设置为 0 ，将默认使用 300秒（5分钟）作为生存时间（time-to-live）。

要读取 tempdata 数据，你可以再一次通过 ``$_SESSION`` 数组::

	$_SESSION['item']

.. important:: ``userdata()`` 方法不会返回 tempdata 数据。

如果你要确保你读取的就是 "tempdata" 数据，而不是其他类型的数据，可以使用 ``tempdata()`` 方法::

	$this->session->tempdata('item');

或者不传参数，直接返回所有的 tempdata 数组::

	$this->session->tempdata();

.. note:: 如果读取的值不存在，``tempdata()`` 方法返回 NULL 。

如果你需要在某个 tempdata 过期之前删除它，你可以直接通过 ``$_SESSION`` 数组来删除::

	unset($_SESSION['item']);

但是，这不会删除这个值的 tempdata 标记（会在下一次 HTTP 请求时失效），所以，
如果你打算在相同的请求中重用这个值，你可以使用 ``unset_tempdata()``::

	$this->session->unset_tempdata('item');

销毁 Session
====================

要清除当前的 session（例如：退出登录时），你可以简单的使用 PHP 自带的 
`session_destroy() <http://php.net/session_destroy>`_ 函数或者 ``sess_destroy()`` 方法。
两种方式效果完全一样::

	session_destroy();

	// or

	$this->session->sess_destroy();

.. note:: 这必须是同一个请求中关于 session 的最后一次操作，所有的 session 数据（包括 flashdata 
	和 tempdata）都被永久性销毁，销毁之后，关于 session 的方法将不可用。

访问 session 元数据
==========================

在之前的 CodeIgniter 版本中，session 数据默认包含 4 项：'session_id' 、 'ip_address' 、 'user_agent' 、 'last_activity' 。

这是由 session 具体的工作方式决定的，但是我们现在的实现没必要这样做了。
尽管如此，你的应用程序可能还依赖于这些值，所以下面提供了访问这些值的替代方法：

  - session_id: ``session_id()``
  - ip_address: ``$_SERVER['REMOTE_ADDR']``
  - user_agent: ``$this->input->user_agent()`` (unused by sessions)
  - last_activity: 取决于 session 的存储方式，没有直接的方法，抱歉！

Session 参数
===================

在 CodeIgniter 中通常所有的东西都是拿来直接就可以用的，尽管如此，session 对于所有的程序来说，
都是一个非常敏感的部分，所以必须要小心的配置它。请花点时间研究下下面所有的选项以及每个选项的作用。

你可以在你的配置文件 **application/config/config.php** 中找到下面的关于 session 的配置参数：

============================ =============== ======================================== ============================================================================================
参数                               默认值         选项                                  描述
============================ =============== ======================================== ============================================================================================
**sess_driver**              files           files/database/redis/memcached/*custom*  使用的存储 session 的驱动
**sess_cookie_name**         ci_session      [A-Za-z\_-] characters only              session cookie 的名称
**sess_expiration**          7200 (2 hours)  Time in seconds (integer)                你希望 session 持续的秒数
                                                                                      如果你希望 session 不过期（直到浏览器关闭），将其设置为 0
**sess_save_path**           NULL            None                                     指定存储位置，取决于使用的存储 session 的驱动
**sess_match_ip**            FALSE           TRUE/FALSE (boolean)                     读取 session cookie 时，是否验证用户的 IP 地址
                                                                                      注意有些 ISP 会动态的修改 IP ，所以如果你想要一个不过期的 session，将其设置为 FALSE 
**sess_time_to_update**      300             Time in seconds (integer)                该选项用于控制过多久将重新生成一个新 session ID 
                                                                                      设置为 0 将禁用 session ID 的重新生成
**sess_regenerate_destroy**  FALSE           TRUE/FALSE (boolean)                     当自动重新生成 session ID 时，是否销毁老的 session ID 对应的数据
                                                                                      如果设置为 FALSE ，数据之后将自动被垃圾回收器删除
============================ =============== ======================================== ============================================================================================

.. note:: 如果上面的某个参数没有配置，Session 类将会试图读取 php.ini 配置文件中的 session 相关的配置
	（例如 'sess_expire_on_close'）。但是，请不要依赖于这个行为，因为这可能会导致不可预期的结果，而且
	这也有可能在未来的版本中修改。请合理的配置每一个参数。

除了上面的这些参数之外，cookie 和 session 原生的驱动还会公用下面这些
由 :doc:`输入类 <input>` 和 :doc:`安全类 <security>` 提供的配置参数。

================== =============== ===========================================================================
参数                 默认值         描述
================== =============== ===========================================================================
**cookie_domain**  ''              session 可用的域
**cookie_path**    /               session 可用的路径
**cookie_secure**  FALSE           是否只在加密连接（HTTPS）时创建 session cookie
================== =============== ===========================================================================

.. note:: 'cookie_httponly' 配置对 session 没有影响。出于安全原因，HttpOnly 参数将一直启用。
	另外，'cookie_prefix' 参数完全可以忽略。

Session 驱动
===============

正如上面提到的，Session 类自带了 4 种不同的驱动（或叫做存储引擎）可供使用：

  - files
  - database
  - redis
  - memcached

默认情况下，初始化 session 时将使用 `文件驱动`_ ，因为这是最安全的选择，可以在所有地方按预期工作
（几乎所有的环境下都有文件系统）。

但是，你也可以通过 **application/config/config.php** 配置文件中的 ``$config['sess_driver']`` 
参数来使用任何其他的驱动。特别提醒的是，每一种驱动都有它自己的注意事项，所以在你选择之前，
确定你熟悉它们。

另外，如果默认提供的这些不能满足你的需求，你也可以创建和使用 `自定义驱动`_ 。

.. note:: 在之前版本的 CodeIgniter 中，只有 "cookie 驱动" 这唯一的一种选择，
	因为这个我们收到了大量的负面的反馈。因此，我们吸取了社区的反馈意见，同时也要提醒你，
	因为它**不安全**，所以已经被废弃了，建议你不要试着通过 自定义驱动 来重新实现它。

文件驱动
------------

文件驱动利用你的文件系统来存储 session 数据。

可以说，文件驱动和 PHP 自带的默认 session 实现非常类似，但是有一个很重要的细节要注意的是，
实际上它们的代码并不相同，而且有一些局限性（以及优势）。

说的更具体点，它不支持 PHP 的 `session.save_path 参数的 目录分级（directory level）和 mode 格式 
<http://php.net/manual/en/session.configuration.php#ini.session.save-path>`_ ，
另外为了安全性大多数的参数都被硬编码。只提供了 ``$config['sess_save_path']`` 参数用于设置绝对路径。

另一个很重要的事情是，确保存储 session 文件的目录不能被公开访问到或者是共享目录，确保 **只有你**
能访问并查看配置的 *sess_save_path* 目录中的内容。否则，如果任何人都能访问，
他们就可以从中窃取到当前的 session （这也被称为 session 固定（session fixation）攻击）

在类 UNIX 操作系统中，这可以通过在该目录上执行 `chmod` 命令，将权限设置为 0700 来实现，
这样就可以只允许目录的所有者执行读取和写入操作。但是要注意的是，脚本的执行者通常不是你自己，
而是类似于 'www-data' 这样的用户，所以只设置权限可能会破坏你的程序。

根据你的环境，你应该像下面这样来操作。
::

	mkdir /<path to your application directory>/sessions/
	chmod 0700 /<path to your application directory>/sessions/
	chown www-data /<path to your application directory>/sessions/

小提示
^^^^^^^^^

有些人可能会选择使用其他的 session 驱动，他们认为文件存储通常比较慢。其实这并不总是对的。

执行一些简单的测试可能会让你真的相信 SQL 数据库更快一点，但是在 99% 的情况下，这只是当你的 
session 并发非常少的时候是对的。当 session 的并发数越来越大，服务器的负载越来越高，
这时就不一样了，文件系统将会胜过几乎所有的关系型数据库。

另外，如果性能是你唯一关心的，你可以看下 `tmpfs <http://eddmann.com/posts/storing-php-sessions-file-caches-in-memory-using-tmpfs/>`_
（注意：外部资源），它可以让你的 session 非常快。

数据库驱动
---------------

数据库驱动使用诸如 MySQL 或 PostgreSQL 这样的关系型数据库来存储 session ，
这是一个非常常见的选择，因为它可以让开发者非常方便的访问应用中的 session 数据，
因为它只是你的数据库中的一个表而已。

但是，还是有几点要求必须满足：

  - 只有设置为 **default** 的数据库连接可以使用（或者在控制器中使用 ``$this->db`` 来访问的连接）
  - 你必须启用 :doc:`查询构造器 </database/query_builder>`
  - 不能使用持久连接
  - 使用的数据库连接不能启用 *cache_on* 参数

为了使用数据库驱动，你还需要创建一个我们刚刚已经提到的数据表，然后将 ``$config['sess_save_path']`` 
参数设置为表名。例如，如果你想使用 'ci_sessions' 这个表名，你可以这样::

	$config['sess_driver'] = 'database';
	$config['sess_save_path'] = 'ci_sessions';

.. note:: 如果你从 CodeIgniter 之前的版本中升级过来的，并且没有配置 'sess_save_path' 参数，
	Session 类将查找并使用老的 'sess_table_name' 参数替代。请不要依赖这个行为，
	因为它可能会在以后的版本中移除。

然后，新建数据表 。

对于 MySQL::

	CREATE TABLE IF NOT EXISTS `ci_sessions` (
		`id` varchar(40) NOT NULL,
		`ip_address` varchar(45) NOT NULL,
		`timestamp` int(10) unsigned DEFAULT 0 NOT NULL,
		`data` blob NOT NULL,
		PRIMARY KEY (id),
		KEY `ci_sessions_timestamp` (`timestamp`)
	);

对于 PostgreSQL::

	CREATE TABLE "ci_sessions" (
		"id" varchar(40) NOT NULL,
		"ip_address" varchar(45) NOT NULL,
		"timestamp" bigint DEFAULT 0 NOT NULL,
		"data" text DEFAULT '' NOT NULL,
		PRIMARY KEY ("id")
	);

	CREATE INDEX "ci_sessions_timestamp" ON "ci_sessions" ("timestamp");

如果你想开启 *sess_match_ip* 参数，你还应该在新建表之后进行如下操作::

	// Works both on MySQL and PostgreSQL
	ALTER TABLE ci_sessions ADD CONSTRAINT ci_sessions_id_ip UNIQUE (id, ip_address);

.. important:: 只有 MySQL 和 PostgreSQL 数据库是被正式支持的，因为其他数据库平台都缺乏合适的锁机制。
	在没锁的情况下使用 session 可能会导致大量的问题，特别是使用了大量的 AJAX ，
	所以我们并不打算支持这种情况。如果你遇到了性能问题，请你在完成 session 数据的处理之后，
	调用 ``session_write_close()`` 方法。

Redis 驱动
------------

.. note:: 由于 Redis 没有锁机制，这个驱动的锁是通过一个保持 300 秒的值来模拟的
	（emulated by a separate value that is kept for up to 300 seconds）。

Redis 是一种存储引擎，通常用于缓存，并由于他的高性能而流行起来，这可能也正是你使用 Redis 驱动的原因。

缺点是它并不像关系型数据库那样普遍，需要你的系统中安装了 `phpredis <https://github.com/phpredis/phpredis>`_ 
这个 PHP 扩展，它并不是 PHP 程序自带的。
可能的情况是，你使用 Redis 驱动的原因是你已经非常熟悉 Redis 了并且你使用它还有其他的目的。

和文件驱动和数据库驱动一样，你必须通过 ``$config['sess_save_path']`` 参数来配置存储 session 的位置。
这里的格式有些不同，同时也要复杂一点，这在 *phpredis* 扩展的 README 文件中有很好的解释，链接如下::

	https://github.com/phpredis/phpredis#php-session-handler

.. warning:: CodeIgniter 的 Session 类并没有真的用到 'redis' 的 ``session.save_handler`` ，
	**只是** 采用了它的路径的格式而已。

最常见的情况是，一个简单 ``host:port`` 对就可以了::

	$config['sess_driver'] = 'redis';
	$config['sess_save_path'] = 'tcp://localhost:6379';

Memcached 驱动
----------------

.. note:: 由于 Memcache 没有锁机制，这个驱动的锁是通过一个保持 300 秒的值来模拟的
	（emulated by a separate value that is kept for up to 300 seconds）。

Memcached 驱动和 Redis 驱动非常相似，除了它的可用性可能要好点，因为 PHP 的 `Memcached
<http://php.net/memcached>`_ 扩展已经通过 PECL 发布了，并且在某些 Linux 发行版本中，
可以非常方便的安装它。

除了这一点，以及排除任何对 Redis 的偏见，关于 Memcached 要说的真的没什么区别，
它也是一款通常用于缓存的产品，而且以它的速度而闻名。

不过，值得注意的是，使用 Memcached 设置 X 的过期时间为 Y 秒，它只能保证 X 会在 Y 秒过后被删除
（但不会早于这个时间）。这个是非常少见的，但是应该注意一下，因为它可能会导致 session 的丢失。

``$config['sess_save_path']`` 参数的格式相当简单，使用 ``host:port`` 对即可::

	$config['sess_driver'] = 'memcached';
	$config['sess_save_path'] = 'localhost:11211';

小提示
^^^^^^^^^

也可以使用一个可选的 *权重* 参数来支持多服务器的配置，权重参数使用冒号分割（``:weight``），
但是我们并没有测试这是绝对可靠的。

如果你想体验这个特性（风险自负），只需简单的将多个服务器使用逗号分隔::

	// localhost will be given higher priority (5) here,
	// compared to 192.0.2.1 with a weight of 1.
	$config['sess_save_path'] = 'localhost:11211:5,192.0.2.1:11211:1';

自定义驱动
--------------

你也可以创建你自己的自定义 session 驱动，但是要记住的是，这通常来说都不是那么简单，
因为需要用到很多知识来正确实现它。

你不仅要知道 session 一般的工作原理，而且要知道它在 PHP 中是如何实现的，
还要知道它的内部存储机制是如何工作的，如何去处理并发，如何去避免死锁（不是通过去掉锁机制），
以及最后一点但也是很重要的一点，如何去处理潜在的安全问题。

总的来说，如果你不知道怎么在原生的 PHP 中实现这些，那么你也不应该在 CodeIgniter 中尝试实现它。
我已经警告过你了。

如果你只想给你的 session 添加一些额外的功能，你只要扩展 Session 基类就可以了，这要容易的多。
要学习如何实现这点，请阅读 :doc:`创建你的类库 <../general/creating_libraries>` 这一节。

言归正传，当你为 CodeIgniter 创建 session 驱动时，有三条规则你必须遵循：

  - 将你的驱动文件放在 **application/libraries/Session/drivers/** 目录下，并遵循 Session 类所使用的命名规范。

    例如，如果你想创建一个名为 'dummy' 的驱动，那么你需要创建一个名为 ``Session_dummy_driver`` 的类，
    并将其放在 *application/libraries/Session/drivers/Session_dummy_driver.php* 文件中。

  - 扩展 ``CI_Session_driver`` 类。

    这只是一个拥有几个内部辅助方法的基本类，同样可以和其他类库一样被扩展。如果你真的需要这样做，
    我们并不打算在这里多做解释，因为如果你知道如何在 CI 中扩展或覆写类，那么你已经知道这样做的方法了。
    如果你还不知道，那么可能你根本就不应该这样做。

  - 实现 `SessionHandlerInterface <http://php.net/sessionhandlerinterface>`_ 接口。

    .. note:: 你可能已经注意到 ``SessionHandlerInterface`` 接口已经在 PHP 5.4.0 之后的版本中提供了。
    	CodeIgniter 会在你运行老版本的 PHP 时自动声明这个接口。

    参考连接中的内容，了解为什么以及如何实现。

所以，使用我们上面的 'dummy' 驱动的例子，你可能会写如下代码::

	// application/libraries/Session/drivers/Session_dummy_driver.php:

	class CI_Session_dummy_driver extends CI_Session_driver implements SessionHandlerInterface
	{

		public function __construct(&$params)
		{
			// DO NOT forget this
			parent::__construct($params);

			// Configuration & other initializations
		}

		public function open($save_path, $name)
		{
			// Initialize storage mechanism (connection)
		}

		public function read($session_id)
		{
			// Read session data (if exists), acquire locks
		}

		public function write($session_id, $session_data)
		{
			// Create / update session data (it might not exist!)
		}

		public function close()
		{
			// Free locks, close connections / streams / etc.
		}

		public function destroy($session_id)
		{
			// Call close() method & destroy data for current session (order may differ)
		}

		public function gc($maxlifetime)
		{
			// Erase data for expired sessions
		}

	}

如果一切顺利，现在你就可以将 *sess_driver* 参数设置为 'dummy' ，来使用你自定义的驱动。恭喜你！

***************
类参考
***************

.. php:class:: CI_Session

	.. php:method:: userdata([$key = NULL])

		:param	mixed	$key: Session item key or NULL
		:returns:	Value of the specified item key, or an array of all userdata
		:rtype:	mixed

		从 ``$_SESSION`` 数组中获取指定的项。如果没有指定参数，返回所有 "userdata" 的数组。
	
		.. note:: 这是个遗留方法，只是为了和老的应用程序向前兼容而保留。
			你可以直接使用 ``$_SESSION`` 替代它。

	.. php:method:: all_userdata()

		:returns:	An array of all userdata
		:rtype:	array

		返回所有 "userdata" 的数组。

		.. note:: 该方法已废弃，使用不带参数的 ``userdata()`` 方法来代替。

	.. php:method:: &get_userdata()

		:returns:	A reference to ``$_SESSION``
		:rtype:	array

		返回一个 ``$_SESSION`` 数组的引用。

		.. note:: 这是个遗留方法，只是为了和老的应用程序向前兼容而保留。

	.. php:method:: has_userdata($key)

		:param	string	$key: Session item key
		:returns:	TRUE if the specified key exists, FALSE if not
		:rtype:	bool

		检查 ``$_SESSION`` 数组中是否存在某项。

		.. note:: 这是个遗留方法，只是为了和老的应用程序向前兼容而保留。
			它只是 ``isset($_SESSION[$key])`` 的一个别名，请使用这个来替代它。

	.. php:method:: set_userdata($data[, $value = NULL])

		:param	mixed	$data: An array of key/value pairs to set as session data, or the key for a single item
		:param	mixed	$value:	The value to set for a specific session item, if $data is a key
		:rtype:	void

		将数据赋值给 ``$_SESSION`` 全局变量。

		.. note:: 这是个遗留方法，只是为了和老的应用程序向前兼容而保留。

	.. php:method:: unset_userdata($key)

		:param	mixed	$key: Key for the session data item to unset, or an array of multiple keys
		:rtype:	void

		从 ``$_SESSION`` 全局变量中删除某个值。

		.. note:: 这是个遗留方法，只是为了和老的应用程序向前兼容而保留。
			它只是 ``unset($_SESSION[$key])`` 的一个别名，请使用这个来替代它。

	.. php:method:: mark_as_flash($key)

		:param	mixed	$key: Key to mark as flashdata, or an array of multiple keys
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		将 ``$_SESSION`` 数组中的一项（或多项）标记为 "flashdata" 。

	.. php:method:: get_flash_keys()

		:returns:	Array containing the keys of all "flashdata" items.
		:rtype:	array

		获取 ``$_SESSION`` 数组中所有标记为 "flashdata" 的一个列表。

	.. php:method:: umark_flash($key)

		:param	mixed	$key: Key to be un-marked as flashdata, or an array of multiple keys
		:rtype:	void

		将 ``$_SESSION`` 数组中的一项（或多项）移除 "flashdata" 标记。

	.. php:method:: flashdata([$key = NULL])

		:param	mixed	$key: Flashdata item key or NULL
		:returns:	Value of the specified item key, or an array of all flashdata
		:rtype:	mixed

		从 ``$_SESSION`` 数组中获取某个标记为 "flashdata" 的指定项。
		如果没有指定参数，返回所有 "flashdata" 的数组。
	
		.. note:: 这是个遗留方法，只是为了和老的应用程序向前兼容而保留。
			你可以直接使用 ``$_SESSION`` 替代它。

	.. php:method:: keep_flashdata($key)

		:param	mixed	$key: Flashdata key to keep, or an array of multiple keys
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		将某个指定的 "flashdata" 设置为在下一次请求中仍然保持有效。

		.. note:: 这是个遗留方法，只是为了和老的应用程序向前兼容而保留。
			它只是 ``mark_as_flash()`` 方法的一个别名。

	.. php:method:: set_flashdata($data[, $value = NULL])

		:param	mixed	$data: An array of key/value pairs to set as flashdata, or the key for a single item
		:param	mixed	$value:	The value to set for a specific session item, if $data is a key
		:rtype:	void

		将数据赋值给 ``$_SESSION`` 全局变量，并标记为 "flashdata" 。

		.. note:: 这是个遗留方法，只是为了和老的应用程序向前兼容而保留。

	.. php:method:: mark_as_temp($key[, $ttl = 300])

		:param	mixed	$key: Key to mark as tempdata, or an array of multiple keys
		:param	int	$ttl: Time-to-live value for the tempdata, in seconds
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		将 ``$_SESSION`` 数组中的一项（或多项）标记为 "tempdata" 。

	.. php:method:: get_temp_keys()

		:returns:	Array containing the keys of all "tempdata" items.
		:rtype:	array

		获取 ``$_SESSION`` 数组中所有标记为 "tempdata" 的一个列表。

	.. php:method:: umark_temp($key)

		:param	mixed	$key: Key to be un-marked as tempdata, or an array of multiple keys
		:rtype:	void

		将 ``$_SESSION`` 数组中的一项（或多项）移除 "tempdata" 标记。

	.. php:method:: tempdata([$key = NULL])

		:param	mixed	$key: Tempdata item key or NULL
		:returns:	Value of the specified item key, or an array of all tempdata
		:rtype:	mixed

		从 ``$_SESSION`` 数组中获取某个标记为 "tempdata" 的指定项。
		如果没有指定参数，返回所有 "tempdata" 的数组。

		.. note:: 这是个遗留方法，只是为了和老的应用程序向前兼容而保留。
			你可以直接使用 ``$_SESSION`` 替代它。

	.. php:method:: set_tempdata($data[, $value = NULL])

		:param	mixed	$data: An array of key/value pairs to set as tempdata, or the key for a single item
		:param	mixed	$value:	The value to set for a specific session item, if $data is a key
		:param	int	$ttl: Time-to-live value for the tempdata item(s), in seconds
		:rtype:	void

		将数据赋值给 ``$_SESSION`` 全局变量，并标记为 "tempdata" 。

		.. note:: 这是个遗留方法，只是为了和老的应用程序向前兼容而保留。

	.. php:method:: sess_regenerate([$destroy = FALSE])

		:param	bool	$destroy: Whether to destroy session data
		:rtype:	void

		重新生成 session ID ，$destroy 参数可选，用于销毁当前的 session 数据。

		.. note:: 该方法只是 PHP 原生的 `session_regenerate_id()
			<http://php.net/session_regenerate_id>`_ 函数的一个别名而已。

	.. php:method:: sess_destroy()

		:rtype:	void

		销毁当前 session 。

		.. note:: 这个方法必须在处理 session 相关的操作的**最后**调用。
			如果调用这个方法，所有的 session 数据都会丢失。

		.. note:: 该方法只是 PHP 原生的 `session_destroy()
			<http://php.net/session_destroy>`_  函数的一个别名而已。

	.. php:method:: __get($key)

		:param	string	$key: Session item key
		:returns:	The requested session data item, or NULL if it doesn't exist
		:rtype:	mixed

		魔术方法，根据你的喜好，使用 ``$this->session->item`` 这种方式来替代 
		``$_SESSION['item']`` 。

		如果你访问 ``$this->session->session_id`` 它也会调用 ``session_id()`` 方法来返回 session ID 。

	.. php:method:: __set($key, $value)

		:param	string	$key: Session item key
		:param	mixed	$value: Value to assign to the session item key
		:returns:	void

		魔术方法，直接赋值给 ``$this->session`` 属性，以此来替代赋值给 ``$_SESSION`` 数组::

			$this->session->foo = 'bar';

			// Results in:
			// $_SESSION['foo'] = 'bar';
