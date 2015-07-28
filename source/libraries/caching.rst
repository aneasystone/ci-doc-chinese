##############
缓存驱动器
##############

CodeIgniter 提供了几种最常用的快速缓存的封装，除了基于文件的缓存，
其他的缓存都需要对服务器进行特殊的配置，如果配置不正确，将会抛出
一个致命错误异常（Fatal Exception）。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

*************
使用示例
*************

下面的示例代码用于加载缓存驱动器，使用 `APC <#alternative-php-cache-apc-caching>`_ 作为缓存，
如果 APC 在服务器环境下不可用，将降级到基于文件的缓存。

::

	$this->load->driver('cache', array('adapter' => 'apc', 'backup' => 'file'));

	if ( ! $foo = $this->cache->get('foo'))
	{
		echo 'Saving to the cache!<br />';
		$foo = 'foobarbaz!';

		// Save into the cache for 5 minutes
		$this->cache->save('foo', $foo, 300);
	}

	echo $foo;

你也可以设置 **key_prefix** 参数来给缓存名添加前缀，当你在同一个环境下运行多个应用时，它可以避免冲突。

::

	$this->load->driver('cache',
		array('adapter' => 'apc', 'backup' => 'file', 'key_prefix' => 'my_')
	);

	$this->cache->get('foo'); // Will get the cache entry named 'my_foo'

***************
类参考
***************

.. php:class:: CI_Cache

	.. php:method:: is_supported($driver)

		:param	string	$driver: the name of the caching driver
		:returns:	TRUE if supported, FALSE if not
		:rtype:	bool

		当使用 ``$this->cache->get()`` 方法来访问驱动器时该方法会被自动调用，但是，如果你使用了某些个人的驱动器，
		应该先调用该方法确保这个驱动器在服务器环境下是否被支持。
		::

			if ($this->cache->apc->is_supported())
			{
				if ($data = $this->cache->apc->get('my_cache'))
				{
					// do things.
				}
			}

	.. php:method:: get($id)

		:param	string	$id: Cache item name
		:returns:	Item value or FALSE if not found
		:rtype:	mixed

		该方法用于从缓存中获取一项条目，如果获取的条目不存在，方法返回 FALSE 。
		::

			$foo = $this->cache->get('my_cached_item');

	.. php:method:: save($id, $data[, $ttl = 60[, $raw = FALSE]])

		:param	string	$id: Cache item name
		:param	mixed	$data: the data to save
		:param	int	$ttl: Time To Live, in seconds (default 60)
		:param	bool	$raw: Whether to store the raw value
		:returns:	TRUE on success, FALSE on failure
		:rtype:	string

		该方法用于将一项条目保存到缓存中，如果保存失败，方法返回 FALSE 。
		::

			$this->cache->save('cache_item_id', 'data_to_cache');

		.. note:: 参数 ``$raw`` 只有在使用 APC 和 Memcache 缓存时才有用，
			它用于 ``increment()`` 和 ``decrement()`` 方法。

	.. php:method:: delete($id)

		:param	string	$id: name of cached item
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		该方法用于从缓存中删除一项指定条目，如果删除失败，方法返回 FALSE 。
		::

			$this->cache->delete('cache_item_id');

	.. php:method:: increment($id[, $offset = 1])

		:param	string	$id: Cache ID
		:param	int	$offset: Step/value to add
		:returns:	New value on success, FALSE on failure
		:rtype:	mixed

		对缓存中的值执行原子自增操作。
		::

			// 'iterator' has a value of 2

			$this->cache->increment('iterator'); // 'iterator' is now 3

			$this->cache->increment('iterator', 3); // 'iterator' is now 6

	.. php:method:: decrement($id[, $offset = 1])

		:param	string	$id: Cache ID
		:param	int	$offset: Step/value to reduce by
		:returns:	New value on success, FALSE on failure
		:rtype:	mixed

		对缓存中的值执行原子自减操作。
		::

			// 'iterator' has a value of 6

			$this->cache->decrement('iterator'); // 'iterator' is now 5

			$this->cache->decrement('iterator', 2); // 'iterator' is now 3

	.. php:method:: clean()

		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		该方法用于清空整个缓存，如果清空失败，方法返回 FALSE 。
		::

			$this->cache->clean();

	.. php:method:: cache_info()

		:returns:	Information on the entire cache database
		:rtype:	mixed

		该方法返回整个缓存的信息。
		::

			var_dump($this->cache->cache_info());

		.. note:: 返回的信息以及数据结构取决于使用的缓存驱动器。

	.. php:method:: get_metadata($id)

		:param	string	$id: Cache item name
		:returns:	Metadata for the cached item
		:rtype:	mixed

		该方法用于获取缓存中某个指定条目的详细信息。
		::

			var_dump($this->cache->get_metadata('my_cached_item'));

		.. note:: 返回的信息以及数据结构取决于使用的缓存驱动器。

*******
驱动器
*******

可选 PHP 缓存（APC）
===================================

上述所有方法都可以直接使用，而不用在加载驱动器时指定 adapter 参数，如下所示::

	$this->load->driver('cache');
	$this->cache->apc->save('foo', 'bar', 10);

关于 APC 的更多信息，请参阅 `http://php.net/apc <http://php.net/apc>`_

基于文件的缓存
==================

和输出类的缓存不同的是，基于文件的缓存支持只缓存视图的某一部分。使用这个缓存时要注意，
确保对你的应用程序进行基准测试，因为当磁盘 I/O 频繁时可能对缓存有负面影响。

上述所有方法都可以直接使用，而不用在加载驱动器时指定 adapter 参数，如下所示::

	$this->load->driver('cache');
	$this->cache->file->save('foo', 'bar', 10);

Memcached 缓存
=================

可以在 memcached.php 配置文件中指定多个 Memcached 服务器，配置文件位于 
*application/config/* 目录。

上述所有方法都可以直接使用，而不用在加载驱动器时指定 adapter 参数，如下所示::

	$this->load->driver('cache');
	$this->cache->memcached->save('foo', 'bar', 10);

关于 Memcached 的更多信息，请参阅 `http://php.net/memcached <http://php.net/memcached>`_

WinCache 缓存
================

在 Windows 下，你还可以使用 WinCache 缓存。

上述所有方法都可以直接使用，而不用在加载驱动器时指定 adapter 参数，如下所示::

	$this->load->driver('cache');
	$this->cache->wincache->save('foo', 'bar', 10);

关于 WinCache 的更多信息，请参阅 `http://php.net/wincache <http://php.net/wincache>`_

Redis 缓存
=============

Redis 是一个在内存中以键值形式存储数据的缓存，使用 LRU（最近最少使用算法）缓存模式，
要使用它，你需要先安装 `Redis 服务器和 phpredis 扩展 <https://github.com/phpredis/phpredis>`_ 。

连接 Redis 服务器的配置信息必须保存到 application/config/redis.php 文件中，可用参数有::
	
	$config['socket_type'] = 'tcp'; //`tcp` or `unix`
	$config['socket'] = '/var/run/redis.sock'; // in case of `unix` socket type
	$config['host'] = '127.0.0.1';
	$config['password'] = NULL;
	$config['port'] = 6379;
	$config['timeout'] = 0;

上述所有方法都可以直接使用，而不用在加载驱动器时指定 adapter 参数，如下所示::

	$this->load->driver('cache');
	$this->cache->redis->save('foo', 'bar', 10);

关于 Redis 的更多信息，请参阅 `http://redis.io <http://redis.io>`_

虚拟缓存（Dummy Cache）
==========================

这是一个永远不会命中的缓存，它不存储数据，但是它允许你在当使用的缓存在你的环境下不被支持时，
仍然保留使用缓存的代码。
