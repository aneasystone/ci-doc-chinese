##########
URL 辅助库
##########

URL 辅助库文件包含了一些帮助你处理 URL 的函数。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

加载辅助库
===================

该辅助库通过下面的代码加载::

	$this->load->helper('url');

可用函数
===================

该辅助库有下列可用函数：

.. php:function:: site_url([$uri = ''[, $protocol = NULL]])

	:param	string	$uri: URI string
	:param	string	$protocol: Protocol, e.g. 'http' or 'https'
	:returns:	Site URL
	:rtype:	string

	根据配置文件返回你的站点 URL 。index.php （获取其他你在配置文件中设置的 **index_page** 参数）
	将会包含在你的 URL 中，另外再加上你传给函数的 URI 参数，以及配置文件中设置的 **url_suffix** 参数。

	推荐在任何时候都使用这种方法来生成你的 URL ，这样在你的 URL 变动时你的代码将具有可移植性。

	传给函数的 URI 段参数可以是一个字符串，也可以是个数组，下面是字符串的例子::

		echo site_url('news/local/123');

	上例将返回类似于：*http://example.com/index.php/news/local/123*	

	下面是使用数组的例子::

		$segments = array('news', 'local', '123');
		echo site_url($segments);

	该函数是 ``CI_Config::site_url()`` 的别名，更多信息请查阅 :doc:`配置类 <../libraries/config>` 文档。

.. php:function:: base_url($uri = '', $protocol = NULL)

	:param	string	$uri: URI string
	:param	string	$protocol: Protocol, e.g. 'http' or 'https'
	:returns:	Base URL
	:rtype:	string

	根据配置文件返回你站点的根 URL ，例如::

		echo base_url();

	该函数和 :php:func:`site_url()` 函数相同，只是不会在 URL 的后面加上 *index_page* 或 *url_suffix* 。

	另外，和 :php:func:`site_url()` 一样的是，你也可以使用字符串或数组格式的 URI 段。下面是字符串的例子::

		echo base_url("blog/post/123");

	上例将返回类似于：*http://example.com/blog/post/123*

	跟 :php:func:`site_url()` 函数不一样的是，你可以指定一个文件路径（例如图片或样式文件），这将很有用，例如::

		echo base_url("images/icons/edit.png");

	将返回类似于：*http://example.com/images/icons/edit.png*

	该函数是 ``CI_Config::base_url()`` 的别名，更多信息请查阅 :doc:`配置类 <../libraries/config>` 文档。

.. php:function:: current_url()

	:returns:	The current URL
	:rtype:	string

	返回当前正在浏览的页面的完整 URL （包括分段）。

	.. note:: 该函数和调用下面的代码效果是一样的：
		|
		| site_url(uri_string());


.. php:function:: uri_string()

	:returns:	An URI string
	:rtype:	string

	返回包含该函数的页面的 URI 分段。例如，如果你的 URL 是::

		http://some-site.com/blog/comments/123

	函数将返回::

		blog/comments/123

	该函数是 ``CI_Config::uri_string()`` 的别名，更多信息请查阅 :doc:`配置类 <../libraries/config>` 文档。


.. php:function:: index_page()

	:returns:	'index_page' value
	:rtype:	mixed

	返回你在配置文件中配置的 **index_page** 参数，例如::

		echo index_page();

.. php:function:: anchor($uri = '', $title = '', $attributes = '')

	:param	string	$uri: URI string
	:param	string	$title: Anchor title
	:param	mixed	$attributes: HTML attributes
	:returns:	HTML hyperlink (anchor tag)
	:rtype:	string

	根据你提供的 URL 生成一个标准的 HTML 链接。

	第一个参数可以包含任何你想添加到 URL 上的段，和上面的 :php:func:`site_url()` 函数一样，URL 
	的段可以是字符串或数组。

	.. note:: 如果你创建的链接是指向你自己的应用程序，那么不用包含根 URL （http&#58;//...）。
		这个会根据你的配置文件自动添加到 URL 前面。所以你只需指定要添加的 URL 段就可以了。

	第二个参数是链接的文本，如果留空，将使用链接本身作为文本。

	第三个参数为你希望添加到链接的属性，可以是一个字符串，也可以是个关联数组。

	这里是一些例子::

		echo anchor('news/local/123', 'My News', 'title="News title"');
		// Prints: <a href="http://example.com/index.php/news/local/123" title="News title">My News</a>

		echo anchor('news/local/123', 'My News', array('title' => 'The best news!'));
		// Prints: <a href="http://example.com/index.php/news/local/123" title="The best news!">My News</a>

		echo anchor('', 'Click here');
		// Prints: <a href="http://example.com">Click Here</a>


.. php:function:: anchor_popup($uri = '', $title = '', $attributes = FALSE)

	:param	string	$uri: URI string
	:param	string	$title: Anchor title
	:param	mixed	$attributes: HTML attributes
	:returns:	Pop-up hyperlink
	:rtype:	string

	和 :php:func:`anchor()` 函数非常类似，只是它生成的 URL 将会在新窗口被打开。你可以通过第三个参数指定
	JavaScript 的窗口属性，以此来控制窗口将如何被打开。如果没有设置第三个参数，将会使用你的浏览器设置打开
	一个新窗口。

	这里是属性的例子::

		$atts = array(
			'width'       => 800,
			'height'      => 600,
			'scrollbars'  => 'yes',
			'status'      => 'yes',
			'resizable'   => 'yes',
			'screenx'     => 0,
			'screeny'     => 0,
			'window_name' => '_blank'
		);

		echo anchor_popup('news/local/123', 'Click Me!', $atts);

	.. note:: 上面的属性是函数的默认值，所以你只需要设置和你想要的不一样的参数。如果想使用所有默认的参数，
		只要简单的传一个空数组即可：
		|
		| echo anchor_popup('news/local/123', 'Click Me!', array());

	.. note:: **window_name** 其实并不算一个属性，而是 Javascript 的
		`window.open() <http://www.w3schools.com/jsref/met_win_open.asp>` 函数的一个参数而已，
		该函数接受一个窗口名称或一个 window 对象。

	.. note:: 任何不同于上面列出来的其他的属性将会作为 HTML 链接的属性。


.. php:function:: mailto($email, $title = '', $attributes = '')

	:param	string	$email: E-mail address
	:param	string	$title: Anchor title
	:param	mixed	$attributes: HTML attributes
	:returns:	A "mail to" hyperlink
	:rtype:	string

	创建一个标准的 HTML e-mail 链接。例如::

		echo mailto('me@my-site.com', 'Click Here to Contact Me');

	和上面的 :php:func:`anchor()` 函数一样，你可以通过第三个参数设置属性::

		$attributes = array('title' => 'Mail me');
		echo mailto('me@my-site.com', 'Contact Me', $attributes);

.. php:function:: safe_mailto($email, $title = '', $attributes = '')

	:param	string	$email: E-mail address
	:param	string	$title: Anchor title
	:param	mixed	$attributes: HTML attributes
	:returns:	A spam-safe "mail to" hyperlink
	:rtype:	string

	和 :php:func:`mailto()` 函数一样，但是它的 *mailto* 标签使用了一个混淆的写法，
	可以防止你的 e-mail 地址被垃圾邮件机器人爬到。

.. php:function:: auto_link($str, $type = 'both', $popup = FALSE)

	:param	string	$str: Input string
	:param	string	$type: Link type ('email', 'url' or 'both')
	:param	bool	$popup: Whether to create popup links
	:returns:	Linkified string
	:rtype:	string

	将一个字符串中的 URL 和 e-mail 地址自动转换为链接，例如::

		$string = auto_link($string);

	第二个参数用于决定是转换 URL 还是 e-mail 地址，默认情况不指定该参数，两者都会被转换。
	E-mail 地址的链接是使用上面介绍的 :php:func:`safe_mailto()` 函数生成的。

	只转换 URL ::

		$string = auto_link($string, 'url');

	只转换 e-mail 地址::

		$string = auto_link($string, 'email');

	第三个参数用于指定链接是否要在新窗口打开。可以是布尔值 TRUE 或 FALSE ::

		$string = auto_link($string, 'both', TRUE);


.. php:function:: url_title($str, $separator = '-', $lowercase = FALSE)

	:param	string	$str: Input string
	:param	string	$separator: Word separator
	:param	string	$lowercase: Whether to transform the output string to lower-case
	:returns:	URL-formatted string
	:rtype:	string

	将字符串转换为对人类友好的 URL 字符串格式。例如，如果你有一个博客，你希望使用博客的标题作为 URL ，
	这时该函数很有用。例如::

		$title = "What's wrong with CSS?";
		$url_title = url_title($title);
		// Produces: Whats-wrong-with-CSS

	第二个参数指定分隔符，默认使用连字符。一般的选择有：**-** （连字符） 或者 **_** （下划线）

	例如::

		$title = "What's wrong with CSS?";
		$url_title = url_title($title, 'underscore');
		// Produces: Whats_wrong_with_CSS

	.. note:: 第二个参数连字符和下划线的老的用法已经废弃。

	第三个参数指定是否强制转换为小写。默认不会，参数类型为布尔值 TRUE 或 FALSE 。

	例如::

		$title = "What's wrong with CSS?";
		$url_title = url_title($title, 'underscore', TRUE);
		// Produces: whats_wrong_with_css


.. php:function:: prep_url($str = '')

	:param	string	$str: URL string
	:returns:	Protocol-prefixed URL string
	:rtype:	string

	当 URL 中缺少协议前缀部分时，使用该函数将会向 URL 中添加 http&#58;// 。

	像下面这样使用该函数::

		$url = prep_url('example.com');


.. php:function:: redirect($uri = '', $method = 'auto', $code = NULL)

	:param	string	$uri: URI string
	:param	string	$method: Redirect method ('auto', 'location' or 'refresh')
	:param	string	$code: HTTP Response code (usually 302 or 303)
	:rtype:	void

	通过 HTTP 头重定向到指定 URL 。你可以指定一个完整的 URL ，也可以指定一个 URL 段，
	该函数会根据配置文件自动生成改 URL 。

	第二个参数用于指定一种重定向方法。可用的方法有：**auto** 、 **location** 和 **refresh** 。
	location 方法速度快，但是在 ISS 服务器上不可靠。默认值为 **auto** ，它会根据你的服务器环境
	智能的选择使用哪种方法。

	第三个参数可选，允许你发送一个指定的 HTTP 状态码，这个可以用来为搜索引擎创建 301 重定向。
	默认的状态码为 302 ，该参数只适用于 **location** 重定向方法，对于 *refresh* 方法无效。例如::

		if ($logged_in == FALSE)
		{      
			redirect('/login/form/');
		}

		// with 301 redirect
		redirect('/article/13', 'location', 301);

	.. note:: 为了让该函数有效，它必须在任何内容输出到浏览器之前被调用。因为输出内容会使用服务器 HTTP 头。

	.. note:: 为了更好的控制服务器头，你应该使用 `输出类 </libraries/output>` 的 ``set_header()`` 方法。

	.. note:: 使用 IIS 的用户要注意，如果你隐藏了 `Server` 这个 HTTP 头， *auto* 方法将无法检测到 IIS 。
		在这种情况下，推荐你使用 **refresh** 方法。

	.. note:: 当使用 HTTP/1.1 的 POST 来访问你的页面时，如果你使用的是 **location** 方法，会自动使用 HTTP 303 状态码。

	.. important:: 该函数会终止脚本的执行。