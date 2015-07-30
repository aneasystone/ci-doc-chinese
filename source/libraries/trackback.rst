###############
引用通告类
###############

引用通告类提供了一些方法用于发送和接受引用通告数据。

如果你还不知道什么是引用通告，可以在 
`这里 <http://en.wikipedia.org/wiki/Trackback>`_ 找到更多信息。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

*************************
使用引用通告类
*************************

初始化类
======================

正如 CodeIgniter 中的其他类一样，在你的控制器中使用 ``$this->load->library()``
方法来初始化引用通告类::

	$this->load->library('trackback');

初始化之后，引用通告类的对象就可以这样访问::

	$this->trackback

发送引用通告
==================

可以在你的控制器的任何方法中使用类似于如下代码来发送引用通告::

	$this->load->library('trackback');

	$tb_data = array(
		'ping_url'  => 'http://example.com/trackback/456',
		'url'       => 'http://www.my-example.com/blog/entry/123',
		'title'     => 'The Title of My Entry',
		'excerpt'   => 'The entry content.',
		'blog_name' => 'My Blog Name',
		'charset'   => 'utf-8'
	);

	if ( ! $this->trackback->send($tb_data))
	{
		echo $this->trackback->display_errors();
	}
	else
	{
		echo 'Trackback was sent!';
	}

数组中每一项的解释：

-  **ping_url** - 你想发送引用通告到该站点的 URL ，你可以同时向发送多个 URL 发送，多个 URL 之间使用逗号分割
-  **url** - 对应的是你的博客的 URL
-  **title** - 你的博客标题
-  **excerpt** - 你的博客内容（摘要）
-  **blog_name** - 你的博客的名称
-  **charset** - 你的博客所使用的字符编码，如果忽略，默认使用 UTF-8

.. note:: 引用通告类会自动发送你的博客的前 500 个字符，同时它也会去除所有的 HTML 标签。

发送引用通告的方法会根据成功或失败返回 TRUE 或 FALSE ，如果失败，可以使用下面的代码获取错误信息::

	$this->trackback->display_errors();

接受引用通告
====================

在接受引用通告之前，你必须先创建一个博客，如果你还没有博客，
那么接下来的内容对你来说就没什么意义。

接受引用通告比发送要复杂一点，这是因为你需要一个数据库表来保存它们，
而且你还需要对接受到的引用通告数据进行验证。我们鼓励你实现一个完整的验证过程，
来防止垃圾信息和重复数据。你可能还希望限制一段时间内从某个 IP 发送过来的引用通告的数量，
以此减少垃圾信息。接受引用通告的过程很简单，验证才是难点。

你的 Ping URL
=============

为了接受引用通告，你必须在你的每篇博客旁边显示一个引用通告 URL ，
人们使用这个 URL 来向你发送引用通告（我们称其为 Ping URL）。

你的 Ping URL 必须指向一个控制器方法，在该方法中写接受引用通告的代码，而且该 URL
必须包含你博客的 ID ，这样当接受到引用通告时你就可以知道是针对哪篇博客的。

例如，假设你的控制器类叫 Trackback ，接受方法叫 receive ，你的 Ping URL
将类似于下面这样::

	http://example.com/index.php/trackback/receive/entry_id

其中，entry_id 代表你每篇博客的 ID 。

新建 Trackback 表
==========================

在接受引用通告之前，你必须创建一个数据库表来储存它。下面是表的一个基本原型::

	CREATE TABLE trackbacks (
		tb_id int(10) unsigned NOT NULL auto_increment,
		entry_id int(10) unsigned NOT NULL default 0,
		url varchar(200) NOT NULL,
		title varchar(100) NOT NULL,
		excerpt text NOT NULL,
		blog_name varchar(100) NOT NULL,
		tb_date int(10) NOT NULL,
		ip_address varchar(45) NOT NULL,
		PRIMARY KEY `tb_id` (`tb_id`),
		KEY `entry_id` (`entry_id`)
	);

在引用通告的规范中只有四项信息是发送一个引用通告所必须的：url、title、excerpt 和 blog_name 。
但为了让数据更有用，我们还在表中添加了几个其他的字段（date、ip_address 等）。

处理引用通告
======================

下面是一个如何接受并处理引用通告的例子。下面的代码将放在你的接受引用通告的控制器方法中::

	$this->load->library('trackback');
	$this->load->database();

	if ($this->uri->segment(3) == FALSE)
	{
		$this->trackback->send_error('Unable to determine the entry ID');
	}

	if ( ! $this->trackback->receive())
	{
		$this->trackback->send_error('The Trackback did not contain valid data');
	}

	$data = array(
		'tb_id'      => '',
		'entry_id'   => $this->uri->segment(3),
		'url'        => $this->trackback->data('url'),
		'title'      => $this->trackback->data('title'),
		'excerpt'    => $this->trackback->data('excerpt'),
		'blog_name'  => $this->trackback->data('blog_name'),
		'tb_date'    => time(),
		'ip_address' => $this->input->ip_address()
	);

	$sql = $this->db->insert_string('trackbacks', $data);
	$this->db->query($sql);

	$this->trackback->send_success();

说明
^^^^^^

entry_id 将从你的 URL 的第三段获取，这是基于我们之前例子中的 URL::

	http://example.com/index.php/trackback/receive/entry_id

注意 entry_id 是第三段，你可以这样获取::

	$this->uri->segment(3);

在我们上面的接受引用通告的代码中，如果第三段为空，我们将发送一个错误信息。
如果没有有效的 entry_id ，没必要继续处理下去。

$this->trackback->receive() 是个简单的验证方法，它检查接受到的数据并确保包含了
我们所需的四种信息：url、title、excerpt 和 blog_name 。该方法成功返回 TRUE ，
失败返回 FALSE 。如果失败，也发送一个错误信息。

接受到的引用通告数据可以通过下面的方法来获取::

	$this->trackback->data('item')

其中，item 代表四种信息中的一种：url、title、excerpt 和 blog_name 。

如果引用通告数据成功接受，你可以使用下面的代码发送一个成功消息::

	$this->trackback->send_success();

.. note:: 上面的代码中不包含数据校验，我们建议你添加。

***************
类参考
***************

.. php:class:: CI_Trackback

	.. attribute:: $data = array('url' => '', 'title' => '', 'excerpt' => '', 'blog_name' => '', 'charset' => '')

		引用通告数据的数组。

	.. attribute:: $convert_ascii = TRUE

		是否将高位 ASCII 和 MS Word 特殊字符 转换为 HTML 实体。

	.. php:method:: send($tb_data)

		:param	array	$tb_data: Trackback data
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		发送引用通告。

	.. php:method:: receive()

		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		该方法简单的检验接受到的引用通告数据，成功返回 TRUE ，失败返回 FALSE 。
		如果数据是有效的，将添加到 ``$this->data`` 数组，以便保存到数据库。

	.. php:method:: send_error([$message = 'Incomplete information')

		:param	string	$message: Error message
		:rtype: void

		向引用通告请求返回一条错误信息。

		.. note:: 该方法将会终止脚本的执行。

	.. php:method:: send_success()

		:rtype:	void

		向引用通告请求返回一条成功信息。

		.. note:: 该方法将会终止脚本的执行。

	.. php:method:: data($item)

		:param	string	$item: Data key
		:returns:	Data value or empty string if not found
		:rtype:	string

		从引用通告数据中获取一项记录。

	.. php:method:: process($url, $data)

		:param	string	$url: Target url
		:param	string	$data: Raw POST data
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		打开一个 socket 连接，并将数据传送到服务器。成功返回 TRUE ，失败返回 FALSE 。

	.. php:method:: extract_urls($urls)

		:param	string	$urls: Comma-separated URL list
		:returns:	Array of URLs
		:rtype:	array

		该方法用于发送多条引用通告，它接受一个包含多条 URL 的字符串
		（以逗号或空格分割），将其转换为一个数组。

	.. php:method:: validate_url(&$url)

		:param	string	$url: Trackback URL
		:rtype:	void

		如果 URL 中没有包括协议部分，该方法简单将 *http://* 前缀添加到 URL 前面。

	.. php:method:: get_id($url)

		:param	string	$url: Trackback URL
		:returns:	URL ID or FALSE on failure
		:rtype:	string

		查找并返回一个引用通告 URL 的 ID ，失败返回 FALSE 。

	.. php:method:: convert_xml($str)

		:param	string	$str: Input string
		:returns:	Converted string
		:rtype:	string

		将 XML 保留字符转换为实体。

	.. php:method:: limit_characters($str[, $n = 500[, $end_char = '&#8230;']])

		:param	string	$str: Input string
		:param	int	$n: Max characters number
		:param	string	$end_char: Character to put at end of string
		:returns:	Shortened string
		:rtype:	string

		将字符串裁剪到指定字符个数，会保持单词的完整性。

	.. php:method:: convert_ascii($str)

		:param	string	$str: Input string
		:returns:	Converted string
		:rtype:	string

		将高位 ASCII 和 MS Word 特殊字符转换为 HTML 实体。

	.. php:method:: set_error($msg)

		:param	string	$msg: Error message
		:rtype:	void

		设置一个错误信息。

	.. php:method:: display_errors([$open = '<p>'[, $close = '</p>']])

		:param	string	$open: Open tag
		:param	string	$close: Close tag
		:returns:	HTML formatted error messages
		:rtype:	string

		返回 HTML 格式的错误信息，如果没有错误，返回空字符串。
