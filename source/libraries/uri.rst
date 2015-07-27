#########
URI 类
#########

URI 类用于帮助你从 URI 字符串中获取信息，如果你使用 URI 路由，
你也可以从路由后的 URI 中获取信息。

.. note:: 该类由系统自己加载，无需手工加载。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

***************
类参考
***************

.. php:class:: CI_URI

	.. php:method:: segment($n[, $no_result = NULL])

		:param	int	$n: Segment index number
		:param	mixed	$no_result: What to return if the searched segment is not found
		:returns:	Segment value or $no_result value if not found
		:rtype:	mixed

		用于从 URI 中获取指定段。参数 n 为你希望获取的段序号，URI 的段从左到右进行编号。
		例如，如果你的完整 URL 是这样的::

			http://example.com/index.php/news/local/metro/crime_is_up

		那么你的每个分段如下::

		#. news
		#. local
		#. metro
		#. crime_is_up

		第二个参数为可选的，默认为 NULL ，它用于设置当所请求的段不存在时的返回值。
		例如，如下代码在失败时将返回数字 0 ::

			$product_id = $this->uri->segment(3, 0);

		它可以避免你写出类似于下面这样的代码::

			if ($this->uri->segment(3) === FALSE)
			{
				$product_id = 0;
			}
			else
			{
				$product_id = $this->uri->segment(3);
			}

	.. php:method:: rsegment($n[, $no_result = NULL])

		:param	int	$n: Segment index number
		:param	mixed	$no_result: What to return if the searched segment is not found
		:returns:	Routed segment value or $no_result value if not found
		:rtype:	mixed

		当你使用 CodeIgniter 的 :doc:`URI 路由 <../general/routing>` 功能时，该方法和 ``segment()`` 类似，
		只是它用于从路由后的 URI 中获取指定段。

	.. php:method:: slash_segment($n[, $where = 'trailing'])

		:param	int	$n: Segment index number
		:param	string	$where: Where to add the slash ('trailing' or 'leading')
		:returns:	Segment value, prepended/suffixed with a forward slash, or a slash if not found
		:rtype:	string

		该方法和 ``segment()`` 类似，只是它会根据第二个参数在返回结果的前面或/和后面添加斜线。
		如果第二个参数未设置，斜线会添加到后面。例如::

			$this->uri->slash_segment(3);
			$this->uri->slash_segment(3, 'leading');
			$this->uri->slash_segment(3, 'both');

		返回结果：

		#. segment/
		#. /segment
		#. /segment/

	.. php:method:: slash_rsegment($n[, $where = 'trailing'])

		:param	int	$n: Segment index number
		:param	string	$where: Where to add the slash ('trailing' or 'leading')
		:returns:	Routed segment value, prepended/suffixed with a forward slash, or a slash if not found
		:rtype:	string

		当你使用 CodeIgniter 的 :doc:`URI 路由 <../general/routing>` 功能时，该方法和 ``slash_segment()`` 类似，
		只是它用于从路由后的 URI 返回结果的前面或/和后面添加斜线。

	.. php:method:: uri_to_assoc([$n = 3[, $default = array()]])

		:param	int	$n: Segment index number
		:param	array	$default: Default values
		:returns:	Associative URI segments array
		:rtype:	array

		该方法用于将 URI 的段转换为一个包含键值对的关联数组。如下 URI::

			index.php/user/search/name/joe/location/UK/gender/male

		使用这个方法你可以将 URI 转为如下的数组原型::

			[array]
			(
				'name'		=> 'joe'
				'location'	=> 'UK'
				'gender'	=> 'male'
			)

		你可以通过第一个参数设置一个位移，默认值为 3 ，这是因为你的 URI 的前两段通常都是控制器和方法。
		例如::

			$array = $this->uri->uri_to_assoc(3);
			echo $array['name'];

		第二个参数用于设置默认的键名，这样即使 URI 中缺少某个键名，也能保证返回的数组中包含该索引。
		例如::

			$default = array('name', 'gender', 'location', 'type', 'sort');
			$array = $this->uri->uri_to_assoc(3, $default);

		如果某个你设置的默认键名在 URI 中不存在，数组中的该索引值将设置为 NULL 。

		另外，如果 URI 中的某个键没有相应的值与之对应（例如 URI 的段数为奇数），
		数组中的该索引值也将设置为 NULL 。

	.. php:method:: ruri_to_assoc([$n = 3[, $default = array()]])

		:param	int	$n: Segment index number
		:param	array	$default: Default values
		:returns:	Associative routed URI segments array
		:rtype:	array

		当你使用 CodeIgniter 的 :doc:`URI 路由 <../general/routing>` 功能时，该方法和 ``uri_to_assoc()`` 类似，
		只是它用于将路由后的 URI 的段转换为一个包含键值对的关联数组。

	.. php:method:: assoc_to_uri($array)

		:param	array	$array: Input array of key/value pairs
		:returns:	URI string
		:rtype:	string

		根据输入的关联数组生成一个 URI 字符串，数组的键将包含在 URI 的字符串中。例如::

			$array = array('product' => 'shoes', 'size' => 'large', 'color' => 'red');
			$str = $this->uri->assoc_to_uri($array);

			// Produces: product/shoes/size/large/color/red

	.. php:method:: uri_string()

		:returns:	URI string
		:rtype:	string

		返回一个相对的 URI 字符串，例如，如果你的完整 URL 为::

			http://example.com/index.php/news/local/345

		该方法返回::

			news/local/345

	.. php:method:: ruri_string()

		:returns:	Routed URI string
		:rtype:	string

		当你使用 CodeIgniter 的 :doc:`URI 路由 <../general/routing>` 功能时，该方法和 ``uri_string()`` 类似，
		只是它用于返回路由后的 URI 。

	.. php:method:: total_segments()

		:returns:	Count of URI segments
		:rtype:	int

		返回 URI 的总段数。

	.. php:method:: total_rsegments()

		:returns:	Count of routed URI segments
		:rtype:	int

		当你使用 CodeIgniter 的 :doc:`URI 路由 <../general/routing>` 功能时，该方法和 ``total_segments()`` 类似，
		只是它用于返回路由后的 URI 的总段数。

	.. php:method:: segment_array()

		:returns:	URI segments array
		:rtype:	array

		返回 URI 所有的段组成的数组。例如::

			$segs = $this->uri->segment_array();

			foreach ($segs as $segment)
			{
				echo $segment;
				echo '<br />';
			}

	.. php:method:: rsegment_array()

		:returns:	Routed URI segments array
		:rtype:	array

		当你使用 CodeIgniter 的 :doc:`URI 路由 <../general/routing>` 功能时，该方法和 ``segment_array()`` 类似，
		只是它用于返回路由后的 URI 的所有的段组成的数组。
