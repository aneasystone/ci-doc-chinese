###########
文本辅助库
###########

文本辅助库文件包含了一些帮助你处理文本的函数。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

加载辅助库
===================

该辅助库通过下面的代码加载::

	$this->load->helper('text');

可用函数
===================

该辅助库有下列可用函数：

.. php:function:: word_limiter($str[, $limit = 100[, $end_char = '&#8230;']])

	:param	string	$str: Input string
	:param	int	$limit: Limit
	:param	string	$end_char: End character (usually an ellipsis)
	:returns:	Word-limited string
	:rtype:	string

	根据指定的 *单词* 个数裁剪字符串。例如::

		$string = "Here is a nice text string consisting of eleven words.";
		$string = word_limiter($string, 4);
		// Returns:  Here is a nice

	第三个参数用于给裁剪的字符串设置一个可选的后缀，默认使用省略号。


.. php:function:: character_limiter($str[, $n = 500[, $end_char = '&#8230;']])

	:param	string	$str: Input string
	:param	int	$n: Number of characters
	:param	string	$end_char: End character (usually an ellipsis)
	:returns:	Character-limited string
	:rtype:	string

	根据指定的 *字符* 个数裁剪字符串。它会保证单词的完整性，所以最终生成的
	字符串长度和你指定的长度有可能会有出入。

	例如::

		$string = "Here is a nice text string consisting of eleven words.";
		$string = character_limiter($string, 20);
		// Returns:  Here is a nice text string

	第三个参数用于给裁剪的字符串设置一个可选的后缀，如果没该参数，默认使用省略号。

	.. note:: 如果你需要将字符串精确的裁剪到指定长度，请参见下面的 :php:func:`ellipsize()` 函数。

.. php:function:: ascii_to_entities($str)

	:param	string	$str: Input string
	:returns:	A string with ASCII values converted to entities
	:rtype:	string

	将 ASCII 字符转换为字符实体，包括高位 ASCII 和 Microsoft Word 中的特殊字符，
	在 Web 页面中使用这些字符可能会导致问题。转换为字符实体后，它们就可以
	不受浏览器设置的影响正确的显示出来，也能可靠的存储到到数据库中。本函数依赖于
	你的服务器支持的字符集，所以它可能并不能保证 100% 可靠，但在大多数情况下，
	它都能正确的识别这些特殊字符（例如重音字符）。

	例如::

		$string = ascii_to_entities($string);

.. php:function::entities_to_ascii($str[, $all = TRUE])

	:param	string	$str: Input string
	:param	bool	$all: Whether to convert unsafe entities as well
	:returns:	A string with HTML entities converted to ASCII characters
	:rtype:	string

	该函数和 :php:func:`ascii_to_entities()` 恰恰相反，它将字符实体转换为 ASCII 字符。

.. php:function:: convert_accented_characters($str)

	:param	string	$str: Input string
	:returns:	A string with accented characters converted
	:rtype:	string

	将高位 ASCII 字符转换为与之相等的普通 ASCII 字符，当你的 URL 中需要使用
	非英语字符，而你的 URL 又设置了只允许出现普通 ASCII 字符时很有用。

	例如::

		$string = convert_accented_characters($string);

	.. note:: 该函数使用了 `application/config/foreign_chars.php` 配置文件来决定
		将什么字符转换为什么字符。

.. php:function:: word_censor($str, $censored[, $replacement = ''])

	:param	string	$str: Input string
	:param	array	$censored: List of bad words to censor
	:param	string	$replacement: What to replace bad words with
	:returns:	Censored string
	:rtype:	string

	对字符串中出现的敏感词进行审查。第一个参数为原始字符串，第二个参数
	为一个数组，包含你要禁用的单词，第三个参数（可选）可以设置将出现
	的敏感词替换成什么，如果未设置，默认替换为磅字符：#### 。

	例如::

		$disallowed = array('darn', 'shucks', 'golly', 'phooey');
		$string = word_censor($string, $disallowed, 'Beep!');

.. php:function:: highlight_code($str)

	:param	string	$str: Input string
	:returns:	String with code highlighted via HTML
	:rtype:	string

	对一段代码（PHP、HTML 等）进行着色。例如::

		$string = highlight_code($string);

	该函数使用了 PHP 的 ``highlight_string()`` 函数，所以着色的颜色是在 php.ini 文件中设置的。


.. php:function:: highlight_phrase($str, $phrase[, $tag_open = '<mark>'[, $tag_close = '</mark>']])

	:param	string	$str: Input string
	:param	string	$phrase: Phrase to highlight
	:param	string	$tag_open: Opening tag used for the highlight
	:param	string	$tag_close: Closing tag for the highlight
	:returns:	String with a phrase highlighted via HTML
	:rtype:	string

	对字符串内的一个短语进行突出显示。第一个参数是原始字符串，
	第二个参数是你想要突出显示的短语。如果要用 HTML 标签对短语进行标记，
	那么第三个和第四个参数分别是你想要对短语使用的 HTML 开始和结束标签。

	例如::

		$string = "Here is a nice text string about nothing in particular.";
		echo highlight_phrase($string, "nice text", '<span style="color:#990000;">', '</span>');

	上面的代码将输出::

		Here is a <span style="color:#990000;">nice text</span> string about nothing in particular.

	.. note:: 该函数默认是使用 ``<strong>`` 标签，老版本的浏览器可能不支持 ``<mark>`` 
		这个 HTML5 新标签，所以如果你想支持这些老的浏览器，推荐你在你的样式文件
		中添加如下 CSS 代码::

			mark {
				background: #ff0;
				color: #000;
			};

.. php:function:: word_wrap($str[, $charlim = 76])

	:param	string	$str: Input string
	:param	int	$charlim: Character limit
	:returns:	Word-wrapped string
	:rtype:	string

	根据指定的 *字符* 数目对文本进行换行操作，并且保持单词的完整性。

	例如::

		$string = "Here is a simple string of text that will help us demonstrate this function.";
		echo word_wrap($string, 25);

		// Would produce:  
		// Here is a simple string
		// of text that will help us
		// demonstrate this
		// function.

.. php:function:: ellipsize($str, $max_length[, $position = 1[, $ellipsis = '&hellip;']])

	:param	string	$str: Input string
	:param	int	$max_length: String length limit
	:param	mixed	$position: Position to split at (int or float)
	:param	string	$ellipsis: What to use as the ellipsis character
	:returns:	Ellipsized string
	:rtype:	string

	该函数移除字符串中出现的标签，并根据指定的长度裁剪字符串，并插入省略号。

	第一个参数是要处理的字符串，第二个参数为最终处理完后的字符串长度，
	第三个参数为插入省略号的位置，值为 0-1 表示从左到右。例如设置为 1
	省略号将插入到字符串的右侧，0.5 将插入到中间，0 将插入到左侧。

	第四个参数是可选的，表示省略号的类型，默认是 &hellip; 。

	例如::

		$str = 'this_string_is_entirely_too_long_and_might_break_my_design.jpg';
		echo ellipsize($str, 32, .5);

	输出结果::

		this_string_is_e&hellip;ak_my_design.jpg