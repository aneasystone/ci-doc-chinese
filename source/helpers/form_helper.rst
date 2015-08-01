###########
表单辅助库
###########

表单辅助库包含了一些函数用于帮助你处理表单。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

加载辅助库
===================

使用下面的代码来加载表单辅助库::

	$this->load->helper('form');

对域值转义
=====================

你可能会需要在表单元素中使用 HTML 或者诸如引号这样的字符，为了安全性，
你需要使用 :doc:`通用函数 <../general/common_functions>` :func:`html_escape()` 。

考虑下面这个例子::

	$string = 'Here is a string containing "quoted" text.';

	<input type="text" name="myfield" value="<?php echo $string; ?>" />

因为上面的字符串中包含了一对引号，它会破坏表单，使用 :php:func:`html_escape()`
函数可以对 HTML 的特殊字符进行转义，从而可以安全的在域值中使用字符串::

	<input type="text" name="myfield" value="<?php echo html_escape($string); ?>" />

.. note:: 如果你使用了这个页面上介绍的任何一个函数，表单的域值会被自动转义，
	所以你无需再调用这个函数。只有在你创建自己的表单元素时需要使用它。

可用函数
===================

该辅助库有下列可用函数：

.. php:function:: form_open([$action = ''[, $attributes = ''[, $hidden = array()]]])

	:param	string	$action: Form action/target URI string
	:param	array	$attributes: HTML attributes
	:param	array	$hidden: An array of hidden fields' definitions
	:returns:	An HTML form opening tag
	:rtype:	string

	生成一个 form 起始标签，并且它的 action URL 会根据你的配置文件自动生成。
	你还可以给表单添加属性和隐藏域，另外，它还会根据你配置文件中的字符集参数
	自动生成 `accept-charset` 属性。

	使用该函数来生成标签比你自己写 HTML 代码最大的好处是：当你的 URL 变动时，
	它可以提供更好的可移植性。

	这里是个简单的例子::

		echo form_open('email/send');

	上面的代码会创建一个表单，它的 action 为根 URL 加上 "email/send"，向下面这样::

		<form method="post" accept-charset="utf-8" action="http://example.com/index.php/email/send">

	**添加属性**

		可以通过第二个参数传递一个关联数组来添加属性，例如::

			$attributes = array('class' => 'email', 'id' => 'myform');
			echo form_open('email/send', $attributes);

		另外，第二个参数你也可以直接使用字符串::

			echo form_open('email/send', 'class="email" id="myform"');

		上面的代码会创建一个类似于下面的表单::

			<form method="post" accept-charset="utf-8" action="http://example.com/index.php/email/send" class="email" id="myform">

	**添加隐藏域**

		可以通过第三个参数传递一个关联数组来添加隐藏域，例如::

			$hidden = array('username' => 'Joe', 'member_id' => '234');
			echo form_open('email/send', '', $hidden);

		你可以使用一个空值跳过第二个参数。

		上面的代码会创建一个类似于下面的表单::

			<form method="post" accept-charset="utf-8" action="http://example.com/index.php/email/send">
				<input type="hidden" name="username" value="Joe" />
				<input type="hidden" name="member_id" value="234" />


.. php:function:: form_open_multipart([$action = ''[, $attributes = array()[, $hidden = array()]])

	:param	string	$action: Form action/target URI string
	:param	array	$attributes: HTML attributes
	:param	array	$hidden: An array of hidden fields' definitions
	:returns:	An HTML multipart form opening tag
	:rtype:	string

	这个函数和上面的 :php:func:`form_open()` 函数完全一样，
	只是它会给表单添加一个 *multipart* 属性，在你使用表单上传文件时必须使用它。


.. php:function:: form_hidden($name[, $value = ''])

	:param	string	$name: Field name
	:param	string	$value: Field value
	:returns:	An HTML hidden input field tag
	:rtype:	string

	生成隐藏域。你可以使用名称和值两个参数来创建一个隐藏域::

		form_hidden('username', 'johndoe');
		// Would produce: <input type="hidden" name="username" value="johndoe" />

	... 或者你可以使用一个关联数组，来生成多个隐藏域::

		$data = array(
			'name'	=> 'John Doe',
			'email'	=> 'john@example.com',
			'url'	=> 'http://example.com'
		);

		echo form_hidden($data);

		/*
			Would produce:
			<input type="hidden" name="name" value="John Doe" />
			<input type="hidden" name="email" value="john@example.com" />
			<input type="hidden" name="url" value="http://example.com" />
		*/

	你还可以向第二个参数传递一个关联数组::

		$data = array(
			'name'	=> 'John Doe',
			'email'	=> 'john@example.com',
			'url'	=> 'http://example.com'
		);

		echo form_hidden('my_array', $data);

		/*
			Would produce:

			<input type="hidden" name="my_array[name]" value="John Doe" />
			<input type="hidden" name="my_array[email]" value="john@example.com" />
			<input type="hidden" name="my_array[url]" value="http://example.com" />
		*/

	如果你想创建带有其他属性的隐藏域，可以这样::

		$data = array(
			'type'	=> 'hidden',
			'name'	=> 'email',
			'id'	=> 'hiddenemail',
			'value'	=> 'john@example.com',
			'class'	=> 'hiddenemail'
		);

		echo form_input($data);

		/*
			Would produce:

			<input type="hidden" name="email" value="john@example.com" id="hiddenemail" class="hiddenemail" />
		*/

.. php:function:: form_input([$data = ''[, $value = ''[, $extra = '']])

	:param	array	$data: Field attributes data
	:param	string	$value: Field value
	:param	string	$extra: Extra attributes to be added to the tag *as is*
	:returns:	An HTML text input field tag
	:rtype:	string

	用于生成标准的文本输入框，你可以简单的使用文本域的名称和值::

		echo form_input('username', 'johndoe');

	或者使用一个关联数组，来包含任何你想要的数据::

		$data = array(
			'name'		=> 'username',
			'id'		=> 'username',
			'value'		=> 'johndoe',
			'maxlength'	=> '100',
			'size'		=> '50',
			'style'		=> 'width:50%'
		);

		echo form_input($data);

		/*
			Would produce:

			<input type="text" name="username" value="johndoe" id="username" maxlength="100" size="50" style="width:50%"  />
		*/

	如果你还希望能包含一些额外的数据，譬如 JavaScript ，你可以通过第三个参数传一个字符串::

		$js = 'onClick="some_function()"';
		echo form_input('username', 'johndoe', $js);

.. php:function:: form_password([$data = ''[, $value = ''[, $extra = '']]])

	:param	array	$data: Field attributes data
	:param	string	$value: Field value
	:param	string	$extra: Extra attributes to be added to the tag *as is*
	:returns:	An HTML password input field tag
	:rtype:	string

	该函数和上面的 :php:func:`form_input()` 函数一样，只是生成的输入框为 "password" 类型。


.. php:function:: form_upload([$data = ''[, $value = ''[, $extra = '']]])

	:param	array	$data: Field attributes data
	:param	string	$value: Field value
	:param	string	$extra: Extra attributes to be added to the tag *as is*
	:returns:	An HTML file upload input field tag
	:rtype:	string

	该函数和上面的 :php:func:`form_input()` 函数一样，只是生成的输入框为 "file" 类型，
	可以用来上传文件。


.. php:function:: form_textarea([$data = ''[, $value = ''[, $extra = '']]])

	:param	array	$data: Field attributes data
	:param	string	$value: Field value
	:param	string	$extra: Extra attributes to be added to the tag *as is*
	:returns:	An HTML textarea tag
	:rtype:	string

	该函数和上面的 :php:func:`form_input()` 函数一样，只是生成的输入框为 "textarea" 类型。

	.. note:: 对于 textarea 类型的输入框，你可以使用 *rows* 和 *cols* 属性，
		来代替上面例子中的 *maxlength* 和 *size* 属性。

.. php:function:: form_dropdown([$name = ''[, $options = array()[, $selected = array()[, $extra = '']]]])

	:param	string	$name: Field name
	:param	array	$options: An associative array of options to be listed
	:param	array	$selected: List of fields to mark with the *selected* attribute
	:param	string	$extra: Extra attributes to be added to the tag *as is*
	:returns:	An HTML dropdown select field tag
	:rtype:	string

	用于生成一个标准的下拉框域。第一个参数为域的名称，第二个参数为一个关联数组，
	包含所有的选项，第三个参数为你希望默认选中的值。你也可以把第三个参数设置成
	一个包含多个值的数组，CodeIgniter 将会为你生成多选下拉框。

	例如::

		$options = array(
			'small'		=> 'Small Shirt',
			'med'		=> 'Medium Shirt',
			'large'		=> 'Large Shirt',
			'xlarge'	=> 'Extra Large Shirt',
		);

		$shirts_on_sale = array('small', 'large');
		echo form_dropdown('shirts', $options, 'large');

		/*
			Would produce:

			<select name="shirts">
				<option value="small">Small Shirt</option>
				<option value="med">Medium  Shirt</option>
				<option value="large" selected="selected">Large Shirt</option>
				<option value="xlarge">Extra Large Shirt</option>
			</select>
		*/

		echo form_dropdown('shirts', $options, $shirts_on_sale);

		/*
			Would produce:

			<select name="shirts" multiple="multiple">
				<option value="small" selected="selected">Small Shirt</option>
				<option value="med">Medium  Shirt</option>
				<option value="large" selected="selected">Large Shirt</option>
				<option value="xlarge">Extra Large Shirt</option>
			</select>
		*/

	如果你希望为起始标签 <select> 添加一些额外的数据，例如 id 属性或 JavaScript ，
	你可以通过第四个参数传一个字符串::

		$js = 'id="shirts" onChange="some_function();"';
		echo form_dropdown('shirts', $options, 'large', $js);

	如果你传递的 ``$options`` 数组是个多维数组，``form_dropdown()`` 函数将会生成带
	<optgroup> 的下拉框，并使用数组的键作为 label 。


.. php:function:: form_multiselect([$name = ''[, $options = array()[, $selected = array()[, $extra = '']]]])

	:param	string	$name: Field name
	:param	array	$options: An associative array of options to be listed
	:param	array	$selected: List of fields to mark with the *selected* attribute
	:param	string	$extra: Extra attributes to be added to the tag *as is*
	:returns:	An HTML dropdown multiselect field tag
	:rtype:	string

	用于生成一个标准的多选下拉框。第一个参数为域的名称，第二个参数为一个关联数组，
	包含所有的选项，第三个参数为你希望默认选中的一个或多个值。

	参数的用法和上面的 :php:func:`form_dropdown()` 函数一样，只是域的名称需要使用
	数组语法，例如：foo[]


.. php:function:: form_fieldset([$legend_text = ''[, $attributes = array()]])

	:param	string	$legend_text: Text to put in the <legend> tag
	:param	array	$attributes: Attributes to be set on the <fieldset> tag
	:returns:	An HTML fieldset opening tag
	:rtype:	string

	用于生成 fieldset 和 legend 域。

	例如::

		echo form_fieldset('Address Information');
		echo "<p>fieldset content here</p>\n";
		echo form_fieldset_close();

		/*
			Produces:

				<fieldset>
					<legend>Address Information</legend>
						<p>form content here</p>
				</fieldset>
		*/

	和其他的函数类似，你也可以通过给第二个参数传一个关联数组来添加额外的属性::

		$attributes = array(
			'id'	=> 'address_info',
			'class'	=> 'address_info'
		);

		echo form_fieldset('Address Information', $attributes);
		echo "<p>fieldset content here</p>\n";
		echo form_fieldset_close();

		/*
			Produces:

			<fieldset id="address_info" class="address_info">
				<legend>Address Information</legend>
				<p>form content here</p>
			</fieldset>
		*/


.. php:function:: form_fieldset_close([$extra = ''])

	:param	string	$extra: Anything to append after the closing tag, *as is*
	:returns:	An HTML fieldset closing tag
	:rtype:	string
	

	用于生成结束标签 </fieldset> ，使用这个函数唯一的一个好处是，
	它可以在结束标签的后面加上一些其他的数据。例如：

	::

		$string = '</div></div>';
		echo form_fieldset_close($string);
		// Would produce: </fieldset></div></div>


.. php:function:: form_checkbox([$data = ''[, $value = ''[, $checked = FALSE[, $extra = '']]]])

	:param	array	$data: Field attributes data
	:param	string	$value: Field value
	:param	bool	$checked: Whether to mark the checkbox as being *checked*
	:param	string	$extra: Extra attributes to be added to the tag *as is*
	:returns:	An HTML checkbox input tag
	:rtype:	string

	用于生成一个复选框，例如::

		echo form_checkbox('newsletter', 'accept', TRUE);
		// Would produce:  <input type="checkbox" name="newsletter" value="accept" checked="checked" />

	第三个参数为布尔值 TRUE 或 FALSE ，用于指定复选框默认是否为选定状态。

	和其他函数一样，你可以传一个属性的数组给它::

		$data = array(
			'name'		=> 'newsletter',
			'id'		=> 'newsletter',
			'value'		=> 'accept',
			'checked'	=> TRUE,
			'style'		=> 'margin:10px'
		);

		echo form_checkbox($data);
		// Would produce: <input type="checkbox" name="newsletter" id="newsletter" value="accept" checked="checked" style="margin:10px" />

	另外，如果你希望向标签中添加额外的数据如 JavaScript ，也可以传一个字符串给第四个参数::

		$js = 'onClick="some_function()"';
		echo form_checkbox('newsletter', 'accept', TRUE, $js)


.. php:function:: form_radio([$data = ''[, $value = ''[, $checked = FALSE[, $extra = '']]]])

	:param	array	$data: Field attributes data
	:param	string	$value: Field value
	:param	bool	$checked: Whether to mark the radio button as being *checked*
	:param	string	$extra: Extra attributes to be added to the tag *as is*
	:returns:	An HTML radio input tag
	:rtype:	string

	该函数和 :php:func:`form_checkbox()` 函数完全一样，只是它生成的是单选框。


.. php:function:: form_label([$label_text = ''[, $id = ''[, $attributes = array()]]])

	:param	string	$label_text: Text to put in the <label> tag
	:param	string	$id: ID of the form element that we're making a label for
	:param	string	$attributes: HTML attributes
	:returns:	An HTML field label tag
	:rtype:	string

	生成 <label> 标签，例如::

		echo form_label('What is your Name', 'username');
		// Would produce:  <label for="username">What is your Name</label>

	和其他的函数一样，如果你想添加额外的属性的话，可以传一个关联数组给第三个参数::

	例如::

		$attributes = array(
			'class' => 'mycustomclass',
			'style' => 'color: #000;'
		);

		echo form_label('What is your Name', 'username', $attributes);
		// Would produce:  <label for="username" class="mycustomclass" style="color: #000;">What is your Name</label>


.. php:function:: form_submit([$data = ''[, $value = ''[, $extra = '']]])

	:param	string	$data: Button name
	:param	string	$value: Button value
	:param	string	$extra: Extra attributes to be added to the tag *as is*
	:returns:	An HTML input submit tag
	:rtype:	string

	用于生成一个标准的提交按钮。例如::

		echo form_submit('mysubmit', 'Submit Post!');
		// Would produce:  <input type="submit" name="mysubmit" value="Submit Post!" />

	和其他的函数一样，如果你想添加额外的属性的话，可以传一个关联数组给第一个参数，
	第三个参数可以向表单添加额外的数据，例如 JavaScript 。


.. php:function:: form_reset([$data = ''[, $value = ''[, $extra = '']]])

	:param	string	$data: Button name
	:param	string	$value: Button value
	:param	string	$extra: Extra attributes to be added to the tag *as is*
	:returns:	An HTML input reset button tag
	:rtype:	string

	用于生成一个标准的重置按钮。用法和 :func:`form_submit()` 函数一样。


.. php:function:: form_button([$data = ''[, $content = ''[, $extra = '']]])

	:param	string	$data: Button name
	:param	string	$content: Button label
	:param	string	$extra: Extra attributes to be added to the tag *as is*
	:returns:	An HTML button tag
	:rtype:	string

	用于生成一个标准的按钮，你可以简单的使用名称和内容来生成按钮::

		echo form_button('name','content');
		// Would produce: <button name="name" type="button">Content</button>

	或者使用一个关联数组，来包含任何你想要的数据::

		$data = array(
			'name'		=> 'button',
			'id'		=> 'button',
			'value'		=> 'true',
			'type'		=> 'reset',
			'content'	=> 'Reset'
		);

		echo form_button($data);
		// Would produce: <button name="button" id="button" value="true" type="reset">Reset</button>

	如果你还希望能包含一些额外的数据，譬如 JavaScript ，你可以通过第三个参数传一个字符串::

		$js = 'onClick="some_function()"';
		echo form_button('mybutton', 'Click Me', $js);


.. php:function:: form_close([$extra = ''])

	:param	string	$extra: Anything to append after the closing tag, *as is*
	:returns:	An HTML form closing tag
	:rtype:	string

	用于生成结束标签 </form> ，使用这个函数唯一的一个好处是，
	它可以在结束标签的后面加上一些其他的数据。例如：

		$string = '</div></div>';
		echo form_close($string);
		// Would produce:  </form> </div></div>


.. php:function:: set_value($field[, $default = ''[, $html_escape = TRUE]])

	:param	string	$field: Field name
	:param	string	$default: Default value
	:param  bool	$html_escape: Whether to turn off HTML escaping of the value
	:returns:	Field value
	:rtype:	string

	用于你显示 input 或者 textarea 类型的输入框的值。你必须在第一个参数中指定名称，
	第二个参数是可选的，允许你设置一个默认值，第三个参数也是可选，可以禁用对值的转义，
	当你在和 :php:func:`form_input()` 函数一起使用时，可以避免重复转义。

	例如::

		<input type="text" name="quantity" value="<?php echo set_value('quantity', '0'); ?>" size="50" />

	当上面的表单元素第一次加载时将会显示 "0" 。


.. php:function:: set_select($field[, $value = ''[, $default = FALSE]])

	:param	string	$field: Field name
	:param	string	$value: Value to check for
	:param	string	$default: Whether the value is also a default one
	:returns:	'selected' attribute or an empty string
	:rtype:	string

	如果你使用 <select> 下拉菜单，此函数允许你显示选中的菜单项。

	第一个参数为下拉菜单的名称，第二个参数必须包含每个菜单项的值。
	第三个参数是可选的，用于设置菜单项是否为默认选中状态（TRUE / FALSE）。

	例如::

		<select name="myselect">
			<option value="one" <?php echo  set_select('myselect', 'one', TRUE); ?> >One</option>
			<option value="two" <?php echo  set_select('myselect', 'two'); ?> >Two</option>
			<option value="three" <?php echo  set_select('myselect', 'three'); ?> >Three</option>
		</select>

.. php:function:: set_checkbox($field[, $value = ''[, $default = FALSE]])

	:param	string	$field: Field name
	:param	string	$value: Value to check for
	:param	string	$default: Whether the value is also a default one
	:returns:	'checked' attribute or an empty string
	:rtype:	string

	允许你显示一个处于提交状态的复选框。

	第一个参数必须包含此复选框的名称，第二个参数必须包含它的值，
	第三个参数是可选的，用于设置复选框是否为默认选中状态（TRUE / FALSE）。

	例如::

		<input type="checkbox" name="mycheck" value="1" <?php echo set_checkbox('mycheck', '1'); ?> />
		<input type="checkbox" name="mycheck" value="2" <?php echo set_checkbox('mycheck', '2'); ?> />

.. php:function:: set_radio($field[, $value = ''[, $default = FALSE]])

	:param	string	$field: Field name
	:param	string	$value: Value to check for
	:param	string	$default: Whether the value is also a default one
	:returns:	'checked' attribute or an empty string
	:rtype:	string

	允许你显示那些处于提交状态的单选框。
	该函数和上面的 :php:func:`set_checkbox()` 函数一样。

	例如::

		<input type="radio" name="myradio" value="1" <?php echo  set_radio('myradio', '1', TRUE); ?> />
		<input type="radio" name="myradio" value="2" <?php echo  set_radio('myradio', '2'); ?> />

	.. note:: 如果你正在使用表单验证类，你必须为你的每一个表单域指定一个规则，
		即使是空的，这样可以确保 ``set_*()`` 函数能正常运行。
		这是因为如果定义了一个表单验证对象，将会调用表单验证的 ``set_*()`` 函数，
		而不是调用辅助库里的函数。

.. php:function:: form_error([$field = ''[, $prefix = ''[, $suffix = '']]])

	:param	string	$field:	Field name
	:param	string	$prefix: Error opening tag
	:param	string	$suffix: Error closing tag
	:returns:	HTML-formatted form validation error message(s)
	:rtype:	string

	从 :doc:`表单验证类 <../libraries/form_validation>` 返回验证错误消息，
	并附上验证出错的域的名称，你可以设置错误消息的起始和结束标签。

	例如::

		// Assuming that the 'username' field value was incorrect:
		echo form_error('myfield', '<div class="error">', '</div>');

		// Would produce: <div class="error">Error message associated with the "username" field.</div>


.. php:function:: validation_errors([$prefix = ''[, $suffix = '']])

	:param	string	$prefix: Error opening tag
	:param	string	$suffix: Error closing tag
	:returns:	HTML-formatted form validation error message(s)
	:rtype:	string

	和 :php:func:`form_error()` 函数类似，返回所有 :doc:`表单验证类 <../libraries/form_validation>`
	生成的错误信息，你可以为为每个错误消息设置起始和结束标签。

	例如::

		echo validation_errors('<span class="error">', '</span>');

		/*
			Would produce, e.g.:

			<span class="error">The "email" field doesn't contain a valid e-mail address!</span>
			<span class="error">The "password" field doesn't match the "repeat_password" field!</span>

		 */

.. php:function:: form_prep($str)

	:param	string	$str: Value to escape
	:returns:	Escaped value
	:rtype:	string

	允许你在表单元素中安全的使用 HTML 和譬如引号这样的字符，而不用担心对表单造成破坏。

	.. note:: 如果你使用了这个页面上介绍的任何一个函数，表单的域值会被自动转义，
		所以你无需再调用这个函数。只有在你创建自己的表单元素时需要使用它。

	.. note:: 该函数已经废弃，现在只是 :doc:`通用函数 <../general/common_functions>` :func:`html_escape()`
		的一个别名，请使用 :func:`html_escape()` 代替它。
