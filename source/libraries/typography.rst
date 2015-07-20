################
排版类
################

排版类提供了一些方法用于帮助你格式化文本。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

**************************
使用排版类
**************************

初始化该类
======================

跟 CodeIgniter 中的其他类一样，可以在你的控制器中使用 ``$this->load->library()`` 
方法加载排版类::

	$this->load->library('typography');

一旦加载，排版类就可以像下面这样使用::

	$this->typography

***************
类参考
***************

.. php:class:: CI_Typography

	.. attribute:: $protect_braced_quotes = FALSE

		当排版类和 :doc:`模板解析器类 <parser>` 同时使用时，经常需要保护大括号中的的单引号和双引号不被转换。
		要保护这个，将 ``protect_braced_quotes`` 属性设置为 TRUE 。

		使用示例::

			$this->load->library('typography');
			$this->typography->protect_braced_quotes = TRUE;

	.. method auto_typography($str[, $reduce_linebreaks = FALSE])

		:param	string	$str: Input string
		:param	bool	$reduce_linebreaks: Whether to reduce consequitive linebreaks
		:returns:	HTML typography-safe string
		:rtype:	string

		格式化文本以便纠正语义和印刷错误的 HTML 代码。按如下规则格式化输入的字符串：

		 -  将段落使用 <p></p> 包起来（看起来像是用两个换行符把段落分隔开似的）。
		 -  除了出现 <pre> 标签外，所有的单个换行符被转换为 <br />。
		 -  块级元素如 <div> 标签，不会被段落包住，但是如果他们包含文本的话文本会被段落包住。
		 -  除了出现在标签中的引号外，引号会被转换成正确的实体。
		 -  撇号被转换为相应的实体。
		 -  双破折号（像 -- 或--）被转换成 em — 破折号。
		 -  三个连续的点也会被转换为省略号… 。
		 -  句子后连续的多个空格将被转换为 &nbsp; 以便在网页中显示。

		使用示例::

			$string = $this->typography->auto_typography($string);

		第二个可选参数用于是否将多于两个连续的换行符压缩成两个，传入 TRUE 启用压缩换行::

			$string = $this->typography->auto_typography($string, TRUE);

		.. note:: 格式排版可能会消耗大量处理器资源，特别是在排版大量内容时。
			如果你选择使用这个函数的话，你可以考虑使用 `缓存 <../general/caching>`。

	.. php:method:: format_characters($str)

		:param	string	$str: Input string
		:returns:	Formatted string
		:rtype:	string

		该方法和上面的 ``auto_typography()`` 类似，但是它只对字符进行处理：

		 -  除了出现在标签中的引号外，引号会被转换成正确的实体。
		 -  撇号被转换为相应的实体。
		 -  双破折号（像 -- 或--）被转换成 em — 破折号。
		 -  三个连续的点也会被转换为省略号… 。
		 -  句子后连续的多个空格将被转换为 &nbsp; 以便在网页中显示。

		使用示例::

			$string = $this->typography->format_characters($string);

	.. php:method:: nl2br_except_pre($str)

		:param	string	$str: Input string
		:returns:	Formatted string
		:rtype:	string

		将换行符转换为 <br /> 标签，忽略 <pre> 标签中的换行符。除了对 <pre> 
		标签中的换行处理有所不同之外，这个函数和 PHP 函数 ``nl2br()`` 是完全一样的。

		使用示例::

			$string = $this->typography->nl2br_except_pre($string);