################
公共函数
################

CodeIgniter 定义了一些全局的函数，你可以在任何地方使用它们，并且不需要加载任何
类库或辅助库。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

.. php:function:: is_php($version)

	:param	string	$version: Version number
	:returns:	TRUE if the running PHP version is at least the one specified or FALSE if not
	:rtype:	bool

	判断当前运行的 PHP 版本是否高于或等于你提供的版本号。

	例如::

		if (is_php('5.3'))
		{
			$str = quoted_printable_encode($str);
		}

	如果当前运行的 PHP 版本等于或高于提供的版本号，该函数返回布尔值 TRUE ，反之则返回 FALSE 。

.. php:function:: is_really_writable($file)

	:param	string	$file: File path
	:returns:	TRUE if the path is writable, FALSE if not
	:rtype:	bool

	在 Windows 服务器上只有当文件标志了只读属性时，PHP 的 ``is_writable()`` 函数才返回 FALSE ，
	其他情况都是返回 TRUE ，即使文件不是真的可写也返回 TRUE 。

	这个函数首先尝试写入该文件，以此来判断该文件是不是真的可写。通常只在 ``is_writable()`` 函数
	返回的结果不准确的平台下才推荐使用该函数。

	例如::

		if (is_really_writable('file.txt'))
		{
			echo "I could write to this if I wanted to";
		}
		else
		{
			echo "File is not writable";
		}

	.. note:: 更多信息，参看 `PHP bug #54709 <https://bugs.php.net/bug.php?id=54709>`_ 。

.. php:function:: config_item($key)

	:param	string	$key: Config item key
	:returns:	Configuration key value or NULL if not found
	:rtype:	mixed

	访问配置信息最好的方式是使用 :doc:`配置类 <../libraries/config>` ，但是，你也可以通过 
	``config_item()`` 函数来访问单个配置项，更多信息，参看 :doc:`配置类 <../libraries/config>`

.. :noindex: function:: show_error($message, $status_code[, $heading = 'An Error Was Encountered'])

	:param	mixed	$message: Error message
	:param	int	$status_code: HTTP Response status code
	:param	string	$heading: Error page heading
	:rtype:	void

	这个函数直接调用 ``CI_Exception::show_error()`` 方法。更多信息，请查看 :doc:`错误处理 <errors>` 文档。

.. :noindex: function:: show_404([$page = ''[, $log_error = TRUE]])

	:param	string	$page: URI string
	:param	bool	$log_error: Whether to log the error
	:rtype:	void

	这个函数直接调用 ``CI_Exception::show_404()`` 方法。更多信息，请查看 :doc:`错误处理 <errors>` 文档。

.. :noindex: function:: log_message($level, $message)

	:param	string	$level: Log level: 'error', 'debug' or 'info'
	:param	string	$message: Message to log
	:rtype:	void

	这个函数直接调用 ``CI_Log::write_log()`` 方法。更多信息，请查看 :doc:`错误处理 <errors>` 文档。

.. php:function:: set_status_header($code[, $text = ''])

	:param	int	$code: HTTP Reponse status code
	:param	string	$text: A custom message to set with the status code
	:rtype:	void

	用于手动设置服务器的 HTTP 状态码，譬如::

		set_status_header(401);
		// Sets the header as:  Unauthorized

	`查看这里 <http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html>`_ 有一份状态码的完整清单。

.. php:function:: remove_invisible_characters($str[, $url_encoded = TRUE])

	:param	string	$str: Input string
	:param	bool	$url_encoded: Whether to remove URL-encoded characters as well
	:returns:	Sanitized string
	:rtype:	string

	这个函数防止在 ASCII 字符串中插入空字符，例如：Java\\0script 。

	举例::

		remove_invisible_characters('Java\\0script');
		// Returns: 'Javascript'

.. php:function:: html_escape($var)

	:param	mixed	$var: Variable to escape (string or array)
	:returns:	HTML escaped string(s)
	:rtype:	mixed

	这个函数类似于 PHP 原生的 ``htmlspecialchars()`` 函数，只是它除了可以接受字符串参数外，还可以接受数组参数。

	它在防止 XSS 攻击时很有用。

.. php:function:: get_mimes()

	:returns:	An associative array of file types
	:rtype:	array

	这个函数返回 *application/config/mimes.php* 文件中定义的 MIME 数组的 **引用** 。

.. php:function:: is_https()

	:returns:	TRUE if currently using HTTP-over-SSL, FALSE if not
	:rtype:	bool

	该函数在使用 HTTPS 安全连接时返回 TRUE ，没有使用 HTTPS（包括非 HTTP 的请求）则返回 FALSE 。

.. php:function:: is_cli()

	:returns:	TRUE if currently running under CLI, FALSE otherwise
	:rtype:	bool

	当程序在命令行下运行时返回 TRUE ，反之返回 FALSE 。

	.. note:: 该函数会检查 ``PHP_SAPI`` 的值是否是 'cli' ，或者是否定义了 ``STDIN`` 常量。

.. php:function:: function_usable($function_name)

	:param	string	$function_name: Function name
	:returns:	TRUE if the function can be used, FALSE if not
	:rtype:	bool

	检查一个函数是否可用，可用返回 TRUE ，否则返回 FALSE 。

	This function runs a ``function_exists()`` check and if the
	`Suhosin extension <http://www.hardened-php.net/suhosin/>` is loaded,
	checks if it doesn't disable the function being checked.

	It is useful if you want to check for the availability of functions
	such as ``eval()`` and ``exec()``, which are dangerous and might be
	disabled on servers with highly restrictive security policies.

	.. note:: This function was introduced because Suhosin terminated
		script execution, but this turned out to be a bug. A fix
		has been available for some time (version 0.9.34), but is
		unfortunately not released yet.