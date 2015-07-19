################
目录辅助库
################

目录辅助库文件包含了一些帮助你处理目录的函数。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

加载辅助库
===================

该辅助库通过下面的代码加载::

	$this->load->helper('directory');

可用函数
===================

该辅助库有下列可用函数：

.. php:function:: directory_map($source_dir[, $directory_depth = 0[, $hidden = FALSE]])

	:param	string	$source_dir: Path to the source directory
	:param	int	$directory_depth: Depth of directories to traverse (0 = fully recursive, 1 = current dir, etc)
	:param	bool	$hidden: Whether to include hidden directories
	:returns:	An array of files
	:rtype:	array

	举例::

		$map = directory_map('./mydirectory/');

	.. note:: 路径总是相对于你的 index.php 文件。

	如果目录内含有子目录，也将被列出。你可以使用第二个参数（整数）
	来控制递归的深度。如果深度为 1，则只列出根目录::

		$map = directory_map('./mydirectory/', 1);

	默认情况下，返回的数组中不会包括那些隐藏文件。如果需要显示隐藏的文件，
	你可以设置第三个参数为 true ::

		$map = directory_map('./mydirectory/', FALSE, TRUE);

	每一个目录的名字都将作为数组的索引，目录所包含的文件将以数字作为索引。
	下面有个典型的数组示例::

		Array (
			[libraries] => Array
				(
					[0] => benchmark.html
					[1] => config.html
					["database/"] => Array
						(
							[0] => query_builder.html
							[1] => binds.html
							[2] => configuration.html
							[3] => connecting.html
							[4] => examples.html
							[5] => fields.html
							[6] => index.html
							[7] => queries.html
						)
					[2] => email.html
					[3] => file_uploading.html
					[4] => image_lib.html
					[5] => input.html
					[6] => language.html
					[7] => loader.html
					[8] => pagination.html
					[9] => uri.html
				)