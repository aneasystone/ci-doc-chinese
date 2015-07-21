############
数组辅助库
############

数组辅助库文件包含了一些帮助你处理数组的函数。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

加载辅助库
===================

该辅助库通过下面的代码加载::

	$this->load->helper('array');


可用函数
===================

该辅助库有下列可用函数：

.. php:function:: element($item, $array[, $default = NULL])

	:param	string	$item: Item to fetch from the array
	:param	array	$array: Input array
	:param	bool	$default: What to return if the array isn't valid
	:returns:	NULL on failure or the array item.
	:rtype:	mixed

	该函数通过索引获取数组中的元素。它会测试索引是否设置并且有值，如果有值，
	函数将返回该值，如果没有值，默认返回 NULL 或返回通过第三个参数设置的默认值。

	示例::

		$array = array(
			'color'	=> 'red',
			'shape'	=> 'round',
			'size'	=> ''
		);

		echo element('color', $array); // returns "red"
		echo element('size', $array, 'foobar'); // returns "foobar"


.. php:function:: elements($items, $array[, $default = NULL])

	:param	string	$item: Item to fetch from the array
	:param	array	$array: Input array
	:param	bool	$default: What to return if the array isn't valid
	:returns:	NULL on failure or the array item.
	:rtype:	mixed

	该函数通过多个索引获取数组中的多个元素。它会测试每一个索引是否设置并且有值，
	如果其中某个索引没有值，返回结果中该索引所对应的元素将被置为 NULL ，或者
	通过第三个参数设置的默认值。

	示例::

		$array = array(
			'color' => 'red',
			'shape' => 'round',
			'radius' => '10',
			'diameter' => '20'
		);

		$my_shape = elements(array('color', 'shape', 'height'), $array);

	上面的函数返回的结果如下::

		array(
			'color' => 'red',
			'shape' => 'round',
			'height' => NULL
		);

	你可以通过第三个参数设置任何你想要设置的默认值。
	::

		 $my_shape = elements(array('color', 'shape', 'height'), $array, 'foobar');

	上面的函数返回的结果如下::

		array(     
			'color' 	=> 'red',
			'shape' 	=> 'round',
			'height'	=> 'foobar'
		);

	当你需要将 ``$_POST`` 数组传递到你的模型中时这将很有用，这可以防止用户发送额外的数据
	被写入到你的数据库。

	::

		$this->load->model('post_model');
		$this->post_model->update(
			elements(array('id', 'title', 'content'), $_POST)
		);

	从上例中可以看出，只有 id、title、content 三个字段被更新。


.. php:function:: random_element($array)

	:param	array	$array: Input array
	:returns:	A random element from the array
	:rtype:	mixed

	传入一个数组，并返回数组中随机的一个元素。

	使用示例::

		$quotes = array(
			"I find that the harder I work, the more luck I seem to have. - Thomas Jefferson",
			"Don't stay in bed, unless you can make money in bed. - George Burns",
			"We didn't lose the game; we just ran out of time. - Vince Lombardi",
			"If everything seems under control, you're not going fast enough. - Mario Andretti",
			"Reality is merely an illusion, albeit a very persistent one. - Albert Einstein",
			"Chance favors the prepared mind - Louis Pasteur"
		);

		echo random_element($quotes);