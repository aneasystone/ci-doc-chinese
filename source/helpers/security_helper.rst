###############
安全辅助库
###############

安全辅助库文件包含了一些和安全相关的函数。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

加载辅助库
===================

该辅助库通过下面的代码加载::

	$this->load->helper('security');

可用函数
===================

该辅助库有下列可用函数：

.. php:function:: xss_clean($str[, $is_image = FALSE])

	:param	string	$str: Input data
	:param	bool	$is_image: Whether we're dealing with an image
	:returns:	XSS-clean string
	:rtype:	string

	该函数提供了 XSS 攻击的过滤。

	它是 ``CI_Input::xss_clean()`` 函数的别名，更多信息，请查阅 :doc:`输入类 <../libraries/input>` 文档。

.. php:function:: sanitize_filename($filename)

	:param	string	$filename: Filename
	:returns:	Sanitized file name
	:rtype:	string

	该函数提供了 目录遍历 攻击的防护。

	它是 ``CI_Security::sanitize_filename()`` 函数的别名，更多信息，请查阅 :doc:`安全类 <../libraries/security>` 文档。


.. php:function:: do_hash($str[, $type = 'sha1'])

	:param	string	$str: Input
	:param	string	$type: Algorithm
	:returns:	Hex-formatted hash
	:rtype:	string

	该函数可计算单向散列，一般用于对密码进行加密，默认使用 SHA1 。

	你可以前往 `hash_algos() <http://php.net/function.hash_algos>`_ 查看所有支持的算法清单。

	举例::

		$str = do_hash($str); // SHA1
		$str = do_hash($str, 'md5'); // MD5

	.. note:: 这个函数前身为 ``dohash()``，已废弃。

	.. note:: 这个函数也不建议使用，使用原生的 ``hash()`` 函数替代。


.. php:function:: strip_image_tags($str)

	:param	string	$str: Input string
	:returns:	The input string with no image tags
	:rtype:	string

	该安全函数从一个字符串中剥除 image 标签，它将 image 标签转为纯图片的 URL 文本。

	举例::

		$string = strip_image_tags($string);

	它是 ``CI_Security::strip_image_tags()`` 函数的别名，更多信息，请查阅 :doc:`安全类 <../libraries/security>` 文档。


.. php:function:: encode_php_tags($str)

	:param	string	$str: Input string
	:returns:	Safely formatted string
	:rtype:	string

	该安全函数将 PHP 标签转换为实体对象。

	.. note:: 如果你使用函数 :php:func:`xss_clean()` ，会自动转换。

	举例::

		$string = encode_php_tags($string);