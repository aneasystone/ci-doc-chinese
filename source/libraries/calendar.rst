#################
日历类
#################

使用日历类可以让你动态的创建日历，并且可以使用日历模板来格式化显示你的日历，
允许你 100% 的控制它设计的每个方面。另外，你还可以向日历的单元格传递数据。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

***************************
使用日历类
***************************

初始化类
======================

跟 CodeIgniter 中的其他类一样，可以在你的控制器中使用 ``$this->load->library()`` 
方法加载日历类::

	$this->load->library('calendar');

一旦加载，日历类就可以像下面这样使用::

	$this->calendar

显示一个日历
=====================

这里是一个简单的例子，告诉你如何去显示一个日历::

	$this->load->library('calendar');
	echo $this->calendar->generate();

上面的代码将根据你服务器时间创建一个当前月/年的日历。要显示一个指定月和年的日历，
你要传递这些信息到日历生成函数:

	$this->load->library('calendar');
	echo $this->calendar->generate(2006, 6);

上面的代码将创建一个显示 2006 年 6 月的日历，第一个参数指定了年，第二个参数指定了月。

传数据到单元格
===================================

如果需要添加数据到日历的单元格中，就要创建一个关联数组，这个数组中索引是想添加数据的日期，
数组对应的值是想添加的数据。该数组通过日历生成函数的第三个参数被传入，参考下面这个例子::

	$this->load->library('calendar');

	$data = array(
		3  => 'http://example.com/news/article/2006/03/',
		7  => 'http://example.com/news/article/2006/07/',
		13 => 'http://example.com/news/article/2006/13/',
		26 => 'http://example.com/news/article/2006/26/'
	);

	echo $this->calendar->generate(2006, 6, $data);

使用上面的例子，天数为 3、7、13 和 26 将变成链接指向你提供的 URL 。

.. note:: 默认情况下，系统假定你的数组中包含了链接。在下面介绍日历模板的部分，
	你会看到你可以自定义处理传入日历单元格的数据，所以你可以传不同类型的信息。

设置显示参数
===========================

有 8 种不同的参数可以让你设置日历的各个方面，你可以通过加载函数的第二个参数来设置参数。
例如::

	$prefs = array(
		'start_day'    => 'saturday',
		'month_type'   => 'long',
		'day_type'     => 'short'
	);

	$this->load->library('calendar', $prefs);

	echo $this->calendar->generate();

上面的代码将显示一个日历从礼拜六开始，使用用完整的月份标题和缩写的天数格式。
更多参数信息请看下面。

======================  =================  ============================================  ===================================================================
参数                    默认值              选项                                         描述
======================  =================  ============================================  ===================================================================
**template**           	None               None                                          字符串或数组，包含了你的日历模板，见下面的模板部分。
**local_time**        	time()             None                                          当前时间的 Unix 时间戳。
**start_day**           sunday             Any week day (sunday, monday, tuesday, etc.)  指定每周的第一天是周几。
**month_type**          long               long, short                                   月份的显示样式（long = January, short = Jan）
**day_type**            abr                long, short, abr                              星期的显示样式（long = Sunday, short = Sun, abr = Su）
**show_next_prev**      FALSE              TRUE/FALSE (boolean)                          是否显示 "上个月" 和 "下个月" 链接，见下文。
**next_prev_url**       controller/method  A URL                                         设置 "上个月" 和 "下个月" 的链接地址。
**show_other_days**     FALSE              TRUE/FALSE (boolean)                          是否显示第一周和最后一周相邻月份的日期。
======================  =================  ============================================  ===================================================================


显示下一月/上一月链接
=================================

要让你的日历通过下一月/上一月链接动态的减少/增加，可以仿照下面的例子建立你的日历::

	$prefs = array(
		'show_next_prev'  => TRUE,
		'next_prev_url'   => 'http://example.com/index.php/calendar/show/'
	);

	$this->load->library('calendar', $prefs);

	echo $this->calendar->generate($this->uri->segment(3), $this->uri->segment(4));

在上面的例子中，你会注意到这几点：

-  "show_next_prev" 参数必须设置为 TRUE
-  "next_prev_url" 参数必须设置一个 URL ，如果不设置，会默认使用当前的 **控制器/方法**
-  通过 URI 的段将 "年" 和 "月" 参数传递给日历生成函数（日历类会自动添加 "年" 和 "月" 到你提供的 URL）

创建一个日历模板
============================

通过创建一个日历模板你能够 100% 的控制你的日历的设计。当使用字符串方式设置模板时，
日历的每一部分都要被放在一对伪变量中，像下面这样::

	$prefs['template'] = '

		{table_open}<table border="0" cellpadding="0" cellspacing="0">{/table_open}

		{heading_row_start}<tr>{/heading_row_start}

		{heading_previous_cell}<th><a href="{previous_url}">&lt;&lt;</a></th>{/heading_previous_cell}
		{heading_title_cell}<th colspan="{colspan}">{heading}</th>{/heading_title_cell}
		{heading_next_cell}<th><a href="{next_url}">&gt;&gt;</a></th>{/heading_next_cell}

		{heading_row_end}</tr>{/heading_row_end}

		{week_row_start}<tr>{/week_row_start}
		{week_day_cell}<td>{week_day}</td>{/week_day_cell}
		{week_row_end}</tr>{/week_row_end}

		{cal_row_start}<tr>{/cal_row_start}
		{cal_cell_start}<td>{/cal_cell_start}
		{cal_cell_start_today}<td>{/cal_cell_start_today}
		{cal_cell_start_other}<td class="other-month">{/cal_cell_start_other}

		{cal_cell_content}<a href="{content}">{day}</a>{/cal_cell_content}
		{cal_cell_content_today}<div class="highlight"><a href="{content}">{day}</a></div>{/cal_cell_content_today}

		{cal_cell_no_content}{day}{/cal_cell_no_content}
		{cal_cell_no_content_today}<div class="highlight">{day}</div>{/cal_cell_no_content_today}

		{cal_cell_blank}&nbsp;{/cal_cell_blank}

		{cal_cell_other}{day}{cal_cel_other}

		{cal_cell_end}</td>{/cal_cell_end}
		{cal_cell_end_today}</td>{/cal_cell_end_today}
		{cal_cell_end_other}</td>{/cal_cell_end_other}
		{cal_row_end}</tr>{/cal_row_end}

		{table_close}</table>{/table_close}
	';

	$this->load->library('calendar', $prefs);

	echo $this->calendar->generate();

当使用数组方式设置模板时，你需要传递 `key => value` 键值对，你可以只设置你想设置的参数，
其他没有设置的参数会使用日历类的默认值代替。

例子::

	$prefs['template'] = array(
		'table_open'           => '<table class="calendar">',
		'cal_cell_start'       => '<td class="day">',
		'cal_cell_start_today' => '<td class="today">'
	);
    
	$this->load->library('calendar', $prefs);
    
	echo $this->calendar->generate();

***************
类参考
***************

.. php:class:: CI_Calendar

	.. php:method:: initialize([$config = array()])

		:param	array	$config: Configuration parameters
		:returns:	CI_Calendar instance (method chaining)
		:rtype:	CI_Calendar

		初始化日历类参数，输入参数为一个关联数组，包含了日历的显示参数。

	.. php:method:: generate([$year = ''[, $month = ''[, $data = array()]]])

		:param	int	$year: Year
		:param	int	$month: Month
		:param	array	$data: Data to be shown in the calendar cells
		:returns:	HTML-formatted calendar
		:rtype:	string

		生成日历。


	.. php:method:: get_month_name($month)

		:param	int	$month: Month
		:returns:	Month name
		:rtype:	string

		提供数字格式的月份，返回月份的名称。

	.. php:method:: get_day_names($day_type = '')

		:param	string	$day_type: 'long', 'short', or 'abr'
		:returns:	Array of day names
		:rtype:	array

		根据类型返回一个包含星期名称（Sunday、Monday 等等）的数组，类型有：long、short 和 abr 。
		如果没有指定 ``$day_type`` 参数（或该参数无效），方法默认使用 abr（缩写） 格式。

	.. php:method:: adjust_date($month, $year)

		:param	int	$month: Month
		:param	int	$year: Year
		:returns:	An associative array containing month and year
		:rtype:	array

		该方法调整日期确保日期有效。例如，如果你将月份设置为 13 ，年份将自动加 1 ，并且月份变为一月::

			print_r($this->calendar->adjust_date(13, 2014));

		输出::

			Array
			(    
				[month] => '01'
				[year] => '2015'
			)

	.. php:method:: get_total_days($month, $year)

		:param	int	$month: Month
		:param	int	$year: Year
		:returns:	Count of days in the specified month
		:rtype:	int

		获取指定月的天数::

			echo $this->calendar->get_total_days(2, 2012);
			// 29

		.. note:: 该方法是 :doc:`日期辅助库 <../helpers/date_helper>` 的 :php:func:`days_in_month()` 函数的别名。

	.. php:method:: default_template()

		:returns:	An array of template values
		:rtype:	array

		默认的模板，当你没有使用你自己的模板时将会使用该方法。


	.. php:method:: parse_template()

		:returns:	CI_Calendar instance (method chaining)
		:rtype:	CI_Calendar

		解析模板中的伪变量 ``{pseudo-variables}`` 显示日历。
