############
XML 辅助库
############

XML 辅助库文件包含了用于处理 XML 数据的一些函数。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

加载辅助库
===================

该辅助库通过下面的代码加载::

	$this->load->helper('xml');

可用函数
===================

该辅助库有下列可用函数：

.. php:function:: xml_convert($str[, $protect_all = FALSE])

	:param string $str: the text string to convert
	:param bool $protect_all: Whether to protect all content that looks like a potential entity instead of just numbered entities, e.g. &foo;
	:returns: XML-converted string
	:rtype:	string

	将输入字符串中的下列 XML 保留字符转换为实体（Entity）：

	  - 和号：&
	  - 小于号和大于号：< >
	  - 单引号和双引号：' "
	  - 减号：-

	如果 & 符号是作为实体编号的一部分，譬如： ``&#123;`` ，该函数将不予处理。
	举例::

		$string = '<p>Here is a paragraph & an entity (&#123;).</p>';
		$string = xml_convert($string);
		echo $string;

	输出结果:

	.. code-block:: html

		&lt;p&gt;Here is a paragraph &amp; an entity (&#123;).&lt;/p&gt;