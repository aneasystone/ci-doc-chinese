###########
Email 类
###########

CodeIgniter 拥有强大的 Email 类支持以下特性：

-  多协议：Mail、Sendmail 和 SMTP
-  SMTP 协议支持 TLS 和 SSL 加密
-  多个收件人
-  抄送（CC）和密送（BCC）
-  HTML 格式邮件 或 纯文本邮件
-  附件
-  自动换行
-  优先级
-  密送批处理模式（BCC Batch Mode），大邮件列表将被分成小批次密送
-  Email 调试工具

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

***********************
使用 Email 类
***********************

发送 Email
=============

发送邮件不仅很简单，而且你可以通过参数或通过配置文件设置发送邮件的不同选项。

下面是个简单的例子，用于演示如何发送邮件。注意：这个例子假设你是在某个 :doc:`控制器 <../general/controllers>`
里面发送邮件。

::

	$this->load->library('email');

	$this->email->from('your@example.com', 'Your Name');
	$this->email->to('someone@example.com');
	$this->email->cc('another@another-example.com');
	$this->email->bcc('them@their-example.com');

	$this->email->subject('Email Test');
	$this->email->message('Testing the email class.');

	$this->email->send();

设置 Email 参数
=========================

有 21 种不同的参数可以用来对你发送的邮件进行配置。你可以像下面这样手工设置它们，
或者通过配置文件自动加载，见下文：

通过向邮件初始化函数传递一个包含参数的数组来设置参数，下面是个如何设置参数的例子::

	$config['protocol'] = 'sendmail';
	$config['mailpath'] = '/usr/sbin/sendmail';
	$config['charset'] = 'iso-8859-1';
	$config['wordwrap'] = TRUE;

	$this->email->initialize($config);

.. note:: 如果你不设置，大多数参数将使用默认值。

在配置文件中设置 Email 参数
------------------------------------------

如果你不喜欢使用上面的方法来设置参数，你可以将它们放到配置文件中。你只需要简单的创建一个新文件
email.php ，将 $config 数组放到该文件，然后保存到 config/email.php ，这样它将会自动被加载。
如果你使用配置文件的方式来设置参数，你就不需要使用 ``$this->email->initialize()`` 方法了。

Email 参数
=================

下表为发送邮件时所有可用的参数。

=================== ====================== ============================ =======================================================================
参数                  默认值                  选项                              描述
=================== ====================== ============================ =======================================================================
**useragent**       CodeIgniter            None                         用户代理（user agent）
**protocol**        mail                   mail, sendmail, or smtp      邮件发送协议
**mailpath**        /usr/sbin/sendmail     None                         服务器上 Sendmail 的实际路径
**smtp_host**       No Default             None                         SMTP 服务器地址
**smtp_user**       No Default             None                         SMTP 用户名
**smtp_pass**       No Default             None                         SMTP 密码
**smtp_port**       25                     None                         SMTP 端口
**smtp_timeout**    5                      None                         SMTP 超时时间（单位：秒）
**smtp_keepalive**  FALSE                  TRUE or FALSE (boolean)      是否启用 SMTP 持久连接
**smtp_crypto**     No Default             tls or ssl                   SMTP 加密方式
**wordwrap**        TRUE                   TRUE or FALSE (boolean)      是否启用自动换行
**wrapchars**       76                                                  自动换行时每行的最大字符数
**mailtype**        text                   text or html                 邮件类型。如果发送的是 HTML 邮件，必须是一个完整的网页，
                                                                        确保网页中没有使用相对路径的链接和图片地址，它们在邮件中不能正确显示。
**charset**         ``$config['charset']``                              字符集（utf-8, iso-8859-1 等）
**validate**        FALSE                  TRUE or FALSE (boolean)      是否验证邮件地址
**priority**        3                      1, 2, 3, 4, 5                Email 优先级（1 = 最高. 5 = 最低. 3 = 正常）
**crlf**            \\n                    "\\r\\n" or "\\n" or "\\r"   换行符（使用 "\r\n" 以遵守 RFC 822）
**newline**         \\n                    "\\r\\n" or "\\n" or "\\r"   换行符（使用 "\r\n" 以遵守 RFC 822）
**bcc_batch_mode**  FALSE                  TRUE or FALSE (boolean)      是否启用密送批处理模式（BCC Batch Mode）
**bcc_batch_size**  200                    None                         使用密送批处理时每一批邮件的数量
**dsn**             FALSE                  TRUE or FALSE (boolean)      是否启用服务器提示消息
=================== ====================== ============================ =======================================================================

取消自动换行
========================

如果你启用了自动换行（推荐遵守 RFC 822），然后你的邮件中又有一个超长的链接，那么它也会被自动换行，
会导致收件人无法点击该链接。CodeIgniter 允许你禁用部分内容的自动换行，像下面这样::

	The text of your email that
	gets wrapped normally.

	{unwrap}http://example.com/a_long_link_that_should_not_be_wrapped.html{/unwrap}

	More text that will be
	wrapped normally.


在你不想自动换行的内容前后使用 {unwrap} {/unwrap} 包起来。

***************
类参考
***************

.. php:class:: CI_Email

	.. php:method:: from($from[, $name = ''[, $return_path = NULL]])

		:param	string	$from: "From" e-mail address
		:param	string	$name: "From" display name
		:param	string	$return_path: Optional email address to redirect undelivered e-mail to
		:returns:	CI_Email instance (method chaining)
		:rtype:	CI_Email

		设置发件人 email 地址和名称::

			$this->email->from('you@example.com', 'Your Name');

		你还可以设置一个 Return-Path 用于重定向未收到的邮件::

			$this->email->from('you@example.com', 'Your Name', 'returned_emails@example.com');

		.. note:: 如果你使用的是 'smtp' 协议，不能使用 Return-Path 。

	.. php:method:: reply_to($replyto[, $name = ''])

		:param	string	$replyto: E-mail address for replies
		:param	string	$name: Display name for the reply-to e-mail address
		:returns:	CI_Email instance (method chaining)
		:rtype:	CI_Email

		设置邮件回复地址，如果没有提供这个信息，将会使用 :meth:from 函数中的值。例如::

			$this->email->reply_to('you@example.com', 'Your Name');

	.. php:method:: to($to)

		:param	mixed	$to: Comma-delimited string or an array of e-mail addresses
		:returns:	CI_Email instance (method chaining)
		:rtype:	CI_Email

		设置收件人 email 地址，地址可以是单个、一个以逗号分隔的列表或是一个数组::

			$this->email->to('someone@example.com');

		::

			$this->email->to('one@example.com, two@example.com, three@example.com');

		::

			$this->email->to(
				array('one@example.com', 'two@example.com', 'three@example.com')
			);

	.. php:method:: cc($cc)

		:param	mixed	$cc: Comma-delimited string or an array of e-mail addresses
		:returns:	CI_Email instance (method chaining)
		:rtype:	CI_Email

		设置抄送（CC）的 email 地址，和 "to" 方法一样，地址可以是单个、一个以逗号分隔的列表或是一个数组。

	.. php:method:: bcc($bcc[, $limit = ''])

		:param	mixed	$bcc: Comma-delimited string or an array of e-mail addresses
		:param	int	$limit: Maximum number of e-mails to send per batch
		:returns:	CI_Email instance (method chaining)
		:rtype:	CI_Email

		设置密送（BCC）的 email 地址，和 "to" 方法一样，地址可以是单个、一个以逗号分隔的列表或是一个数组。

		如果设置了 ``$limit`` 参数，将启用批处理模式，批处理模式可以同时发送一批邮件，每一批不超过设置的 ``$limit`` 值。

	.. php:method:: subject($subject)

		:param	string	$subject: E-mail subject line
		:returns:	CI_Email instance (method chaining)
		:rtype:	CI_Email

		设置 email 主题::

			$this->email->subject('This is my subject');

	.. php:method:: message($body)

		:param	string	$body: E-mail message body
		:returns:	CI_Email instance (method chaining)
		:rtype:	CI_Email

		设置 email 正文部分::

			$this->email->message('This is my message');

	.. php:method:: set_alt_message($str)

		:param	string	$str: Alternative e-mail message body
		:returns:	CI_Email instance (method chaining)
		:rtype:	CI_Email

		设置可选的 email 正文部分::

			$this->email->set_alt_message('This is the alternative message');

		如果你发送的是 HTML 格式的邮件，可以设置一个可选的正文部分。对于那些设置了不接受 HTML 格式的邮件的人来说，
		可以显示一段备选的不包含 HTML 格式的文本。如果你没有设置该参数，CodeIgniter 会自动从 HTML 格式邮件中删掉 HTML 标签。

	.. php:method:: set_header($header, $value)

		:param	string	$header: Header name
		:param	string	$value: Header value
		:returns:	CI_Email instance (method chaining)
		:rtype: CI_Email

		向 email 添加额外的头::

			$this->email->set_header('Header1', 'Value1');
			$this->email->set_header('Header2', 'Value2');

	.. php:method:: clear([$clear_attachments = FALSE])

		:param	bool	$clear_attachments: Whether or not to clear attachments
		:returns:	CI_Email instance (method chaining)
		:rtype: CI_Email

		将所有的 email 变量清空，当你在一个循环中发送邮件时，这个方法可以让你在每次发邮件之前将变量重置。

		::

			foreach ($list as $name => $address)
			{
				$this->email->clear();

				$this->email->to($address);
				$this->email->from('your@example.com');
				$this->email->subject('Here is your info '.$name);
				$this->email->message('Hi '.$name.' Here is the info you requested.');
				$this->email->send();
			}

		如果将参数设置为 TRUE ，邮件的附件也会被清空。

			$this->email->clear(TRUE);

	.. php:method:: send([$auto_clear = TRUE])

		:param	bool	$auto_clear: Whether to clear message data automatically
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		发送 email ，根据成功或失败返回布尔值 TRUE 或 FALSE ，可以在条件语句中使用::

			if ( ! $this->email->send())
			{
				// Generate error
			}

		如果发送成功，该方法将会自动清除所有的参数。如果不想清除，可以将参数置为 FALSE ::

		 	if ($this->email->send(FALSE))
		 	{
		 		// Parameters won't be cleared
		 	}

		.. note:: 为了使用 ``print_debugger()`` 方法，你必须避免清空 email 的参数。

	.. php:method:: attach($filename[, $disposition = ''[, $newname = NULL[, $mime = '']]])

		:param	string	$filename: File name
		:param	string	$disposition: 'disposition' of the attachment. Most
			email clients make their own decision regardless of the MIME
			specification used here. https://www.iana.org/assignments/cont-disp/cont-disp.xhtml
		:param	string	$newname: Custom file name to use in the e-mail
		:param	string	$mime: MIME type to use (useful for buffered data)
		:returns:	CI_Email instance (method chaining)
		:rtype:	CI_Email

		添加附件，第一个参数为文件的路径。要添加多个附件，可以调用该方法多次。例如::

			$this->email->attach('/path/to/photo1.jpg');
			$this->email->attach('/path/to/photo2.jpg');
			$this->email->attach('/path/to/photo3.jpg');

		要让附件使用默认的 Content-Disposition（默认为：attachment）将第二个参数留空，
		你也可以使用其他的 Content-Disposition ::

			$this->email->attach('image.jpg', 'inline');

		另外，你也可以使用 URL::

			$this->email->attach('http://example.com/filename.pdf');

		如果你想自定义文件名，可以使用第三个参数::

			$this->email->attach('filename.pdf', 'attachment', 'report.pdf');

		如果你想使用一段字符串来代替物理文件，你可以将第一个参数设置为该字符串，第三个参数设置为文件名，
		第四个参数设置为 MIME 类型::

			$this->email->attach($buffer, 'attachment', 'report.pdf', 'application/pdf');

	.. php:method:: attachment_cid($filename)

		:param	string	$filename: Existing attachment filename
		:returns:	Attachment Content-ID or FALSE if not found
		:rtype:	string
 
		设置并返回一个附件的 Content-ID ，可以让你将附件（图片）内联显示到 HTML 正文中去。
		第一个参数必须是一个已经添加到附件中的文件名。
		::
 
			$filename = '/img/photo1.jpg';
			$this->email->attach($filename);
			foreach ($list as $address)
			{
				$this->email->to($address);
				$cid = $this->email->attachment_cid($filename);
				$this->email->message('<img src='cid:". $cid ."' alt="photo1" />');
				$this->email->send();
			}

		.. note:: 每个 email 的 Content-ID 都必须重新创建，为了保证唯一性。

	.. php:method:: print_debugger([$include = array('headers', 'subject', 'body')])

		:param	array	$include: Which parts of the message to print out
		:returns:	Formatted debug data
		:rtype:	string

		返回一个包含了所有的服务器信息、email 头部信息、以及 email 信息的字符串。用于调试。

		你可以指定只返回消息的哪个部分，有效值有：**headers** 、 **subject** 和 **body** 。

		例如::

			// You need to pass FALSE while sending in order for the email data
			// to not be cleared - if that happens, print_debugger() would have
			// nothing to output.
			$this->email->send(FALSE);

			// Will only print the email headers, excluding the message subject and body
			$this->email->print_debugger(array('headers'));

		.. note:: 默认情况，所有的数据都会被打印出来。