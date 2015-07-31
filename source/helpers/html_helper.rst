###############
HTML 辅助库
###############

HTML 辅助库文件包含了用于处理 HTML 的一些函数。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

加载辅助库
===================

该辅助库通过下面的代码加载::

	$this->load->helper('html');

可用函数
===================

该辅助库有下列可用函数：

.. php:function:: heading([$data = ''[, $h = '1'[, $attributes = '']]])

	:param	string	$data: Content
	:param	string	$h: Heading level
	:param	mixed	$attributes: HTML attributes
	:returns:	HTML heading tag
	:rtype:	string

	用于创建 HTML 标题标签，第一个参数为标题内容，第二个参数为标题大小。例如::

		echo heading('Welcome!', 3);

	上面代码将生成：<h3>Welcome!</h3>

	另外，为了向标题标签添加属性，例如 HTML class、id 或内联样式，可以通过第三个参数传一个字符串或者一个数组::

		echo heading('Welcome!', 3, 'class="pink"');
		echo heading('How are you?', 4, array('id' => 'question', 'class' => 'green'));

	上面代码将生成：

	.. code-block:: html

		<h3 class="pink">Welcome!<h3>
		<h4 id="question" class="green">How are you?</h4>

.. php:function:: img([$src = ''[, $index_page = FALSE[, $attributes = '']]])

	:param	string	$src: Image source data
	:param	bool	$index_page: Whether to treat $src as a routed URI string
	:param	array	$attributes: HTML attributes
	:returns:	HTML image tag
	:rtype:	string

	用于生成 HTML <img /> 标签，第一个参数为图片地址，例如::

		echo img('images/picture.jpg'); // gives <img src="http://site.com/images/picture.jpg" />

	第二个参数可选，其值为 TRUE 或 FALSE，用于指定是否在生成的图片地址中添加由 ``$config['index_page']`` 所设置的起始页面。
	一般来说，当你使用媒体控制器时才使用这个::

		echo img('images/picture.jpg', TRUE); // gives <img src="http://site.com/index.php/images/picture.jpg" alt="" />

	另外，你也可以通过向 ``img()`` 函数传递一个关联数组来完全控制所有的属性和值，如果没有提供 *alt* 属性，
	CodeIgniter 默认使用一个空字符串。

	例如::

		$image_properties = array(
			'src' 	=> 'images/picture.jpg',
			'alt' 	=> 'Me, demonstrating how to eat 4 slices of pizza at one time',
			'class' => 'post_images',
			'width' => '200',
			'height'=> '200',
			'title' => 'That was quite a night',
			'rel' 	=> 'lightbox'
		);

		img($image_properties);
		// <img src="http://site.com/index.php/images/picture.jpg" alt="Me, demonstrating how to eat 4 slices of pizza at one time" class="post_images" width="200" height="200" title="That was quite a night" rel="lightbox" />

.. php:function:: link_tag([$href = ''[, $rel = 'stylesheet'[, $type = 'text/css'[, $title = ''[, $media = ''[, $index_page = FALSE]]]]]])

	:param	string	$href: What are we linking to
	:param	string	$rel: Relation type
	:param	string	$type: Type of the related document
	:param	string	$title: Link title
	:param	string	$media: Media type
	:param	bool	$index_page: Whether to treat $src as a routed URI string
	:returns:	HTML link tag
	:rtype:	string

	用于生成 HTML <link /> 标签，这在生成样式的 link 标签时很有用，当然也可以生成其他的 link 标签。
	参数为 *href* ，后面的是可选的：*rel* 、 *type* 、 *title* 、 *media* 和 *index_page* 。

	*index_page* 参数是个布尔值，用于指定是否在 *href* 链接中添加由 ``$config['index_page']`` 所设置的起始页面。

	例如::

		echo link_tag('css/mystyles.css');
		// gives <link href="http://site.com/css/mystyles.css" rel="stylesheet" type="text/css" />

	另一个例子::

		echo link_tag('favicon.ico', 'shortcut icon', 'image/ico');
		// <link href="http://site.com/favicon.ico" rel="shortcut icon" type="image/ico" />

		echo link_tag('feed', 'alternate', 'application/rss+xml', 'My RSS Feed');
		// <link href="http://site.com/feed" rel="alternate" type="application/rss+xml" title="My RSS Feed" />

	另外，你也可以通过向 ``link()`` 函数传递一个关联数组来完全控制所有的属性和值::

		$link = array(
			'href'	=> 'css/printer.css',
			'rel'	=> 'stylesheet',
			'type'	=> 'text/css',
			'media'	=> 'print'
		);

		echo link_tag($link);
		// <link href="http://site.com/css/printer.css" rel="stylesheet" type="text/css" media="print" />


.. php:function:: ul($list[, $attributes = ''])

	:param	array	$list: List entries
	:param	array	$attributes: HTML attributes
	:returns:	HTML-formatted unordered list
	:rtype:	string

	用于生成 HTML 无序列表（ <ul> ），参数为简单的数组或者多维数组。例如::

		$list = array(
			'red',
			'blue',
			'green',
			'yellow'
		);

		$attributes = array(
			'class'	=> 'boldlist',
			'id'	=> 'mylist'
		);

		echo ul($list, $attributes);

	上面的代码将生成：

	.. code-block:: html

		<ul class="boldlist" id="mylist">
			<li>red</li>
			<li>blue</li>
			<li>green</li>
			<li>yellow</li>
		</ul>

	下面是个更复杂的例子，使用了多维数组::

		$attributes = array(
			'class'	=> 'boldlist',
			'id'	=> 'mylist'
		);

		$list = array(
			'colors'  => array(
				'red',
				'blue',
				'green'
			),
			'shapes'  => array(
				'round',
				'square',
				'circles' => array(
					'ellipse',
					'oval',
					'sphere'
				)
			),
			'moods'  => array(
				'happy',
				'upset' => array(
					'defeated' => array(
						'dejected',
						'disheartened',
						'depressed'
					),
					'annoyed',
					'cross',
					'angry'
				)
			)
		);

		echo ul($list, $attributes);

	上面的代码将生成：

	.. code-block:: html

		<ul class="boldlist" id="mylist">
			<li>colors
				<ul>
					<li>red</li>
					<li>blue</li>
					<li>green</li>
				</ul>
			</li>
			<li>shapes
				<ul>
					<li>round</li>
					<li>suare</li>
					<li>circles
						<ul>
							<li>elipse</li>
							<li>oval</li>
							<li>sphere</li>
						</ul>
					</li>
				</ul>
			</li>
			<li>moods
				<ul>
					<li>happy</li>
					<li>upset
						<ul>
							<li>defeated
								<ul>
									<li>dejected</li>
									<li>disheartened</li>
									<li>depressed</li>
								</ul>
							</li>
							<li>annoyed</li>
							<li>cross</li>
							<li>angry</li>
						</ul>
					</li>
				</ul>
			</li>
		</ul>

.. php:function:: ol($list, $attributes = '')

	:param	array	$list: List entries
	:param	array	$attributes: HTML attributes
	:returns:	HTML-formatted ordered list
	:rtype:	string

	和 :php:func:`ul()` 函数一样，只是它生成的是有序列表（ <ol> ）。

.. php:function:: meta([$name = ''[, $content = ''[, $type = 'name'[, $newline = "\n"]]]])

	:param	string	$name: Meta name
	:param	string	$content: Meta content
	:param	string	$type: Meta type
	:param	string	$newline: Newline character
	:returns:	HTML meta tag
	:rtype:	string

	用于生成 meta 标签，你可以传递一个字符串参数，或者一个数组，或者一个多维数组。

	例如::

		echo meta('description', 'My Great site');
		// Generates:  <meta name="description" content="My Great Site" />

		echo meta('Content-type', 'text/html; charset=utf-8', 'equiv');
		// Note the third parameter.  Can be "equiv" or "name"
		// Generates:  <meta http-equiv="Content-type" content="text/html; charset=utf-8" />

		echo meta(array('name' => 'robots', 'content' => 'no-cache'));
		// Generates:  <meta name="robots" content="no-cache" />

		$meta = array(
			array(
				'name' => 'robots',
				'content' => 'no-cache'
			),
			array(
				'name' => 'description',
				'content' => 'My Great Site'
			),
			array(
				'name' => 'keywords',
				'content' => 'love, passion, intrigue, deception'
			),
			array(
				'name' => 'robots',
				'content' => 'no-cache'
			),
			array(
				'name' => 'Content-type',
				'content' => 'text/html; charset=utf-8', 'type' => 'equiv'
			)
		);

		echo meta($meta);
		// Generates:
		// <meta name="robots" content="no-cache" />
		// <meta name="description" content="My Great Site" />
		// <meta name="keywords" content="love, passion, intrigue, deception" />
		// <meta name="robots" content="no-cache" />
		// <meta http-equiv="Content-type" content="text/html; charset=utf-8" />


.. php:function:: doctype([$type = 'xhtml1-strict'])

	:param	string	$type: Doctype name
	:returns:	HTML DocType tag
	:rtype:	string

	用于生成 DTD （文档类型声明，document type declaration），默认使用的是 XHTML 1.0 Strict ，但是你也可以选择其他的。

	例如::

		echo doctype(); // <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">

		echo doctype('html4-trans'); // <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">

	下表是可选的文档类型，它是可配置的，你可以在 application/config/doctypes.php 文件中找到它。

	=============================== =================== ==================================================================================================================================================
	文档类型                        选项                 结果
	=============================== =================== ==================================================================================================================================================
	XHTML 1.1                       xhtml11             <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.1//EN" "http://www.w3.org/TR/xhtml11/DTD/xhtml11.dtd">
	XHTML 1.0 Strict                xhtml1-strict       <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">
	XHTML 1.0 Transitional          xhtml1-trans        <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
	XHTML 1.0 Frameset              xhtml1-frame        <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Frameset//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-frameset.dtd">
	XHTML Basic 1.1                 xhtml-basic11       <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML Basic 1.1//EN" "http://www.w3.org/TR/xhtml-basic/xhtml-basic11.dtd">
	HTML 5                          html5               <!DOCTYPE html>
	HTML 4 Strict                   html4-strict        <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
	HTML 4 Transitional             html4-trans         <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
	HTML 4 Frameset                 html4-frame         <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Frameset//EN" "http://www.w3.org/TR/html4/frameset.dtd">
	MathML 1.01                     mathml1             <!DOCTYPE math SYSTEM "http://www.w3.org/Math/DTD/mathml1/mathml.dtd">
	MathML 2.0                      mathml2             <!DOCTYPE math PUBLIC "-//W3C//DTD MathML 2.0//EN" "http://www.w3.org/Math/DTD/mathml2/mathml2.dtd">
	SVG 1.0                         svg10               <!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.0//EN" "http://www.w3.org/TR/2001/REC-SVG-20010904/DTD/svg10.dtd">
	SVG 1.1 Full                    svg11               <!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
	SVG 1.1 Basic                   svg11-basic         <!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1 Basic//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11-basic.dtd">
	SVG 1.1 Tiny                    svg11-tiny          <!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1 Tiny//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11-tiny.dtd">
	XHTML+MathML+SVG (XHTML host)   xhtml-math-svg-xh   <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.1 plus MathML 2.0 plus SVG 1.1//EN" "http://www.w3.org/2002/04/xhtml-math-svg/xhtml-math-svg.dtd">
	XHTML+MathML+SVG (SVG host)     xhtml-math-svg-sh   <!DOCTYPE svg:svg PUBLIC "-//W3C//DTD XHTML 1.1 plus MathML 2.0 plus SVG 1.1//EN" "http://www.w3.org/2002/04/xhtml-math-svg/xhtml-math-svg.dtd">
	XHTML+RDFa 1.0                  xhtml-rdfa-1        <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML+RDFa 1.0//EN" "http://www.w3.org/MarkUp/DTD/xhtml-rdfa-1.dtd">
	XHTML+RDFa 1.1                  xhtml-rdfa-2        <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML+RDFa 1.1//EN" "http://www.w3.org/MarkUp/DTD/xhtml-rdfa-2.dtd">
	=============================== =================== ==================================================================================================================================================

.. php:function:: br([$count = 1])

	:param	int	$count: Number of times to repeat the tag
	:returns:	HTML line break tag
	:rtype:	string

	根据指定的个数生成多个换行标签（ <br /> ）。
	例如::

		echo br(3);

	上面的代码将生成：

	.. code-block:: html

		<br /><br /><br />

	.. note:: 该函数已经废弃，请使用原生的 ``str_repeat()`` 函数代替。

.. php:function:: nbs([$num = 1])

	:param	int	$num: Number of space entities to produce
	:returns:	A sequence of non-breaking space HTML entities
	:rtype:	string

	根据指定的个数生成多个不换行空格（&nbsp;）。
	例如::

		echo nbs(3);

	上面的代码将生成：

	.. code-block:: html

		&nbsp;&nbsp;&nbsp;

	.. note:: 该函数已经废弃，请使用原生的 ``str_repeat()`` 函数代替。
