####################
文件上传类
####################

CodeIgniter 的文件上传类用于上传文件，你可以设置参数限制上传文件的类型和大小。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

***********
处理流程
***********

上传一个文件通常涉及以下几步：

-  显示一个上传表单，用户选择文件并上传。
-  当提交表单时，文件将被上传到你指定的目录。
-  同时，根据你设置的参数对文件进行校验是否允许上传。
-  上传成功后，向用户显示成功消息。

下面是个简单的教程来示范该过程，然后会列出一些其他的参考信息。

创建上传表单
========================

使用文本编辑器新建一个文件 upload_form.php ，放入如下代码，并保存到 **application/views/** 目录下::

	<html>
	<head>
	<title>Upload Form</title>
	</head>
	<body>

	<?php echo $error;?>

	<?php echo form_open_multipart('upload/do_upload');?>

	<input type="file" name="userfile" size="20" />

	<br /><br />

	<input type="submit" value="upload" />

	</form>

	</body>
	</html>

你会注意到我们使用了表单辅助库来创建 form 的起始标签，文件上传需要使用 multipart 表单，
辅助库可以帮你正确生成它。还要注意的是，代码里有一个 $error 变量，当发生错误时，
可以用它来显示错误信息。

上传成功页面
================

使用文本编辑器新建一个文件 upload_success.php ，放入如下代码，并保存到 **application/views/** 目录下::

	<html>
	<head>
	<title>Upload Form</title>
	</head>
	<body>

	<h3>Your file was successfully uploaded!</h3>

	<ul>
	<?php foreach ($upload_data as $item => $value):?>
	<li><?php echo $item;?>: <?php echo $value;?></li>
	<?php endforeach; ?>
	</ul>

	<p><?php echo anchor('upload', 'Upload Another File!'); ?></p>

	</body>
	</html>

控制器
==============

使用文本编辑器新建一个控制器 Upload.php ，放入如下代码，并保存到 **application/controllers/** 目录下::

	<?php

	class Upload extends CI_Controller {

		public function __construct()
		{
			parent::__construct();
			$this->load->helper(array('form', 'url'));
		}

		public function index()
		{
			$this->load->view('upload_form', array('error' => ' ' ));
		}

		public function do_upload()
		{
			$config['upload_path']		= './uploads/';
			$config['allowed_types']	= 'gif|jpg|png';
			$config['max_size']		= 100;
			$config['max_width']		= 1024;
			$config['max_height']		= 768;

			$this->load->library('upload', $config);

			if ( ! $this->upload->do_upload('userfile'))
			{
				$error = array('error' => $this->upload->display_errors());

				$this->load->view('upload_form', $error);
			}
			else
			{
				$data = array('upload_data' => $this->upload->data());

				$this->load->view('upload_success', $data);
			}
		}
	}
	?>

上传文件目录
====================

你需要一个目录来保存上传的图片，在 CodeIgniter 的安装根目录下创建一个 uploads 目录，
并将它的权限设置为 777 。

尝试一下！
==========

使用类似于下面的 URL 来方法你的站点::

	example.com/index.php/upload/

你应该能看到一个上传文件的表单，尝试着上传一个图片文件（jpg、gif 或 png 都可以），
如果你的控制器中路径设置正确，你就可以成功上传文件了。

***************
参考指南
***************

初始化文件上传类
=============================

正如 CodeIgniter 中的其他类一样，在你的控制器中使用 ``$this->load->library()``
方法来初始化文件上传类::

	$this->load->library('upload');

初始化之后，文件上传类的对象就可以这样访问::

	$this->upload

参数设置
===================

和其他的类库一样，你可以通过你配置的参数来控制允许上传什么类型的文件。
在上面的控制器中，你设置了下面的这些参数::

	$config['upload_path'] = './uploads/';
	$config['allowed_types'] = 'gif|jpg|png';
	$config['max_size']	= '100';
	$config['max_width'] = '1024';
	$config['max_height'] = '768';

	$this->load->library('upload', $config);

	// Alternately you can set preferences by calling the ``initialize()`` method. Useful if you auto-load the class:
	$this->upload->initialize($config);

上面的参数根据它的名称就能很容易理解，下表列出了所有可用的参数。

参数
===========

下表列出了所有可用的参数，当没有指定参数时程序会使用默认值。

============================ ================= ======================= ======================================================================
参数                         默认值            选项                    描述
============================ ================= ======================= ======================================================================
**upload_path**              None              None                    文件上传的位置，必须是可写的，可以是相对路径或绝对路径
**allowed_types**            None              None                    允许上的文件 MIME 类型，通常文件的后缀名可作为 MIME 类型
                                                                       可以是数组，也可以是以管道符（|）分割的字符串
**file_name**                None              Desired file name       如果设置了，CodeIgniter 将会使用该参数重命名上传的文件
                                                                       设置的文件名后缀必须也要是允许的文件类型
                                                                       如果没有设置后缀，将使用原文件的后缀名
**file_ext_tolower**         FALSE             TRUE/FALSE (boolean)    如果设置为 TRUE ，文件后缀名将转换为小写
**overwrite**                FALSE             TRUE/FALSE (boolean)    如果设置为 TRUE ，上传的文件如果和已有的文件同名，将会覆盖已存在文件
                                                                       如果设置为 FALSE ，将会在文件名后加上一个数字
**max_size**                 0                 None                    允许上传文件大小的最大值（单位 KB），设置为 0 表示无限制
                                                                       注意：大多数 PHP 会有它们自己的限制值，定义在 php.ini 文件中
                                                                       通常是默认的 2 MB （2048 KB）。
**max_width**                0                 None                    图片的最大宽度（单位为像素），设置为 0 表示无限制
**max_height**               0                 None                    图片的最大高度（单位为像素），设置为 0 表示无限制
**min_width**                0                 None                    图片的最小宽度（单位为像素），设置为 0 表示无限制
**min_height**               0                 None                    图片的最小高度（单位为像素），设置为 0 表示无限制
**max_filename**             0                 None                    文件名的最大长度，设置为 0 表示无限制
**max_filename_increment**   100               None                    当 overwrite 参数设置为 FALSE 时，将会在同名文件的后面加上一个自增的数字
                                                                       这个参数用于设置这个数字的最大值
**encrypt_name**             FALSE             TRUE/FALSE (boolean)    如果设置为 TRUE ，文件名将会转换为一个随机的字符串
                                                                       如果你不希望上传文件的人知道保存后的文件名，这个参数会很有用
**remove_spaces**            TRUE              TRUE/FALSE (boolean)    如果设置为 TRUE ，文件名中的所有空格将转换为下划线，推荐这样做
**detect_mime**              TRUE              TRUE/FALSE (boolean)    如果设置为 TRUE ，将会在服务端对文件类型进行检测，可以预防代码注入攻击
                                                                       除非不得已，请不要禁用该选项，这将导致安全风险
**mod_mime_fix**             TRUE              TRUE/FALSE (boolean)    如果设置为 TRUE ，那么带有多个后缀名的文件将会添加一个下划线后缀
                                                                       这样可以避免触发 `Apache mod_mime
                                                                       <http://httpd.apache.org/docs/2.0/mod/mod_mime.html#multipleext>`_ 。
                                                                       如果你的上传目录是公开的，请不要关闭该选项，这将导致安全风险
============================ ================= ======================= ======================================================================

在配置文件中设置参数
====================================

如果你不喜欢使用上面的方法来设置参数，你可以将参数保存到配置文件中。你只需简单的创建一个文件 
upload.php 并将 $config 数组放到该文件中，然后保存文件到 **config/upload.php** ，这些参数将会自动被使用。
如果你在配置文件中设置参数，那么你就不需要使用 ``$this->upload->initialize()`` 方法了。

***************
类参考
***************

.. php:class:: CI_Upload

	.. php:method:: initialize([array $config = array()[, $reset = TRUE]])

		:param	array	$config: Preferences
		:param	bool	$reset: Whether to reset preferences (that are not provided in $config) to their defaults
		:returns:	CI_Upload instance (method chaining)
		:rtype:	CI_Upload

	.. php:method:: do_upload([$field = 'userfile'])

		:param	string	$field: Name of the form field
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		根据你设置的参数上传文件。

		.. note:: 默认情况下上传文件是来自于表单的 userfile 字段，而且表单应该是 "multipart" 类型。

		::

			<form method="post" action="some_action" enctype="multipart/form-data" />

		如果你想设置你自己的字段，可以将字段名传给 ``do_upload()`` 方法::

			$field_name = "some_field_name";
			$this->upload->do_upload($field_name);

	.. php:method:: display_errors([$open = '<p>'[, $close = '</p>']])

		:param	string	$open: Opening markup
		:param	string	$close: Closing markup
		:returns:	Formatted error message(s)
		:rtype:	string

		如果 ``do_upload()`` 方法返回 FALSE ，可以使用该方法来获取错误信息。
		该方法返回所有的错误信息，而不是是直接显示出来。

		**格式化错误信息**

			默认情况下该方法会将错误信息包在 <p> 标签中，你可以设置你自己的标签::

				$this->upload->display_errors('<p>', '</p>');


	.. php:method:: data([$index = NULL])

		:param	string	$data: Element to return instead of the full array
		:returns:	Information about the uploaded file
		:rtype:	mixed

		该方法返回一个数组，包含你上传的文件的所有信息，下面是数组的原型::

			Array
			(
				[file_name]	=> mypic.jpg
				[file_type]	=> image/jpeg
				[file_path]	=> /path/to/your/upload/
				[full_path]	=> /path/to/your/upload/jpg.jpg
				[raw_name]	=> mypic
				[orig_name]	=> mypic.jpg
				[client_name]	=> mypic.jpg
				[file_ext]	=> .jpg
				[file_size]	=> 22.2
				[is_image]	=> 1
				[image_width]	=> 800
				[image_height]	=> 600
				[image_type]	=> jpeg
				[image_size_str] => width="800" height="200"
			)

		你也可以只返回数组中的一项::

			$this->upload->data('file_name');	// Returns: mypic.jpg

		下表解释了上面列出的所有数组项：

		================ ====================================================================================================
		项               描述
		================ ====================================================================================================
		file_name        上传文件的文件名，包含后缀名
		file_type        文件的 MIME 类型
		file_path        文件的绝对路径
		full_path        文件的绝对路径，包含文件名
		raw_name         文件名，不含后缀名
		orig_name        原始的文件名，只有在使用了 encrypt_name 参数时该值才有用
		client_name      用户提交过来的文件名，还没有对该文件名做任何处理
		file_ext         文件后缀名，包括句点
		file_size        文件大小（单位 kb）
		is_image         文件是否为图片（1 = image. 0 = not.）
		image_width      图片宽度
		image_height     图片高度
		image_type       图片类型（通常是不带句点的文件后缀名）
		image_size_str   一个包含了图片宽度和高度的字符串（用于放在 image 标签中）
		================ ====================================================================================================
