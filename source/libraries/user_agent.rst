################
用户代理类
################

用户代理（User Agent）类提供了一些方法来帮助你识别正在访问你的站点的浏览器、
移动设备或机器人的信息。另外，你还可以通过它获取 referrer 信息，以及
支持的语言和字符集信息。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

**************************
使用用户代理类
**************************

初始化类
======================

正如 CodeIgniter 中的其他类一样，在你的控制器中使用 ``$this->load->library()``
方法来初始化用户代理类::

	$this->load->library('user_agent');

初始化之后，用户代理类的对象就可以这样访问::

	$this->agent

用户代理的定义
======================

用户代理的名称定义在 application/config/user_agents.php 配置文件中。
你也可以根据需要向相应的数组中添加你自己的用户代理。

例子
=======

当用户代理类初始化之后，它会尝试判断正在访问你的站点的是 Web 浏览器，还是移动设备，
或者是机器人。它还可以获取平台的相关信息。

::

	$this->load->library('user_agent');

	if ($this->agent->is_browser())
	{
		$agent = $this->agent->browser().' '.$this->agent->version();
	}
	elseif ($this->agent->is_robot())
	{
		$agent = $this->agent->robot();
	}
	elseif ($this->agent->is_mobile())
	{
		$agent = $this->agent->mobile();
	}
	else
	{
		$agent = 'Unidentified User Agent';
	}

	echo $agent;

	echo $this->agent->platform(); // Platform info (Windows, Linux, Mac, etc.)

***************
类参考
***************

.. php:class:: CI_User_agent

	.. php:method:: is_browser([$key = NULL])

		:param	string	$key: Optional browser name
		:returns:	TRUE if the user agent is a (specified) browser, FALSE if not
		:rtype:	bool

		判断用户代理是否为某个已知的 Web 浏览器，返回布尔值 TRUE 或 FALSE 。
		::

			if ($this->agent->is_browser('Safari'))
			{
				echo 'You are using Safari.';
			}
			elseif ($this->agent->is_browser())
			{
				echo 'You are using a browser.';
			}

		.. note:: 这个例子中的 "Safari" 字符串是配置文件中定义的 browser 数组的一个元素，你可以在
			**application/config/user_agents.php** 文件中找到它，如果需要的话，你可以对其进行添加或修改。

	.. php:method:: is_mobile([$key = NULL])

		:param	string	$key: Optional mobile device name
		:returns:	TRUE if the user agent is a (specified) mobile device, FALSE if not
		:rtype:	bool

		判断用户代理是否为某个已知的移动设备，返回布尔值 TRUE 或 FALSE 。
		::

			if ($this->agent->is_mobile('iphone'))
			{
				$this->load->view('iphone/home');
			}
			elseif ($this->agent->is_mobile())
			{
				$this->load->view('mobile/home');
			}
			else
			{
				$this->load->view('web/home');
			}

	.. php:method:: is_robot([$key = NULL])

		:param	string	$key: Optional robot name
		:returns:	TRUE if the user agent is a (specified) robot, FALSE if not
		:rtype:	bool

		判断用户代理是否为某个已知的机器人，返回布尔值 TRUE 或 FALSE 。

		.. note:: 用户代理类只定义了一些常见的机器人，它并不是完整的机器人列表，因为可能存在上百个不同的机器人，
			遍历这个列表效率会很低。如果你发现某个机器人经常访问你的站点，并且它不在这个列表中，你可以将其添加到文件
			**application/config/user_agents.php** 中。

	.. php:method:: is_referral()

		:returns:	TRUE if the user agent is a referral, FALSE if not
		:rtype:	bool

		判断用户代理是否为从另一个网站跳过来的（Referer 为另一个网站），返回布尔值 TRUE 或 FALSE 。

	.. php:method:: browser()

		:returns:	Detected browser or an empty string
		:rtype:	string

		返回当前正在浏览你的站点的浏览器名称。

	.. php:method:: version()

		:returns:	Detected browser version or an empty string
		:rtype:	string

		返回当前正在浏览你的站点的浏览器版本号。

	.. php:method:: mobile()

		:returns:	Detected mobile device brand or an empty string
		:rtype:	string

		返回当前正在浏览你的站点的移动设备名称。

	.. php:method:: robot()

		:returns:	Detected robot name or an empty string
		:rtype:	string

		返回当前正在浏览你的站点的机器人名称。

	.. php:method:: platform()

		:returns:	Detected operating system or an empty string
		:rtype:	string

		返回当前正在浏览你的站点的平台（Linux、Windows、OSX 等）。

	.. php:method:: referrer()

		:returns:	Detected referrer or an empty string
		:rtype:	string

		如果用户代理引用了另一个站点，返回 referrer 。一般你会像下面这样做::

			if ($this->agent->is_referral())
			{
				echo $this->agent->referrer();
			}

	.. php:method:: agent_string()

		:returns:	Full user agent string or an empty string
		:rtype:	string

		返回完整的用户代理字符串，一般字符串的格式如下::

			Mozilla/5.0 (Macintosh; U; Intel Mac OS X; en-US; rv:1.8.0.4) Gecko/20060613 Camino/1.0.2

	.. php:method:: accept_lang([$lang = 'en'])

		:param	string	$lang: Language key
		:returns:	TRUE if provided language is accepted, FALSE if not
		:rtype:	bool

		判断用户代理是否支持某个语言。例如::

			if ($this->agent->accept_lang('en'))
			{
				echo 'You accept English!';
			}

		.. note:: 这个方法一般不太可靠，因为有些浏览器并不提供语言信息，甚至在那些提供了语言信息的浏览器中，也并不一定准确。

	.. php:method:: languages()

		:returns:	An array list of accepted languages
		:rtype:	array

		返回一个数组，包含用户代理支持的所有语言。

	.. php:method:: accept_charset([$charset = 'utf-8'])

		:param	string	$charset: Character set
		:returns:	TRUE if the character set is accepted, FALSE if not
		:rtype:	bool

		判断用户代理是否支持某个字符集。例如::

			if ($this->agent->accept_charset('utf-8'))
			{
				echo 'You browser supports UTF-8!';
			}

		.. note:: 这个方法一般不太可靠，因为有些浏览器并不提供字符集信息，甚至在那些提供了字符集信息的浏览器中，也并不一定准确。

	.. php:method:: charsets()

		:returns:	An array list of accepted character sets
		:rtype:	array

		返回一个数组，包含用户代理支持的所有字符集。

	.. php:method:: parse($string)

		:param	string	$string: A custom user-agent string
		:rtype:	void

		解析一个自定义的用户代理字符串，而不是当前正在访问站点的用户代理。
