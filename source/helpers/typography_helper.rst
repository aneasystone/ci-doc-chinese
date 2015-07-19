#################
排版辅助库
#################

排版辅助库文件包含了文本排版相关的一些函数。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

加载辅助库
===================

该辅助库通过下面的代码加载::

	$this->load->helper('typography');

可用函数
===================

该辅助库有下列可用函数：

.. php:function:: auto_typography($str[, $reduce_linebreaks = FALSE])

	:param	string	$str: Input string
	:param	bool	$reduce_linebreaks: Whether to reduce multiple instances of double newlines to two
	:returns:	HTML-formatted typography-safe string
	:rtype: string

	格式化 HTML 文本，使其在语义和排版上都是正确的。

	这个函数是 ``CI_Typography::auto_typography`` 函数的别名。
	更多信息，查看 :doc:`排版类 <../libraries/typography>` 。

	Usage example::

		$string = auto_typography($string);

	.. note:: 格式排版可能会消耗大量处理器资源，特别是在排版大量内容时。
		如果你选择使用这个函数的话，你可以考虑使用 `缓存 <../general/caching>`。


.. php:function:: nl2br_except_pre($str)

	:param	string	$str: Input string
	:returns:	String with HTML-formatted line breaks
	:rtype:	string

	将换行符转换为 <br /> 标签，忽略 <pre> 标签中的换行符。除了对 <pre> 
	标签中的换行处理有所不同之外，这个函数和 PHP 函数 ``nl2br()`` 是完全一样的。

	使用示例::

		$string = nl2br_except_pre($string);

.. php:function:: entity_decode($str, $charset = NULL)

	:param	string	$str: Input string
	:param	string	$charset: Character set
	:returns:	String with decoded HTML entities
	:rtype:	string

	这个函数是 ``CI_Security::entity_decode()`` 函数的别名。
	更多信息，查看 :doc:`安全类 <../libraries/security>` 。