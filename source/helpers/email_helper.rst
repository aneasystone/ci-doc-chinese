############
邮件辅助库
############

邮件辅助库文件包含了用于处理邮件的一些函数。欲了解关于邮件更全面的解决方案，
可以参考 CodeIgniter 的 :doc:`Email 类 <../libraries/email>` 。

.. important:: 不鼓励继续使用邮件辅助库，这个库当前仅是为了向前兼容而存在。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

加载辅助库
===================

该辅助库通过下面的代码加载::

	$this->load->helper('email');

可用函数
===================

该辅助库有下列可用函数：

.. php:function:: valid_email($email)

	:param	string	$email: E-mail address
	:returns:	TRUE if a valid email is supplied, FALSE otherwise
	:rtype:	bool

	检查 Email 地址格式是否正确，注意该函数只是简单的检查它的格式是否正确，
	并不能保证该 Email 地址能接受到邮件。

	Example::

		if (valid_email('email@somesite.com'))
		{
			echo 'email is valid';
		}
		else
		{
			echo 'email is not valid';
		}

	.. note:: 该函数实际上就是调用 PHP 原生的 ``filter_var()`` 函数而已::

		(bool) filter_var($email, FILTER_VALIDATE_EMAIL);

.. php:function:: send_email($recipient, $subject, $message)

	:param	string	$recipient: E-mail address
	:param	string	$subject: Mail subject
	:param	string	$message: Message body
	:returns:	TRUE if the mail was successfully sent, FALSE in case of an error
	:rtype:	bool

	使用 PHP 函数 `mail() <http://php.net/function.mail>`_ 发送邮件。

	.. note:: 该函数实际上就是调用 PHP 原生的 ``mail()`` 函数而已

		::

			mail($recipient, $subject, $message);

	欲了解关于邮件更全面的解决方案，可以参考 CodeIgniter 的 :doc:`Email 类 <../libraries/email>` 。