###########
路径辅助库
###########

路径辅助库文件包含了用于处理服务端文件路径的一些函数。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

加载辅助库
===================

该辅助库通过下面的代码加载::

	$this->load->helper('path');

可用函数
===================

该辅助库有下列可用函数：

.. php:function:: set_realpath($path[, $check_existance = FALSE])

	:param	string	$path: Path
	:param	bool	$check_existance: Whether to check if the path actually exists
	:returns:	An absolute path
	:rtype:	string

	该函数返回指定路径在服务端的绝对路径（不是符号路径或相对路径），
	可选的第二个参数用于指定当文件路径不存在时是否报错。

	Examples::

		$file = '/etc/php5/apache2/php.ini';
		echo set_realpath($file); // Prints '/etc/php5/apache2/php.ini'

		$non_existent_file = '/path/to/non-exist-file.txt';
		echo set_realpath($non_existent_file, TRUE);	// Shows an error, as the path cannot be resolved
		echo set_realpath($non_existent_file, FALSE);	// Prints '/path/to/non-exist-file.txt'

		$directory = '/etc/php5';
		echo set_realpath($directory);	// Prints '/etc/php5/'

		$non_existent_directory = '/path/to/nowhere';
		echo set_realpath($non_existent_directory, TRUE);	// Shows an error, as the path cannot be resolved
		echo set_realpath($non_existent_directory, FALSE);	// Prints '/path/to/nowhere'