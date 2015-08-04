##################################
XML-RPC 与 XML-RPC 服务器类
##################################

CodeIgniter 的 XML-RPC  类允许你向另一个服务器发送请求，
或者建立一个你自己的 XML-RPC 服务器来接受请求。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

***********************
什么是 XML-RPC ？
***********************

这是一种在两台计算机之间使用 XML 通过互联网进行通信的简单方法。
一台计算机 , 我们称之为客户端 , 发送一个 XML-RPC 请求给另外一台计算机，
我们称之为服务器，当服务器收到请求时，对其进行处理然后将结果返回给客户端。

例如，使用 MetaWeblog API 时，XML-RPC 客户端（通常是桌面发布工具）
将会发送请求到你站点上的 XML-RPC 服务器，这个请求可能是发布一篇新博客，
或者编辑一篇已有的博客。当 XML-RPC 服务器收到该请求时，它会决定使用
哪个类和方法来处理该请求，请求处理完成后，服务器将发送一条回复消息。

关于 XML-RPC 的规范，你可以查看 `XML-RPC <http://www.xmlrpc.com/>`_ 的网站。

***********************
使用 XML-RPC 类
***********************

初始化类
======================

跟 CodeIgniter 中的其他类一样，可以在你的控制器中使用 ``$this->load->library()`` 
方法加载 XML-RPC 类和 XML-RPC 服务器类。

加载 XML-RPC 类如下::

	$this->load->library('xmlrpc');

一旦加载，XML-RPC 类就可以像下面这样使用::

	$this->xmlrpc

加载 XML-RPC 服务器类如下::

	$this->load->library('xmlrpc');
	$this->load->library('xmlrpcs');

一旦加载，XML-RPC 服务器类就可以像下面这样使用::

	$this->xmlrpcs

.. note:: 当使用 XML-RPC 服务器类时，xmlrpc 和 xmlrpcs 都需要加载。

发送 XML-RPC 请求
========================

向 XML-RPC 服务器发送一个请求，你需要指定以下信息：

-  服务器的 URL
-  你想要调用的服务器方法
-  **请求** 数据（下面解释）

下面是个基本的例子，向 `Ping-o-Matic <http://pingomatic.com/>`_
发送一个简单的 Weblogs.com ping 请求。

::

	$this->load->library('xmlrpc');

	$this->xmlrpc->server('http://rpc.pingomatic.com/', 80);
	$this->xmlrpc->method('weblogUpdates.ping');

	$request = array('My Photoblog', 'http://www.my-site.com/photoblog/');
	$this->xmlrpc->request($request);

	if ( ! $this->xmlrpc->send_request())
	{
		echo $this->xmlrpc->display_error();
	}

解释
-----------

上面的代码初始化了一个 XML-RPC 类，并设置了服务器 URL 和要调用的方法
（weblogUpdates.ping）。然后通过 request() 方法编译请求，
例子中请求是一个数组（标题和你网站的 URL）。最后，使用 send_request()
方法发送完整的请求。如果发送请求方法返回 FALSE ，我们会显示出 XML-RPC
服务器返回的错误信息。

请求解析
====================

XML-RPC 请求就是你发送给 XML-RPC 服务器的数据，请求中的每一个数据也被称为请求参数。
上面的例子中有两个参数：你网站的 URL 和 标题。当 XML-RPC 服务器收到请求后，
它会查找它所需要的参数。

请求参数必须放在一个数组中，且数组中的每个参数都必须是 7 种数据类型中的一种
（string、number、date 等），如果你的参数不是 string 类型，你必须在请求数组中
指定它的数据类型。

下面是三个参数的简单例子::

	$request = array('John', 'Doe', 'www.some-site.com');
	$this->xmlrpc->request($request);

如果你的数据类型不是 string ，或者你有几个不同类型的数据，那么你需要将
每个参数放到它单独的数组中，并在数组的第二位声明其数据类型::

	$request = array(
		array('John', 'string'),
		array('Doe', 'string'),
		array(FALSE, 'boolean'),
		array(12345, 'int')
	); 
	$this->xmlrpc->request($request);

下面的 `数据类型 <#datatypes>`_ 一节列出了所有支持的数据类型。

创建一个 XML-RPC 服务器
==========================

XML-RPC 服务器扮演着类似于交通警察的角色，等待进入的请求，
并将它们转到恰当的函数进行处理。

要创建你自己的 XML-RPC 服务器，你需要先在负责处理请求的控制器中初始化
XML-RPC 服务器类，然后设置一个映射数组，用于将请求转发到合适的类和方法，
以便进行处理。

下面是个例子::

	$this->load->library('xmlrpc');
	$this->load->library('xmlrpcs');

	$config['functions']['new_post'] = array('function' => 'My_blog.new_entry');
	$config['functions']['update_post'] = array('function' => 'My_blog.update_entry');
	$config['object'] = $this;

	$this->xmlrpcs->initialize($config);
	$this->xmlrpcs->serve();

上例中包含了两个服务器允许的请求方法，数组的左边是允许的方法名，
数组的右边是当请求该方法时，将会映射到的类和方法。

其中，'object' 是个特殊的键，用于传递一个实例对象，当映射的方法无法使用
CodeIgniter 超级对象时，它将是必须的。

换句话说，如果 XML-RPC 客户端发送一个请求到 new\_post 方法，
你的服务器会加载 My\_blog 类并调用 new\_entry 函数。如果这个请求是到 
``update_post`` 方法的，那么你的服务器会加载 My\_blog 类并调用 
``update_entry`` 方法。

上面例子中的函数名是任意的。你可以决定这些函数在你的服务器上叫什么名字，
如果你使用的是标准的 API，比如 Blogger 或者 MetaWeblog 的 API，
你必须使用标准的函数名。

这里还有两个附加的配置项，可以在服务器类初始化时配置使用。debug 设为 TRUE 以便调试，
``xss_clean`` 可被设置为 FALSE 以避免数据被安全类库的 ``xss_clean`` 函数过滤。

处理服务器请求
==========================

当 XML-RPC 服务器收到请求并加载类与方法来处理时，它会接收一个包含客户端发送的数据参数。

在上面的例子中，如果请求的是 new_post 方法，服务器请求的类与方法会像这样::

	class My_blog extends CI_Controller {

		public function new_post($request)
		{

		}
	}

$request 变量是一个由服务端汇集的对象，包含由 XML-RPC 客户端发送来的数据。
使用该对象可以让你访问到请求参数以便处理请求。请求处理完成后，
发送一个响应返回给客户端。

下面是一个实际的例子，使用 Blogger API 。Blogger API 中的一个方法是 getUserInfo()，
XML-RPC 客户端可以使用该方法发送用户名和密码到服务器，在服务器返回的数据中，
会包含该用户的信息（昵称，用户 ID，Email 地址等等）。下面是处理的代码::

	class My_blog extends CI_Controller {

		public function getUserInfo($request)
		{
			$username = 'smitty';
			$password = 'secretsmittypass';

			$this->load->library('xmlrpc');

			$parameters = $request->output_parameters();

			if ($parameters[1] != $username && $parameters[2] != $password)
			{
				return $this->xmlrpc->send_error_message('100', 'Invalid Access');
			}

			$response = array(
				array(
					'nickname'  => array('Smitty', 'string'),
					'userid'    => array('99', 'string'),
					'url'       => array('http://yoursite.com', 'string'),
					'email'     => array('jsmith@yoursite.com', 'string'),
					'lastname'  => array('Smith', 'string'),
					'firstname' => array('John', 'string')
				),
	                         'struct'
			);

			return $this->xmlrpc->send_response($response);
		}
	}

注意
------

``output_parameters()`` 函数获取一个由客户端发送的请求参数数组。
上面的例子中输出参数将会是用户名和密码。

如果客户端发送的用户名和密码无效的话，将使用 ``send_error_message()`` 函数返回错误信息。

如果操作成功，客户端会收到包含用户信息的响应数组。

格式化响应
=====================

和请求一样，响应也必须被格式化为数组。然而不同于请求信息，响应数组 **只包含一项**  。
该项可以是一个包含其他数组的数组，但是只能有一个主数组，换句话说，
响应的结果大概是下面这个样子::

	$response = array('Response data', 'array');

但是，响应通常会包含多个信息。要做到这样，我们必须把各个信息放到他们自己的数组中，
这样主数组就始终只有一个数据项。下面是一个例子展示如何实现这样的效果::

	$response = array(
		array(
			'first_name' => array('John', 'string'),
			'last_name' => array('Doe', 'string'),
			'member_id' => array(123435, 'int'),
			'todo_list' => array(array('clean house', 'call mom', 'water plants'), 'array'),
		),
		'struct'
	);

注意：上面的数组被格式化为 struct，这是响应最常见的数据类型。

如同请求一样，响应可以是七种数据类型中的一种，参见 `数据类型 <#datatypes>`_ 一节。

发送错误信息
=========================

如果你需要发送错误信息给客户端，可以使用下面的代码::

	return $this->xmlrpc->send_error_message('123', 'Requested data not available');

第一个参数为错误编号，第二个参数为错误信息。

创建你自己的客户端与服务端
===================================

为了帮助你理解目前为止讲的这些内容，让我们来创建两个控制器，演示下 XML-RPC
的客户端和服务端。你将用客户端来发送一个请求到服务端并从服务端收到一个响应。

客户端
----------

使用文本编辑器创建一个控制器 Xmlrpc_client.php ，在这个控制器中，
粘贴以下的代码并保存到 applications/controllers/ 目录::

	<?php

	class Xmlrpc_client extends CI_Controller {

		public function index()
		{
			$this->load->helper('url');
			$server_url = site_url('xmlrpc_server');

			$this->load->library('xmlrpc');

			$this->xmlrpc->server($server_url, 80);
			$this->xmlrpc->method('Greetings');

			$request = array('How is it going?');
			$this->xmlrpc->request($request);

			if ( ! $this->xmlrpc->send_request())
			{
				echo $this->xmlrpc->display_error();
			}
			else
			{
				echo '<pre>';
				print_r($this->xmlrpc->display_response());
				echo '</pre>';
			}
		}
	}
	?>

.. note:: 上面的代码中我们使用了一个 URL 辅助库，更多关于辅助库的信息，
	你可以阅读 :doc:`这里 <../general/helpers>` 。

服务端
----------

使用文本编辑器创建一个控制器 Xmlrpc_server.php ，在这个控制器中，
粘贴以下的代码并保存到 applications/controllers/ 目录::

	<?php

	class Xmlrpc_server extends CI_Controller {

		public function index()
		{
			$this->load->library('xmlrpc');
			$this->load->library('xmlrpcs');

			$config['functions']['Greetings'] = array('function' => 'Xmlrpc_server.process');

			$this->xmlrpcs->initialize($config);
			$this->xmlrpcs->serve();
		}


		public function process($request)
		{
			$parameters = $request->output_parameters();

			$response = array(
				array(
					'you_said'  => $parameters[0],
					'i_respond' => 'Not bad at all.'
				),
				'struct'
			);

			return $this->xmlrpc->send_response($response);
		}
	}


尝试一下
-------------

现在使用类似于下面这样的链接访问你的站点::

	example.com/index.php/xmlrpc_client/

你应该能看到你发送到服务端的信息，以及服务器返回的响应信息。

在客户端，你发送了一条消息（"How's is going?"）到服务端，
随着一个请求发送到 "Greetings" 方法。服务端收到这个请求并映射到 
"process" 函数，然后返回响应信息。

在请求参数中使用关联数组
===============================================

如果你希望在你的方法参数中使用关联数组，那么你需要使用 struct 数据类型::

	$request = array(
		array(
			// Param 0
			array('name' => 'John'),
			'struct'
		),
		array(
			// Param 1
			array(
				'size' => 'large',
				'shape'=>'round'
			),
			'struct'
		)
	);

	$this->xmlrpc->request($request);

你可以在服务端处理请求信息时获取该关联数组。

::

	$parameters = $request->output_parameters();
	$name = $parameters[0]['name'];
	$size = $parameters[1]['size'];
	$shape = $parameters[1]['shape'];

数据类型
==========

根据 `XML-RPC 规范 <http://www.xmlrpc.com/spec>`_ 一共有七种不同的数据类型可以在 XML-RPC 中使用：

-  *int* or *i4*
-  *boolean*
-  *string*
-  *double*
-  *dateTime.iso8601*
-  *base64*
-  *struct* (contains array of values)
-  *array* (contains array of values)

***************
类参考
***************

.. php:class:: CI_Xmlrpc

	.. php:method:: initialize([$config = array()])

		:param	array	$config: Configuration data
		:rtype:	void

		初始化 XML-RPC 类，接受一个包含你设置的参数的关联数组。

	.. php:method:: server($url[, $port = 80[, $proxy = FALSE[, $proxy_port = 8080]]])

		:param	string	$url: XML-RPC server URL
		:param	int	$port: Server port
		:param	string	$proxy: Optional proxy
		:param	int	$proxy_port: Proxy listening port
		:rtype:	void

		用于设置 XML-RPC 服务器端的 URL 和端口::

			$this->xmlrpc->server('http://www.sometimes.com/pings.php', 80);

		支持基本的 HTTP 身份认证，只需简单的将其添加到 URL中::

			$this->xmlrpc->server('http://user:pass@localhost/', 80);

	.. php:method:: timeout($seconds = 5)

		:param	int	$seconds: Timeout in seconds
		:rtype:	void

		设置一个超时时间（单位为秒），超过该时间，请求将被取消::

			$this->xmlrpc->timeout(6);

	.. php:method:: method($function)

		:param	string	$function: Method name
		:rtype:	void

		设置 XML-RPC 服务器接受的请求方法::

			$this->xmlrpc->method('method');

		其中 method 参数为请求方法名。

	.. php:method:: request($incoming)

		:param	array	$incoming: Request data
		:rtype:	void

		接受一个数组参数，并创建一个发送到 XML-RPC 服务器的请求::

			$request = array(array('My Photoblog', 'string'), 'http://www.yoursite.com/photoblog/');
			$this->xmlrpc->request($request);

	.. php:method:: send_request()

		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		发送请求的方法，成功返回 TRUE，失败返回 FALSE ，可以用在条件判断里。

	.. method set_debug($flag = TRUE)

		:param	bool	$flag: Debug status flag
		:rtype:	void

		启用或禁用调试，在开发环境下，可以用它来显示调试信息和错误数据。

	.. php:method:: display_error()

		:returns:	Error message string
		:rtype:	string

		当请求失败后，返回错误信息。
		::

			echo $this->xmlrpc->display_error();

	.. php:method:: display_response()

		:returns:	Response
		:rtype:	mixed

		远程服务器接收请求后返回的响应，返回的数据通常是一个关联数组。
		::

			$this->xmlrpc->display_response();

	.. php:method:: send_error_message($number, $message)

		:param	int	$number: Error number
		:param	string	$message: Error message
		:returns:	XML_RPC_Response instance
		:rtype:	XML_RPC_Response

		这个方法允许你从服务器发送一个错误消息到客户端。
		第一个参数是错误编号，第二个参数是错误信息。
		::

			return $this->xmlrpc->send_error_message(123, 'Requested data not available');

	.. method send_response($response)

		:param	array	$response: Response data
		:returns:	XML_RPC_Response instance
		:rtype:	XML_RPC_Response

		从服务器发送响应到客户端，发送的数组必须是有效的。
		::

			$response = array(
				array(
					'flerror' => array(FALSE, 'boolean'),
					'message' => "Thanks for the ping!"
				),
				'struct'
			);

			return $this->xmlrpc->send_response($response);