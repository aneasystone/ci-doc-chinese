#############
Cookie 辅助库
#############

Cookie 辅助库文件包含了一些帮助你处理 Cookie 的函数。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

加载辅助库
===================

该辅助库通过下面的代码加载::

	$this->load->helper('cookie');

可用函数
===================

该辅助库有下列可用函数：

.. php:function:: set_cookie($name[, $value = ''[, $expire = ''[, $domain = ''[, $path = '/'[, $prefix = ''[, $secure = FALSE[, $httponly = FALSE]]]]]]]])

	:param	mixed	$name: Cookie name *or* associative array of all of the parameters available to this function
	:param	string	$value: Cookie value
	:param	int	$expire: Number of seconds until expiration
	:param	string	$domain: Cookie domain (usually: .yourdomain.com)
	:param	string	$path: Cookie path
	:param	string	$prefix: Cookie name prefix
	:param	bool	$secure: Whether to only send the cookie through HTTPS
	:param	bool	$httponly: Whether to hide the cookie from JavaScript
	:rtype:	void

	该辅助函数提供给你一种更友好的语法来设置浏览器 Cookie，参考
	:doc:`输入类 <../libraries/input>` 获取它的详细用法，另外，它是
	``CI_Input::set_cookie()`` 函数的别名。

.. php:function:: get_cookie($index[, $xss_clean = NULL]])

	:param	string	$index: Cookie name
	:param	bool	$xss_clean: Whether to apply XSS filtering to the returned value
	:returns:	The cookie value or NULL if not found
	:rtype:	mixed

	该辅助函数提供给你一种更友好的语法来获取浏览器 Cookie，参考
	:doc:`输入类 <../libraries/input>` 获取它的详细用法，同时，这个函数
	和 ``CI_Input::cookie()`` 函数非常类似，只是它会根据配置文件
	*application/config/config.php* 中的 ``$config['cookie_prefix']`` 参数
	来作为 Cookie 的前缀。

.. php:function:: delete_cookie($name[, $domain = ''[, $path = '/'[, $prefix = '']]]])

	:param	string	$name: Cookie name
	:param	string	$domain: Cookie domain (usually: .yourdomain.com)
	:param	string	$path: Cookie path
	:param	string	$prefix: Cookie name prefix
	:rtype:	void

	删除一条 Cookie，只需要传入 Cookie 名即可，也可以设置路径或其他参数
	来删除特定 Cookie。
	::

		delete_cookie('name');

	这个函数和 ``set_cookie()`` 比较类似，只是它并不提供 Cookie 的值和
	过期时间等参数。第一个参数也可以是个数组，包含多个要删除的 Cookie 。
	另外，你也可以像下面这样删除特定条件的 Cookie 。
	::

		delete_cookie($name, $domain, $path, $prefix);
