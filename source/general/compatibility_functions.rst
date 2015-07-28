#######################
兼容性函数
#######################

CodeIgniter 提供了一系列兼容性函数可以让你使用，它们只有在高版本的 PHP 中才有，
或者需要依赖其他的扩展才有。

由于是自己实现的，这些函数本身也可能有它自己的依赖性，但如果你的 PHP 中不提供这些函数时，
这些函数还是有用的。

.. note:: 和 `公用函数 <common_functions>` 一样，兼容性函数也一直可以访问，只要满足了他们的依赖条件。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

****************
密码哈希
****************

这几个兼容性函数移植了 PHP 标准的 `密码哈希扩展 <http://php.net/password>`_ 的实现，
这些函数只有在 PHP 5.5 以后的版本中才有。

依赖性
============

- PHP 5.3.7
- ``crypt()`` 函数需支持 ``CRYPT_BLOWFISH``

常量
=========

- ``PASSWORD_BCRYPT``
- ``PASSWORD_DEFAULT``

函数参考
==================

.. php:function:: password_get_info($hash)

	:param	string	$hash: Password hash
	:returns:	Information about the hashed password
	:rtype:	array

	更多信息，请参考 `PHP 手册中的 password_get_info() 函数 <http://php.net/password_get_info>`_

.. php:function:: password_hash($password, $algo[, $options = array()])

	:param	string	$password: Plain-text password
	:param	int	$algo: Hashing algorithm
	:param	array	$options: Hashing options
	:returns:	Hashed password or FALSE on failure
	:rtype:	string

	更多信息，请参考 `PHP 手册中的 password_hash() 函数 <http://php.net/password_hash>`_

	.. note:: 除非提供了你自己的有效盐值，该函数会依赖于一个可用的 CSPRNG 源（密码学安全的伪随机数生成器），
		下面列表中的每一个都可以满足这点：
		- ``mcrypt_create_iv()`` with ``MCRYPT_DEV_URANDOM``
		- ``openssl_random_pseudo_bytes()``
		- /dev/arandom
		- /dev/urandom

.. php:function:: password_needs_rehash()

	:param	string	$hash: Password hash
	:param	int	$algo: Hashing algorithm
	:param	array	$options: Hashing options
	:returns:	TRUE if the hash should be rehashed to match the given algorithm and options, FALSE otherwise
	:rtype:	bool

	更多信息，请参考 `PHP 手册中的 password_needs_rehash() 函数 <http://php.net/password_needs_rehash>`_

.. php:function:: password_verify($password, $hash)

	:param	string	$password: Plain-text password
	:param	string	$hash: Password hash
	:returns:	TRUE if the password matches the hash, FALSE if not
	:rtype:	bool

	更多信息，请参考 `PHP 手册中的 password_verify() 函数 <http://php.net/password_verify>`_

*********************
哈希（信息摘要）
*********************

兼容性函数移植了 ``hash_equals()`` 和 ``hash_pbkdf2()`` 的实现，
这两函数分别在 PHP 5.6 和 PHP 5.5 以后的版本中才有。

依赖性
============

- 无

函数参考
==================

.. php:function:: hash_equals($known_string, $user_string)

	:param	string	$known_string: Known string
	:param	string	$user_string: User-supplied string
	:returns:	TRUE if the strings match, FALSE otherwise
	:rtype:	string

	更多信息，请参考 `PHP 手册中的 hash_equals() 函数 <http://php.net/hash_equals>`_

.. php:function:: hash_pbkdf2($algo, $password, $salt, $iterations[, $length = 0[, $raw_output = FALSE]])

	:param	string	$algo: Hashing algorithm
	:param	string	$password: Password
	:param	string	$salt: Hash salt
	:param	int	$iterations: Number of iterations to perform during derivation
	:param	int	$length: Output string length
	:param	bool	$raw_output: Whether to return raw binary data
	:returns:	Password-derived key or FALSE on failure
	:rtype:	string

	更多信息，请参考 `PHP 手册中的 hash_pbkdf2() 函数 <http://php.net/hash_pbkdf2>`_

****************
多字节字符串
****************

这一系列兼容性函数提供了对 PHP 的 `多字节字符串扩展 <http://php.net/mbstring>`_ 的有限支持，
由于可选的解决方法有限，所以只有几个函数是可用的。

.. note:: 如果没有指定字符集参数，默认使用 ``$config['charset']`` 配置。

依赖性
============

- `iconv <http://php.net/iconv>`_ 扩展

.. important:: 这个依赖是可选的，无论 iconv 扩展是否存在，这些函数都已经定义了，
	如果 iconv 扩展不可用，它们会降级到非多字节字符串的函数版本。

.. important:: 当设置了字符集时，该字符集必须被 iconv 支持，并且要设置成它可以识别的格式。

.. note:: 如果你需要判断是否支持真正的多字节字符串扩展，可以使用 ``MB_ENABLED`` 常量。

函数参考
==================

.. php:function:: mb_strlen($str[, $encoding = NULL])

	:param	string	$str: Input string
	:param	string	$encoding: Character set
	:returns:	Number of characters in the input string or FALSE on failure
	:rtype:	string

	更多信息，请参考 `PHP 手册中的 mb_strlen() 函数 <http://php.net/mb_strlen>`_

.. php:function:: mb_strpos($haystack, $needle[, $offset = 0[, $encoding = NULL]])

	:param	string	$haystack: String to search in
	:param	string	$needle: Part of string to search for
	:param	int	$offset: Search offset
	:param	string	$encoding: Character set
	:returns:	Numeric character position of where $needle was found or FALSE if not found
	:rtype:	mixed

	更多信息，请参考 `PHP 手册中的 mb_strpos() 函数 <http://php.net/mb_strpos>`_

.. php:function:: mb_substr($str, $start[, $length = NULL[, $encoding = NULL]])

	:param	string	$str: Input string
	:param	int	$start: Position of first character
	:param	int	$length: Maximum number of characters
	:param	string	$encoding: Character set
	:returns:	Portion of $str specified by $start and $length or FALSE on failure
	:rtype:	string

	更多信息，请参考 `PHP 手册中的 mb_substr() 函数 <http://php.net/mb_substr>`_

******************
标准函数
******************

这一系列兼容性函数提供了一些高版本的 PHP 中才有的标准函数。

依赖性
============

- None

函数参考
==================

.. php:function:: array_column(array $array, $column_key[, $index_key = NULL])

	:param	array	$array: Array to fetch results from
	:param	mixed	$column_key: Key of the column to return values from
	:param	mixed	$index_key: Key to use for the returned values
	:returns:	An array of values representing a single column from the input array
	:rtype:	array

	更多信息，请参考 `PHP 手册中的 array_column() 函数 <http://php.net/array_column>`_

.. php:function:: array_replace(array $array1[, ...])

	:param	array	$array1: Array in which to replace elements
	:param	array	...: Array (or multiple ones) from which to extract elements
	:returns:	Modified array
	:rtype:	array

	更多信息，请参考 `PHP 手册中的 array_replace() 函数 <http://php.net/array_replace>`_

.. php:function:: array_replace_recursive(array $array1[, ...])

	:param	array	$array1: Array in which to replace elements
	:param	array	...: Array (or multiple ones) from which to extract elements
	:returns:	Modified array
	:rtype:	array

	更多信息，请参考 `PHP 手册中的 array_replace_recursive() 函数 <http://php.net/array_replace_recursive>`_

	.. important:: 只有 PHP 原生的函数才可以检测到无穷递归，如果你使用的是 PHP 5.3+ ，使用时要担心引用！

.. php:function:: hex2bin($data)

	:param	array	$data: Hexadecimal representation of data
	:returns:	Binary representation of the given data
	:rtype:	string

	更多信息，请参考 `PHP 手册中的 hex2bin() 函数 <http://php.net/hex2bin>`_

.. php:function:: quoted_printable_encode($str)

	:param	string	$str: Input string
	:returns:	8bit-encoded string
	:rtype:	string

	更多信息，请参考 `PHP 手册中的 quoted_printable_encode() 函数 <http://php.net/quoted_printable_encode>`_
