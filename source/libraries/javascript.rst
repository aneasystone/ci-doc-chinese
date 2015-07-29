################
Javascript 类
################

CodeIgniter 提供一个类库和一些共用的方法来处理 Javascript 。要注意的是，
CodeIgniter 并不是只能用于 jQuery ，其他脚本库也可以。JQuery 仅仅是
作为一个方便的工具，如果你选择使用它的话。

.. important:: 这个类库已经废弃，不要使用它。它将永远处于 "实验" 版本，
	而且现在也已经不提供支持了。保留它只是为了向前兼容。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

**************************
使用 Javascript 类
**************************

初始化类
======================

要初始化 Javascript 类，你可以在控制器的构造函数中使用 ``$this->load->library()``
函数。目前，唯一可用的库是 jQuery ，可以使用下面的方法加载::

	$this->load->library('javascript');

Javascript 类也可以接受参数：

- js_library_driver (string) *default: 'jquery'*
- autoload (bool) *default: TRUE*

你可以通过一个关联数组覆盖默认的参数::

	$this->load->library(
		'javascript',
		array(
			'js_library_driver' => 'scripto',
			'autoload' => FALSE
		)
	);

再次说明，目前只有 jQuery 是可用的，如果你不想让 jQuery 脚本文件自动的包含在
script 标签中，你可以设置 autoload 参数为 FALSE 。这在当你在 CodeIgniter 之外
加载它时，或者 script 标签已经有了的时候很有用。

一旦加载完成，jQuery 类对象就可以通过下面的方式使用::

	$this->javascript

初始化配置
=======================

在视图文件中设置变量
--------------------------------

作为一个 Javascript 库，源文件必须能被应用程序访问到。

由于 Javascript 是一种客户端语言，库必须能写入内容到最终的输出中去，
这通常就是视图。你需要在输出的 ``<head>`` 中包含下面的变量。

::

	<?php echo $library_src;?>
	<?php echo $script_head;?>

``$library_src`` 是要载入的库文件的路径，以及之后所有插件脚本的路径；
``$script_head`` 是需要显示的具体的一些事件、函数和其他的命令。

设置库路径
----------------------------------------------

在 Javascript 类库中有一些配置项，它们可以在 *application/config.php* 文件中
设置，也可以在它们自己的配置文件 *config/javascript.php* 中设置，还可以通过
在控制器中使用 ``set_item()`` 方法来设置。

例如，有一个 "加载中" 的图片，或者进度条指示，如果没有它的话，当调用 Ajax 请求时，
将会显示 "加载中" 这样的文本。

::

	$config['javascript_location'] = 'http://localhost/codeigniter/themes/js/jquery/';
	$config['javascript_ajax_img'] = 'images/ajax-loader.gif';

如果你把文件留在与图片下载路径相同的目录里，那么你不需要设置这个配置项。

jQuery 类
================

要在你的控制器构造函数中手工初始化 jQuery 类，使用 ``$this->load->library()`` 方法::

	$this->load->library('javascript/jquery');

你可以提供一个可选的参数来决定加载该库时是否将其自动包含到 script 标签中。
默认情况下会包含，如果不需要，可以像下面这样来加载::

	$this->load->library('javascript/jquery', FALSE);

加载完成后，jQuery 类对象可以使用下面的代码来访问::

	$this->jquery

jQuery 事件
=============

使用下面的语法来设置事件。
::

	$this->jquery->event('element_path', code_to_run());

在上面的例子中：

-  "event" 可以是 blur、change、click、dblclick、error、focus、hover、
   keydown、keyup、load、mousedown、mouseup、mouseover、mouseup、resize、
   scroll 或者 unload 中的任何一个事件。
-  "element_path" 可以是任何的 `jQuery 选择器 <http://api.jquery.com/category/selectors/>`_ 。
   使用 jQuery 独特的选择器语法，通常是一个元素 ID 或 CSS 选择器。例如，"#notice_area" 
   会影响到 ``<div id="notice_area">`` ，"#content a.notice" 会影响到 ID 为 "content"
   的元素下的所有 class 为 "notice" 的链接。
-  "``code_to_run()``" 为你自己写的脚本，或者是一个 jQuery 动作，譬如下面所介绍的特效。

特效
=======

jQuery 库支持很多强大的 `特效 <http://api.jquery.com/category/effects/>`_ ，在使用特效之前，
必须使用下面的方法加载::

	$this->jquery->effect([optional path] plugin name); // for example $this->jquery->effect('bounce');


hide() / show()
---------------

这两个函数会影响你的页面上元素的可见性，hide() 函数用于将元素隐藏，show() 则相反。

::

	$this->jquery->hide(target, optional speed, optional extra information);
	$this->jquery->show(target, optional speed, optional extra information);


-  "target" 是任何有效的 jQuery 选择器。
-  "speed" 可选，可以设置为 slow、normal、fast 或你自己设置的毫秒数。
-  "extra information" 可选，可以包含一个回调，或者其他的附加信息。

toggle()
--------

toggle() 用于将元素的可见性改成和当前的相反，将可见的元素隐藏，将隐藏的元素可见。

::

	$this->jquery->toggle(target);


-  "target" 是任何有效的 jQuery 选择器。

animate()
---------

::

	 $this->jquery->animate(target, parameters, optional speed, optional extra information);


-  "target" 是任何有效的 jQuery 选择器。
-  "parameters" 通常是你想改变元素的一些 CSS 属性。
-  "speed" 可选，可以设置为 slow、normal、fast 或你自己设置的毫秒数。
-  "extra information" 可选，可以包含一个回调，或者其他的附加信息。

更完整的说明，参见 `http://api.jquery.com/animate/ <http://api.jquery.com/animate/>`_

下面是个在 ID 为 "note" 的一个 div 上使用 animate() 的例子，它使用了 jQuery 库的 click 事件，
通过 click 事件触发。

::

	$params = array(
	'height' => 80,
	'width' => '50%',
	'marginLeft' => 125
	);
	$this->jquery->click('#trigger', $this->jquery->animate('#note', $params, 'normal'));

toggleClass()
-------------

该函数用于往目标元素添加或移除一个 CSS 类。

::

	$this->jquery->toggleClass(target, class)


-  "target" 是任何有效的 jQuery 选择器。
-  "class" 是任何 CSS 类名，注意这个类必须是在某个已加载的 CSS 文件中定义的。

fadeIn() / fadeOut()
--------------------

这两个特效会使某个元素渐变的隐藏和显示。

::

	$this->jquery->fadeIn(target,  optional speed, optional extra information);
	$this->jquery->fadeOut(target,  optional speed, optional extra information);


-  "target" 是任何有效的 jQuery 选择器。
-  "speed" 可选，可以设置为 slow、normal、fast 或你自己设置的毫秒数。
-  "extra information" 可选，可以包含一个回调，或者其他的附加信息。

slideUp() / slideDown() / slideToggle()
---------------------------------------

这些特效可以让元素滑动。

::

	$this->jquery->slideUp(target,  optional speed, optional extra information);
	$this->jquery->slideDown(target,  optional speed, optional extra information);
	$this->jquery->slideToggle(target,  optional speed, optional extra information);


-  "target" 是任何有效的 jQuery 选择器。
-  "speed" 可选，可以设置为 slow、normal、fast 或你自己设置的毫秒数。
-  "extra information" 可选，可以设置为 slow、normal、fast 或你自己设置的毫秒数。

插件
=======

使用这个库时还有几个 jQuery 插件可用。

corner()
--------

用于在页面的某个元素四周添加不同样式的边角。更多详细信息，参考
`http://malsup.com/jquery/corner/ <http://malsup.com/jquery/corner/>`_

::

	$this->jquery->corner(target, corner_style);


-  "target" 是任何有效的 jQuery 选择器。
-  "corner_style" 可选，可以设置为任何有效的样式，譬如：
   round、sharp、bevel、bite、dog 等。如果只想设置某个边角的样式，
   可以在样式后添加一个空格，然后使用 "tl" （左上），"tr" （右上），
   "bl" （左下），和 "br" （右下）。

::

	$this->jquery->corner("#note", "cool tl br");


tablesorter()
-------------

待添加

modal()
-------

待添加

calendar()
----------

待添加