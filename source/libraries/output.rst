############
输出类
############

输出类是个核心类，它的功能只有一个：发送 Web 页面内容到请求的浏览器。
如果你开启缓存，它也负责 :doc:`缓存 <../general/caching>` 你的 Web 页面。

.. note:: 这个类由系统自动加载，你无需手工加载。

在一般情况下，你可能根本就不会注意到输出类，因为它无需你的干涉，
对你来说完全是透明的。例如，当你使用 :doc:`加载器 <../libraries/loader>`
加载一个视图文件时，它会自动传入到输出类，并在系统执行的最后由
CodeIgniter 自动调用。尽管如此，在你需要时，你还是可以对输出进行手工处理。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

***************
类参考
***************

.. php:class:: CI_Output

	.. attribute:: $parse_exec_vars = TRUE;

		启用或禁用解析伪变量（{elapsed_time} 和 {memory_usage}）。

		CodeIgniter 默认会在输出类中解析这些变量。要禁用它，可以在你的控制器中设置
		这个属性为 FALSE 。
		::

			$this->output->parse_exec_vars = FALSE;

	.. php:method:: set_output($output)

		:param	string	$output: String to set the output to
		:returns:	CI_Output instance (method chaining)
		:rtype:	CI_Output

		允许你手工设置最终的输出字符串。使用示例::

			$this->output->set_output($data);

		.. important:: 如果你手工设置输出，这必须放在方法的最后一步。例如，
			如果你正在某个控制器的方法中构造页面，将 set_output 
			这句代码放在方法的最后。

	.. php:method:: set_content_type($mime_type[, $charset = NULL])

		:param	string	$mime_type: MIME Type idenitifer string
		:param	string	$charset: Character set
		:returns:	CI_Output instance (method chaining)
		:rtype:	CI_Output

		允许你设置你的页面的 MIME 类型，可以很方便的提供 JSON 数据、JPEG、XML 等等格式。
		::

			$this->output
				->set_content_type('application/json')
				->set_output(json_encode(array('foo' => 'bar')));

			$this->output
				->set_content_type('jpeg') // You could also use ".jpeg" which will have the full stop removed before looking in config/mimes.php
				->set_output(file_get_contents('files/something.jpg'));

		.. important:: 确保你传入到这个方法的 MIME 类型在 *application/config/mimes.php* 
			文件中能找到，要不然方法不起任何作用。

		你也可以通过第二个参数设置文档的字符集。

			$this->output->set_content_type('css', 'utf-8');

	.. php:method:: get_content_type()

		:returns:	Content-Type string
		:rtype:	string

		获取当前正在使用的 HTTP 头 Content-Type ，不包含字符集部分。
		::

			$mime = $this->output->get_content_type();

		.. note::  如果 Content-Type 没有设置，默认返回 'text/html' 。

	.. php:method:: get_header($header)

		:param	string	$header: HTTP header name
		:returns:	HTTP response header or NULL if not found
		:rtype:	mixed

		返回请求的 HTTP 头，如果 HTTP 头还没设置，返回 NULL 。
		例如::

			$this->output->set_content_type('text/plain', 'UTF-8');
			echo $this->output->get_header('content-type');
			// Outputs: text/plain; charset=utf-8

		.. note:: HTTP 头名称是不区分大小写的。

		.. note:: 返回结果中也包括通过 PHP 原生的 ``header()`` 函数发送的原始 HTTP 头。

	.. php:method:: get_output()

		:returns:	Output string
		:rtype:	string

		允许你手工获取存储在输出类中的待发送的内容。使用示例::

			$string = $this->output->get_output();

		注意，只有通过 CodeIgniter 输出类的某个方法设置过的数据，例如 
		``$this->load->view()`` 方法，才可以使用该方法获取到。

	.. php:method:: append_output($output)

		:param	string	$output: Additional output data to append
		:returns:	CI_Output instance (method chaining)
		:rtype:	CI_Output

		向输出字符串附加数据。
		::

			$this->output->append_output($data);

	.. php:method:: set_header($header[, $replace = TRUE])

		:param	string	$header: HTTP response header
		:param	bool	$replace: Whether to replace the old header value, if it is already set
		:returns:	CI_Output instance (method chaining)
		:rtype:	CI_Output

		允许你手工设置服务器的 HTTP 头，输出类将在最终显示页面时发送它。例如::

			$this->output->set_header('HTTP/1.0 200 OK');
			$this->output->set_header('HTTP/1.1 200 OK');
			$this->output->set_header('Last-Modified: '.gmdate('D, d M Y H:i:s', $last_update).' GMT');
			$this->output->set_header('Cache-Control: no-store, no-cache, must-revalidate');
			$this->output->set_header('Cache-Control: post-check=0, pre-check=0');
			$this->output->set_header('Pragma: no-cache');

	.. php:method:: set_status_header([$code = 200[, $text = '']])

		:param	int	$code: HTTP status code
		:param	string	$text: Optional message
		:returns:	CI_Output instance (method chaining)
		:rtype:	CI_Output

		允许你手工设置服务器的 HTTP 状态码。例如::

			$this->output->set_status_header('401');
			// Sets the header as:  Unauthorized

		`阅读这里 <http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html>`_ 得到一份完整的 HTTP 状态码列表。

		.. note:: 这个方法是 :doc:`通用方法 <../general/common_functions>` 中的
			:func:`set_status_header()` 的别名。

	.. php:method:: enable_profiler([$val = TRUE])

		:param	bool	$val: Whether to enable or disable the Profiler
		:returns:	CI_Output instance (method chaining)
		:rtype:	CI_Output

		允许你启用或禁用 :doc:`程序分析器 <../general/profiling>` ，它可以在你的页面底部显示
		基准测试的结果或其他一些数据帮助你调试和优化程序。

		要启用分析器，将下面这行代码放到你的 :doc:`控制器 <../general/controllers>` 
		方法的任何位置::

			$this->output->enable_profiler(TRUE);

		当启用它时，将生成一份报告并插入到你的页面的最底部。

		要禁用分析器，你可以这样::

			$this->output->enable_profiler(FALSE);

	.. php:method:: set_profiler_sections($sections)

		:param	array	$sections: Profiler sections
		:returns:	CI_Output instance (method chaining)
		:rtype:	CI_Output

		当程序分析器启用时，该方法允许你启用或禁用程序分析器的特定字段。
		请参考 :doc:`程序分析器 <../general/profiling>` 文档获取详细信息。

	.. php:method:: cache($time)

		:param	int	$time: Cache expiration time in seconds
		:returns:	CI_Output instance (method chaining)
		:rtype:	CI_Output

		将当前页面缓存一段时间。

		更多信息，请阅读 :doc:`文档缓存 <../general/caching>` 。

	.. php:method:: _display([$output = ''])

		:param	string	$output: Output data override
		:returns:	void
		:rtype:	void

		发送最终输出结果以及服务器的 HTTP 头到浏览器，同时它也会停止基准测试的计时器。

		.. note:: 这个方法会在脚本执行的最后自动被调用，你无需手工调用它。
			除非你在你的代码中使用了 ``exit()`` 或 ``die()`` 结束了脚本执行。
		
		例如::

			$response = array('status' => 'OK');

			$this->output
				->set_status_header(200)
				->set_content_type('application/json', 'utf-8')
				->set_output(json_encode($response, JSON_PRETTY_PRINT | JSON_UNESCAPED_UNICODE | JSON_UNESCAPED_SLASHES))
				->_display();
			exit;

		.. note:: 手工调用该方法而不结束脚本的执行，会导致重复输出结果。