###########
输入类
###########

输入类有两个用途：

#. 为了安全性，对输入数据进行预处理
#. 提供了一些辅助方法来获取输入数据并处理

.. note:: 该类由系统自动加载，你无需手工加载

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

***************
对输入进行过滤
***************

安全性过滤
==================

当访问 :doc:`控制器 <../general/controllers>` 时，安全过滤方法会自动被调用，
它做了以下几件事情：

-  如果 ``$config['allow_get_array']`` 设置为 FALSE （默认是 TRUE），销毁全局的 GET 数组。
-  当开启 register_globals 时，销毁所有的全局变量。
-  过滤 GET/POST/COOKIE 数据的键值，只允许出现字母和数字（和其他一些）字符。
-  提供了 XSS （跨站脚本攻击）过滤，可全局启用，或按需启用。
-  将换行符统一为 ``PHP_EOL`` （基于 UNIX 的系统下为 \\n，Windows 系统下为 \\r\\n），这个是可配置的。

XSS 过滤
=============

输入类可以自动的对输入数据进行过滤，来阻止跨站脚本攻击。如果你希望在每次遇到 POST 或 COOKIE
数据时自动运行过滤，你可以在 *application/config/config.php* 配置文件中设置如下参数::

	$config['global_xss_filtering'] = TRUE;

关于 XSS 过滤的信息，请参考 :doc:`安全类 <security>` 文档。

.. important:: 参数 'global_xss_filtering' 已经废弃，保留它只是为了实现向前兼容。
	XSS 过滤应该在*输出*的时候进行，而不是*输入*的时候！

*******************
访问表单数据
*******************

使用 POST、GET、COOKIE 和 SERVER 数据
=======================================

CodeIgniter 提供了几个辅助方法来从 POST、GET、COOKIE 和 SERVER 数组中获取数据。
使用这些方法来获取数据而不是直接访问数组（``$_POST['something']``）的最大的好处是，
这些方法会检查获取的数据是否存在，如果不存在则返回 NULL 。这使用起来将很方便，
你不再需要去检查数据是否存在。换句话说，通常你需要像下面这样做::

	$something = isset($_POST['something']) ? $_POST['something'] : NULL;

使用 CodeIgniter 的方法，你可以简单的写成::

	$something = $this->input->post('something');

主要有下面几个方法：

-  ``$this->input->post()``
-  ``$this->input->get()``
-  ``$this->input->cookie()``
-  ``$this->input->server()``

使用 php://input 流
============================

如果你需要使用 PUT、DELETE、PATCH 或其他的请求方法，你只能通过一个特殊的输入流来访问，
这个流只能被读一次，这和从诸如 ``$_POST`` 数组中读取数据相比起来要复杂一点，因为 POST
数组可以被访问多次来获取多个变量，而不用担心它会消失。

CodeIgniter 为你解决了这个问题，你只需要使用下面的 ``$raw_input_stream`` 属性即可，
就可以在任何时候读取 **php://input** 流中的数据::

	$this->input->raw_input_stream;

另外，如果输入流的格式和 $_POST 数组一样，你也可以通过 ``input_stream()`` 方法来访问它的值::

	$this->input->input_stream('key');

和其他的 ``get()`` 和 ``post()`` 方法类似，如果请求的数据不存在，则返回 NULL 。
你也可以将第二个参数设置为 TRUE ，来让数据经过 ``xss_clean()`` 的检查::

	$this->input->input_stream('key', TRUE); // XSS Clean
	$this->input->input_stream('key', FALSE); // No XSS filter

.. note:: 你可以使用 ``method()`` 方法来获取你读取的是什么数据，PUT、DELETE 还是 PATCH 。

***************
类参考
***************

.. php:class:: CI_Input

	.. attribute:: $raw_input_stream
		
		返回只读的 php://input 流数据。
		
		该属性可以被多次读取。

	.. php:method:: post([$index = NULL[, $xss_clean = NULL]])

		:param	mixed	$index: POST parameter name
		:param	bool	$xss_clean: Whether to apply XSS filtering
		:returns:	$_POST if no parameters supplied, otherwise the POST value if found or NULL if not
		:rtype:	mixed

		第一个参数为你想要获取的 POST 数据名::

			$this->input->post('some_data');

		如果获取的数据不存在，该方法返回 NULL 。

		第二个参数可选，用于决定是否使用 XSS 过滤器对数据进行过滤。
		要使用过滤器，可以将第二个参数设置为 TRUE ，或者将 
		``$config['global_xss_filtering']`` 参数设置为 TRUE 。
		::

			$this->input->post('some_data', TRUE);

		如果不带任何参数该方法将返回 POST 中的所有元素。

		如果希望返回 POST 所有元素并将它们通过 XSS 过滤器进行过滤，
		可以将第一个参数设为 NULL ，第二个参数设为 TRUE ::

			$this->input->post(NULL, TRUE); // returns all POST items with XSS filter
			$this->input->post(NULL, FALSE); // returns all POST items without XSS filter

		如果要返回 POST 中的多个元素，将所有需要的键值作为数组传给它::

			$this->input->post(array('field1', 'field2'));

		和上面一样，如果希望数据通过 XSS 过滤器进行过滤，将第二个参数设置为 TRUE::

			$this->input->post(array('field1', 'field2'), TRUE);

	.. php:method:: get([$index = NULL[, $xss_clean = NULL]])

		:param	mixed	$index: GET parameter name
		:param	bool	$xss_clean: Whether to apply XSS filtering
		:returns:	$_GET if no parameters supplied, otherwise the GET value if found or NULL if not
		:rtype:	mixed

		该函数和 ``post()`` 一样，只是它用于获取 GET 数据。
		::

			$this->input->get('some_data', TRUE);

		如果不带任何参数该方法将返回 GET 中的所有元素。

		如果希望返回 GET 所有元素并将它们通过 XSS 过滤器进行过滤，
		可以将第一个参数设为 NULL ，第二个参数设为 TRUE ::

			$this->input->get(NULL, TRUE); // returns all GET items with XSS filter
			$this->input->get(NULL, FALSE); // returns all GET items without XSS filtering

		如果要返回 GET 中的多个元素，将所有需要的键值作为数组传给它::

			$this->input->get(array('field1', 'field2'));

		和上面一样，如果希望数据通过 XSS 过滤器进行过滤，将第二个参数设置为 TRUE::

			$this->input->get(array('field1', 'field2'), TRUE);

	.. php:method:: post_get($index[, $xss_clean = NULL])

		:param	string	$index: POST/GET parameter name
		:param	bool	$xss_clean: Whether to apply XSS filtering
		:returns:	POST/GET value if found, NULL if not
		:rtype:	mixed

		该方法和 ``post()`` 和 ``get()`` 方法类似，它会同时查找 POST 和 GET 两个数组来获取数据，
		先查找 POST ，再查找 GET::

			$this->input->post_get('some_data', TRUE);

	.. php:method:: get_post($index[, $xss_clean = NULL])

		:param	string	$index: GET/POST parameter name
		:param	bool	$xss_clean: Whether to apply XSS filtering
		:returns:	GET/POST value if found, NULL if not
		:rtype:	mixed

		该方法和 ``post_get()`` 方法一样，只是它先查找 GET 数据::

			$this->input->get_post('some_data', TRUE);

		.. note:: 这个方法在之前的版本中和 ``post_get()`` 方法是完全一样的，在 CodeIgniter 3.0 中有所修改。

	.. php:method:: cookie([$index = NULL[, $xss_clean = NULL]])

		:param	mixed	$index: COOKIE name
		:param	bool	$xss_clean: Whether to apply XSS filtering
		:returns:	$_COOKIE if no parameters supplied, otherwise the COOKIE value if found or NULL if not
		:rtype:	mixed

		该方法和 ``post()`` 和 ``get()`` 方法一样，只是它用于获取 COOKIE 数据::

			$this->input->cookie('some_cookie');
			$this->input->cookie('some_cookie, TRUE); // with XSS filter

		如果要返回 COOKIE 中的多个元素，将所有需要的键值作为数组传给它::

			$this->input->cookie(array('some_cookie', 'some_cookie2'));

		.. note:: 和 :doc:`Cookie 辅助库 <../helpers/cookie_helper>` 中的 :php:func:`get_cookie()`
			函数不同的是，这个方法不会根据 ``$config['cookie_prefix']`` 来添加前缀。

	.. php:method:: server($index[, $xss_clean = NULL])

		:param	mixed	$index: Value name
		:param	bool	$xss_clean: Whether to apply XSS filtering
		:returns:	$_SERVER item value if found, NULL if not
		:rtype:	mixed

		该方法和 ``post()`` 、 ``get()`` 和 ``cookie()`` 方法一样，只是它用于获取 SERVER 数据::

			$this->input->server('some_data');

		如果要返回 SERVER 中的多个元素，将所有需要的键值作为数组传给它::

			$this->input->server(array('SERVER_PROTOCOL', 'REQUEST_URI'));

	.. php:method:: input_stream([$index = NULL[, $xss_clean = NULL]])

		:param	mixed	$index: Key name
		:param	bool	$xss_clean: Whether to apply XSS filtering
		:returns:	Input stream array if no parameters supplied, otherwise the specified value if found or NULL if not
		:rtype:	mixed

		该方法和 ``get()`` 、 ``post()`` 和 ``cookie()`` 方法一样，只是它用于获取 *php://input* 流数据。

	.. php:method:: set_cookie($name = ''[, $value = ''[, $expire = ''[, $domain = ''[, $path = '/'[, $prefix = ''[, $secure = FALSE[, $httponly = FALSE]]]]]]])

		:param	mixed	$name: Cookie name or an array of parameters
		:param	string	$value: Cookie value
		:param	int	$expire: Cookie expiration time in seconds
		:param	string	$domain: Cookie domain
		:param	string	$path: Cookie path
		:param	string	$prefix: Cookie name prefix
		:param	bool	$secure: Whether to only transfer the cookie through HTTPS
		:param	bool	$httponly: Whether to only make the cookie accessible for HTTP requests (no JavaScript)
		:rtype:	void


		设置 COOKIE 的值，有两种方法来设置 COOKIE 值：数组方式和参数方式。

		**数组方式**

		使用这种方式，可以将第一个参数设置为一个关联数组::

			$cookie = array(
				'name'   => 'The Cookie Name',
				'value'  => 'The Value',
				'expire' => '86500',
				'domain' => '.some-domain.com',
				'path'   => '/',
				'prefix' => 'myprefix_',
				'secure' => TRUE
			);

			$this->input->set_cookie($cookie);

		**注意**

		只有 name 和 value 两项是必须的，要删除 COOKIE 的话，将 expire 设置为空。

		COOKIE 的过期时间是 **秒** ，将它加到当前时间上就是 COOKIE 的过期时间。
		记住不要把它设置成时间了，只要设置成距离当前时间的秒数即可，那么在这段
		时间内，COOKIE 都将保持有效。如果将过期时间设置为 0 ，那么 COOKIE 只在
		浏览器打开的期间是有效的，关闭后就失效了。

		如果需要设置一个全站范围内的 COOKIE ，而不关心用户是如何访问你的站点的，
		可以将 **domain** 参数设置为你的 URL 前面以句点开头，如：.your-domain.com

		path 参数通常不用设，上面的例子设置为根路径。

		prefix 只在你想避免和其他相同名称的 COOKIE 冲突时才需要使用。

		secure 参数只有当你需要使用安全的 COOKIE 时使用。

		**参数方式**

		如果你喜欢，你也可以使用下面的方式来设置 COOKIE::

			$this->input->set_cookie($name, $value, $expire, $domain, $path, $prefix, $secure);

	.. php:method:: ip_address()

		:returns:	Visitor's IP address or '0.0.0.0' if not valid
		:rtype:	string

		返回当前用户的 IP 地址，如果 IP 地址无效，则返回 '0.0.0.0'::

			echo $this->input->ip_address();

		.. important:: 该方法会根据 ``$config['proxy_ips']`` 配置，来返回 HTTP_X_FORWARDED_FOR、
			HTTP_CLIENT_IP、HTTP_X_CLIENT_IP 或 HTTP_X_CLUSTER_CLIENT_IP 。

	.. php:method:: valid_ip($ip[, $which = ''])

		:param	string	$ip: IP address
		:param	string	$which: IP protocol ('ipv4' or 'ipv6')
		:returns:	TRUE if the address is valid, FALSE if not
		:rtype:	bool

		判断一个 IP 地址是否有效，返回 TRUE/FALSE 。

		.. note:: 上面的 $this->input->ip_address() 方法会自动验证 IP 地址的有效性。

		::

			if ( ! $this->input->valid_ip($ip))
			{
				echo 'Not Valid';
			}
			else
			{
				echo 'Valid';
			}

		第二个参数可选，可以是字符串 'ipv4' 或 'ipv6' 用于指定 IP 的格式，默认两种格式都会检查。

	.. php:method:: user_agent([$xss_clean = NULL])

		:returns:	User agent string or NULL if not set
		:param	bool	$xss_clean: Whether to apply XSS filtering
		:rtype:	mixed

		返回当前用户的用户代理字符串（Web 浏览器），如果不可用则返回 FALSE 。
		::

			echo $this->input->user_agent();

		关于用户代理的相关方法请参考 :doc:`用户代理类 <user_agent>` 。

	.. php:method:: request_headers([$xss_clean = FALSE])

		:param	bool	$xss_clean: Whether to apply XSS filtering
		:returns:	An array of HTTP request headers
		:rtype:	array

		返回 HTTP 请求头数组。当在非 Apache 环境下运行时，
		`apache_request_headers() <http://php.net/apache_request_headers>`_ 函数不可用，
		这个方法将很有用。
		::

			$headers = $this->input->request_headers();

	.. php:method:: get_request_header($index[, $xss_clean = FALSE])

		:param	string	$index: HTTP request header name
		:param	bool	$xss_clean: Whether to apply XSS filtering
		:returns:	An HTTP request header or NULL if not found
		:rtype:	string

		返回某个指定的 HTTP 请求头，如果不存在，则返回 NULL 。
		::

			$this->input->get_request_header('some-header', TRUE);

	.. php:method:: is_ajax_request()

		:returns:	TRUE if it is an Ajax request, FALSE if not
		:rtype:	bool

		检查服务器头中是否含有 HTTP_X_REQUESTED_WITH ，如果有返回 TRUE ，否则返回 FALSE 。

	.. php:method:: is_cli_request()

		:returns:	TRUE if it is a CLI request, FALSE if not
		:rtype:	bool

		检查程序是否从命令行界面运行。

		.. note:: 该方法检查当前正在使用的 PHP SAPI 名称，同时检查是否定义了 ``STDIN`` 常量，
			来判断当前 PHP 是否从命令行运行。

		::

			$this->input->is_cli_request()

		.. note:: 该方法已经被废弃，现在只是 :func:`is_cli()` 函数的一个别名而已。

	.. php:method:: method([$upper = FALSE])

		:param	bool	$upper: Whether to return the request method name in upper or lower case
		:returns:	HTTP request method
		:rtype:	string

		返回 ``$_SERVER['REQUEST_METHOD']`` 的值，它有一个参数用于设置返回大写还是小写。
		::

			echo $this->input->method(TRUE); // Outputs: POST
			echo $this->input->method(FALSE); // Outputs: post
			echo $this->input->method(); // Outputs: post
