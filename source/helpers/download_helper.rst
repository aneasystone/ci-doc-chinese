###############
下载辅助库
###############

下载辅助库文件包含了下载相关的一些函数。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

加载辅助库
===================

该辅助库通过下面的代码加载::

	$this->load->helper('download');

可用函数
===================

该辅助库有下列可用函数：

.. php:function:: force_download([$filename = ''[, $data = ''[, $set_mime = FALSE]]])

	:param	string	$filename: Filename
	:param	mixed	$data: File contents
	:param	bool	$set_mime: Whether to try to send the actual MIME type
	:rtype:	void

	生成 HTTP 头强制下载数据到客户端，这在实现文件下载时很有用。
	第一个参数为下载文件名称，第二个参数为文件数据。

	如果第二个参数为空，并且 ``$filename`` 参数是一个存在并可读的文件路径，
	那么这个文件的内容将被下载。

	如果第三个参数设置为 TRUE，那么将发送文件实际的 MIME 类型（根据文件的扩展名），
	这样你的浏览器会根据该 MIME 类型来处理。

	Example::

		$data = 'Here is some text!';
		$name = 'mytext.txt';
		force_download($name, $data);

	下载一个服务器上已存在的文件的例子如下::

		// Contents of photo.jpg will be automatically read
		force_download('/path/to/photo.jpg', NULL);