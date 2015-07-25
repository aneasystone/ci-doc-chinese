###########
文件辅助库
###########

文件辅助库文件包含了一些帮助你处理文件的函数。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

加载辅助库
===================

该辅助库通过下面的代码加载::

	$this->load->helper('file');

可用函数
===================

该辅助库有下列可用函数：

.. php:function:: read_file($file)

	:param	string	$file: File path
	:returns:	File contents or FALSE on failure
	:rtype:	string

	返回指定文件的内容。

	例如::

		$string = read_file('./path/to/file.php');

	可以是相对路径或绝对路径，如果失败返回 FALSE 。

	.. note:: 路径是相对于你网站的 index.php 文件的，而不是相对于控制器或视图文件。
		这是因为 CodeIgniter 使用的前端控制器，所以所有的路径都是相对于 index.php 所在路径。

	.. note:: 该函数已废弃，使用 PHP 的原生函数 ``file_get_contents()`` 代替。

	.. important:: 如果你的服务器配置了 **open_basedir** 限制，该函数可能无法访问限制之外的文件。

.. php:function:: write_file($path, $data[, $mode = 'wb'])

	:param	string	$path: File path
	:param	string	$data: Data to write to file
	:param	string	$mode: ``fopen()`` mode
	:returns:	TRUE if the write was successful, FALSE in case of an error
	:rtype:	bool

	向指定文件中写入数据，如果文件不存在，则创建该文件。

	例如::

		$data = 'Some file data';
		if ( ! write_file('./path/to/file.php', $data))
		{     
			echo 'Unable to write the file';
		}
		else
		{     
			echo 'File written!';
		}

	你还可以通过第三个参数设置写模式::

		write_file('./path/to/file.php', $data, 'r+');

	默认的模式的 'wb' ，请阅读 `PHP 用户指南 <http://php.net/manual/en/function.fopen.php>`_ 
	了解写模式的选项。

	.. note: 为了保证该函数成功写入文件，必须要有写入该文件的权限。如果文件不存在，
		那么该文件所处目录必须是可写的。

	.. note:: 路径是相对于你网站的 index.php 文件的，而不是相对于控制器或视图文件。
		这是因为 CodeIgniter 使用的前端控制器，所以所有的路径都是相对于 index.php 所在路径。

	.. note:: 该函数在写入文件时会申请一个排他性锁。

.. php:function:: delete_files($path[, $del_dir = FALSE[, $htdocs = FALSE]])

	:param	string	$path: Directory path
	:param	bool	$del_dir: Whether to also delete directories
	:param	bool	$htdocs: Whether to skip deleting .htaccess and index page files
	:returns:	TRUE on success, FALSE in case of an error
	:rtype:	bool

	删除指定路径下的所有文件。

	例如::

		delete_files('./path/to/directory/');

	如果第二个参数设置为 TRUE ，那么指定路径下的文件夹也一并删除。

	例如::

		delete_files('./path/to/directory/', TRUE);

	.. note:: 要被删除的文件必须是当前系统用户所有或者是当前用户对之具有写权限。

.. php:function:: get_filenames($source_dir[, $include_path = FALSE])

	:param	string	$source_dir: Directory path
	:param	bool	$include_path: Whether to include the path as part of the filenames
	:returns:	An array of file names
	:rtype:	array

	获取指定目录下所有文件名组成的数组。如果需要完整路径的文件名，
	可以将第二个参数设置为 TRUE 。

	例如::

		$controllers = get_filenames(APPPATH.'controllers/');

.. php:function:: get_dir_file_info($source_dir, $top_level_only)

	:param	string	$source_dir: Directory path
	:param	bool	$top_level_only: Whether to look only at the specified directory (excluding sub-directories)
	:returns:	An array containing info on the supplied directory's contents
	:rtype:	array

	获取指定目录下所有文件信息组成的数组，包括文件名、文件大小、日期 和 权限。
	默认不包含子目录下的文件信息，如有需要，可以设置第二个参数为 FALSE ，这可能会是一个耗时的操作。

	例如::

		$models_info = get_dir_file_info(APPPATH.'models/');

.. php:function:: get_file_info($file[, $returned_values = array('name', 'server_path', 'size', 'date')])

	:param	string	$file: File path
	:param	array	$returned_values: What type of info to return
	:returns:	An array containing info on the specified file or FALSE on failure
	:rtype:	array

	获取指定文件的信息，包括文件名、路径、文件大小，修改日期等。第二个参数可以用于
	声明只返回回你想要的信息。

	第二个参数 ``$returned_values`` 有效的值有：`name`、`size`、`date`、`readable`、`writeable`、
	`executable` 和 `fileperms` 。

.. php:function:: get_mime_by_extension($filename)

	:param	string	$filename: File name
	:returns:	MIME type string or FALSE on failure
	:rtype:	string

	根据 *config/mimes.php* 文件中的配置将文件扩展名转换为 MIME 类型。
	如果无法判断 MIME 类型或 MIME 配置文件读取失败，则返回 FALSE 。

	::

		$file = 'somefile.png';
		echo $file.' is has a mime type of '.get_mime_by_extension($file);

	.. note:: 这个函数只是一种简便的判断 MIME 类型的方法，并不准确，所以
		请不要用于安全相关的地方。

.. php:function:: symbolic_permissions($perms)

	:param	int	$perms: Permissions
	:returns:	Symbolic permissions string
	:rtype:	string

	将文件权限的数字格式（譬如 ``fileperms()`` 函数的返回值）转换为标准的符号格式。

	::

		echo symbolic_permissions(fileperms('./index.php'));  // -rw-r--r--

.. php:function:: octal_permissions($perms)

	:param	int	$perms: Permissions
	:returns:	Octal permissions string
	:rtype:	string

	将文件权限的数字格式（譬如 ``fileperms()`` 函数的返回值）转换为三个字符的八进制表示格式。

	::

		echo octal_permissions(fileperms('./index.php')); // 644