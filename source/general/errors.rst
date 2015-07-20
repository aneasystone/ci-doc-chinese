##############
错误处理
##############

CodeIgniter 可以通过下面介绍的方法来在你的应用程序中生成错误报告。
另外，还有一个错误日志类用来将错误或调试信息保存到文本文件中。

.. note:: CodeIgniter 默认将显示所有的 PHP 错误，你可能在开发结束之后
	改变该行为。在你的 index.php 文件的顶部有一个 error_reporting()
	函数，通过它可以修改错误设置。当发生错误时，禁用错误报告
	并不会阻止向日志文件写入错误信息。

和 CodeIgniter 中的大多数系统不同，错误函数是一个可以在整个应用程序中
使用的简单接口，这让你在使用该函数时不用担心类或方法的作用域的问题。

当任何一处核心代码调用 ``exit()`` 时，CodeIgniter 会返回一个状态码。
这个状态码和 HTTP 状态码不同，是用来通知其他程序 PHP 脚本是否成功运行的，
如果运行不成功，又是什么原因导致了脚本退出。状态码的值被定义在
*application/config/constants.php* 文件中。状态码在 CLI 形式下非常有用，
可以帮助你的服务器跟踪并监控你的脚本。

下面的函数用于生成错误信息：

.. php:function:: show_error($message, $status_code, $heading = 'An Error Was Encountered')

	:param	mixed	$message: Error message
	:param	int	$status_code: HTTP Response status code
	:param	string	$heading: Error page heading
	:rtype:	void

	该函数使用下面的错误模板来显示错误信息::

		application/views/errors/html/error_general.php

	或:

		application/views/errors/cli/error_general.php

	可选参数 ``$status_code`` 将决定发送什么 HTTP 状态码。
	如果 ``$status_code`` 小于 100，HTTP 状态码将被置为 500 ，
	退出状态码将被置为 ``$status_code + EXIT__AUTO_MIN`` 。
	如果它的值大于 ``EXIT__AUTO_MAX`` 或者如果 ``$status_code`` 
	大于等于 100 ，退出状态码将被置为 ``EXIT_ERROR`` 。
	详情可查看 *application/config/constants.php* 文件。

.. php:function:: show_404($page = '', $log_error = TRUE)

	:param	string	$page: URI string
	:param	bool	$log_error: Whether to log the error
	:rtype:	void

	该函数使用下面的错误模板来显示 404 错误信息::

		application/views/errors/html/error_404.php

	或:

		application/views/errors/cli/error_404.php

	传递给该函数的字符串代表的是找不到的文件路径。退出状态码
	将设置为 ``EXIT_UNKNOWN_FILE`` 。
	注意如果找不到控制器 CodeIgniter 将自动显示 404 错误信息。

	默认 CodeIgniter 会自动将 ``show_404()`` 函数调用记录到错误日志中。
	将第二个参数设置为 FALSE 将跳过记录日志。

.. php:function:: log_message($level, $message, $php_error = FALSE)

	:param	string	$level: Log level: 'error', 'debug' or 'info'
	:param	string	$message: Message to log
	:param	bool	$php_error: Whether we're logging a native PHP error message
	:rtype:	void

	该函数用于向你的日志文件中写入信息，第一个参数你必须提供
	三个信息级别中的一个，用于指定记录的是什么类型的信息（调试，
	错误和一般信息），第二个参数为信息本身。

	示例::

		if ($some_var == '')
		{
			log_message('error', 'Some variable did not contain a value.');
		}
		else
		{
			log_message('debug', 'Some variable was correctly set');
		}

		log_message('info', 'The purpose of some variable is to provide some value.');

	有三种信息类型：

	#. 错误信息。这些是真正的错误，譬如 PHP 错误或用户错误。
	#. 调试信息。这些信息帮助你调试程序，譬如，你可以在一个类
	   初始化的地方记录下来作为调试信息。
	#. 一般信息。这些是最低级别的信息，简单的给出程序运行过程中的一些信息。

	.. note:: 为了保证日志文件被正确写入，*logs/* 目录必须设置为可写的。
		此外，你必须要设置 *application/config/config.php* 文件中的
		"threshold"  参数，举个例子，譬如你只想记录错误信息，而不想
		记录另外两种类型的信息，可以通过这个参数来控制。如果你将
		该参数设置为 0 ，日志就相当于被禁用了。
