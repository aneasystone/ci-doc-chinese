###################
Inflector 辅助库
###################

Inflector 辅助库文件包含了一些帮助你将单词转换为单复数或驼峰格式等等的函数。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

加载辅助库
===================

该辅助库通过下面的代码加载::

	$this->load->helper('inflector');

可用函数
===================

该辅助库有下列可用函数：

.. php:function:: singular($str)

	:param	string	$str: Input string
	:returns:	A singular word
	:rtype:	string

	将一个单词的复数形式变为单数形式。例如::

		echo singular('dogs'); // Prints 'dog'

.. php:function:: plural($str)

	:param	string	$str: Input string
	:returns:	A plular word
	:rtype:	string

	将一个单词的单数形式变为复数形式。例如::

		echo plural('dog'); // Prints 'dogs'

.. php:function:: camelize($str)

	:param	string	$str: Input string
	:returns:	Camelized string
	:rtype:	string

	将一个以空格或下划线分隔的单词转换为驼峰格式。例如::

		echo camelize('my_dog_spot'); // Prints 'myDogSpot'

.. php:function:: underscore($str)

	:param	string	$str: Input string
	:returns:	String containing underscores instead of spaces
	:rtype:	string

	将以空格分隔的多个单词转换为下划线分隔格式。例如::

		echo underscore('my dog spot'); // Prints 'my_dog_spot'

.. php:function:: humanize($str[, $separator = '_'])

	:param	string	$str: Input string
	:param	string	$separator: Input separator
	:returns:	Humanized string
	:rtype:	string

	将以下划线分隔的多个单词转换为以空格分隔，并且每个单词以大写开头。例如::

		echo humanize('my_dog_spot'); // Prints 'My Dog Spot'

	如果单词是以连接符分割的，第二个参数传入连接符::

		echo humanize('my-dog-spot', '-'); // Prints 'My Dog Spot'

.. php:function:: is_countable($word)

	:param	string	$word: Input string
	:returns:	TRUE if the word is countable or FALSE if not
	:rtype:	bool

	判断某个单词是否有复数形式。例如::

		is_countable('equipment'); // Returns FALSE