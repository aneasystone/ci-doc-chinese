##################
Zip 编码类
##################

CodeIgniter 的 Zip 编码类允许你创建 Zip 压缩文档，文档可以被下载到你的桌面
或者 保存到某个文件夹里。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

****************************
使用 Zip 编码类
****************************

初始化类
======================

正如 CodeIgniter 中的其他类一样，在你的控制器中使用 ``$this->load->library()``
方法来初始化 Zip 编码类::

	$this->load->library('zip');

初始化之后，Zip 编码类的对象就可以这样访问::

	$this->zip

使用示例
=============

下面这个例子演示了如何压缩一个文件，将其保存到服务器上的一个目录下，并下载到你的桌面上。

::

	$name = 'mydata1.txt';
	$data = 'A Data String!';

	$this->zip->add_data($name, $data);

	// Write the zip file to a folder on your server. Name it "my_backup.zip"
	$this->zip->archive('/path/to/directory/my_backup.zip');

	// Download the file to your desktop. Name it "my_backup.zip"
	$this->zip->download('my_backup.zip');

***************
类参考
***************

.. php:class:: CI_Zip

	.. attribute:: $compression_level = 2

		使用的压缩等级。

		压缩等级的范围为 0 到 9 ，9 为最高等级，0 为禁用压缩::

			$this->zip->compression_level = 0;

	.. php:method:: add_data($filepath[, $data = NULL])

		:param	mixed	$filepath: A single file path or an array of file => data pairs
		:param	array	$data: File contents (ignored if $filepath is an array)
		:rtype:	void

		向 Zip 文档中添加数据，可以添加单个文件，也可以添加多个文件。

		当添加单个文件时，第一个参数为文件名，第二个参数包含文件的内容::

			$name = 'mydata1.txt';
			$data = 'A Data String!';
			$this->zip->add_data($name, $data);

			$name = 'mydata2.txt';
			$data = 'Another Data String!';
			$this->zip->add_data($name, $data);

		当添加多个文件时，第一个参数为包含 *file => contents* 这样的键值对的数组，第二个参数被忽略::

			$data = array(
				'mydata1.txt' => 'A Data String!',
				'mydata2.txt' => 'Another Data String!'
			);

			$this->zip->add_data($data);

		如果你想要将你压缩的数据组织到一个子目录下，只需简单的将文件路径包含到文件名中即可::

			$name = 'personal/my_bio.txt';
			$data = 'I was born in an elevator...';

			$this->zip->add_data($name, $data);

		上面的例子将会把 my_bio.txt 文件放到 personal 目录下。

	.. php:method:: add_dir($directory)

		:param	mixed	$directory: Directory name string or an array of multiple directories
		:rtype:	void

		允许你往压缩文档中添加一个目录，通常这个方法是没必要的，因为你完全可以使用 ``$this->zip->add_data()``
		方法将你的数据添加到特定的目录下。但是如果你想创建一个空目录，你可以使用它::

			$this->zip->add_dir('myfolder'); // Creates a directory called "myfolder"

	.. php:method:: read_file($path[, $archive_filepath = FALSE])

		:param	string	$path: Path to file
		:param	mixed	$archive_filepath: New file name/path (string) or (boolean) whether to maintain the original filepath
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		允许你压缩一个已经存在于你的服务器上的文件。该方法的参数为一个文件路径，Zip 
		类会读取该文件的内容并添加到压缩文档中::

			$path = '/path/to/photo.jpg';

			$this->zip->read_file($path);

			// Download the file to your desktop. Name it "my_backup.zip"
			$this->zip->download('my_backup.zip');

		如果你希望 Zip 文档中的文件保持它原有的目录结构，将第二个参数设置为布尔值 TRUE 。例如::

			$path = '/path/to/photo.jpg';

			$this->zip->read_file($path, TRUE);

			// Download the file to your desktop. Name it "my_backup.zip"
			$this->zip->download('my_backup.zip');

		在上面的例子中，photo.jpg 文件将会被放在 *path/to/* 目录下。

		你也可以为新添加的文件指定一个新的名称（包含文件路径）::

			$path = '/path/to/photo.jpg';
			$new_path = '/new/path/some_photo.jpg';

			$this->zip->read_file($path, $new_path);

			// Download ZIP archive containing /new/path/some_photo.jpg
			$this->zip->download('my_archive.zip');

	.. php:method:: read_dir($path[, $preserve_filepath = TRUE[, $root_path = NULL]])

		:param	string	$path: Path to directory
		:param	bool	$preserve_filepath: Whether to maintain the original path
		:param	string	$root_path: Part of the path to exclude from the archive directory
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		允许你压缩一个已经存在于你的服务器上的目录（包括里面的内容）。该方法的参数为目录的路径，Zip
		类会递归的读取它里面的内容并重建成一个 Zip 文档。指定目录下的所有文件以及子目录下的文件都会被压缩。
		例如::

			$path = '/path/to/your/directory/';

			$this->zip->read_dir($path);

			// Download the file to your desktop. Name it "my_backup.zip"
			$this->zip->download('my_backup.zip');

		默认情况下，Zip 文档中会保留第一个参数中指定的目录结构，如果你希望忽略掉这一大串的树形目录结构，
		你可以将第二个参数设置为布尔值 FALSE 。例如::

			$path = '/path/to/your/directory/';

			$this->zip->read_dir($path, FALSE);

		上面的代码将会创建一个 Zip 文档，文档里面直接是 "directory" 目录，然后是它下面的所有的子目录，
		不会包含 */path/to/your* 路径在里面。

	.. php:method:: archive($filepath)

		:param	string	$filepath: Path to target zip archive
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		向你的服务器指定目录下写入一个 Zip 编码文档，该方法的参数为一个有效的目录路径，后加一个文件名，
		确保这个目录是可写的（权限为 755 通常就可以了）。例如::

			$this->zip->archive('/path/to/folder/myarchive.zip'); // Creates a file named myarchive.zip

	.. php:method:: download($filename = 'backup.zip')

		:param	string	$filename: Archive file name
		:rtype:	void

		从你的服务器上下载 Zip 文档，你需要指定 Zip 文档的名称。例如::

			$this->zip->download('latest_stuff.zip'); // File will be named "latest_stuff.zip"

		.. note:: 在调用这个方法的控制器里不要显示任何数据，因为这个方法会发送多个服务器 HTTP 头，
			让文件以二进制的格式被下载。

	.. php:method:: get_zip()

		:returns:	Zip file content
		:rtype:	string

		返回使用 Zip 编码压缩后的文件数据，通常情况你无需使用该方法，除非你要对压缩后的数据做些特别的操作。
		例如::

			$name = 'my_bio.txt';
			$data = 'I was born in an elevator...';

			$this->zip->add_data($name, $data);

			$zip_file = $this->zip->get_zip();

	.. php:method:: clear_data()

		:rtype:	void

		Zip 类会缓存压缩后的数据，这样就不用在调用每个方法的时候重新压缩一遍了。但是，如果你需要创建多个 Zip
		文档，每个 Zip 文档有着不同的数据，那么你可以在多次调用之间把缓存清除掉。例如::

			$name = 'my_bio.txt';
			$data = 'I was born in an elevator...';

			$this->zip->add_data($name, $data);
			$zip_file = $this->zip->get_zip();

			$this->zip->clear_data();

			$name = 'photo.jpg';
			$this->zip->read_file("/path/to/photo.jpg"); // Read the file's contents

			$this->zip->download('myphotos.zip');
