#########
FTP 类
#########

CodeIgniter 的 FTP 类允许你传输文件至远程服务器，也可以对远程文件进行移动、重命名或删除操作。
FTP 类还提供了一个 "镜像" 功能，允许你将你本地的一个目录通过 FTP 整个的同步到远程服务器上。

.. note:: 只支持标准的 FTP 协议，不支持 SFTP 和 SSL FTP 。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

**************************
使用 FTP 类
**************************

初始化类
======================

正如 CodeIgniter 中的其他类一样，在你的控制器中使用 ``$this->load->library()``
方法来初始化 FTP 类::

	$this->load->library('ftp');

初始化之后，FTP 类的对象就可以这样访问::

	$this->ftp

使用示例
==============

在这个例子中，首先建立一个到 FTP 服务器的连接，接着读取一个本地文件然后以 ASCII 
模式上传到服务器上。文件的权限被设置为 755 。
::

	$this->load->library('ftp');

	$config['hostname'] = 'ftp.example.com';
	$config['username'] = 'your-username';
	$config['password'] = 'your-password';
	$config['debug']	= TRUE;

	$this->ftp->connect($config);

	$this->ftp->upload('/local/path/to/myfile.html', '/public_html/myfile.html', 'ascii', 0775);

	$this->ftp->close();

下面的例子从 FTP 服务器上获取文件列表。
::

	$this->load->library('ftp');

	$config['hostname'] = 'ftp.example.com';
	$config['username'] = 'your-username';
	$config['password'] = 'your-password';
	$config['debug']	= TRUE;

	$this->ftp->connect($config);

	$list = $this->ftp->list_files('/public_html/');

	print_r($list);

	$this->ftp->close();

下面的例子在 FTP 服务器上创建了一个本地目录的镜像。
::

	$this->load->library('ftp');

	$config['hostname'] = 'ftp.example.com';
	$config['username'] = 'your-username';
	$config['password'] = 'your-password';
	$config['debug']	= TRUE;

	$this->ftp->connect($config);

	$this->ftp->mirror('/path/to/myfolder/', '/public_html/myfolder/');

	$this->ftp->close();

***************
类参考
***************

.. php:class:: CI_FTP

	.. php:method:: connect([$config = array()])

		:param	array	$config: Connection values
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		连接并登录到 FTP 服务器，通过向函数传递一个数组来设置连接参数，或者你可以把这些参数保存在一个配置文件中。

		下面例子演示了如何手动设置参数::

			$this->load->library('ftp');

			$config['hostname'] = 'ftp.example.com';
			$config['username'] = 'your-username';
			$config['password'] = 'your-password';
			$config['port']     = 21;
			$config['passive']  = FALSE;
			$config['debug']    = TRUE;

			$this->ftp->connect($config);

		**在配置文件中设置 FTP 参数**

		如果你喜欢，你可以把 FTP 参数保存在一个配置文件中，只需创建一个名为 ftp.php 的文件，
		然后把 $config 数组添加到该文件中，然后将文件保存到 *application/config/ftp.php* ，
		它就会自动被读取。

		**可用的连接选项**

		============== =============== =============================================================================
		选项名称        默认值           描述
		============== =============== =============================================================================
		**hostname**   n/a             FTP 主机名（通常类似于这样：ftp.example.com）
		**username**   n/a             FTP 用户名
		**password**   n/a             FTP 密码
		**port**       21              FTP 服务端口
		**debug**      FALSE           TRUE/FALSE (boolean): 是否开启调试模式，显示错误信息
		**passive**    TRUE            TRUE/FALSE (boolean): 是否使用被动模式
		============== =============== =============================================================================

	.. php:method:: upload($locpath, $rempath[, $mode = 'auto'[, $permissions = NULL]])

		:param	string	$locpath: Local file path
		:param	string	$rempath: Remote file path
		:param	string	$mode: FTP mode, defaults to 'auto' (options are: 'auto', 'binary', 'ascii')
		:param	int	$permissions: File permissions (octal)
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		将一个文件上传到你的服务器上。必须指定本地路径和远程路径这两个参数，而传输模式和权限设置这两个参数则是可选的。例如:

			$this->ftp->upload('/local/path/to/myfile.html', '/public_html/myfile.html', 'ascii', 0775);

		如果使用了 auto 模式，将根据源文件的扩展名来自动选择传输模式。

		设置权限必须使用一个 八进制 的权限值。

	.. php:method:: download($rempath, $locpath[, $mode = 'auto'])

		:param	string	$rempath: Remote file path
		:param	string	$locpath: Local file path
		:param	string	$mode: FTP mode, defaults to 'auto' (options are: 'auto', 'binary', 'ascii')
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		从你的服务器下载一个文件。必须指定远程路径和本地路径，传输模式是可选的。例如：

			$this->ftp->download('/public_html/myfile.html', '/local/path/to/myfile.html', 'ascii');

		如果使用了 auto 模式，将根据源文件的扩展名来自动选择传输模式。

		如果下载失败（包括 PHP 没有写入本地文件的权限）函数将返回 FALSE 。

	.. php:method:: rename($old_file, $new_file[, $move = FALSE])

		:param	string	$old_file: Old file name
		:param	string	$new_file: New file name
		:param	bool	$move: Whether a move is being performed
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		允许你重命名一个文件。需要指定原文件的文件路径和名称，以及新的文件路径和名称。
		::

			// Renames green.html to blue.html
			$this->ftp->rename('/public_html/foo/green.html', '/public_html/foo/blue.html');

	.. php:method:: move($old_file, $new_file)

		:param	string	$old_file: Old file name
		:param	string	$new_file: New file name
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		允许你移动一个文件。需要指定原路径和目的路径::

			// Moves blog.html from "joe" to "fred"
			$this->ftp->move('/public_html/joe/blog.html', '/public_html/fred/blog.html');

		.. note:: 如果目的文件名和原文件名不同，文件将会被重命名。

	.. php:method:: delete_file($filepath)

		:param	string	$filepath: Path to file to delete
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		用于删除一个文件。需要提供原文件的路径。
		::

			 $this->ftp->delete_file('/public_html/joe/blog.html');

	.. php:method:: delete_dir($filepath)

		:param	string	$filepath: Path to directory to delete
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		用于删除一个目录以及该目录下的所有文件。需要提供目录的路径（以斜线结尾）。

		.. important:: 使用该方法要非常小心！
			它会递归的删除目录下的所有内容，包括子目录和所有文件。请确保你提供的路径是正确的。
			你可以先使用 ``list_files()`` 方法来验证下路径是否正确。

		::

			 $this->ftp->delete_dir('/public_html/path/to/folder/');

	.. php:method:: list_files([$path = '.'])

		:param	string	$path: Directory path
		:returns:	An array list of files or FALSE on failure
		:rtype:	array

		用于获取服务器上某个目录的文件列表，你需要指定目录路径。
		::

			$list = $this->ftp->list_files('/public_html/');
			print_r($list);

	.. php:method:: mirror($locpath, $rempath)

		:param	string	$locpath: Local path
		:param	string	$rempath: Remote path
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		递归的读取文本的一个目录和它下面的所有内容（包括子目录），然后通过 FTP 在远程服务器上创建一个镜像。
		无论原文件的路径和目录结构是什么样的，都会在远程服务器上一模一样的重建。你需要指定一个原路径和目的路径::

			 $this->ftp->mirror('/path/to/myfolder/', '/public_html/myfolder/');

	.. php:method:: mkdir($path[, $permissions = NULL])

		:param	string	$path: Path to directory to create
		:param	int	$permissions: Permissions (octal)
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		用于在服务器上创建一个目录。需要指定目录的路径并以斜线结尾。

		还可以通过第二个参数传递一个 八进制的值 设置权限。
		::

			// Creates a folder named "bar"
			$this->ftp->mkdir('/public_html/foo/bar/', 0755);

	.. php:method:: chmod($path, $perm)

		:param	string	$path: Path to alter permissions for
		:param	int	$perm: Permissions (octal)
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		用于设置文件权限。需要指定你想修改权限的文件或目录的路径::

			// Chmod "bar" to 755
			$this->ftp->chmod('/public_html/foo/bar/', 0755);

	.. php:method:: changedir($path[, $suppress_debug = FALSE])

		:param	string	$path: Directory path
		:param	bool	$suppress_debug: Whether to turn off debug messages for this command
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		用于修改当前工作目录到指定路径。

		如果你希望使用这个方法作为 ``is_dir()`` 的一个替代，``$suppress_debug`` 参数将很有用。

	.. php:method:: close()

		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		断开和服务器的连接。当你上传完毕时，建议使用这个函数。
