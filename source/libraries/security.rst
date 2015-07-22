##############
安全类
##############

安全类包含了一些方法，用于安全的处理输入数据，帮助你创建一个安全的应用。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

*************
XSS 过滤
*************

CodeIgniter 自带了一个 XSS 过滤器来防御攻击，它可以设置为自动运行过滤
所有遇到的 POST 和 COOKIE 数据，也可以针对某一条数据进行过滤。默认情况下
它不是全局运行的，因为它会有相当的开销，况且你并不是在所有地方都需要它。

XSS 过滤器会查找那些常被用来触发 JavaScript 脚本或者其他类型的企图劫持
Cookie 或者其它恶意行为的代码。如果发现任何不允许的内容，它将把那些内容
转换为字符实体，以确保安全。

注意：这个函数只应该用来处理那些提交过来的数据，它不适合在一般情况下使用，
因为它的执行会有相当大的开销。

使用 XSS 过滤器过滤数据可以使用 ``xss_clean()`` 方法::

	$data = $this->security->xss_clean($data);

它还有一个可选的第二个参数 is_image ，允许此函数对图片进行检测以发现那些潜在的
XSS 攻击, 这对于保证文件上传的安全非常有用。当此参数被设置为 TRUE 时，
函数的返回值将是一个布尔值，而不是一个修改过的字符串。如果图片是安全的则返回 TRUE ，
相反, 如果图片中包含有潜在的、可能会被浏览器尝试运行的恶意信息，函数将返回 FALSE 。

::

	if ($this->security->xss_clean($file, TRUE) === FALSE)
	{
		// file failed the XSS test
	}

*********************************
跨站请求伪造（CSRF）
*********************************

打开你的 application/config/config.php 文件，进行如下设置，即可启用 CSRF 保护::

	$config['csrf_protection'] = TRUE;

如果你使用 :doc:`表单辅助库 <../helpers/form_helper>` ，:func:`form_open()`
函数将会自动地在你的表单中插入一个隐藏的 CSRF 字段。如果没有插入这个字段，
你可以手工调用 ``get_csrf_token_name()`` 和 ``get_csrf_hash()`` 这两个函数。

::

	$csrf = array(
		'name' => $this->security->get_csrf_token_name(),
		'hash' => $this->security->get_csrf_hash()
	);

	...

	<input type="hidden" name="<?=$csrf['name'];?>" value="<?=$csrf['hash'];?>" />

令牌（tokens）默认会在每一次提交时重新生成，或者你也可以设置成在 CSRF cookie 
的生命周期内一直有效。默认情况下令牌重新生成提供了更严格的安全机制，但可能会对
可用性带来一定的影响，因为令牌很可能会变得失效（譬如使用浏览器的返回前进按钮、
使用多窗口或多标签页浏览、异步调用等等）。你可以修改下面这个参数来改变这一点。

::

	$config['csrf_regenerate'] = TRUE;

另外，你可以添加一个 URI 的白名单，跳过 CSRF 保护（例如某个 API 接口希望接受
原始的 POST 数据），将这些 URI 添加到 'csrf_exclude_uris' 配置参数中::

	$config['csrf_exclude_uris'] = array('api/person/add');

URI 中也支持使用正则表达式（不区分大小写）::

	$config['csrf_exclude_uris'] = array(
		'api/record/[0-9]+',
		'api/title/[a-z]+'
	);

***************
类参考
***************

.. php:class:: CI_Security

	.. php:method:: xss_clean($str[, $is_image = FALSE])

		:param	mixed	$str: Input string or an array of strings
		:returns:	XSS-clean data
		:rtype:	mixed

		尝试移除输入数据中的 XSS 代码，并返回过滤后的数据。
		如果第二个参数设置为 TRUE ，将检查图片中是否含有恶意数据，是的话返回 TRUE ，否则返回 FALSE 。

	.. php:method:: sanitize_filename($str[, $relative_path = FALSE])

		:param	string	$str: File name/path
		:param	bool	$relative_path: Whether to preserve any directories in the file path
		:returns:	Sanitized file name/path
		:rtype:	string

		尝试对文件名进行净化，防止目录遍历尝试以及其他的安全威胁，当文件名作为用户输入的参数时格外有用。
		::

			$filename = $this->security->sanitize_filename($this->input->post('filename'));

		如果允许用户提交相对路径，譬如 *file/in/some/approved/folder.txt* ，你可以将第二个参数 ``$relative_path`` 设置为 TRUE 。
		::

			$filename = $this->security->sanitize_filename($this->input->post('filename'), TRUE);

	.. php:method:: get_csrf_token_name()

		:returns:	CSRF token name
		:rtype:	string

		返回 CSRF 的令牌名（token name），也就是 ``$config['csrf_token_name']`` 的值。

	.. php:method:: get_csrf_hash()

		:returns:	CSRF hash
		:rtype:	string

		返回 CSRF 哈希值（hash value），在和 ``get_csrf_token_name()`` 函数一起使用时很有用，用于生成表单里的 CSRF 字段
		以及发送有效的 AJAX POST 请求。

	.. php:method:: entity_decode($str[, $charset = NULL])

		:param	string	$str: Input string
		:param	string	$charset: Character set of the input string
		:returns:	Entity-decoded string
		:rtype:	string

		该方法和 ENT_COMPAT 模式下的 PHP 原生函数 ``html_entity_decode()`` 差不多，只是它除此之外，还会检测不以分号结尾的 
		HTML 实体，因为有些浏览器允许这样。

		如果没有设置 ``$charset`` 参数，则使用你配置的 ``$config['charset']`` 参数作为编码格式。

	.. php:method:: get_random_bytes($length)

		:param	int	$length: Output length
		:returns:	A binary stream of random bytes or FALSE on failure
		:rtype:	string

		这是一种生成随机字符串的简易方法，该方法通过按顺序调用 ``mcrypt_create_iv()``， ``/dev/urandom``
		和 ``openssl_random_pseudo_bytes()`` 这三个函数，只要有一个函数是可用的，都可以返回随机字符串。

		用于生成 CSRF 和 XSS 的令牌。

		.. note:: 输出并不能保证绝对安全，只是尽量做到更安全。