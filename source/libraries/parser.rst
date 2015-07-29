#####################
模板解析类
#####################

模板解析类可以对你视图文件中的伪变量进行简单的替换，它可以解析简单的变量和变量标签对。

如果你从没使用过模板引擎，下面是个例子，伪变量名称使用大括号括起来::

	<html>
		<head>
			<title>{blog_title}</title>
		</head>
		<body>
			<h3>{blog_heading}</h3>

		{blog_entries}
			<h5>{title}</h5>
			<p>{body}</p>
		{/blog_entries}

		</body>
	</html>

这些变量并不是真正的 PHP 变量，只是普通的文本，这样能让你的模板（视图文件）中没有任何 PHP 代码。

.. note:: CodeIgniter **并没有** 让你必须使用这个类，因为直接在视图中使用纯 PHP 可能速度会更快点。
	尽管如此，一些开发者还是喜欢使用模板引擎，他们可能是和一些其他的不熟悉 PHP 的设计师共同工作。

.. important:: 模板解析类 **不是** 一个全面的模板解析方案，我们让它保持简洁，为了达到更高的性能。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

*******************************
使用模板解析类
*******************************

初始化类
======================

跟 CodeIgniter 中的其他类一样，可以在你的控制器中使用 ``$this->load->library()`` 
方法加载模板解析类::

	$this->load->library('parser');

一旦加载，模板解析类就可以像下面这样使用::

	$this->parser

解析模板
=================

你可以使用 ``parse()`` 方法来解析（或显示）简单的模板，如下所示::

	$data = array(
		'blog_title' => 'My Blog Title',
		'blog_heading' => 'My Blog Heading'
	);

	$this->parser->parse('blog_template', $data);

第一个参数为 :doc:`视图文件 <../general/views>` 的名称（在这个例子里，文件名为 blog_template.php），
第二个参数为一个关联数组，它包含了要对模板进行替换的数据。上例中，模板将包含两个变量：
{blog_title} 和 {blog_heading} 。

没有必要对 $this->parser->parse() 方法返回的结果进行 echo 或其他的处理，它会自动的保存到输出类，
以待发送给浏览器。但是，如果你希望它将数据返回而不是存到输出类里去，你可以将第三个参数设置为 TRUE ::

	$string = $this->parser->parse('blog_template', $data, TRUE);

变量对
==============

上面的例子可以允许替换简单的变量，但是如果你想重复某一块代码，并且每次重复的值都不同又该怎么办呢？
看下我们一开始的时候展示那个模板例子::

	<html>
		<head>
			<title>{blog_title}</title>
		</head>
		<body>
			<h3>{blog_heading}</h3>

		{blog_entries}
			<h5>{title}</h5>
			<p>{body}</p>
		{/blog_entries}

		</body>
	</html>

在上面的代码中，你会发现一对变量：{blog_entries} data... {/blog_entries} 。这个例子的意思是，
这个变量对之间的整个数据块将重复多次，重复的次数取决于 "blog_entries" 参数中元素的个数。

解析变量对和上面的解析单个变量的代码完全一样，除了一点，你需要根据变量对的数据使用一个多维的数组，
像下面这样::

	$this->load->library('parser');

	$data = array(
		'blog_title'   => 'My Blog Title',
		'blog_heading' => 'My Blog Heading',
		'blog_entries' => array(
			array('title' => 'Title 1', 'body' => 'Body 1'),
			array('title' => 'Title 2', 'body' => 'Body 2'),
			array('title' => 'Title 3', 'body' => 'Body 3'),
			array('title' => 'Title 4', 'body' => 'Body 4'),
			array('title' => 'Title 5', 'body' => 'Body 5')
		)
	);

	$this->parser->parse('blog_template', $data);

如果你的变量对数据来自于数据库查询结果，那么它已经是一个多维数组了，你可以简单的使用数据库的
``result_array()`` 方法::

	$query = $this->db->query("SELECT * FROM blog");

	$this->load->library('parser');

	$data = array(
		'blog_title'   => 'My Blog Title',
		'blog_heading' => 'My Blog Heading',
		'blog_entries' => $query->result_array()
	);

	$this->parser->parse('blog_template', $data);

使用说明
===========

如果你传入的某些参数在模板中没用到，它们将被忽略::

	$template = 'Hello, {firstname} {lastname}';
	$data = array(
		'title' => 'Mr',
		'firstname' => 'John',
		'lastname' => 'Doe'
	);
	$this->parser->parse_string($template, $data);

	// Result: Hello, John Doe

如果你的模板中用到了某个变量，但是你传入的参数中没有，将直接显示出原始的伪变量::

	$template = 'Hello, {firstname} {initials} {lastname}';
	$data = array(
		'title' => 'Mr',
		'firstname' => 'John',
		'lastname' => 'Doe'
	);
	$this->parser->parse_string($template, $data);

	// Result: Hello, John {initials} Doe

如果你的模板中需要使用某个数组变量，但是你传入的参数是个字符串类型，那么变量对的起始标签将会被替换，
但是结束标签不会被正确显示::

	$template = 'Hello, {firstname} {lastname} ({degrees}{degree} {/degrees})';
	$data = array(
		'degrees' => 'Mr',
		'firstname' => 'John',
		'lastname' => 'Doe',
		'titles' => array(
			array('degree' => 'BSc'),
			array('degree' => 'PhD')
		)
	);
	$this->parser->parse_string($template, $data);

	// Result: Hello, John Doe (Mr{degree} {/degrees})

如果你的某个单一变量的名称和变量对中的某个变量名称一样，显示结果可能会不对::

	$template = 'Hello, {firstname} {lastname} ({degrees}{degree} {/degrees})';
	$data = array(
		'degree' => 'Mr',
		'firstname' => 'John',
		'lastname' => 'Doe',
		'degrees' => array(
			array('degree' => 'BSc'),
			array('degree' => 'PhD')
		)
	);
	$this->parser->parse_string($template, $data);

	// Result: Hello, John Doe (Mr Mr )

视图片段
==============

你没必要在你的视图文件中使用变量对来实现重复，你也可以在变量对之间使用一个视图片段，
在控制器，而不是视图文件中，来控制重复。

下面是一个在视图中实现重复的例子::

	$template = '<ul>{menuitems}
		<li><a href="{link}">{title}</a></li>
	{/menuitems}</ul>';

	$data = array(
		'menuitems' => array(
			array('title' => 'First Link', 'link' => '/first'),
			array('title' => 'Second Link', 'link' => '/second'),
		)
	);
	$this->parser->parse_string($template, $data);

结果::

	<ul>
		<li><a href="/first">First Link</a></li>
		<li><a href="/second">Second Link</a></li>
	</ul>

下面是一个在控制器中利用视图片段来实现重复的例子::

	$temp = '';
	$template1 = '<li><a href="{link}">{title}</a></li>';
	$data1 = array(
		array('title' => 'First Link', 'link' => '/first'),
		array('title' => 'Second Link', 'link' => '/second'),
	);

	foreach ($data1 as $menuitem)
	{
		$temp .= $this->parser->parse_string($template1, $menuitem, TRUE);
	}

	$template = '<ul>{menuitems}</ul>';
	$data = array(
		'menuitems' => $temp
	);
	$this->parser->parse_string($template, $data);

结果::

	<ul>
		<li><a href="/first">First Link</a></li>
		<li><a href="/second">Second Link</a></li>
	</ul>

***************
类参考
***************

.. php:class:: CI_Parser

	.. php:method:: parse($template, $data[, $return = FALSE])

		:param	string	$template: Path to view file
		:param	array	$data: Variable data
		:param	bool	$return: Whether to only return the parsed template
		:returns:	Parsed template string
		:rtype:	string

		根据提供的路径和变量解析一个模板。

	.. php:method:: parse_string($template, $data[, $return = FALSE])

		:param	string	$template: Path to view file
		:param	array	$data: Variable data
		:param	bool	$return: Whether to only return the parsed template
		:returns:	Parsed template string
		:rtype:	string

		该方法和 ``parse()`` 方法一样，只是它接受一个字符串作为模板，而不是去加载视图文件。

	.. php:method:: set_delimiters([$l = '{'[, $r = '}']])

		:param	string	$l: Left delimiter
		:param	string	$r: Right delimiter
		:rtype: void

		设置模板中伪变量的分割符（起始标签和结束标签）。
