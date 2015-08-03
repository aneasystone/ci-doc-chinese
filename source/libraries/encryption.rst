##################
加密类（新版）
##################

.. important:: 绝不要使用这个类或其他任何加密类来进行密码处理！密码应该是被*哈希*，
	你应该使用 PHP 自带的 `密码哈希扩展 <http://php.net/password>`_ 。


加密类提供了双向数据加密的方式，为了实现密码学意义上的安全，它使用了一些并非在所有系统上都可用的 PHP 的扩展，
要使用这个类，你的系统上必须安装了下面的扩展：

- `OpenSSL <http://php.net/openssl>`_ （以及 PHP 5.3.3）
- `MCrypt <http://php.net/mcrypt>`_ （要支持 `MCRYPT_DEV_URANDOM`）

只要有一点不满足，我们就无法为你提供足够高的安全性。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

****************************
使用加密类
****************************

初始化类
======================

正如 CodeIgniter 中的其他类一样，在你的控制器中使用 ``$this->load->library()``
方法来初始化加密类::

	$this->load->library('encryption');

初始化之后，加密类的对象就可以这样访问::

	$this->encryption

默认行为
================

默认情况下，加密类会通过你配置的 *encryption_key* 参数和 SHA512 HMAC 认证，
使用 AES-128 算法的 CBC 模式。

.. note:: 选择使用 AES-128 算法不仅是因为它已经被证明相当强壮，
	而且它也已经在不同的加密软件和编程语言 API 中广泛的使用了。

但是要注意的是，*encryption_key* 参数的用法可能并不是你想的那样。

如果你对密码学有点熟悉的话，你应该知道，使用 HMAC 算法认证也需要使用一个密钥，
而在加密的过程和认证的过程中使用相同的密钥可不是个好的做法。

正因为此，程序会从你的配置的 *encryption_key* 参数中派生出两个密钥来：
一个用于加密，另一个用于认证。这其实是通过一种叫做 `HKDF <<http://en.wikipedia.org/wiki/HKDF>>`_ 
（HMAC-based Key Derivation Function）的技术实现的。

设置 encryption_key 参数
===========================

加密密钥（ *encryption key* ）是用于控制加密过程的一小段信息，使用它可以对普通文本进行加密和解密。

这个过程可以保证只有你能对数据进行解密，其他人是看不到你的数据的，这其中的关键就是 加密密钥。
如果你使用了一个密钥来加密数据，那么就只能通过这个密钥来解密，所以你不仅应该仔细选择你的密钥，
还应该好好的保管好它，不要忘记了。

还有一点要注意的是，为了确保最高的安全性，这个密钥不仅 *应该* 越强壮越好，而且 *应该* 经常修改。
不过这在现实中很难做到，也不好实现，所以 CodeIgniter 提供了一个配置参数用于设置你的密钥，
这个密钥（几乎）每次都会用到。

不用说，你应该小心保管好你的密钥，如果有人得到了你的密钥，那么数据就能很容易的被解密。
如果你的服务器不在你的控制之下，想保证你的密钥绝对安全是不可能的，
所以在在你使用密钥对敏感数据（譬如信用卡号码）进行加密之前，请再三斟酌。

你的加密密钥的长度 **必须** 满足正在使用的加密算法允许的长度。例如，AES-128 算法最长支持
128 位（16 字节）。下面有一个表列出了不同算法支持的密钥长度。

你所使用的密钥应该越随机越好，它不能是一个普通的文本字符串，经过哈希函数处理过也不行。
为了生成一个合适的密钥，你应该使用加密类提供的 ``create_key()`` 方法::

	// $key will be assigned a 16-byte (128-bit) random key
	$key = $this->encryption->create_key(16);

密钥可以保存在 *application/config/config.php* 配置文件中，或者你也可以设计你自己的存储机制，
然后加密解密的时候动态的去获取它。

如果要保存在配置文件 *application/config/config.php* 中，可以打开该文件，然后设置::

	$config['encryption_key'] = 'YOUR KEY';

你会发现 ``create_key()`` 方法返回的是二进制数据，没办法复制粘贴，所以你可能还需要使用
``bin2hex()`` 、 ``hex2bin()`` 或 Base64 编码来更好的处理密钥数据。例如::

	// Get a hex-encoded representation of the key:
	$key = bin2hex($this->encryption->create_key(16));

	// Put the same value in your config with hex2bin(),
	// so that it is still passed as binary to the library:
	$config['encryption_key'] = hex2bin(<your hex-encoded key>);

.. _ciphers-and-modes:

支持的加密算法和模式
======================================

可移植的算法（Portable ciphers）
--------------------------------

因为 MCrypt 和 OpenSSL （我们也称之为“驱动”）支持的加密算法不同，而且实现方式也不太一样，
CodeIgniter 将它们设计成一种可移植的方式来使用，换句话说，你可以交换使用它们两个，
至少对它们两个驱动都支持的算法来说是这样。

而且 CodeIgniter 的实现也和其他编程语言和类库的标准实现一致。

下面是可移植算法的清单，其中 "CodeIgniter 名称" 一栏就是你在使用加密类的时候使用的名称：

======================== ================== ============================ ===============================
算法名称                 CodeIgniter 名称   密钥长度 （位 / 字节）       支持的模式
======================== ================== ============================ ===============================
AES-128 / Rijndael-128   aes-128            128 / 16                     CBC, CTR, CFB, CFB8, OFB, ECB
AES-192                  aes-192            192 / 24                     CBC, CTR, CFB, CFB8, OFB, ECB
AES-256                  aes-256            256 / 32                     CBC, CTR, CFB, CFB8, OFB, ECB
DES                      des                56 / 7                       CBC, CFB, CFB8, OFB, ECB
TripleDES                tripledes          56 / 7, 112 / 14, 168 / 21   CBC, CFB, CFB8, OFB
Blowfish                 blowfish           128-448 / 16-56              CBC, CFB, OFB, ECB
CAST5 / CAST-128         cast5              88-128 / 11-16               CBC, CFB, OFB, ECB
RC4 / ARCFour            rc4                40-2048 / 5-256              Stream
======================== ================== ============================ ===============================

.. important:: 由于 MCrypt 的内部实现，如果你提供了一个长度不合适的密钥，它会使用另一种不同的算法来加密，
	这将和你配置的算法不一致，所以要特别注意这一点！

.. note:: 上表中还有一点要澄清，Blowfish、CAST5 和 RC4 算法支持可变长度的密钥，也就是说，
	只要密钥的长度在指定范围内都是可以的。

.. note:: 尽管 CAST5 支持的密钥的长度可以小于 128 位（16 字节），其实实际上，根据 `RFC 2144
	<http://tools.ietf.org/rfc/rfc2144.txt>`_ 我们知道，它会用 0 进行补齐到最大长度。

.. note:: Blowfish 算法支持最短 32 位（4 字节）的密钥，但是经过我们的测试发现，只有密钥长度大于等于 128 位（16 字节）
	时，才可以很好的同时支持 MCrypt 和 OpenSSL ，再说，设置这么短的密钥也不是好的做法。

特定驱动的算法（Driver-specific ciphers）
----------------------------------------------

正如前面所说，MCrypt 和 OpenSSL 支持不同的加密算法，所以你也可以选择下面这些只针对某一特定驱动的算法。
但是为了移植性考虑，而且这些算法也没有经过彻底测试，我们并不建议你使用这些算法。

============== ========= ============================== =========================================
算法名称       驱动      密钥长度 （位 / 字节）         支持的模式
============== ========= ============================== =========================================
AES-128        OpenSSL   128 / 16                       CBC, CTR, CFB, CFB8, OFB, ECB, XTS
AES-192        OpenSSL   192 / 24                       CBC, CTR, CFB, CFB8, OFB, ECB, XTS
AES-256        OpenSSL   256 / 32                       CBC, CTR, CFB, CFB8, OFB, ECB, XTS
Rijndael-128   MCrypt    128 / 16, 192 / 24, 256 / 32   CBC, CTR, CFB, CFB8, OFB, OFB8, ECB
Rijndael-192   MCrypt    128 / 16, 192 / 24, 256 / 32   CBC, CTR, CFB, CFB8, OFB, OFB8, ECB
Rijndael-256   MCrypt    128 / 16, 192 / 24, 256 / 32   CBC, CTR, CFB, CFB8, OFB, OFB8, ECB
GOST           MCrypt    256 / 32                       CBC, CTR, CFB, CFB8, OFB, OFB8, ECB
Twofish        MCrypt    128 / 16, 192 / 24, 256 / 32   CBC, CTR, CFB, CFB8, OFB, OFB8, ECB
CAST-128       MCrypt    40-128 / 5-16                  CBC, CTR, CFB, CFB8, OFB, OFB8, ECB
CAST-256       MCrypt    128 / 16, 192 / 24, 256 / 32   CBC, CTR, CFB, CFB8, OFB, OFB8, ECB
Loki97         MCrypt    128 / 16, 192 / 24, 256 / 32   CBC, CTR, CFB, CFB8, OFB, OFB8, ECB
SaferPlus      MCrypt    128 / 16, 192 / 24, 256 / 32   CBC, CTR, CFB, CFB8, OFB, OFB8, ECB
Serpent        MCrypt    128 / 16, 192 / 24, 256 / 32   CBC, CTR, CFB, CFB8, OFB, OFB8, ECB
XTEA           MCrypt    128 / 16                       CBC, CTR, CFB, CFB8, OFB, OFB8, ECB
RC2            MCrypt    8-1024 / 1-128                 CBC, CTR, CFB, CFB8, OFB, OFB8, ECB
RC2            OpenSSL   8-1024 / 1-128                 CBC, CFB, OFB, ECB
Camellia-128   OpenSSL   128 / 16                       CBC, CFB, CFB8, OFB, ECB
Camellia-192   OpenSSL   192 / 24                       CBC, CFB, CFB8, OFB, ECB
Camellia-256   OpenSSL   256 / 32                       CBC, CFB, CFB8, OFB, ECB
Seed           OpenSSL   128 / 16                       CBC, CFB, OFB, ECB
============== ========= ============================== =========================================

.. note:: 如果你要使用这些算法，你只需将它的名称以小写形式传递给加密类即可。

.. note:: 你可能已经注意到，所有的 AES 算法（以及 Rijndael-128 算法）也在上面的可移植算法列表中出现了，
	这是因为这些算法支持不同的模式。还有很重要的一点是，在使用 128 位的密钥时，AES-128 和 Rijndael-128
	算法其实是一样的。

.. note:: CAST-128 / CAST-5 算法也在两个表格都出现了，这是因为当密钥长度小于等于 80 位时，
	OpenSSL 的实现貌似有问题。

.. note:: 列表中可以看到 RC2 算法同时被 MCrypt 和 OpenSSL 支持，但是两个驱动对它的实现方式是不一样的，
	而且也是不能移植的。我们只找到了一条关于这个的不确定的消息可能是 MCrypt 的实现有问题。

.. _encryption-modes:

加密模式
----------------

加密算法的不同模式有着不同的特性，它们有着不同的目的，有的可能比另一些更强壮，有的可能速度更快，
有的可能提供了额外的功能。
我们并不打算深入研究这个，这应该是密码学专家做的事。下表将向我们普通的用户列出一些简略的参考信息。
如果你是个初学者，直接使用 CBC 模式就可以了，一般情况下它已经足够强壮和安全，并且已经被广泛接受。

=========== ================== ================= ===================================================================================================================================================
模式名称    CodeIgniter 名称   支持的驱动        备注
=========== ================== ================= ===================================================================================================================================================
CBC         cbc                MCrypt, OpenSSL   安全的默认选择
CTR         ctr                MCrypt, OpenSSL   理论上比 CBC 更好，但并没有广泛使用
CFB         cfb                MCrypt, OpenSSL   N/A
CFB8        cfb8               MCrypt, OpenSSL   和 CFB 一样，但是使用 8 位模式（不推荐）
OFB         ofb                MCrypt, OpenSSL   N/A
OFB8        ofb8               MCrypt            和 OFB 一样，但是使用 8 位模式（不推荐）
ECB         ecb                MCrypt, OpenSSL   忽略 IV （不推荐）
XTS         xts                OpenSSL           通常用来加密可随机访问的数据，如 RAM 或 硬盘
Stream      stream             MCrypt, OpenSSL   这其实并不是一种模式，只是表明使用了流加密，通常在 算法+模式 的初始化过程中会用到。
=========== ================== ================= ===================================================================================================================================================

消息长度
==============

有一点对你来说可能很重要，加密的字符串通常要比原始的文本字符串要长（取决于算法）。

这个会取决于加密所使用的算法，添加到密文上的 IV ，以及添加的 HMAC 认证信息。
另外，为了保证传输的安全性，加密消息还会被 Base64 编码。

当你选择数据保存机制时请记住这一点，譬如 Cookie 只能存储 4k 的信息。

.. _configuration:

配置类库
=======================

考虑到可用性，性能，以及一些历史原因，加密类使用了和老的 :doc:`加密类 <encrypt>` 一样的驱动、
加密算法、模式 和 密钥。

上面的 "默认行为" 一节已经提到，系统将自动检测驱动（OpenSSL 优先级要高点），使用 CBC 模式的
AES-128 算法，以及 ``$config['encryption_key']`` 参数。

如果你想改变这点，你需要使用 ``initialize()`` 方法，它的参数为一个关联数组，每一项都是可选：

======== ===============================================
选项     可能的值
======== ===============================================
driver   'mcrypt', 'openssl'
cipher   算法名称（参见 :ref:`ciphers-and-modes`）
mode     加密模式（参见 :ref:`encryption-modes`）
key      加密密钥 
======== ===============================================

例如，如果你想将加密算法和模式改为 AES-126 CTR ，可以这样::

	$this->encryption->initialize(
		array(
			'cipher' => 'aes-256',
			'mode' => 'ctr',
			'key' => '<a 32-character random string>'
		)
	);

另外，我们也可以设置一个密钥，如前文所说，针对所使用的算法选择一个合适的密钥非常重要。

我们还可以修改驱动，如果你两种驱动都支持，但是出于某种原因你想使用 MCrypt 来替代 OpenSSL ::

	// Switch to the MCrypt driver
	$this->encryption->initialize(array('driver' => 'mcrypt'));

	// Switch back to the OpenSSL driver
	$this->encryption->initialize(array('driver' => 'openssl'));

对数据进行加密与解密
==============================

使用已配置好的参数来对数据进行加密和解密是非常简单的，你只要将字符串传给 ``encrypt()`` 
和/或 ``decrypt()`` 方法即可::

	$plain_text = 'This is a plain-text message!';
	$ciphertext = $this->encryption->encrypt($plain_text);

	// Outputs: This is a plain-text message!
	echo $this->encryption->decrypt($ciphertext);

这样就行了！加密类会为你完成所有必须的操作并确保安全，你根本不用关系细节。

.. important:: 两个方法在遇到错误时都会返回 FALSE ，如果是 ``encrypt()`` 返回 FALSE ，
	那么只可能是配置参数错了。在生产代码中一定要对 ``decrypt()`` 方法进行检查。

实现原理
------------

如果你非要知道整个过程的实现步骤，下面是内部的实现：

- ``$this->encryption->encrypt($plain_text)``

  #. 通过 HKDF 和 SHA-512 摘要算法，从你配置的 *encryption_key* 参数中获取两个密钥：加密密钥 和 HMAC 密钥。

  #. 生成一个随机的初始向量（IV）。

  #. 使用上面的加密密钥和 IV ，通过 AES-128 算法的 CBC 模式（或其他你配置的算法和模式）对数据进行加密。

  #. 将 IV 附加到密文后。

  #. 对结果进行 Base64 编码，这样就可以安全的保存和传输它，而不用担心字符集问题。

  #. 使用 HMAC 密钥生成一个 SHA-512 HMAC 认证消息，附加到 Base64 字符串后，以保证数据的完整性。

- ``$this->encryption->decrypt($ciphertext)``

  #. 通过 HKDF 和 SHA-512 摘要算法，从你配置的 *encryption_key* 参数中获取两个密钥：加密密钥 和 HMAC 密钥。
     由于 *encryption_key* 不变，所以生成的结果和上面 ``encrypt()`` 方法生成的结果是一样的，否则你没办法解密。

  #. 检查字符串的长度是否足够长，并从字符串中分离出 HMAC ，然后验证是否一致（这可以防止时序攻击（timing attack）），
     如果验证失败，返回 FALSE 。

  #. 进行 Base64 解码。

  #. 从密文中分离出 IV ，并使用 IV 和 加密密钥对数据进行解密。

.. _custom-parameters:

使用自定义参数
-----------------------

假设你需要和另一个系统交互，这个系统不受你的控制，而且它使用了其他的方法来加密数据，
加密的方式和我们上面介绍的流程不一样。

在这种情况下，加密类允许你修改它的加密和解密的流程，这样你就可以简单的调整成自己的解决方案。

.. note:: 通过这种方式，你可以不用在配置文件中配置 *encryption_key* 就能使用加密类。

你所需要做的就是传一个包含一些参数的关联数组到 ``encrypt()`` 或 ``decrypt()`` 方法，下面是个例子::

	// Assume that we have $ciphertext, $key and $hmac_key
	// from on outside source

	$message = $this->encryption->decrypt(
		$ciphertext,
		array(
			'cipher' => 'blowfish',
			'mode' => 'cbc',
			'key' => $key,
			'hmac_digest' => 'sha256',
			'hmac_key' => $hmac_key
		)
	);

在上面的例子中，我们对一段使用 CBC 模式的 Blowfish 算法加密的消息进行解密，并使用 SHA-256 HMAC 认证方式。

.. important:: 注意在这个例子中 'key' 和 'hmac_key' 参数都要指定，当使用自定义参数时，加密密钥和 HMAC 密钥
	不再是默认的那样从配置参数中自动获取的了。

下面是所有可用的选项。

但是，除非你真的需要这样做，并且你知道你在做什么，否则我们建议你不要修改加密的流程，因为这会影响安全性，
所以请谨慎对待。

============= =============== ============================= ======================================================
选项          默认值          必须的 / 可选的               描述
============= =============== ============================= ======================================================
cipher        N/A             Yes                           加密算法（参见 :ref:`ciphers-and-modes`）
mode          N/A             Yes                           加密模式（参见 :ref:`encryption-modes`）
key           N/A             Yes                           加密密钥
hmac          TRUE            No                            是否使用 HMAC
                                                            布尔值，如果为 FALSE ，*hmac_digest* 和 *hmac_key* 将被忽略
hmac_digest   sha512          No                            HMAC 消息摘要算法（参见 :ref:`digests`）
hmac_key      N/A             Yes，除非 *hmac* 设为 FALSE   HMAC 密钥
raw_data      FALSE           No                            加密文本是否保持原样
                                                            布尔值，如果为 TRUE ，将不执行 Base64 编码和解码操作
                                                            HMAC 也不会是十六进制字符串
============= =============== ============================= ======================================================

.. important:: ``encrypt()`` and ``decrypt()`` will return FALSE if
	a mandatory parameter is not provided or if a provided
	value is incorrect. This includes *hmac_key*, unless *hmac*
	is set to FALSE.

.. _digests:

支持的 HMAC 认证算法
----------------------------------------

对于 HMAC 消息认证，加密类支持使用 SHA-2 家族的算法：

=========== ==================== ============================
算法        原始长度（字节）     十六进制编码长度（字节）
=========== ==================== ============================
sha512      64                   128
sha384      48                   96
sha256      32                   64
sha224      28                   56
=========== ==================== ============================

之所以没有包含一些其他的流行算法，譬如 MD5 或 SHA1 ，是因为这些算法目前已被证明不够安全，
我们并不鼓励使用它们。如果你非要使用这些算法，简单的使用 PHP 的原生函数
`hash_hmac() <http://php.net/manual/en/function.hash-hmac.php>`_ 也可以。

当未来出现广泛使用的更好的算法时，我们自然会将其添加进去。

***************
类参考
***************

.. php:class:: CI_Encryption

	.. php:method:: initialize($params)

		:param	array	$params: Configuration parameters
		:returns:	CI_Encryption instance (method chaining)
		:rtype:	CI_Encryption

		初始化加密类的配置，使用不同的驱动，算法，模式 或 密钥。

		例如::

			$this->encryption->initialize(
				array('mode' => 'ctr')
			);

		请参考 :ref:`configuration` 一节了解详细信息。

	.. php:method:: encrypt($data[, $params = NULL])

		:param	string	$data: Data to encrypt
		:param	array	$params: Optional parameters
		:returns:	Encrypted data or FALSE on failure
		:rtype:	string

		对输入数据进行加密，并返回密文。

		例如::

			$ciphertext = $this->encryption->encrypt('My secret message');

		请参考 :ref:`custom-parameters` 一节了解更多参数信息。

	.. php:method:: decrypt($data[, $params = NULL])

		:param	string	$data: Data to decrypt
		:param	array	$params: Optional parameters
		:returns:	Decrypted data or FALSE on failure
		:rtype:	string

		对输入数据进行解密，并返回解密后的文本。

		例如::

			echo $this->encryption->decrypt($ciphertext);

		请参考 :ref:`custom-parameters` 一节了解更多参数信息。

	.. php:method:: create_key($length)

		:param	int	$length: Output length
		:returns:	A pseudo-random cryptographic key with the specified length, or FALSE on failure
		:rtype:	string

		从操作系统获取随机数据（例如 /dev/urandom），并生成加密密钥。

	.. php:method:: hkdf($key[, $digest = 'sha512'[, $salt = NULL[, $length = NULL[, $info = '']]]])

		:param	string	$key: Input key material
		:param	string	$digest: A SHA-2 family digest algorithm
		:param	string	$salt: Optional salt
		:param	int	$length: Optional output length
		:param	string	$info: Optional context/application-specific info
		:returns:	A pseudo-random key or FALSE on failure
		:rtype:	string

		从一个密钥生成另一个密钥（较弱的密钥）。

		这是内部使用的一个方法，用于从配置的 *encryption_key* 参数生成一个加密密钥和 HMAC 密钥。

		将这个方法公开，是为了可能会在其他地方使用到。关于这个算法的描述可以看
		`RFC 5869 <https://tools.ietf.org/rfc/rfc5869.txt>`_ 。

		和 RFC 5869 描述不同的是，这个方法不支持 SHA1 。

		例如::

			$hmac_key = $this->encryption->hkdf(
				$key,
				'sha512',
				NULL,
				NULL,
				'authentication'
			);

			// $hmac_key is a pseudo-random key with a length of 64 bytes