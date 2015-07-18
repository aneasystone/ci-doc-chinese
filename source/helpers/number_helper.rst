#############
数字辅助库
#############

数字辅助库文件包含了用于处理数字的一些函数。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

加载辅助库
===================

该辅助库通过下面的代码加载::

	$this->load->helper('number');

可用函数
===================

该辅助库有下列可用函数：

.. php:function:: byte_format($num[, $precision = 1])

	:param	mixed	$num: Number of bytes
	:param	int	$precision: Floating point precision
	:returns:	Formatted data size string
	:rtype:	string

	根据数值大小以字节的形式格式化，并添加适合的缩写单位。
	譬如::

		echo byte_format(456); // Returns 456 Bytes
		echo byte_format(4567); // Returns 4.5 KB
		echo byte_format(45678); // Returns 44.6 KB
		echo byte_format(456789); // Returns 447.8 KB
		echo byte_format(3456789); // Returns 3.3 MB
		echo byte_format(12345678912345); // Returns 1.8 GB
		echo byte_format(123456789123456789); // Returns 11,228.3 TB

	可选的第二个参数允许你设置结果的精度::

		 echo byte_format(45678, 2); // Returns 44.61 KB

	.. note:: 这个函数生成的缩写单位可以在 *language/<your_lang>/number_lang.php* 语言文件中找到。