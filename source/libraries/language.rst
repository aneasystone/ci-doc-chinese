##############
语言类
##############

语言类提供了一些方法用于获取语言文件和不同语言的文本来实现国际化。

在你的 CodeIgniter 的 **system** 目录，有一个 **language** 子目录，
它包含了一系列 **英文** 的语言文件。
在 **system/language/english/** 这个目录下的这些文件定义了 CodeIgniter 
框架的各个部分使用到的一些常规消息，错误消息，以及其他一些通用的单词或短语。

如果需要的话，你可以创建属于你自己的语言文件，用于提供应用程序的错误消息和其他消息，
或者将核心部分的消息翻译为其他的语言。翻译的消息或你另加的消息应该放在 
**application/language/** 目录下，每种不同的语言都有相应的一个子目录（例如，
'french' 或者 'german'）。

CodeIgniter 框架自带了一套 "英语" 语言文件，另外可以在 
`CodeIgniter 3 翻译仓库 <https://github.com/bcit-ci/codeigniter3-translations>`_ 
找到其他不同的语言，每个语言都有一个独立的目录。

当 CodeIgniter 加载语言文件时，它会先加载 **system/language/** 目录下的，然后再加载
你的 **application/language/** 目录下的来覆盖它。

.. note:: 每个语言都有它自己的目录，例如，英语语言文件位于：system/language/english

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

***************************
处理多语言
***************************

如果你想让你的应用程序支持多语言，你就需要在 **application/language/** 目录下提供不同语言的文件，
然后在 **application/config/config.php** 配置文件中指定默认语言。

**application/language/english/** 目录可以包含你的应用程序需要的额外语言文件，例如错误消息。

每个语言对应的目录中都应该包含从 翻译仓库 中获取到的核心文件，或者你自己翻译它们，你也可以添加
你的程序需要的其他文件。

你应该将你正在使用的语言保存到一个会话变量中。

语言文件的例子
=====================

::

	system/
		language/
			english/
				...
				email_lang.php
				form_validation_lang.php
				...

	application/
		language/
			english/
				error_messages_lang.php
			french/
				...
				email_lang.php
				error_messages_lang.php
				form_validation_lang.php
				...

切换语言
==============================

::

	$idiom = $this->session->get_userdata('language');
	$this->lang->load('error_messages', $idiom);
	$oops = $this->lang->line('message_key');

********************
国际化
********************

CodeIgniter 的语言类给你的应用程序提供了一种简单轻便的方式来实现多语言，
它并不是通常我们所说的 `国际化与本地化 <http://en.wikipedia.org/wiki/Internationalization_and_localization>`_
的完整实现。

我们可以给每一种语言一个别名，一个更通用的名字，而不是使用诸如 "en"、
"en-US"、"en-CA-x-ca" 这种国际标准的缩写名字。

.. note:: 当然，你完全可以在你的程序中使用国际标准的缩写名字。

************************
使用语言类
************************

创建语言文件
=======================

语言文件的命名必须以 **_lang.php** 结尾，例如，你想创建一个包含错误消息的文件，
你可以把它命名为：error_lang.php 。

在此文件中，你可以在每行把一个字符串赋值给名为 $lang 的数组，例如::

	$lang['language_key'] = 'The actual message to be shown';

.. note:: 在每个文件中使用一个通用的前缀来避免和其他文件中的相似名称冲突是个好方法。
	例如，如果你在创建错误消息你可以使用 error\_ 前缀。

::

	$lang['error_email_missing'] = 'You must submit an email address';
	$lang['error_url_missing'] = 'You must submit a URL';
	$lang['error_username_missing'] = 'You must submit a username';

加载语言文件
=======================

在使用语言文件之前，你必须先加载它。可以使用下面的代码::

	$this->lang->load('filename', 'language');

其中 filename 是你要加载的语言文件名（不带扩展名），language 是要加载哪种语言（比如，英语）。
如果没有第二个参数，将会使用 **application/config/config.php** 中设置的默认语言。

你也可以通过传一个语言文件的数组给第一个参数来同时加载多个语言文件。
::

	$this->lang->load(array('filename1', 'filename2'));

.. note:: *language* 参数只能包含字母。

读取语言文本
=======================

当你的语言文件已经加载，你就可以通过下面的方法来访问任何一行语言文本::

	$this->lang->line('language_key');

其中，*language_key* 参数是你想显示的文本行所对应的数组的键名。

万一你不确定你想读取的那行文本是否存在，你还可以将第二个参数设置为 FALSE 禁用错误日志::

	$this->lang->line('misc_key', FALSE);

.. note:: 该方法只是简单的返回文本行，而不是显示出它。

使用语言行作为表单的标签
-----------------------------------

这一特性已经从语言类中废弃，并移到了 :doc:`语言辅助库 <../helpers/language_helper>`
的 :php:func:`lang()` 函数。

自动加载语言文件
======================

如果你发现你需要在整个应用程序中使用某个语言文件，你可以让 CodeIgniter
在系统初始化的时候 :doc:`自动加载 <../general/autoloader>` 该语言文件。
可以打开 **application/config/autoload.php** 文件，把语言放在 autoload 数组中。

***************
类参考
***************

.. php:class:: CI_Lang

	.. php:method:: load($langfile[, $idiom = ''[, $return = FALSE[, $add_suffix = TRUE[, $alt_path = '']]]])

		:param	mixed	$langfile: Language file to load or array with multiple files
		:param	string	$idiom: Language name (i.e. 'english')
		:param	bool	$return: Whether to return the loaded array of translations
		:param	bool	$add_suffix: Whether to add the '_lang' suffix to the language file name
		:param	string	$alt_path: An alternative path to look in for the language file
		:returns:	Array of language lines if $return is set to TRUE, otherwise void
		:rtype:	mixed

		加载一个语言文件。

	.. php:method:: line($line[, $log_errors = TRUE])

		:param	string	$line: Language line key name
		:param	bool	$log_errors: Whether to log an error if the line isn't found
		:returns:	Language line string or FALSE on failure
		:rtype:	string

		从一个已加载的语言文件中，通过行名获取一行该语言的文本。