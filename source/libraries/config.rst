############
配置类
############

配置类用于获取配置参数，这些参数可以来自于默认的配置文件（application/config/config.php），
也可以来自你自定义的配置文件。

.. note:: 该类由系统自动初始化，你无需手工加载。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

*****************************
使用配置类
*****************************

配置文件剖析
========================

CodeIgniter 默认有一个主要的配置文件，位于 application/config/config.php 。
如果你使用文本编辑器打开它的话，你会看到配置项都存储在一个叫做 $config 的数组中。

你可以往这个文件中添加你自己的配置项，或者你喜欢将你的配置项和系统的分开的话，
你也可以创建你自己的配置文件并保存到配置目录下。

.. note:: 如果你要创建你自己的配置文件，使用和主配置文件相同的格式，将配置项保存到名为 $config 的数组中。
	CodeIgniter 会智能的管理这些文件，所以就算数组名都一样也不会冲突（假设数组的索引没有相同的）。

加载配置文件
=====================

.. note::
	CodeIgniter 会自动加载主配置文件（application/config/config.php），所以你只需要加载你自己
	创建的配置文件就可以了。

有两种加载配置文件的方式：

手工加载
**************

要加载你自定义的配置文件，你需要在 :doc:`控制器 </general/controllers>` 中使用下面的方法::

	$this->config->load('filename');

其中，filename 是你的配置文件的名称，无需 .php 扩展名。

如果你需要加载多个配置文件，它们会被合并成一个大的 config 数组里。尽管你是在不同的配置文件中定义的，
但是，如果有两个数组索引名称一样的话还是会发生名称冲突。为了避免冲突，你可以将第二个参数设置为 TRUE ，
这样每个配置文件中的配置会被存储到以该配置文件名为索引的数组中去。例如::

	// Stored in an array with this prototype: $this->config['blog_settings'] = $config
	$this->config->load('blog_settings', TRUE);

请阅读下面的 “获取配置项” 一节，学习如何获取通过这种方式加载的配置。

第三个参数用于抑制错误信息，当配置文件不存在时，不会报错::

	$this->config->load('blog_settings', FALSE, TRUE);

自动加载
************

如果你发现有一个配置文件你需要在全局范围内使用，你可以让系统自动加载它。
要实现这点，打开位于 application/config/ 目录下的 **autoload.php** 文件，
将你的配置文件添加到自动加载数组中。


获取配置项
=====================

要从你的配置文件中获取某个配置项，使用如下方法::

	$this->config->item('item_name');

其中 item_name 是你希望获取的 $config 数组的索引名，例如，要获取语言选项，
你可以这样::

	$lang = $this->config->item('language');

如果你要获取的配置项不存在，方法返回 NULL 。

如果你在使用 $this->config->load 方法时使用了第二个参数，每个配置文件中的配置
被存储到以该配置文件名为索引的数组中，要获取该配置项，你可以将 $this->config->item()
方法的第二个参数设置为这个索引名（也就是配置文件名）。例如::

	// Loads a config file named blog_settings.php and assigns it to an index named "blog_settings"
	$this->config->load('blog_settings', TRUE);

	// Retrieve a config item named site_name contained within the blog_settings array
	$site_name = $this->config->item('site_name', 'blog_settings');

	// An alternate way to specify the same item:
	$blog_config = $this->config->item('blog_settings');
	$site_name = $blog_config['site_name'];

设置配置项
=====================

如果你想动态的设置一个配置项，或修改某个已存在的配置项，你可以这样::

	$this->config->set_item('item_name', 'item_value');

其中，item_name 是你希望修改的 $config 数组的索引名，item_value 为要设置的值。

.. _config-environments:

多环境
============

你可以根据当前的环境来加载不同的配置文件，index.php 文件中定义了 ENVIRONMENT
常量，在 :doc:`处理多环境 </general/environments>` 中有更详细的介绍。

要创建特定环境的配置文件，新建或复制一个配置文件到 application/config/{ENVIRONMENT}/{FILENAME}.php 。

例如，要新建一个生产环境的配置文件，你可以：

#. 新建目录 application/config/production/
#. 将已有的 config.php 文件拷贝到该目录
#. 编辑 application/config/production/config.php 文件，使用生产环境下配置

当你将 ENVIRONMENT 常量设置为 'production' 时，你新建的生产环境下的 config.php 
里的配置将会加载。

你可以放置以下配置文件到特定环境的目录下：

-  默认的 CodeIgniter 配置文件
-  你自己的配置文件

.. note::
	CodeIgniter 总是先加载全局配置文件（例如，application/config/ 目录下的配置文件），
	然后再去尝试加载当前环境的配置文件。这意味着你没必要将所有的配置文件都放到特定环境的配置目录下，
	只需要放那些在每个环境下不一样的配置文件就可以了。另外，你也不用拷贝所有的配置文件内容到
	特定环境的配置文件中，只需要将那些在每个环境下不一样的配置项拷进去就行了。定义在环境目录下的配置项，
	会覆盖掉全局的配置。


***************
类参考
***************

.. php:class:: CI_Config

	.. attribute:: $config

		所有已加载的配置项组成的数组。

	.. attribute:: $is_loaded

		所有已加载的配置文件组成的数组。


	.. php:method:: item($item[, $index=''])

		:param	string	$item: Config item name
		:param	string	$index: Index name
		:returns:	Config item value or NULL if not found
		:rtype:	mixed

		获取某个配置项。

	.. php:method:: set_item($item, $value)

		:param	string	$item: Config item name
		:param	string	$value: Config item value
		:rtype:	void

		设置某个配置项的值。

	.. php:method:: slash_item($item)

		:param	string	$item: config item name
		:returns:	Config item value with a trailing forward slash or NULL if not found
		:rtype:	mixed

		这个方法和 ``item()`` 一样，只是在获取的配置项后面添加一个斜线，如果配置项不存在，返回 NULL 。

	.. php:method:: load([$file = ''[, $use_sections = FALSE[, $fail_gracefully = FALSE]]])

		:param	string	$file: Configuration file name
		:param	bool	$use_sections: Whether config values shoud be loaded into their own section (index of the main config array)
		:param	bool	$fail_gracefully: Whether to return FALSE or to display an error message
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		加载配置文件。

	.. php:method:: site_url()

		:returns:	Site URL
		:rtype:	string

		该方法返回你的网站的 URL ，包括你在配置文件中设置的 "index" 值。

		这个方法通常通过 :doc:`URL 辅助库 </helpers/url_helper>` 中函数来访问。

	.. php:method:: base_url()

		:returns:	Base URL
		:rtype:	string

		该方法返回你的网站的根 URL ，你可以在后面加上样式和图片的路径来访问它们。

		这个方法通常通过 :doc:`URL 辅助库 </helpers/url_helper>` 中函数来访问。

	.. php:method:: system_url()

		:returns:	URL pointing at your CI system/ directory
		:rtype:	string

		该方法返回 CodeIgniter 的 system 目录的 URL 。

		.. note:: 该方法已经废弃，因为这是一个不安全的编码实践。你的 *system/* 目录不应该被公开访问。
