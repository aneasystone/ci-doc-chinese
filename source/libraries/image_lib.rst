########################
图像处理类
########################

CodeIgniter 的图像处理类可以使你完成以下的操作：

-  调整图像大小
-  创建缩略图
-  图像裁剪
-  图像旋转
-  添加图像水印

可以很好的支持三个主流的图像库：GD/GD2、NetPBM 和 ImageMagick 。

.. note:: 添加水印操作仅仅在使用 GD/GD2 时可用。另外，即使支持其他的图像处理库，
	但是为了计算图像的属性，GD 仍是必需的。然而在进行图像处理操作时，
	还是会使用你指定的库。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

**********************
初始化类
**********************

跟 CodeIgniter 中的其他类一样，可以在你的控制器中使用 ``$this->load->library()`` 
方法加载图像处理类::

	$this->load->library('image_lib');

一旦加载，图像处理类就可以像下面这样使用::

	$this->image_lib

处理图像
===================

不管你想进行何种图像处理操作（调整大小，图像裁剪，图像旋转，添加水印），
通常过程都是一样的。你会先设置一些你想进行的图像操作的参数，
然后调用四个可用方法中的一个。例如，创建一个图像缩略图::

	$config['image_library'] = 'gd2';
	$config['source_image']	= '/path/to/image/mypic.jpg';
	$config['create_thumb'] = TRUE;
	$config['maintain_ratio'] = TRUE;
	$config['width']	 = 75;
	$config['height']	= 50;

	$this->load->library('image_lib', $config); 

	$this->image_lib->resize();

以上代码告诉 image_resize 函数去查找位于 source_image 目录下的名为 mypic.jpg
的图片，然后运用 GD2 图像库创建一个 75 X 50 像素的缩略图。 当 maintain_ratio 
选项设为 TRUE 时，生成的缩略图将保持图像的纵横比例，同时尽可能的在宽度和
高度上接近所设定的 width 和 height 。
缩略图将被命名为类似 mypic_thumb.jpg 的形式。

.. note:: 为了让图像类能进行所有操作，包含图片的文件夹必须开启可写权限。

.. note:: 图像处理的某些操作可能需要大量的服务器内存。如果在处理图像时，
	你遇到了内存不足错误，您可能需要限制图像大小的最大值，
	和/或调整 PHP 的内存限制。

处理函数
==================

有五个处理函数可以调用：

-  $this->image_lib->resize()
-  $this->image_lib->crop()
-  $this->image_lib->rotate()
-  $this->image_lib->watermark()

当调用成功时，这些函数会返回 TRUE ，否则会返回 FALSE 。
如果调用失败时，用以下函数可以获取错误信息::

	echo $this->image_lib->display_errors();

下面是一个好的做法，将函数调用放在条件判断里，当调用失败时显示错误的信息::

	if ( ! $this->image_lib->resize())
	{
		echo $this->image_lib->display_errors();
	}

.. note:: 你也可以给错误信息指定 HTML 格式，像下面这样添加起始和结束标签::

	$this->image_lib->display_errors('<p>', '</p>');

.. _processing-preferences:

参数
===========

你可以用下面的参数来对图像处理进行配置，满足你的要求。

注意，不是所有的参数都可以应用到每一个函数中。例如，x/y 轴参数只能被图像裁剪使用。
但是，宽度和高度参数对裁剪函数是无效的。下表的 "可用性" 一栏将指明哪些函数可以使用对应的参数。

"可用性" 符号说明：

-  R - 调整图像大小
-  C - 图像裁剪
-  X - 图像旋转
-  W - 添加图像水印

======================= ======================= =============================== =========================================================================== =============
参数                      默认值                  选项                            描述                                                                           可用性
======================= ======================= =============================== =========================================================================== =============
**image_library**       GD2                     GD, GD2, ImageMagick, NetPBM    设置要使用的图像库                                                             R, C, X, W
**library_path**        None                    None                            设置 ImageMagick 或 NetPBM 库在服务器上的路径。                              R, C, X
                                                                                要使用它们中的其中任何一个，你都需要设置它们的路径。
**source_image**        None                    None                            设置原始图像的名称和路径。                                                   R, C, S, W
                                                                                路径只能是相对或绝对的服务器路径，不能使用URL 。
**dynamic_output**      FALSE                   TRUE/FALSE (boolean)            决定新生成的图像是要写入硬盘还是内存中。                                      R, C, X, W
                                                                                注意，如果是生成到内存的话，一次只能显示一副图像，而且
                                                                                不能调整它在你页面中的位置，它只是简单的将图像数据以及图像的
                                                                                HTTP 头发送到浏览器。
**file_permissions**    0644                    (integer)                       设置生成图像文件的权限。                                                      R, C, X, W
                                                                                注意：权限值为八进制表示法。
**quality**             90%                     1 - 100%                        设置图像的品质。品质越高，图像文件越大。                                       R, C, X, W
**new_image**           None                    None                            设置目标图像的名称和路径。                                                    R, C, X, W
                                                                                创建图像副本时使用该参数，路径只能是相对或绝对的服务器路径，
                                                                                不能使用URL 。
**width**               None                    None                            设置你想要的图像宽度。                                                                 R, C
**height**              None                    None                            设置你想要的图像高度。                                                                 R, C
**create_thumb**        FALSE                   TRUE/FALSE (boolean)            告诉图像处理函数生成缩略图。                                                    R
**thumb_marker**        _thumb                  None                            指定缩略图后缀，它会被插入到文件扩展名的前面，                                R
                                                                                所以 mypic.jpg 文件会变成 mypic_thumb.jpg
**maintain_ratio**      TRUE                    TRUE/FALSE (boolean)            指定是否在缩放或使用硬值的时候                                                 R, C
                                                                                使图像保持原始的纵横比例。
**master_dim**          auto                    auto, width, height             指定一个选项作为缩放和创建缩略图时的主轴。                                         R
                                                                                例如，你想要将一张图片缩放到 100×75 像素。
                                                                                如果原来的图像的大小不能完美的缩放到这个尺寸，
                                                                                那么由这个参数决定把哪个轴作为硬值。
                                                                                "auto" 依据图片到底是过高还是过长自动设定轴。
**rotation_angle**      None                    90, 180, 270, vrt, hor          指定图片旋转的角度。                                                         X
                                                                                注意，旋转是逆时针的，如果想向右转 90 度，
                                                                                就得把这个参数定义为 270 。
**x_axis**              None                    None                            为图像的裁剪设定 X 轴上的长度。                                                   C
                                                                                例如，设为 30 就是将图片左边的 30 像素裁去。
**y_axis**              None                    None                            为图像的裁剪设定Y轴上的长度。                                                     C
                                                                                例如，设为30就是将图片顶端的30像素裁去。
======================= ======================= =============================== =========================================================================== =============

在配置文件中设置参数
====================================

如果你不喜欢使用上面的方法来设置参数，你可以将参数保存到配置文件中。你只需简单的创建一个文件 
image_lib.php 并将 $config 数组放到该文件中，然后保存文件到 **config/image_lib.php** ，这些参数将会自动被使用。
如果你在配置文件中设置参数，那么你就不需要使用 ``$this->image_lib->initialize()`` 方法了。

******************
添加图像水印
******************

水印处理功能需要 GD/GD2 库的支持。

水印的两种类型
=========================

你可以使用以下两种图像水印处理方式：

-  **Text**：水印信息将以文字方式生成，要么使用你所指定的 TrueType 字体，
   要么使用 GD 库所支持的内部字体。如果你要使用 TrueType 版本，
   那么你安装的 GD 库必须是以支持 TrueType 的形式编译的（大多数都是，但不是所有）。
-  **Overlay**：水印信息将以图像方式生成，新生成的水印图像
   （通常是透明的 PNG 或者 GIF）将覆盖在原图像上。

.. _watermarking:

给图像添加水印
=====================

类似使用其他类型的图像处理函数（resizing、cropping 和 rotating），
你也要对水印处理函数进行参数设置来生成你要的结果，例子如下::

	$config['source_image']	= '/path/to/image/mypic.jpg';
	$config['wm_text'] = 'Copyright 2006 - John Doe';
	$config['wm_type'] = 'text';
	$config['wm_font_path'] = './system/fonts/texb.ttf';
	$config['wm_font_size']	= '16';
	$config['wm_font_color'] = 'ffffff';
	$config['wm_vrt_alignment'] = 'bottom';
	$config['wm_hor_alignment'] = 'center';
	$config['wm_padding'] = '20';

	$this->image_lib->initialize($config); 

	$this->image_lib->watermark();

上面的例子是使用 16 像素 True Type 字体来生成文本水印 "Copyright 2006 - John Doe" ，
该水印将出现在离图像底部 20 像素的中下部位置。

.. note:: 当调用图像类处理图像时，所有的目标图片必须有 "写入" 权限， 例如：777

水印处理参数
========================

下表列举的参数对于两种水印处理方式（text 或 overlay）都适用。

======================= =================== ======================= ==========================================================================
参数                    默认值               选项                         描述
======================= =================== ======================= ==========================================================================
**wm_type**             text                text, overlay           设置想要使用的水印处理类型。
**source_image**        None                None                    设置原图像的名称和路径，路径必须是相对或绝对路径，不能是 URL 。
**dynamic_output**      FALSE               TRUE/FALSE (boolean)    决定新生成的图像是要写入硬盘还是内存中。
                                                                    注意，如果是生成到内存的话，一次只能显示一副图像，而且
                                                                    不能调整它在你页面中的位置，它只是简单的将图像数据以及图像的
                                                                    HTTP 头发送到浏览器。
**quality**             90%                 1 - 100%                设置图像的品质。品质越高，图像文件越大。
**wm_padding**          None                A number                内边距，以像素为单位，是水印与图片边缘之间的距离。
**wm_vrt_alignment**    bottom              top, middle, bottom     设置水印图像的垂直对齐方式。
**wm_hor_alignment**    center              left, center, right     设置水印图像的水平对齐方式。
**wm_hor_offset**       None                None                    你可以指定一个水平偏移量（以像素为单位），
                                                                    用于设置水印的位置。偏移量通常是向右移动水印，
                                                                    除非你把水平对齐方式设置为 "right" ，那么你的偏移量将会向左移动水印。
**wm_vrt_offset**       None                None                    你可以指定一个垂直偏移量（以像素为单位），
                                                                    用于设置水印的位置。偏移量通常是向下移动水印，
                                                                    除非你把垂直对齐方式设置为 "bottom"，那么你的偏移量将会向上移动水印。
======================= =================== ======================= ==========================================================================

Text 参数
----------------

下表列举的参数只适用于 text 水印处理方式。

======================= =================== =================== ==========================================================================
参数                            默认值       选项                     描述
======================= =================== =================== ==========================================================================
**wm_text**             None                None                你想作为水印显示的文本。通常是一份版权声明。
**wm_font_path**        None                None                你想使用的 TTF 字体（TrueType）在服务器上的路径。
                                                                如果你没有使用这个选项，系统将使用原生的GD字体。
**wm_font_size**        16                  None                字体大小。 说明：如果你没有使用上面的 TTF 字体选项，
                                                                那么这个数值必须是 1-5 之间的一个数字，如果使用了 TTF ，
                                                                你可以使用任意有效的字体大小。
**wm_font_color**       ffffff              None                字体颜色，以十六进制给出。
                                                                注意，你必须给出完整的 6 位数的十六进制值（如：993300），
                                                                而不能使用 3 位数的简化值（如：fff）。
**wm_shadow_color**     None                None                阴影的颜色, 以十六进制给出。如果此项为空，将不使用阴影。
                                                                注意，你必须给出完整的 6 位数的十六进制值（如：993300），
                                                                而不能使用 3 位数的简化值（如：fff）。
**wm_shadow_distance**  3                   None                阴影与文字之间的距离（以像素为单位）。
======================= =================== =================== ==========================================================================

Overlay 参数
-------------------

下表列举的参数只适用于 overlay 水印处理方式。

======================= =================== =================== ==========================================================================
参数                            默认值       选项                     描述
======================= =================== =================== ==========================================================================
**wm_overlay_path**     None                None                你想要用作水印的图片在你服务器上的路径。
                                                                只在你使用了 overlay 方法时需要。
**wm_opacity**          50                  1 - 100             图像不透明度。你可以指定你的水印图片的不透明度。
                                                                这将使水印模糊化，从而不会掩盖住底层原始图片，通常设置为 50 。
**wm_x_transp**         4                   A number            如果你的水印图片是一个 PNG 或 GIF 图片，
                                                                你可以指定一种颜色用来使图片变得 "透明" 。这项设置
                                                                （以及下面那项）将允许你指定这种颜色。它的原理是，通过指定
                                                                "X" 和 "Y" 坐标值（从左上方开始测量）来确定图片上对应位置的某个像素，
                                                                这个像素所代表的颜色就是你要设置为透明的颜色。
**wm_y_transp**         4                   A number            与前一个选项一起，允许你指定某个像素的坐标值，
                                                                这个像素所代表的颜色就是你要设置为透明的颜色。
======================= =================== =================== ==========================================================================

***************
类参考
***************

.. php:class:: CI_Image_lib

	.. php:method:: initialize([$props = array()])

		:param	array	$props: Image processing preferences
		:returns:	TRUE on success, FALSE in case of invalid settings
		:rtype:	bool

		初始化图像处理类。

	.. php:method:: resize()

		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		该函数让你能调整原始图像的大小，创建一个副本（调整或未调整过的），
		或者创建一个缩略图。

		创建一个副本和创建一个缩略图之间没有实际上的区别，
		除了缩略图的文件名会有一个自定义的后缀（如：mypic_thumb.jpg）。

		所有列在上面 :ref:`processing-preferences` 表中的参数对这个函数都可用，
		除了这三个： *rotation_angle* 、 *x_axis* 和 *y_axis* 。

		**创建一个缩略图**

		resize 函数能用来创建缩略图（并保留原图），只要你把这个参数设为 TRUE ::

			$config['create_thumb'] = TRUE;

		这一个参数决定是否创建一个缩略图。

		**创建一个副本**

		resize 函数能创建一个图像的副本（并保留原图），
		只要你通过以下参数设置一个新的路径或者文件名::

			$config['new_image'] = '/path/to/new_image.jpg';

		注意以下规则：

		-  如果只指定新图像的名字，那么它会被放在原图像所在的文件夹下。
		-  如果只指定路径，新图像会被放在指定的文件夹下，并且名字和原图像相同。
		-  如果同时定义了路径和新图像的名字，那么新图像会以指定的名字放在指定的文件夹下。

		**调整原图像的大小**

		如果上述两个参数（create_thumb 和 new_image）均未被指定，
		resize 函数的处理将直接作用于原图像。

	.. php:method:: crop()

		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		crop 函数的用法与 resize 函数十分接近，除了它需要你设置用于裁剪的 X 和 Y 值
		（单位是像素），如下::

			$config['x_axis'] = 100;
			$config['y_axis'] = 40;

		前面那张 :ref:`processing-preferences` 表中所列的所有参数都可以用于这个函数，
		除了这些：*rotation_angle* 、*width* 、*height* 、*create_thumb* 、*new_image* 。

		这是一个如何裁剪一张图片的示例::

			$config['image_library'] = 'imagemagick';
			$config['library_path'] = '/usr/X11R6/bin/';
			$config['source_image']	= '/path/to/image/mypic.jpg';
			$config['x_axis'] = 100;
			$config['y_axis'] = 60;

			$this->image_lib->initialize($config); 

			if ( ! $this->image_lib->crop())
			{
				echo $this->image_lib->display_errors();
			}

		.. note:: 如果没有一个可视化的界面，是很难裁剪一张图片的。
			因此，除非你打算制作这么一个界面，否则这个函数并不是很有用。
			事实上我们在自己开发的 CMS 系统 ExpressionEngine 的相册模块中
			添加的一个基于 JavaScript 的用户界面来选择裁剪的区域。

	.. php:method:: rotate()

		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		rotate 函数需要通过参数设置旋转的角度::

			$config['rotation_angle'] = '90';

		以下是 5 个可选项：

		#. 90 - 逆时针旋转90度。
		#. 180 - 逆时针旋转180度。
		#. 270 - 逆时针旋转270度。
		#. hor - 水平翻转。
		#. vrt - 垂直翻转。

		下面是旋转图片的一个例子::

			$config['image_library'] = 'netpbm';
			$config['library_path'] = '/usr/bin/';
			$config['source_image']	= '/path/to/image/mypic.jpg';
			$config['rotation_angle'] = 'hor';

			$this->image_lib->initialize($config); 

			if ( ! $this->image_lib->rotate())
			{
				echo $this->image_lib->display_errors();
			}

	.. php:method:: watermark()

		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		在图像上添加一个水印，更多信息请参考 :ref:`watermarking` 。

	.. php:method:: clear()

		:rtype:	void

		clear 函数重置所有之前用于处理图片的值。当你用循环来处理一批图片时，你可能会想使用它。

		::

			$this->image_lib->clear();

	.. php:method:: display_errors([$open = '<p>[, $close = '</p>']])

		:param	string	$open: Error message opening tag
		:param	string	$close: Error message closing tag
		:returns:	Error messages
		:rtype:	string

		返回所有检测到的错误信息。
		::

			echo $this->image_lib->diplay_errors();