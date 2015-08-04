###############
表单验证类
###############

CodeIgniter 提供了一个全面的表单验证和数据预处理类可以帮你少写很多代码。

.. contents:: Page Contents

********
概述
********

在解释 CodeIgniter 的数据验证处理之前，让我们先描述一下一般的情况：

#. 显示一个表单。
#. 你填写并提交了它。
#. 如果你提交了一些无效的信息，或者可能漏掉了一个必填项，
   表单将会重新显示你的数据，并提示一个错误信息。
#. 这个过程将继续，直到你提交了一个有效的表单。

在接收端，脚本必须：

#. 检查必填的数据。
#. 验证数据类型是否为正确，条件是否满足。例如，如果提交一个用户名，
   必须验证它是否只包含了允许的字符，必须有一个最小长度，不能超过最大长度。
   用户名不能和已存在的其他人名字相同，或者不能是某个保留字，等等。
#. 为确保安全性对数据进行过滤。
#. 如果需要，预格式化数据（数据需要清除空白吗？需要 HTML 编码？等等）
#. 准备数据，插入数据库。

尽管上面的过程并不是很复杂，但是通常需要编写很多代码，而且为了显示错误信息，
在网页中经常要使用多种不同的控制结构。表单验证虽然简单，但是实现起来却非常枯燥。

************************
表单验证指南
************************

下面是实现 CodeIgniter 表单验证的一个简易教程。

为了进行表单验证，你需要这三样东西：

#. 一个包含表单的 :doc:`视图 <../general/views>` 文件。
#. 一个包含“成功”信息的视图文件，在成功提交后将被显示。
#. 一个接收并处理所提交数据的 :doc:`控制器 <../general/controllers>` 方法。

让我们以一个会员注册表单为例来创建这三样东西。

表单
========

使用文本编辑器创建一个名为 myform.php 的文件，在它里面插入如下代码，
并把它保存到你的 applications/views/ 目录下::

	<html>
	<head>
	<title>My Form</title>
	</head>
	<body>

	<?php echo validation_errors(); ?>

	<?php echo form_open('form'); ?>

	<h5>Username</h5>
	<input type="text" name="username" value="" size="50" />

	<h5>Password</h5>
	<input type="text" name="password" value="" size="50" />

	<h5>Password Confirm</h5>
	<input type="text" name="passconf" value="" size="50" />

	<h5>Email Address</h5>
	<input type="text" name="email" value="" size="50" />

	<div><input type="submit" value="Submit" /></div>

	</form>

	</body>
	</html>

成功页面
================

使用文本编辑器创建一个名为 formsuccess.php 的文件，在它里面插入如下代码，
并把它保存到你的 applications/views/ 目录下::

	<html>
	<head>
	<title>My Form</title>
	</head>
	<body>

	<h3>Your form was successfully submitted!</h3>

	<p><?php echo anchor('form', 'Try it again!'); ?></p>

	</body>
	</html>

控制器
==============

使用文本编辑器创建一个名为 Form.php 的控制器文件，在它里面插入如下代码，
并把它保存到你的 application/controllers/ 目录下::

	<?php

	class Form extends CI_Controller {

		public function index()
		{
			$this->load->helper(array('form', 'url'));

			$this->load->library('form_validation');

			if ($this->form_validation->run() == FALSE)
			{
				$this->load->view('myform');
			}
			else
			{
				$this->load->view('formsuccess');
			}
		}
	}

试一下！
========

访问类似于下面这样的 URL 来体验一下你的表单::

	example.com/index.php/form/

如果你提交表单，你会看到表单只是简单重新加载了，这是因为你还没有设置任何验证规则。

**由于你还没有告诉表单验证类验证什么东西，它默认返回 FALSE， ``run()``
方法只在全部成功匹配了你的规则后才会返回 TRUE 。**

解释
===========

在这个页面上你会注意到以下几点：

例子中的表单（myform.php）是一个标准的 Web 表单，除了以下两点：

#. 它使用了一个 表单辅助函数 来创建表单的起始标签。，严格来说这并不是必要的，
   你完全可以使用标准的 HTML 来创建，使用辅助函数的好处是它生成 action 的时候，
   是基于你配置文件来生成 URL 的，这使得你的应用在更改 URL 时更具移植性。
#. 在表单的顶部你将注意到如下函数调用：
   ::

	<?php echo validation_errors(); ?>

   这个函数将会返回验证器返回的所有错误信息。如果没有错误信息，它将返回空字符串。

控制器（Form.php）有一个方法： ``index()`` 。这个方法初始化验证类，
并加载你视图中用到的 表单辅助库 和 URL 辅助库，它也会 执行 验证流程，
基于验证是否成功，它会重新显示表单或显示成功页面。

.. _setting-validation-rules:

设置验证规则
========================

CodeIgniter 允许你为单个表单域创建多个验证规则，按顺序层叠在一起，
你也可以同时对表单域的数据进行预处理。要设置验证规则，
可以使用 ``set_rules()``  方法::

	$this->form_validation->set_rules();

上面的方法有 **三个** 参数：

#. 表单域名 - 就是你给表单域取的那个名字。
#. 表单域的 "人性化" 名字，它将被插入到错误信息中。例如，
   如果你有一个表单域叫做 “user” ，你可能会给它一个人性化的名字叫做 “用户名” 。
#. 为此表单域设置的验证规则。
#. （可选的）当此表单域设置自定义的错误信息，如果没有设置该参数，将使用默认的。

.. note:: 如果你想让表单域的名字保存在一个语言文件里，请参考 :ref:`translating-field-names`

下面是个例子，在你的控制器（Form.php）中紧接着验证初始化函数之后，添加这段代码::

	$this->form_validation->set_rules('username', 'Username', 'required');
	$this->form_validation->set_rules('password', 'Password', 'required');
	$this->form_validation->set_rules('passconf', 'Password Confirmation', 'required');
	$this->form_validation->set_rules('email', 'Email', 'required');

你的控制器现在看起来像这样::

	<?php

	class Form extends CI_Controller {

		public function index()
		{
			$this->load->helper(array('form', 'url'));

			$this->load->library('form_validation');

			$this->form_validation->set_rules('username', 'Username', 'required');
			$this->form_validation->set_rules('password', 'Password', 'required',
				array('required' => 'You must provide a %s.')
			);
			$this->form_validation->set_rules('passconf', 'Password Confirmation', 'required');
			$this->form_validation->set_rules('email', 'Email', 'required');

			if ($this->form_validation->run() == FALSE)
			{
				$this->load->view('myform');
			}
			else
			{
				$this->load->view('formsuccess');
			}
		}
	}

现在如果你不填写表单就提交，你将会看到错误信息。如果你填写了所有的表单域并提交，你会看到成功页。

.. note:: 当出现错误时表单页将重新加载，所有的表单域将会被清空，并没有被重新填充。
	稍后我们再去处理这个问题。

使用数组来设置验证规则
============================

在继续之前请注意，如果你更喜欢通过一个操作设置所有规则的话，
你也可以使用一个数组来设置验证规则，如果你使用这种方式，
你必须像下面这样来定义你的数组::

	$config = array(
		array(
			'field' => 'username',
			'label' => 'Username',
			'rules' => 'required'
		),
		array(
			'field' => 'password',
			'label' => 'Password',
			'rules' => 'required',
			'errors' => array(
				'required' => 'You must provide a %s.',
			),
		),
		array(
			'field' => 'passconf',
			'label' => 'Password Confirmation',
			'rules' => 'required'
		),
		array(
			'field' => 'email',
			'label' => 'Email',
			'rules' => 'required'
		)
	);

	$this->form_validation->set_rules($config);

级联规则（Cascading Rules）
==============================

CodeIgniter 允许你将多个规则连接在一起。让我们试一试，修改规则设置函数中的第三个参数，如下::

	$this->form_validation->set_rules(
		'username', 'Username',
		'required|min_length[5]|max_length[12]|is_unique[users.username]',
		array(
			'required'	=> 'You have not provided %s.',
			'is_unique'	=> 'This %s already exists.'
		)
	);
	$this->form_validation->set_rules('password', 'Password', 'required');
	$this->form_validation->set_rules('passconf', 'Password Confirmation', 'required|matches[password]');
	$this->form_validation->set_rules('email', 'Email', 'required|valid_email|is_unique[users.email]');

上面的代码设置了以下规则：

#. 用户名表单域长度不得小于 5 个字符、不得大于 12 个字符。
#. 密码表单域必须跟密码确认表单域的数据一致。
#. 电子邮件表单域必须是一个有效邮件地址。

马上试试看！提交不合法的数据后你会看到新的错误信息，跟你设置的新规则相符。
还有很多其他的验证规则，你可以阅读验证规则参考。

.. note:: 你也可以传一个包含规则的数组给 ``set_rules()`` 方法来替代字符串，例如::

	$this->form_validation->set_rules('username', 'Username', array('required', 'min_length[5]'));

预处理数据
=============

除了上面我们使用的那些验证函数，你还可以以多种方式来预处理你的数据。
例如，你可以设置像这样的规则::

	$this->form_validation->set_rules('username', 'Username', 'trim|required|min_length[5]|max_length[12]');
	$this->form_validation->set_rules('password', 'Password', 'trim|required|min_length[8]');
	$this->form_validation->set_rules('passconf', 'Password Confirmation', 'trim|required|matches[password]');
	$this->form_validation->set_rules('email', 'Email', 'trim|required|valid_email');

在上面的例子里，我们去掉字符串两端空白（trimming），检查字符串的长度，确保两次输入的密码一致。

**任何只有一个参数的 PHP 原生函数都可以被用作一个规则，比如 ``htmlspecialchars``， ``trim`` 等等。**

.. note:: 你一般会在验证规则**之后**使用预处理功能，这样如果发生错误，原数据将会被显示在表单。

重新填充表单
======================

目前为止我们只是在处理错误，是时候用提交的数据重新填充表单了。
CodeIgniter 为此提供了几个辅助函数，你最常用到的一个是::

	set_value('field name')

打开 myform.php 视图文件并使用 :php:func:`set_value()` 函数更新每个表单域的 **值** ：

**不要忘记在 :php:func:`set_value()` 函数中包含每个表单域的名字！**

::

	<html>
	<head>
	<title>My Form</title>
	</head>
	<body>

	<?php echo validation_errors(); ?>

	<?php echo form_open('form'); ?>

	<h5>Username</h5>
	<input type="text" name="username" value="<?php echo set_value('username'); ?>" size="50" />

	<h5>Password</h5>
	<input type="text" name="password" value="<?php echo set_value('password'); ?>" size="50" />

	<h5>Password Confirm</h5>
	<input type="text" name="passconf" value="<?php echo set_value('passconf'); ?>" size="50" />

	<h5>Email Address</h5>
	<input type="text" name="email" value="<?php echo set_value('email'); ?>" size="50" />

	<div><input type="submit" value="Submit" /></div>

	</form>

	</body>
	</html>

现在刷新你的页面并提交表单触发一个错误，你的表单域应该被重新填充了。

.. note:: 下面的 :ref:`class-reference` 节包含了可以让你重填下拉菜单，单选框和复选框的函数。

.. important:: 如果你使用一个数组作为一个表单域的名字，那么函数的参数也应该是一个数组。例如::

	<input type="text" name="colors[]" value="<?php echo set_value('colors[]'); ?>" size="50" />

更多信息请参考下面的 :ref:`using-arrays-as-field-names` 一节。

回调：你自己的验证函数
======================================

验证系统支持设置你自己的验证函数，这样你可以扩展验证类以适应你自己的需求。
例如，如果你需要查询数据库来检查用户名是否唯一，你可以创建一个回调函数，
让我们来新建一个例子。

在你的控制器中，将用户名的规则修改为::

	$this->form_validation->set_rules('username', 'Username', 'callback_username_check');

然后在你的控制器中添加一个新的方法 ``username_check()`` 。你的控制器现在看起来是这样::

	<?php

	class Form extends CI_Controller {

		public function index()
		{
			$this->load->helper(array('form', 'url'));

			$this->load->library('form_validation');

			$this->form_validation->set_rules('username', 'Username', 'callback_username_check');
			$this->form_validation->set_rules('password', 'Password', 'required');
			$this->form_validation->set_rules('passconf', 'Password Confirmation', 'required');
			$this->form_validation->set_rules('email', 'Email', 'required|is_unique[users.email]');

			if ($this->form_validation->run() == FALSE)
			{
				$this->load->view('myform');
			}
			else
			{
				$this->load->view('formsuccess');
			}
		}

		public function username_check($str)
		{
			if ($str == 'test')
			{
				$this->form_validation->set_message('username_check', 'The {field} field can not be the word "test"');
				return FALSE;
			}
			else
			{
				return TRUE;
			}
		}

	}

重新载入表单并以 “test” 作为用户名提交数据，你会看到表单域数据被传递到你的回调函数中处理了。

要调用一个回调函数只需把函数名加一个 "callback\_" **前缀**并放在验证规则里。
如果你需要在你的回调函数中调用一个额外的参数，你只需要在回调函数后面用[]把参数
（这个参数只能是字符串类型）括起来，例如："callback_foo**[bar]**" ，
其中 bar 将成为你的回调函数中的第二个参数。

.. note:: 你也可以对传给你的表单数据进行处理并返回，如果你的回调函数返回了除布尔型的 
	TRUE 或 FALSE 之外的任何值，它将被认为是你新处理过的表单数据。

使用任何可调用的方法作为验证规则
================================

如果回调的规则对你来说还不够好（譬如，它们被限制只能定义在控制器中），
别失望，还有一种方法来创建自定义的规则：任何 ``is_callable()`` 函数返回 
TRUE 的东西都可以作为规则。

看下面的例子::

	$this->form_validation->set_rules(
		'username', 'Username',
		array(
			'required',
			array($this->users_model, 'valid_username')
		)
	);

上面的代码将使用 ``Users_model`` 模型的 ``valid_username()`` 方法来作为验证规则。

当然，这只是个例子，规则不只限于使用模型的方法，你可以使用任何对象和方法
来接受域值作为第一个参数。如果你使用 PHP 5.3+ ，还可以使用匿名方法::

	$this->form_validation->set_rules(
		'username', 'Username',
		array(
			'required',
			function($value)
			{
				// Check $value
			}
		)
	);

但是，由于可调用的规则并不是一个字符串，也没有一个规则名，所以当你需要为它们设置
相应的错误消息时会有麻烦。为了解决这个问题，你可以将这样的规则放到一个数组的第二个值，
第一个值放置规则名::

	$this->form_validation->set_rules(
		'username', 'Username',
		array(
			'required',
			array('username_callable', array($this->users_model, 'valid_username'))
		)
	);

下面是使用匿名方法（PHP 5.3+）的版本::

	$this->form_validation->set_rules(
		'username', 'Username',
		array(
			'required',
			array(
				'username_callable',
				function($str)
				{
					// Check validity of $str and return TRUE or FALSE
				}
			)
		)
	);

.. _setting-error-messages:

设置错误信息
======================

所有原生的错误信息都位于下面的语言文件中： 
**language/english/form_validation_lang.php**

如果要为某个规则设置你的自定义信息你可以编辑那个文件，或使用下面的方法::

	$this->form_validation->set_message('rule', 'Error Message');

如果你要为某个域的某个规则设置你的自定义信息，可以使用 set_rules() 方法::

	$this->form_validation->set_rules('field_name', 'Field Label', 'rule1|rule2|rule3',
		array('rule2' => 'Error Message on rule2 for this field_name')
	);

其中， rule 是该规则的名称，Error Message 为该规则显示的错误信息。

如果你希望在错误信息中包含域的人性化名称，或者某些规则设置的一个可选参数
（例如：max_length），你可以在消息中使用 **{field}** 和 **{param}** 标签::

	$this->form_validation->set_message('min_length', '{field} must have at least {param} characters.');

如果域的人性化名称为 Username ，并有一个规则 min_length[5] ，那么错误信息会显示：
"Username must have at least 5 characters."

.. note:: 老的 `sprintf()` 方法和在字符串使用 **%s** 也还可以工作，但是会覆写掉上面的标签。
	所以你同时只应该使用两个中的一个。

在上面回调的例子中，错误信息是通过方法的名称（不带 "callback\_" 前缀）来设置的::

	$this->form_validation->set_message('username_check')

.. _translating-field-names:

翻译表单域名称
=======================

如果你希望将传递给 ``set_rules()`` 方法的人性化名称存储在一个语言文件中，
使他们能被翻译成其他语言，你可以这么做：

首先，给人性化名称添加一个前缀：**lang:**，如下：

	 $this->form_validation->set_rules('first_name', 'lang:first_name', 'required');

然后，将该名称保存到你的某个语言文件数组中（不带前缀）::

	$lang['first_name'] = 'First Name';

.. note:: 如果你保存的语言文件没有自动被 CI 加载，你要记住在你的控制器中使用下面的方法手工加载::

	$this->lang->load('file_name');

关于语言文件的更多信息，参看 :doc:`语言类 <language>` 。

.. _changing-delimiters:

更改错误定界符
=============================

在默认情况下，表单验证类会使用 <p> 标签来分割每条错误信息。
你可以通过全局的，单独的，或者通过配置文件对其进行自定义。

#. **全局的修改定界符**
   要在全局范围内修改错误定界符，你可以在控制器方法中加载表单验证类之后，使用下面的代码::

      $this->form_validation->set_error_delimiters('<div class="error">', '</div>');

   在这个例子中，我们改成使用 <div> 标签来作为定界符。

#. **单独的修改定界符**
   有两个错误生成方法可以用于设置它们自己的定界符，如下::

      <?php echo form_error('field name', '<div class="error">', '</div>'); ?>

   或者::

      <?php echo validation_errors('<div class="error">', '</div>'); ?>

#. **在配置文件中设置定界符**
   你还可以在配置文件 application/config/form_validation.php 中定义错误定界符，如下::

      $config['error_prefix'] = '<div class="error_prefix">';
      $config['error_suffix'] = '</div>';

单独显示错误
===========================

如果你喜欢紧挨着每个表单域显示错误信息而不是显示为一个列表，
你可以使用 :php:func:`form_error()` 方法。

尝试一下！修改你的表单如下::

	<h5>Username</h5>
	<?php echo form_error('username'); ?>
	<input type="text" name="username" value="<?php echo set_value('username'); ?>" size="50" />

	<h5>Password</h5>
	<?php echo form_error('password'); ?>
	<input type="text" name="password" value="<?php echo set_value('password'); ?>" size="50" />

	<h5>Password Confirm</h5>
	<?php echo form_error('passconf'); ?>
	<input type="text" name="passconf" value="<?php echo set_value('passconf'); ?>" size="50" />

	<h5>Email Address</h5>
	<?php echo form_error('email'); ?>
	<input type="text" name="email" value="<?php echo set_value('email'); ?>" size="50" />

如果没有错误信息，将不会显示。如果有错误信息，将会在输入框的旁边单独显示。

.. important:: 如果你使用一个数组作为一个表单域的名字，那么函数的参数也应该是一个数组。例如::

	<?php echo form_error('options[size]'); ?>
	<input type="text" name="options[size]" value="<?php echo set_value("options[size]"); ?>" size="50" />

更多信息，请参考下面的 :ref:`using-arrays-as-field-names` 一节。

验证数组（除 $_POST 数组）
=======================================

有时你可能希望对一个单纯的数组进行验证，而不是对 ``$_POST`` 数组。

在这种情况下，你可以先定义要验证的数组::

	$data = array(
		'username' => 'johndoe',
		'password' => 'mypassword',
		'passconf' => 'mypassword'
	);

	$this->form_validation->set_data($data);

创建验证规则，运行验证，获取错误消息和上面说讲的那些验证 ``$_POST`` 数组是一样的。

.. important:: 如果你想验证多个数组，那么你应该在验证下一个新数组之前先调用 ``reset_validation()`` 方法。

更多信息，请参数下面的 :ref:`class-reference` 一节。

.. _saving-groups:

************************************************
将一系列验证规则保存到一个配置文件
************************************************

表单验证类的一个不错的特性是，它允许你将整个应用的所有验证规则存储到一个配置文件中去。
你可以对这些规则进行分组，这些组既可以在匹配控制器和方法时自动加载，也可以在需要时手动调用。

如何保存你的规则
======================

如果要保存验证规则，你需要在 application/config/ 目录下创建一个名为 form_validation.php 的文件。
然后在该文件中，将验证规则保存在数组 $config 中即可。和之前介绍的一样，验证规则数组格式如下::

	$config = array(
		array(
			'field' => 'username',
			'label' => 'Username',
			'rules' => 'required'
		),
		array(
			'field' => 'password',
			'label' => 'Password',
			'rules' => 'required'
		),
		array(
			'field' => 'passconf',
			'label' => 'Password Confirmation',
			'rules' => 'required'
		),
		array(
			'field' => 'email',
			'label' => 'Email',
			'rules' => 'required'
		)
	);

你的验证规则会被自动加载，当用户触发 ``run()`` 方法时被调用。

请务必要将数组名称定义成 ``$config`` 。

创建规则集
======================

为了将你的多个规则组织成规则集，你需要将它们放置到子数组中。
请参考下面的例子，在此例中我们设置了两组规则集，我们分别命名为 
"signup" 和 "email" ，你可以根据自己的需求任意命名::

	$config = array(
		'signup' => array(
			array(
				'field' => 'username',
				'label' => 'Username',
				'rules' => 'required'
			),
			array(
				'field' => 'password',
				'label' => 'Password',
				'rules' => 'required'
			),
			array(
				'field' => 'passconf',
				'label' => 'Password Confirmation',
				'rules' => 'required'
			),
			array(
				'field' => 'email',
				'label' => 'Email',
				'rules' => 'required'
			)
		),
		'email' => array(
			array(
				'field' => 'emailaddress',
				'label' => 'EmailAddress',
				'rules' => 'required|valid_email'
			),
			array(
				'field' => 'name',
				'label' => 'Name',
				'rules' => 'required|alpha'
			),
			array(
				'field' => 'title',
				'label' => 'Title',
				'rules' => 'required'
			),
			array(
				'field' => 'message',
				'label' => 'MessageBody',
				'rules' => 'required'
			)
		)
	);

调用某组验证规则
=============================

为了调用特定组的验证规则，你可以将它的名称传给 ``run()`` 方法。
例如，使用 signup 规则你可以这样::

	if ($this->form_validation->run('signup') == FALSE)
	{
		$this->load->view('myform');
	}
	else
	{
		$this->load->view('formsuccess');
	}

将控制器方法和规则集关联在一起
=================================================

调用一组规则的另一种方法是将控制器方法和规则集关联在一起（这种方法也更自动），
例如，假设你有一个控制器类 Member 和一个方法 signup ，你的类如下::

	<?php

	class Member extends CI_Controller {

		public function signup()
		{
			$this->load->library('form_validation');

			if ($this->form_validation->run() == FALSE)
			{
				$this->load->view('myform');
			}
			else
			{
				$this->load->view('formsuccess');
			}
		}
	}

在你的验证规则配置文件中，使用 member/signup 来给这组规则集命名::

	$config = array(
		'member/signup' => array(
			array(
				'field' => 'username',
				'label' => 'Username',
				'rules' => 'required'
			),
			array(
				'field' => 'password',
				'label' => 'Password',
				'rules' => 'required'
			),
			array(
				'field' => 'passconf',
				'label' => 'PasswordConfirmation',
				'rules' => 'required'
			),
			array(
				'field' => 'email',
				'label' => 'Email',
				'rules' => 'required'
			)
		)
	);

当一组规则的名称和控制器类/方法名称完全一样时，它会在该控制器类/方法中自动被 
``run()`` 方法调用。

.. _using-arrays-as-field-names:

***************************
使用数组作为域名称
***************************

表单验证类支持使用数组作为域名称，比如::

	<input type="text" name="options[]" value="" size="50" />

如果你将域名称定义为数组，那么在使用域名称作为参数的 :ref:`辅助库函数 <helper-functions>` 时，
你必须传递给他们与域名称完全一样的数组名，对这个域名称的验证规则也一样。

例如，为上面的域设置验证规则::

	$this->form_validation->set_rules('options[]', 'Options', 'required');

或者，为上面的域显示错误信息::

	<?php echo form_error('options[]'); ?>

或者，重新填充该域的值::

	<input type="text" name="options[]" value="<?php echo set_value('options[]'); ?>" size="50" />

你也可以使用多维数组作为域的名称，例如::

	<input type="text" name="options[size]" value="" size="50" />

甚至::

	<input type="text" name="sports[nba][basketball]" value="" size="50" />

和上面的例子一样，你必须在辅助函数中使用完全一样的数组名::

	<?php echo form_error('sports[nba][basketball]'); ?>

如果你正在使用复选框（或其他拥有多个选项的域），不要忘了在每个选项后加个空的方括号，
这样，所有的选择才会被添加到 POST 数组中::

	<input type="checkbox" name="options[]" value="red" />
	<input type="checkbox" name="options[]" value="blue" />
	<input type="checkbox" name="options[]" value="green" />

或者，使用多维数组::

	<input type="checkbox" name="options[color][]" value="red" />
	<input type="checkbox" name="options[color][]" value="blue" />
	<input type="checkbox" name="options[color][]" value="green" />

当你使用辅助函数时，也要添加方括号::

	<?php echo form_error('options[color][]'); ?>


**************
规则参考
**************

下表列出了所有可用的原生规则：

========================= ========== ============================================================================================= =======================
规则                      参数        描述                                                                                          例子
========================= ========== ============================================================================================= =======================
**required**              No         如果表单元素为空，返回 FALSE 
**matches**               Yes        如果表单元素值与参数中对应的表单字段的值不相等，返回 FALSE                                     matches[form_item]
**regex_match**           Yes        如果表单元素不匹配正则表达式，返回 FALSE                                                       regex_match[/regex/]
**differs**               Yes        如果表单元素值与参数中对应的表单字段的值相等，返回 FALSE                                       differs[form_item]
**is_unique**             Yes        如果表单元素值在指定的表和字段中并不唯一，返回 FALSE                                           is_unique[table.field]
                                     注意：这个规则需要启用 :doc:`查询构造器 <../database/query_builder>`
**min_length**            Yes        如果表单元素值的长度小于参数值，返回 FALSE                                                     min_length[3]
**max_length**            Yes        如果表单元素值的长度大于参数值，返回 FALSE                                                     max_length[12]
**exact_length**          Yes        如果表单元素值的长度不等于参数值，返回 FALSE                                                   exact_length[8]
**greater_than**          Yes        如果表单元素值小于或等于参数值或非数字，返回 FALSE                                             greater_than[8]
**greater_than_equal_to** Yes        如果表单元素值小于参数值或非数字，返回 FALSE                                                   greater_than_equal_to[8]
**less_than**             Yes        如果表单元素值大于或等于参数值或非数字，返回 FALSE                                             less_than[8]
**less_than_equal_to**    Yes        如果表单元素值大于参数值或非数字，返回 FALSE                                                   less_than_equal_to[8]
**in_list**               Yes        如果表单元素值不在规定的列表中，返回 FALSE                                                     in_list[red,blue,green]
**alpha**                 No         如果表单元素值包含除字母以外的其他字符，返回 FALSE
**alpha_numeric**         No         如果表单元素值包含除字母和数字以外的其他字符，返回 FALSE
**alpha_numeric_spaces**  No         如果表单元素值包含除字母、数字和空格以外的其他字符，返回 FALSE
                                     应该在 trim 之后使用，避免首尾的空格
**alpha_dash**            No         如果表单元素值包含除字母/数字/下划线/破折号以外的其他字符，返回 FALSE
**numeric**               No         如果表单元素值包含除数字以外的字符，返回 FALSE
**integer**               No         如果表单元素包含除整数以外的字符，返回 FALSE
**decimal**               No         如果表单元素包含非十进制数字时，返回 FALSE
**is_natural**            No         如果表单元素值包含了非自然数的其他数值 （不包括零），返回 FALSE
                                     自然数形如：0、1、2、3 .... 等等。
**is_natural_no_zero**    No         如果表单元素值包含了非自然数的其他数值 （包括零），返回 FALSE
                                     非零的自然数：1、2、3 .... 等等。
**valid_url**             No         如果表单元素值包含不合法的 URL，返回 FALSE
**valid_email**           No         如果表单元素值包含不合法的 email 地址，返回 FALSE
**valid_emails**          No         如果表单元素值包含不合法的 email 地址（地址之间用逗号分割），返回 FALSE
**valid_ip**              No         如果表单元素值不是一个合法的 IP 地址，返回 FALSE
                                     通过可选参数 "ipv4" 或 "ipv6" 来指定 IP 地址格式。
**valid_base64**          No         如果表单元素值包含除了 base64 编码字符之外的其他字符，返回 FALSE
========================= ========== ============================================================================================= =======================

.. note:: 这些规则也可以作为独立的函数被调用，例如::

		$this->form_validation->required($string);

.. note:: 你也可以使用任何一个接受两个参数的原生 PHP 函数（其中至少有一个参数是必须的，用于传递域值）

******************
预处理参考
******************

下表列出了所有可用的预处理方法：

==================== ========= =======================================================================================================
名称                 参数      描述
==================== ========= =======================================================================================================
**prep_for_form**    No        将特殊字符的转换，以便可以在表单域中显示 HTML 数据，而不会破坏它
**prep_url**         No        当 URL 丢失 "http://" 时，添加 "http://"
**strip_image_tags** No        移除 HTML 中的 image 标签，只保留 URL
**encode_php_tags**  No        将 PHP 标签转成实体
==================== ========= =======================================================================================================

.. note:: 你也可以使用任何一个接受一个参数的原生 PHP 函数。
	例如： ``trim()`` 、 ``htmlspecialchars()`` 、 ``urldecode()`` 等

.. _class-reference:

***************
类参考
***************

.. php:class:: CI_Form_validation

	.. php:method:: set_rules($field[, $label = ''[, $rules = '']])

		:param	string	$field: Field name
		:param	string	$label: Field label
		:param	mixed	$rules: Validation rules, as a string list separated by a pipe "|", or as an array or rules
		:returns:	CI_Form_validation instance (method chaining)
		:rtype:	CI_Form_validation

		允许您设置验证规则，如在本教程上面描述的：

		-  :ref:`setting-validation-rules`
		-  :ref:`saving-groups`

	.. php:method:: run([$group = ''])

		:param	string	$group: The name of the validation group to run
		:returns:	TRUE on success, FALSE if validation failed
		:rtype:	bool

		运行验证程序。成功返回 TRUE，失败返回 FALSE。
		您也可以传一个验证规则集的名称作为参数，参考 :ref:`saving-groups`

	.. php:method:: set_message($lang[, $val = ''])

		:param	string	$lang: The rule the message is for
		:param	string	$val: The message
		:returns:	CI_Form_validation instance (method chaining)
		:rtype:	CI_Form_validation

		允许您设置自定义错误消息，参考 :ref:`setting-error-messages`

	.. php:method:: set_error_delimiters([$prefix = '<p>'[, $suffix = '</p>']])

		:param	string	$prefix: Error message prefix
		:param	string	$suffix: Error message suffix
		:returns:	CI_Form_validation instance (method chaining)
		:rtype:	CI_Form_validation

		设置错误消息的前缀和后缀。

	.. php:method:: set_data($data)

		:param	array	$data: Array of data validate
		:returns:	CI_Form_validation instance (method chaining)
		:rtype:	CI_Form_validation

		允许你设置一个数组来进行验证，取代默认的 ``$_POST`` 数组

	.. php:method:: reset_validation()

		:returns:	CI_Form_validation instance (method chaining)
		:rtype:	CI_Form_validation

		当你验证多个数组时，该方法可以重置验证规则，当验证下一个新数组时应该调用它。

	.. php:method:: error_array()

		:returns:	Array of error messages
		:rtype:	array

		返回错误信息数组。

	.. php:method:: error_string([$prefix = ''[, $suffix = '']])

		:param	string	$prefix: Error message prefix
		:param	string	$suffix: Error message suffix
		:returns:	Error messages as a string
		:rtype:	string

		返回所有的错误信息（和 error_array() 返回结果一样），并使用换行符分割格式化成字符串

	.. php:method:: error($field[, $prefix = ''[, $suffix = '']])

		:param	string $field: Field name
		:param	string $prefix: Optional prefix
		:param	string $suffix: Optional suffix
		:returns:	Error message string
		:rtype:	string

		返回特定域的错误消息，也可以添加一个前缀和/或后缀（通常是 HTML 标签）

	.. php:method:: has_rule($field)

		:param	string	$field: Field name
		:returns:	TRUE if the field has rules set, FALSE if not
		:rtype:	bool

		检查某个域是否有验证规则。

.. _helper-functions:

****************
辅助库参考
****************

请参考 :doc:`表单辅助库 <../helpers/form_helper>` 手册了解以下函数：

-  :php:func:`form_error()`
-  :php:func:`validation_errors()`
-  :php:func:`set_value()`
-  :php:func:`set_select()`
-  :php:func:`set_checkbox()`
-  :php:func:`set_radio()`

注意这些都是过程式的函数，所以 **不需要** 添加 ``$this->form_validation`` 就可以直接调用它们。