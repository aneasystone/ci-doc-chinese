############
加载器类
############

加载器，顾名思义，是用于加载元素的，加载的元素可以是库（类），:doc:`视图文件 <../general/views>` ，
:doc:`驱动器 <../general/drivers>` ，:doc:`辅助库 <../general/helpers>` ，
:doc:`模型 <../general/models>` 或其他你自己的文件。

.. note:: 该类由系统自动加载，你无需手工加载。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

**********************
应用程序"包"
**********************

应用程序包（Package）可以很便捷的将你的应用部署在一个独立的目录中，
以实现自己整套的类库，模型，辅助函数，配置，文件和语言包。
建议将这些应用程序包放置在 application/third_party 目录下。
下面是一个简单应用程序包的目录结构。

下面是一个名为 "Foo Bar" 的应用程序包目录的例子。

::

	/application/third_party/foo_bar

	config/
	helpers/
	language/
	libraries/
	models/

无论应用程序包是为了实现什么样的目的，它都包含了属于自己的配置文件、
辅助函数、语言包、类库和模型。如果要在你的控制器里使用这些资源，
你首先需要告知加载器（Loader）从应用程序包加载资源，使用
``add_package_path()`` 方法来添加包的路径。

包的视图文件
------------------

默认情况下，当调用 ``add_package_path()`` 方法时，包的视图文件路径就设置好了。
视图文件的路径是通过一个循环来查找的，一旦找到第一个匹配的即加载该视图。

在这种情况下，它可能在包内产生视图命名冲突，并可能导致加载错误的包。
为了确保不会发生此类问题，在调用 ``add_package_path()`` 方法时，
可以将可选的第二个参数设置为 FALSE 。

::

	$this->load->add_package_path(APPPATH.'my_app', FALSE);
	$this->load->view('my_app_index'); // Loads
	$this->load->view('welcome_message'); // Will not load the default welcome_message b/c the second param to add_package_path is FALSE

	// Reset things
	$this->load->remove_package_path(APPPATH.'my_app');

	// Again without the second parameter:
	$this->load->add_package_path(APPPATH.'my_app');
	$this->load->view('my_app_index'); // Loads
	$this->load->view('welcome_message'); // Loads

***************
类参考
***************

.. php:class:: CI_Loader

	.. php:method:: library($library[, $params = NULL[, $object_name = NULL]])

		:param	mixed	$library: Library name as a string or an array with multiple libraries
		:param	array	$params: Optional array of parameters to pass to the loaded library's constructor
		:param	string	$object_name: Optional object name to assign the library to
		:returns:	CI_Loader instance (method chaining)
		:rtype:	CI_Loader

		该方法用于加载核心类。

		.. note:: 我们有时候说 "类" ，有时候说 "库" ，这两个词不做区分。

		例如，如果你想使用 CodeIgniter 发送邮件，第一步就是在控制器中加载 email 类::

			$this->load->library('email');

		加载完之后，email 类就可以使用 ``$this->email`` 来访问使用了。

		类库文件可以被保存到主 libraries 目录的子目录下面，或者保存到个人的 *application/libraries* 
		目录下。要载入子目录下的文件，只需将路径包含进来就可以了，注意这里说的路径是指相对于 
		libraries 目录的路径。 例如，当你有一个文件保存在下面这个位置::

			libraries/flavors/Chocolate.php

		你应该使用下面的方式来载入它::

			$this->load->library('flavors/chocolate');

		你可以随心所欲地将文件保存到多层的子目录下。

		另外，你可以同时加载多个类，只需给 library 方法传入一个包含所有要载入的类名的数组即可::
		::

			$this->load->library(array('email', 'table'));

		**设置选项**

		第二个参数是可选的，用于选择性地传递配置参数。一般来说，你可以将参数以数组的形式传递过去::

			$config = array (
				'mailtype' => 'html',
				'charset'  => 'utf-8,
				'priority' => '1'
			);

			$this->load->library('email', $config);

		配置参数通常也可以保存在一个配置文件中，在每个类库自己的页面中有详细的说明，
		所以在使用类库之前，请认真阅读说明。

		请注意，当第一个参数使用数组来同时载入多个类时，每个类将获得相同的参数信息。

		**给类库分配不同的对象名**

		第三个参数也是可选的，如果为空，类库通常就会被赋值给一个与类库同名的对象。
		例如，如果类库名为 Calendar ，它将会被赋值给一个名为 ``$this->calendar`` 的变量。

		如果你希望使用你的自定义名称，你可以通过第三个参数把它传递过去::

			$this->load->library('calendar', NULL, 'my_calendar');

			// Calendar class is now accessed using:
			$this->my_calendar

		请注意，当第一个参数使用数组来同时载入多个类时，第三个参数将不起作用。

	.. php:method:: driver($library[, $params = NULL[, $object_name]])

		:param	mixed	$library: Library name as a string or an array with multiple libraries
		:param	array	$params: Optional array of parameters to pass to the loaded library's constructor
		:param	string	$object_name: Optional object name to assign the library to
		:returns:	CI_Loader instance (method chaining)
		:rtype:	CI_Loader

		该方法用于加载驱动器类，和 ``library()`` 方法非常相似。

		例如，如果你想在 CodeIgniter 中使用会话，第一步就是在控制器中加载 session 驱动器::

			$this->load->driver('session');

		加载完之后，session 驱动器就可以使用 ``$this->session`` 来访问使用了。

		驱动器文件可以被保存到主 libraries 目录的子目录下面，或者保存到个人的 *application/libraries* 
		目录下。子目录的名称必须和驱动器父类的名称一致，你可以阅读 :doc:`驱动器 <../general/drivers>`
		了解详细信息。

		另外，你可以同时加载多个驱动器，只需给 driver 方法传入一个包含所有要载入的驱动器名的数组即可::
		::

			$this->load->driver(array('session', 'cache'));

		**设置选项**

		第二个参数是可选的，用于选择性地传递配置参数。一般来说，你可以将参数以数组的形式传递过去::

			$config = array(
				'sess_driver' => 'cookie',
				'sess_encrypt_cookie'  => true,
				'encryption_key' => 'mysecretkey'
			);

			$this->load->driver('session', $config);

		配置参数通常也可以保存在一个配置文件中，在每个类库自己的页面中有详细的说明，
		所以在使用类库之前，请认真阅读说明。

		**给类库分配不同的对象名**

		第三个参数也是可选的，如果为空，驱动器通常就会被赋值给一个与它同名的对象。
		例如，如果驱动器名为 Session ，它将会被赋值给一个名为 ``$this->session`` 的变量。

		如果你希望使用你的自定义名称，你可以通过第三个参数把它传递过去::

			$this->load->driver('session', '', 'my_session');

			// Session class is now accessed using:
			$this->my_session

	.. php:method:: view($view[, $vars = array()[, return = FALSE]])

		:param	string	$view: View name
		:param	array	$vars: An associative array of variables
		:param	bool	$return: Whether to return the loaded view
		:returns:	View content string if $return is set to TRUE, otherwise CI_Loader instance (method chaining)
		:rtype:	mixed

		该方法用于加载你的视图文件。如果你尚未阅读本手册的 :doc:`视图 <../general/views>`
		章节的话，建议你先去阅读那里的内容，会有更详细的函数使用说明。

		第一个参数是必须的，指定你要载入的视图文件的名称。

		.. note:: 无需加上 .php 扩展名，除非你使用了其他的扩展名。

		第二个参数是**可选的**，允许你传入一个数组或对象参数，传入的参数将使用 PHP 的
		`extract() <http://php.net/extract>`_  函数进行提取，提取出来的变量可以在视图中使用。
		再说一遍，请阅读 :doc:`视图 <../general/views>` 章节了解该功能的更多用法。

		第三个参数是**可选的**，用于改变方法的行为，将数据以字符串的形式返回，
		而不是发送给浏览器。当你希望对数据进行一些特殊处理时，这个参数就非常有用。
		如果你将这个参数设置为 TRUE，方法就会返回数据。这个参数的默认值是 FALSE，
		也就是数据将会被发送给浏览器。如果你希望数据被返回，记得要将它赋值给一个变量::

			$string = $this->load->view('myfile', '', TRUE);

	.. php:method:: vars($vars[, $val = ''])

		:param	mixed	$vars: An array of variables or a single variable name
		:param	mixed	$val: Optional variable value
		:returns:	CI_Loader instance (method chaining)
		:rtype:	CI_Loader

		这个方法以一个关联数组作为输入参数,将这个数组用 PHP 的 `extract() 
		<http://php.net/extract>`_ 函数转化成与之对应的变量。这个方法的结果与上面的 
		``$this->load->view()`` 方法使用第二个参数的结果一样。
		假如你想在控制器的构造函数中定义一些全局变量，并希望这些变量在控制器的
		每一个方法加载的视图文件中都可用，这种情况下你可能想单独使用这个函数。
		你可以多次调用该方法，数据将被缓存，并被合并为一个数组，以便转换成变量。

	.. php:method:: get_var($key)

		:param	string	$key: Variable name key
		:returns:	Value if key is found, NULL if not
		:rtype:	mixed

		该方法检查关联数组中的变量对你的视图是否可用。当一个变量在一个类
		或者控制器的另一个方法里被以这样的方式定义时：``$this->load->vars()``，
		会做这样的检查。

	.. php:method:: get_vars()

		:returns:	An array of all assigned view variables
		:rtype:	array

		该方法返回所有对视图可用的变量。

	.. php:method:: clear_vars()

		:returns:	CI_Loader instance (method chaining)
		:rtype:	CI_Loader

		清除缓存的视图变量。

	.. php:method:: model($model[, $name = ''[, $db_conn = FALSE]])

		:param	mixed	$model: Model name or an array containing multiple models
		:param	string	$name: Optional object name to assign the model to
		:param	string	$db_conn: Optional database configuration group to load
		:returns:	CI_Loader instance (method chaining)
		:rtype:	CI_Loader

		::

			$this->load->model('model_name');


		如果你的模型位于子目录下，加载时将路径包含进来即可。例如，
		如果你有一个模型位于 *application/models/blog/Queries.php* ，
		你可以使用下面的方法来加载::

			$this->load->model('blog/queries');

		如果你希望将你的模型赋值给一个不同的变量，你可以在第二个参数中指定::

			$this->load->model('model_name', 'fubar');
			$this->fubar->method();

	.. php:method:: database([$params = ''[, $return = FALSE[, $query_builder = NULL]]])

		:param	mixed	$params: Database group name or configuration options
		:param	bool	$return: Whether to return the loaded database object
		:param	bool	$query_builder: Whether to load the Query Builder
		:returns:	Loaded CI_DB instance or FALSE on failure if $return is set to TRUE, otherwise CI_Loader instance (method chaining)
		:rtype:	mixed

		该方法用于加载数据库类，有两个可选的参数。
		更多信息，请阅读 :doc:`数据库 <../database/index>` 。

	.. php:method:: dbforge([$db = NULL[, $return = FALSE]])

		:param	object	$db: Database object
		:param	bool	$return: Whether to return the Database Forge instance
		:returns:	Loaded CI_DB_forge instance if $return is set to TRUE, otherwise CI_Loader instance (method chaining)
		:rtype:	mixed

		加载 :doc:`数据库工厂类 <../database/forge>` ，更多信息，请参考该页面。

	.. php:method:: dbutil([$db = NULL[, $return = FALSE]])

		:param	object	$db: Database object
		:param	bool	$return: Whether to return the Database Utilities instance
		:returns:	Loaded CI_DB_utility instance if $return is set to TRUE, otherwise CI_Loader instance (method chaining)
		:rtype:	mixed

		加载 :doc:`数据库工具类 <../database/utilities>` ，更多信息，请参考该页面。

	.. php:method:: helper($helpers)

		:param	mixed	$helpers: Helper name as a string or an array containing multiple helpers
		:returns:	CI_Loader instance (method chaining)
		:rtype:	CI_Loader

		该方法用于加载辅助库文件，其中 file_name 为加载的文件名，不带 _helper.php 后缀。

	.. php:method:: file($path[, $return = FALSE])

		:param	string	$path: File path
		:param	bool	$return: Whether to return the loaded file
		:returns:	File contents if $return is set to TRUE, otherwise CI_Loader instance (method chaining)
		:rtype:	mixed

		这是一个通用的文件载入方法，在第一个参数中给出文件所在的路径和文件名，
		将会打开并读取对应的文件。默认情况下，数据会被发送给浏览器，
		就如同视图文件一样，但如果你将第二个参数设置为 TRUE ，
		那么数据就会以字符串的形式被返回，而不是发送给浏览器。

	.. php:method:: language($files[, $lang = ''])

		:param	mixed	$files: Language file name or an array of multiple language files
		:param	string	$lang: Language name
		:returns:	CI_Loader instance (method chaining)
		:rtype:	CI_Loader

		该方法是 :doc:`语言加载方法 <language>` ``$this->lang->load()`` 的一个别名。

	.. php:method:: config($file[, $use_sections = FALSE[, $fail_gracefully = FALSE]])

		:param	string	$file: Configuration file name
		:param	bool	$use_sections: Whether configuration values should be loaded into their own section
		:param	bool	$fail_gracefully: Whether to just return FALSE in case of failure
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		该方法是 :doc:`配置文件加载方法 <config>` ``$this->config->load()`` 的一个别名。

	.. php:method:: is_loaded($class)

		:param	string	$class: Class name
		:returns:	Singleton property name if found, FALSE if not
		:rtype:	mixed

		用于检查某个类是否已经被加载。

		.. note:: 这里的类指的是类库和驱动器。

		如果类已经被加载，方法返回它在 CodeIgniter 超级对象中被赋值的变量的名称，
		如果没有加载，返回 FALSE::

			$this->load->library('form_validation');
			$this->load->is_loaded('Form_validation');	// returns 'form_validation'

			$this->load->is_loaded('Nonexistent_library');	// returns FALSE

		.. important:: 如果你有类的多个实例（被赋值给多个不同的属性），那么将返回第一个的名称。

		::

			$this->load->library('form_validation', $config, 'fv');
			$this->load->library('form_validation');

			$this->load->is_loaded('Form_validation');	// returns 'fv'

	.. php:method:: add_package_path($path[, $view_cascade = TRUE])

		:param	string	$path: Path to add
		:param	bool	$view_cascade: Whether to use cascading views
		:returns:	CI_Loader instance (method chaining)
		:rtype:	CI_Loader

		添加一个包路径，用于告诉加载器类使用给定的路径来加载后续请求的资源。
		例如，"Foo Bar" 应用程序包里有一个名为 Foo_bar.php 的类，在控制器中，
		我们可以按照如下的方法调用::

			$this->load->add_package_path(APPPATH.'third_party/foo_bar/')
				->library('foo_bar');

	.. php:method:: remove_package_path([$path = ''])

		:param	string	$path: Path to remove
		:returns:	CI_Loader instance (method chaining)
		:rtype:	CI_Loader

		当你的控制器完成从应用程序包中读取资源，如果你还需要读取其他的应用程序包的资源，
		你会希望删除当前使用的包路径来让加载器不再使用这个文件夹中的资源。
		要删除最后一次使用的包路径，你可以直接不带参数的调用该方法。

		或者你也可以删除一个特定的包路径，指定与之前使用 ``add_package_path()`` 方法时
		所加载的包相同的路径::

			$this->load->remove_package_path(APPPATH.'third_party/foo_bar/');

	.. php:method:: get_package_paths([$include_base = TRUE])

		:param	bool	$include_base: Whether to include BASEPATH
		:returns:	An array of package paths
		:rtype:	array

		返回当前所有可用的包路径。
