#############
表情辅助库
#############

表情辅助库文件包含了一些让你管理表情的函数。

.. important:: 表情辅助库已经废弃，不建议使用。现在只是为了向前兼容而保留。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

加载辅助库
===================

该辅助库通过下面的代码加载::

	$this->load->helper('smiley');

概述
========

表情辅助库用于将纯文本的表情转换为图片，譬如：:-) 转换为 |smile!|

另外它还可以显示一组表情图片，当你点击其中的某个表情时将会被插入到一个表单域中。
例如，如果你有一个博客并允许用户提交评论，你可以将这组表情图片显示在评论的旁边，
这样用户就可以点击想要的表情，然后通过一点点的 Javascript 代码，将该表情插入到
用户的评论中去。

可点击的表情包教程
==========================

这里是一个如何在表单中使用可点击的表情包的示例，这个示例需要你首先下载并安装表情图片，
然后按下面的步骤创建一个控制器和视图。

.. important:: 开始之前，请先 `下载表情图片 <https://ellislab.com/asset/ci_download_files/smileys.zip>`_
	然后将其放置到服务器的一个公共目录，并打开 `application/config/smileys.php` 文件设置表情替换的规则。

控制器
--------------

在 **application/controllers/** 目录下，创建一个文件 Smileys.php 然后输入下面的代码。

.. important:: 修改下面的 :php:func:`get_clickable_smileys()` 函数的 URL 参数，让其指向你的表情目录。

你会发现我们除了使用到了表情库，还使用到了 :doc:`表格类 <../libraries/table>`::

	<?php

	class Smileys extends CI_Controller {

		public function index()
		{
			$this->load->helper('smiley');
			$this->load->library('table');

			$image_array = get_clickable_smileys('http://example.com/images/smileys/', 'comments');
			$col_array = $this->table->make_columns($image_array, 8);

			$data['smiley_table'] = $this->table->generate($col_array);
			$this->load->view('smiley_view', $data);
		}

	}

然后，在 **application/views/** 目录下新建一个文件 **smiley_view.php** 并输入以下代码::

	<html>
		<head>
			<title>Smileys</title>
			<?php echo smiley_js(); ?>
		</head>
		<body>
			<form name="blog">
				<textarea name="comments" id="comments" cols="40" rows="4"></textarea>
			</form>
			<p>Click to insert a smiley!</p>
			<?php echo $smiley_table; ?> </body> </html>
			When you have created the above controller and view, load it by visiting http://www.example.com/index.php/smileys/
		</body>
	</html>

字段别名
-------------

当修改视图的时候，会牵扯到控制器中的 id 字段，带来不便。为了解决这一问题，
你可以在视图中给表情一个别名，并将其映射到 id 字段。

::

	$image_array = get_smiley_links("http://example.com/images/smileys/", "comment_textarea_alias");

将别名映射到 id 字段，可以使用 smiley_js 函数并传入这两个参数::

	$image_array = smiley_js("comment_textarea_alias", "comments");

可用函数
===================

.. php:function:: get_clickable_smileys($image_url[, $alias = ''[, $smileys = NULL]])

	:param	string	$image_url: URL path to the smileys directory
	:param	string	$alias: Field alias
	:returns:	An array of ready to use smileys
	:rtype:	array

	返回一个已经绑定了可点击表情的数组。你必须提供表情文件夹的 URL ，
	还有表单域的 ID 或者表单域的别名。

	举例::

		$image_array = get_clickable_smileys('http://example.com/images/smileys/', 'comment');

.. php:function:: smiley_js([$alias = ''[, $field_id = ''[, $inline = TRUE]]])

	:param	string	$alias: Field alias
	:param	string	$field_id: Field ID
	:param	bool	$inline: Whether we're inserting an inline smiley
	:returns:	Smiley-enabling JavaScript code
	:rtype:	string

	生成可以让图片点击后插入到表单域中的 JavaScript 代码。如果你在生成表情链接的时候
	提供了一个别名来代替 id ，你需要在函数中传入别名和相应的 id ，此函数被设计为
	应放在你 Web 页面的 <head> 部分。

	举例::

		<?php echo smiley_js(); ?>

.. php:function:: parse_smileys([$str = ''[, $image_url = ''[, $smileys = NULL]]])

	:param	string	$str: Text containing smiley codes
	:param	string	$image_url: URL path to the smileys directory
	:param	array	$smileys: An array of smileys
	:returns:	Parsed smileys
	:rtype:	string

	输入一个文本字符串，并将其中的纯文本表情替换为等效的表情图片，第一个参数为你的字符串，
	第二个参数是你的表情目录对应的 URL 。

	举例::

		$str = 'Here are some smileys: :-)  ;-)';
		$str = parse_smileys($str, 'http://example.com/images/smileys/');
		echo $str;

.. |smile!| image:: ../images/smile.gif