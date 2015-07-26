#############
字符串辅助库
#############

字符串辅助库文件包含了一些帮助你处理字符串的函数。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

加载辅助库
===================

该辅助库通过下面的代码加载::

	$this->load->helper('string');

可用函数
===================

该辅助库有下列可用函数：

.. php:function:: random_string([$type = 'alnum'[, $len = 8]])

	:param	string	$type: Randomization type
	:param	int	$len: Output string length
	:returns:	A random string
	:rtype:	string

	根据你所指定的类型和长度产生一个随机字符串。可用于生成密码或随机字符串。

	第一个参数指定字符串类型，第二个参数指定其长度。有下列几种字符串类型可供选择：

	-  **alpha**: 只含有大小写字母的字符串
	-  **alnum**: 含有大小写字母以及数字的字符串
	-  **basic**: 根据 ``mt_rand()`` 函数生成的一个随机数字
	-  **numeric**: 数字字符串
	-  **nozero**: 数字字符串（不含零）
	-  **md5**: 根据 ``md5()`` 生成的一个加密的随机数字（长度固定为 32）
	-  **sha1**: 根据 ``sha1()`` 生成的一个加密的随机数字（长度固定为 40）

	使用示例::

		echo random_string('alnum', 16);

	.. note:: *unique* 和 *encrypt* 类型已经废弃，它们只是 *md5* 和 *sha1* 的别名。

.. php:function:: increment_string($str[, $separator = '_'[, $first = 1]])

	:param	string	$str: Input string
	:param	string	$separator: Separator to append a duplicate number with
	:param	int	$first: Starting number
	:returns:	An incremented string
	:rtype:	string

	自增字符串是指向字符串尾部添加一个数字，或者对这个数字进行自增。
	这在生成文件的拷贝时非常有用，或者向数据库中某列（例如 title 或 slug）添加重复的内容，
	但是这一列设置了唯一索引时。

	使用示例::

		echo increment_string('file', '_'); // "file_1"
		echo increment_string('file', '-', 2); // "file-2"
		echo increment_string('file_4'); // "file_5"


.. php:function:: alternator($args)

	:param	mixed	$args: A variable number of arguments
	:returns:	Alternated string(s)
	:rtype:	mixed

	当执行一个循环时，让两个或两个以上的条目轮流使用。示例::

		for ($i = 0; $i < 10; $i++)
		{     
			echo alternator('string one', 'string two');
		}

	你可以添加任意多个参数，每一次循环后下一个条目将成为返回值。

	::

		for ($i = 0; $i < 10; $i++)
		{     
			echo alternator('one', 'two', 'three', 'four', 'five');
		}

	.. note:: 如果要多次调用该函数，可以简单的通过不带参数重新初始化下。

.. php:function:: repeater($data[, $num = 1])

	:param	string	$data: Input
	:param	int	$num: Number of times to repeat
	:returns:	Repeated string
	:rtype:	string

	重复生成你的数据。例如::

		$string = "\n";
		echo repeater($string, 30);

	上面的代码会生成 30 个空行。

	.. note:: 该函数已经废弃，使用原生的 ``str_repeat()`` 函数替代。


.. php:function:: reduce_double_slashes($str)

	:param	string	$str: Input string
	:returns:	A string with normalized slashes
	:rtype:	string

	将字符串中的双斜线（'//'）转换为单斜线（'/'），但不转换 URL 协议中的双斜线（例如：http://）

	示例::

		$string = "http://example.com//index.php";
		echo reduce_double_slashes($string); // results in "http://example.com/index.php"


.. php:function:: strip_slashes($data)

	:param	mixed	$data: Input string or an array of strings
	:returns:	String(s) with stripped slashes
	:rtype:	mixed

	移除一个字符串数组中的所有斜线。

	示例::

		$str = array(
			'question'  => 'Is your name O\'reilly?',
			'answer' => 'No, my name is O\'connor.'
		);

		$str = strip_slashes($str);

	上面的代码将返回下面的数组::

		array(
			'question'  => "Is your name O'reilly?",
			'answer' => "No, my name is O'connor."
		);

	.. note:: 由于历史原因，该函数也接受一个字符串参数，这时该函数就相当于 ``stripslashes()`` 的别名。

.. php:function:: trim_slashes($str)

	:param	string	$str: Input string
	:returns:	Slash-trimmed string
	:rtype:	string

	移除字符串开头和结尾的所有斜线。例如::

		$string = "/this/that/theother/";
		echo trim_slashes($string); // results in this/that/theother

	.. note:: 该函数已废弃，使用原生的 ``trim()`` 函数代替：
		|
		| trim($str, '/');

.. php:function:: reduce_multiples($str[, $character = ''[, $trim = FALSE]])

	:param	string	$str: Text to search in
	:param	string	$character: Character to reduce
	:param	bool	$trim: Whether to also trim the specified character
	:returns:	Reduced string
	:rtype:	string

	移除字符串中重复出现的某个指定字符。例如::

		$string = "Fred, Bill,, Joe, Jimmy";
		$string = reduce_multiples($string,","); //results in "Fred, Bill, Joe, Jimmy"

	如果设置第三个参数为 TRUE ，该函数将移除出现在字符串首尾的指定字符。例如::

		$string = ",Fred, Bill,, Joe, Jimmy,";
		$string = reduce_multiples($string, ", ", TRUE); //results in "Fred, Bill, Joe, Jimmy"

.. php:function:: quotes_to_entities($str)

	:param	string	$str: Input string
	:returns:	String with quotes converted to HTML entities
	:rtype:	string

	将字符串中的单引号和双引号转换为相应的 HTML 实体。例如::

		$string = "Joe's \"dinner\"";
		$string = quotes_to_entities($string); //results in "Joe&#39;s &quot;dinner&quot;"


.. php:function:: strip_quotes($str)

	:param	string	$str: Input string
	:returns:	String with quotes stripped
	:rtype:	string

	移除字符串中出现的单引号和双引号。例如::

		$string = "Joe's \"dinner\"";
		$string = strip_quotes($string); //results in "Joes dinner"