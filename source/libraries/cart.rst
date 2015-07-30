###################
购物车类
###################

购物车类允许项目被添加到 session 中，session 在用户浏览你的网站期间都保持有效状态。
这些项目能够以标准的 "购物车" 格式被检索和显示，并允许用户更新数量或者从购物车中移除项目。

.. important:: 购物车类已经废弃，请不要使用。目前保留它只是为了向前兼容。

请注意购物车类只提供核心的 "购物车" 功能。它不提供配送、信用卡授权或者其它处理组件。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

********************
使用购物车类
********************

初始化购物车类
====================================

.. important:: 购物车类利用 CodeIgniter 的 :doc:`Session 类 <sessions>` 把购物车信息保存到数据库中，
	所以在使用购物车类之前，你必须根据 :doc:`Session 类文档 <sessions>` 中的说明来创建数据库表，
	并且在 application/config/config.php 文件中把 Session 相关参数设置为使用数据库。

为了在你的控制器构造函数中初始化购物车类，请使用 $this->load->library 函数::

	$this->load->library('cart');

一旦加载，就可以通过调用 $this->cart 来使用购物车对象了::

	$this->cart

.. note:: 购物车类会自动加载和初始化 Session 类，因此除非你在别处要用到 session，否则你不需要再次加载 Session 类。

将一个项目添加到购物车
==========================

要添加项目到购物车，只需将一个包含了商品信息的数组传递给 ``$this->cart->insert()`` 函数即可，就像下面这样::

	$data = array(
		'id'      => 'sku_123ABC',
		'qty'     => 1,
		'price'   => 39.95,
		'name'    => 'T-Shirt',
		'options' => array('Size' => 'L', 'Color' => 'Red')
	);

	$this->cart->insert($data);

.. important:: 上面的前四个数组索引（id、qty、price 和 name）是 **必需的** 。
	如果缺少其中的任何一个，数据将不会被保存到购物车中。第5个索引（options）
	是可选的。当你的商品包含一些相关的选项信息时，你就可以使用它。
	正如上面所显示的那样，请使用一个数组来保存选项信息。

五个保留的索引分别是：

-  **id** - 你的商店里的每件商品都必须有一个唯一的标识符。典型的标识符是库存量单位（SKU）或者其它类似的标识符。
-  **qty** - 购买的数量。
-  **price** - 商品的价格。
-  **name** - 商品的名称。
-  **options** - 标识商品的任何附加属性。必须通过数组来传递。

除以上五个索引外，还有两个保留字：rowid 和 subtotal。它们是购物车类内部使用的，
因此，往购物车中插入数据时，请不要使用这些词作为索引。

你的数组可能包含附加的数据。你的数组中包含的所有数据都会被存储到 session 中。
然而，最好的方式是标准化你所有商品的数据，这样更方便你在表格中显示它们。

::

	$data = array(
		'id'      => 'sku_123ABC',
		'qty'     => 1,
		'price'   => 39.95,
		'name'    => 'T-Shirt',
		'coupon'	 => 'XMAS-50OFF'
	);

	$this->cart->insert($data);

如果成功的插入一条数据后，``insert()`` 方法将会返回一个 id 值（ $rowid ）。

将多个项目添加到购物车
=================================

通过下面这种多维数组的方式，可以一次性添加多个产品到购物车中。
当你希望允许用户选择同一页面中的多个项目时，这就非常有用了。

::

	$data = array(
		array(
			'id'      => 'sku_123ABC',
			'qty'     => 1,
			'price'   => 39.95,
			'name'    => 'T-Shirt',
			'options' => array('Size' => 'L', 'Color' => 'Red')
		),
		array(
			'id'      => 'sku_567ZYX',
			'qty'     => 1,
			'price'   => 9.95,
			'name'    => 'Coffee Mug'
		),
		array(
			'id'      => 'sku_965QRS',
			'qty'     => 1,
			'price'   => 29.95,
			'name'    => 'Shot Glass'
		)
	);

	$this->cart->insert($data);

显示购物车
===================

为了显示购物车的数据，你得创建一个 :doc:`视图文件 </general/views>`，它的代码类似于下面这个。

请注意这个范例使用了 :doc:`表单辅助函数 </helpers/form_helper>` 。

::

	<?php echo form_open('path/to/controller/update/method'); ?>

	<table cellpadding="6" cellspacing="1" style="width:100%" border="0">

	<tr>
		<th>QTY</th>
		<th>Item Description</th>
		<th style="text-align:right">Item Price</th>
		<th style="text-align:right">Sub-Total</th>
	</tr>

	<?php $i = 1; ?>

	<?php foreach ($this->cart->contents() as $items): ?>

		<?php echo form_hidden($i.'[rowid]', $items['rowid']); ?>

		<tr>
			<td><?php echo form_input(array('name' => $i.'[qty]', 'value' => $items['qty'], 'maxlength' => '3', 'size' => '5')); ?></td>
			<td>
				<?php echo $items['name']; ?>

				<?php if ($this->cart->has_options($items['rowid']) == TRUE): ?>

					<p>
						<?php foreach ($this->cart->product_options($items['rowid']) as $option_name => $option_value): ?>

							<strong><?php echo $option_name; ?>:</strong> <?php echo $option_value; ?><br />

						<?php endforeach; ?>
					</p>

				<?php endif; ?>

			</td>
			<td style="text-align:right"><?php echo $this->cart->format_number($items['price']); ?></td>
			<td style="text-align:right">$<?php echo $this->cart->format_number($items['subtotal']); ?></td>
		</tr>

	<?php $i++; ?>

	<?php endforeach; ?>

	<tr>
		<td colspan="2"> </td>
		<td class="right"><strong>Total</strong></td>
		<td class="right">$<?php echo $this->cart->format_number($this->cart->total()); ?></td>
	</tr>

	</table>

	<p><?php echo form_submit('', 'Update your Cart'); ?></p>

更新购物车
=================

为了更新购物车中的信息，你必须将一个包含了 Row ID 和数量的数组传递给 ``$this->cart->update()`` 函数。

.. note:: 如果数量被设置为 0 ，那么购物车中对应的项目会被移除。

::

	$data = array(
		'rowid' => 'b99ccdf16028f015540f341130b6d8ec',
		'qty'   => 3
	);

	$this->cart->update($data);

	// Or a multi-dimensional array

	$data = array(
		array(
			'rowid'   => 'b99ccdf16028f015540f341130b6d8ec',
			'qty'     => 3
		),
		array(
			'rowid'   => 'xw82g9q3r495893iajdh473990rikw23',
			'qty'     => 4
		),
		array(
			'rowid'   => 'fh4kdkkkaoe30njgoe92rkdkkobec333',
			'qty'     => 2
		)
	);

	$this->cart->update($data);

你也可以更新任何一个在新增购物车时定义的属性，如：options、price 或其他用户自定义字段。

::

	$data = array(
		'rowid'  => 'b99ccdf16028f015540f341130b6d8ec',
		'qty'    => 1,
		'price'	 => 49.95,
		'coupon' => NULL
	);

	$this->cart->update($data);

什么是 Row ID?  
*****************

当一个项目被添加到购物车时，程序所生成的那个唯一的标识符就是 row ID。
创建唯一 ID 的理由是，当购物车中相同的商品有不同的选项时，购物车就能够对它们进行管理。

比如说，有人购买了两件相同的 T-shirt （相同的商品ID），但是尺寸不同。
商品 ID （以及其它属性）都会完全一样，因为它们是相同的 T-shirt ，
它们唯一的差别就是尺寸不同。因此购物车必须想办法来区分它们，
这样才能独立地管理这两件尺寸不同的 T-shirt 。而基于商品 ID 
和其它相关选项信息来创建一个唯一的 "row ID" 就能解决这个问题。

在几乎所有情况下，更新购物车都将是用户通过 "查看购物车" 页面来实现的，因此对开发者来说，
不必太担心 "row ID" ，只要保证你的 "查看购物车" 页面中的一个隐藏表单字段包含了这个信息，
并且确保它能被传递给表单提交时所调用的更新函数就行了。
请仔细分析上面的 "查看购物车" 页面的结构以获取更多信息。

***************
类参考
***************

.. php:class:: CI_Cart

	.. attribute:: $product_id_rules = '\.a-z0-9_-'

		用于验证商品 ID 有效性的正则表达式规则，默认是：字母、数字、连字符（-）、下划线（_）、句点（.）

	.. attribute:: $product_name_rules	= '\w \-\.\:'

		用于验证商品 ID 和商品名有效性的正则表达式规则，默认是：字母、数字、连字符（-）、下划线（_）、冒号（:）、句点（.）

	.. attribute:: $product_name_safe = TRUE

		是否只接受安全的商品名称，默认为 TRUE 。


	.. php:method:: insert([$items = array()])

		:param	array	$items: Items to insert into the cart
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		将项目添加到购物车并保存到 session 中，根据成功或失败返回 TRUE 或 FALSE 。


	.. php:method:: update([$items = array()])

		:param	array	$items: Items to update in the cart
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		该方法用于更新购物车中某个项目的属性。一般情况下，它会在 "查看购物车" 页面被调用，
		例如用户在下单之前修改商品数量。参数是个数组，数组的每一项必须包含 rowid 。

	.. php:method:: remove($rowid)

		:param	int	$rowid: ID of the item to remove from the cart
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		根据 ``$rowid`` 从购物车中移除某个项目。

	.. php:method:: total()

		:returns:	Total amount
		:rtype:	int

		显示购物车总额。


	.. php:method:: total_items()

		:returns:	Total amount of items in the cart
		:rtype:	int

		显示购物车中商品数量。


	.. php:method:: contents([$newest_first = FALSE])

		:param	bool	$newest_first: Whether to order the array with newest items first
		:returns:	An array of cart contents
		:rtype:	array

		返回一个数组，包含购物车的所有信息。参数为布尔值，用于控制数组的排序方式。
		TRUE 为按购物车里的项目从新到旧排序，FALSE 为从旧到新。

	.. php:method:: get_item($row_id)

		:param	int	$row_id: Row ID to retrieve
		:returns:	Array of item data
		:rtype:	array

		根据指定的 ``$rowid`` 返回购物车中该项的信息，如果不存在，返回 FALSE 。

	.. php:method:: has_options($row_id = '')

		:param	int	$row_id: Row ID to inspect
		:returns:	TRUE if options exist, FALSE otherwise
		:rtype:	bool

		如果购物车的某项包含 options 则返回 TRUE 。该方法可以用在针对 ``contents()`` 方法的循环中，
		你需要指定项目的 rowid ，正如上文 "显示购物车" 的例子中那样。

	.. php:method:: product_options([$row_id = ''])

		:param	int	$row_id: Row ID
		:returns:	Array of product options
		:rtype:	array

		该方法返回购物车中某个商品的 options 数组。该方法可以用在针对 ``contents()`` 方法的循环中，
		你需要指定项目的 rowid ，正如上文 "显示购物车" 的例子中那样。

	.. php:method:: destroy()

		:rtype: void

		清空购物车。该函数一般在用户订单处理完成之后调用。
