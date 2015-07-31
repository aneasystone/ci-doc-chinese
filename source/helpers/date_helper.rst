###########
日期辅助库
###########

日期辅助库文件包含了一些帮助你处理日期的函数。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

加载辅助库
===================

该辅助库通过下面的代码加载::

	$this->load->helper('date');

可用函数
===================

该辅助库有下列可用函数：

.. php:function:: now([$timezone = NULL])

	:param	string	$timezone: Timezone
	:returns:	UNIX timestamp
	:rtype:	int

	根据服务器的本地时间，以及一个 PHP 支持的时区参数或配置文件中的 "基准时间" 参数返回当前时间的 UNIX 时间戳，
	如果你不打算设置 "基准时间" （如果你的站点允许用户设置他们自己的时区，你通常需要设置这个），
	该函数就和 PHP 的 ``time()`` 函数没什么区别。
	::

		echo now('Australia/Victoria');

	如果没有指定时区，该函数将使用 **time_reference** 参数调用 ``time()`` 函数。

.. php:function:: mdate([$datestr = ''[, $time = '']])

	:param	string	$datestr: Date string
	:param	int	$time: UNIX timestamp
	:returns:	MySQL-formatted date
	:rtype:	string

	该函数和 PHP 的 `date() <http://php.net/manual/en/function.date.php>`_ 函数一样，
	但是它支持 MySQL 风格的日期格式，在代码之前使用百分号，例如：`%Y %m %d`

	使用这个函数的好处是你不用关心去转义那些不是日期代码的字符，如果使用 ``date()`` 函数时，你就要这么做。

	例如::

		$datestring = 'Year: %Y Month: %m Day: %d - %h:%i %a';
		$time = time();
		echo mdate($datestring, $time);

	如果第二个参数没有提供一个时间，那么默认会使用当前时间。

.. php:function:: standard_date([$fmt = 'DATE_RFC822'[, $time = NULL]])

	:param	string	$fmt: Date format
	:param	int	$time: UNIX timestamp
	:returns:	Formatted date or FALSE on invalid format
	:rtype:	string

	生成标准格式的时间字符串。

	例如::

		$format = 'DATE_RFC822';
		$time = time();
		echo standard_date($format, $time);

	.. note:: 该函数已经废弃，请使用原生的 ``date()`` 函数和
		`时间格式化常量 <http://php.net/manual/en/class.datetime.php#datetime.constants.types>`_
		替代::

			echo date(DATE_RFC822, time());

	**支持的格式：**

	===============	=======================	======================================
	Constant        Description             Example
	===============	=======================	======================================
	DATE_ATOM       Atom                    2005-08-15T16:13:03+0000
	DATE_COOKIE     HTTP Cookies            Sun, 14 Aug 2005 16:13:03 UTC
	DATE_ISO8601    ISO-8601                2005-08-14T16:13:03+00:00
	DATE_RFC822     RFC 822                 Sun, 14 Aug 05 16:13:03 UTC
	DATE_RFC850     RFC 850                 Sunday, 14-Aug-05 16:13:03 UTC
	DATE_RFC1036    RFC 1036                Sunday, 14-Aug-05 16:13:03 UTC
	DATE_RFC1123    RFC 1123                Sun, 14 Aug 2005 16:13:03 UTC
	DATE_RFC2822    RFC 2822                Sun, 14 Aug 2005 16:13:03 +0000
	DATE_RSS        RSS                     Sun, 14 Aug 2005 16:13:03 UTC
	DATE_W3C        W3C                     2005-08-14T16:13:03+0000
	===============	=======================	======================================

.. php:function:: local_to_gmt([$time = ''])

	:param	int	$time: UNIX timestamp
	:returns:	UNIX timestamp
	:rtype:	int

	将时间转换为 GMT 时间。

	例如::

		$gmt = local_to_gmt(time());

.. php:function:: gmt_to_local([$time = ''[, $timezone = 'UTC'[, $dst = FALSE]]])

	:param	int	$time: UNIX timestamp
	:param	string	$timezone: Timezone
	:param	bool	$dst: Whether DST is active
	:returns:	UNIX timestamp
	:rtype:	int

	根据指定的时区和 DST （夏令时，Daylight Saving Time） 将 GMT 时间转换为本地时间。

	例如::

		$timestamp = 1140153693;
		$timezone  = 'UM8';
		$daylight_saving = TRUE;
		echo gmt_to_local($timestamp, $timezone, $daylight_saving);


	.. note:: 时区列表请参见本页末尾。

.. php:function:: mysql_to_unix([$time = ''])

	:param	string	$time: MySQL timestamp
	:returns:	UNIX timestamp
	:rtype:	int

	将 MySQL 时间戳转换为 UNIX 时间戳。

	例如::

		$unix = mysql_to_unix('20061124092345');

.. php:function:: unix_to_human([$time = ''[, $seconds = FALSE[, $fmt = 'us']]])

	:param	int	$time: UNIX timestamp
	:param	bool	$seconds: Whether to show seconds
	:param	string	$fmt: format (us or euro)
	:returns:	Formatted date
	:rtype:	string

	将 UNIX 时间戳转换为方便人类阅读的格式，如下::

		YYYY-MM-DD HH:MM:SS AM/PM

	这在当你需要在一个表单字段中显示日期时很有用。

	格式化后的时间可以带也可以不带秒数，也可以设置成欧洲或美国时间格式。
	如果只指定了一个时间参数，将使用不带秒数的美国时间格式。

	例如::

		$now = time();
		echo unix_to_human($now); // U.S. time, no seconds
		echo unix_to_human($now, TRUE, 'us'); // U.S. time with seconds
		echo unix_to_human($now, TRUE, 'eu'); // Euro time with seconds

.. php:function:: human_to_unix([$datestr = ''])

	:param	int	$datestr: Date string
	:returns:	UNIX timestamp or FALSE on failure
	:rtype:	int

	该函数和 :php:func:`unix_to_human()` 函数相反，将一个方便人类阅读的时间格式转换为 UNIX 时间戳。
	这在当你需要在一个表单字段中显示日期时很有用。如果输入的时间不同于上面的格式，函数返回 FALSE 。

	例如::

		$now = time();
		$human = unix_to_human($now);
		$unix = human_to_unix($human);

.. php:function:: nice_date([$bad_date = ''[, $format = FALSE]])

	:param	int	$bad_date: The terribly formatted date-like string
	:param	string	$format: Date format to return (same as PHP's ``date()`` function)
	:returns:	Formatted date
	:rtype:	string

	该函数解析一个没有格式化过的数字格式的日期，并将其转换为格式化的日期。它也能解析格式化好的日期。

	默认该函数将返回 UNIX 时间戳，你也可以提供一个格式化字符串给第二个参数（和 PHP 的 ``date()`` 函数一样）。

	例如::

		$bad_date = '199605';
		// Should Produce: 1996-05-01
		$better_date = nice_date($bad_date, 'Y-m-d');

		$bad_date = '9-11-2001';
		// Should Produce: 2001-09-11
		$better_date = nice_date($bad_date, 'Y-m-d');

.. php:function:: timespan([$seconds = 1[, $time = ''[, $units = '']]])

	:param	int	$seconds: Number of seconds
	:param	string	$time: UNIX timestamp
	:param	int	$units: Number of time units to display
	:returns:	Formatted time difference
	:rtype:	string

	将一个 UNIX 时间戳转换为以下这种格式::

		1 Year, 10 Months, 2 Weeks, 5 Days, 10 Hours, 16 Minutes

	第一个参数为一个 UNIX 时间戳，第二个参数是一个比第一个参数大的 UNIX 时间戳。
	第三个参数可选，用于限制要显示的时间单位个数。

	如果第二个参数为空，将使用当前时间。

	这个函数最常见的用途是，显示从过去某个时间点到当前时间经过了多少时间。

	例如::

		$post_date = '1079621429';
		$now = time();
		$units = 2;
		echo timespan($post_date, $now, $units);

	.. note:: 该函数生成的本文可以在语言文件 `language/<your_lang>/date_lang.php` 中找到。

.. php:function:: days_in_month([$month = 0[, $year = '']])

	:param	int	$month: a numeric month
	:param	int	$year: a numeric year
	:returns:	Count of days in the specified month
	:rtype:	int

	返回指定某个月的天数，会考虑闰年。

	例如::

		echo days_in_month(06, 2005);

	如果第二个参数为空，将使用今年。

	.. note:: 该函数其实是原生的 ``cal_days_in_month()`` 函数的别名，如果它可用的话。

.. php:function:: date_range([$unix_start = ''[, $mixed = ''[, $is_unix = TRUE[, $format = 'Y-m-d']]]])

	:param	int	$unix_start: UNIX timestamp of the range start date
	:param	int	$mixed: UNIX timestamp of the range end date or interval in days
	:param	bool	$is_unix: set to FALSE if $mixed is not a timestamp
	:param	string	$format: Output date format, same as in ``date()``
	:returns:	An array of dates
	:rtype:	array

	返回某一段时间的日期列表。

	例如::

		$range = date_range('2012-01-01', '2012-01-15');
		echo "First 15 days of 2012:";
		foreach ($range as $date)
		{
			echo $date."\n";
		}

.. php:function:: timezones([$tz = ''])

	:param	string	$tz: A numeric timezone
	:returns:	Hour difference from UTC
	:rtype:	int

	根据指定的时区（可用的时区列表参见下文的 "时区参考"）返回它的 UTC 时间偏移。

	例如::

		echo timezones('UM5');


	这个函数和 :php:func:`timezone_menu()` 函数一起使用时很有用。

.. php:function:: timezone_menu([$default = 'UTC'[, $class = ''[, $name = 'timezones'[, $attributes = '']]]])

	:param	string	$default: Timezone
	:param	string	$class: Class name
	:param	string	$name: Menu name
	:param	mixed	$attributes: HTML attributes
	:returns:	HTML drop down menu with time zones
	:rtype:	string

	该函数用于生成一个时区下拉菜单，像下面这样。

	.. raw:: html

		<form action="#">
			<select name="timezones">
				<option value='UM12'>(UTC -12:00) Baker/Howland Island</option>
				<option value='UM11'>(UTC -11:00) Samoa Time Zone, Niue</option>
				<option value='UM10'>(UTC -10:00) Hawaii-Aleutian Standard Time, Cook Islands, Tahiti</option>
				<option value='UM95'>(UTC -9:30) Marquesas Islands</option>
				<option value='UM9'>(UTC -9:00) Alaska Standard Time, Gambier Islands</option>
				<option value='UM8'>(UTC -8:00) Pacific Standard Time, Clipperton Island</option>
				<option value='UM7'>(UTC -7:00) Mountain Standard Time</option>
				<option value='UM6'>(UTC -6:00) Central Standard Time</option>
				<option value='UM5'>(UTC -5:00) Eastern Standard Time, Western Caribbean Standard Time</option>
				<option value='UM45'>(UTC -4:30) Venezuelan Standard Time</option>
				<option value='UM4'>(UTC -4:00) Atlantic Standard Time, Eastern Caribbean Standard Time</option>
				<option value='UM35'>(UTC -3:30) Newfoundland Standard Time</option>
				<option value='UM3'>(UTC -3:00) Argentina, Brazil, French Guiana, Uruguay</option>
				<option value='UM2'>(UTC -2:00) South Georgia/South Sandwich Islands</option>
				<option value='UM1'>(UTC -1:00) Azores, Cape Verde Islands</option>
				<option value='UTC' selected='selected'>(UTC) Greenwich Mean Time, Western European Time</option>
				<option value='UP1'>(UTC +1:00) Central European Time, West Africa Time</option>
				<option value='UP2'>(UTC +2:00) Central Africa Time, Eastern European Time, Kaliningrad Time</option>
				<option value='UP3'>(UTC +3:00) Moscow Time, East Africa Time</option>
				<option value='UP35'>(UTC +3:30) Iran Standard Time</option>
				<option value='UP4'>(UTC +4:00) Azerbaijan Standard Time, Samara Time</option>
				<option value='UP45'>(UTC +4:30) Afghanistan</option>
				<option value='UP5'>(UTC +5:00) Pakistan Standard Time, Yekaterinburg Time</option>
				<option value='UP55'>(UTC +5:30) Indian Standard Time, Sri Lanka Time</option>
				<option value='UP575'>(UTC +5:45) Nepal Time</option>
				<option value='UP6'>(UTC +6:00) Bangladesh Standard Time, Bhutan Time, Omsk Time</option>
				<option value='UP65'>(UTC +6:30) Cocos Islands, Myanmar</option>
				<option value='UP7'>(UTC +7:00) Krasnoyarsk Time, Cambodia, Laos, Thailand, Vietnam</option>
				<option value='UP8'>(UTC +8:00) Australian Western Standard Time, Beijing Time, Irkutsk Time</option>
				<option value='UP875'>(UTC +8:45) Australian Central Western Standard Time</option>
				<option value='UP9'>(UTC +9:00) Japan Standard Time, Korea Standard Time, Yakutsk Time</option>
				<option value='UP95'>(UTC +9:30) Australian Central Standard Time</option>
				<option value='UP10'>(UTC +10:00) Australian Eastern Standard Time, Vladivostok Time</option>
				<option value='UP105'>(UTC +10:30) Lord Howe Island</option>
				<option value='UP11'>(UTC +11:00) Srednekolymsk Time, Solomon Islands, Vanuatu</option>
				<option value='UP115'>(UTC +11:30) Norfolk Island</option>
				<option value='UP12'>(UTC +12:00) Fiji, Gilbert Islands, Kamchatka Time, New Zealand Standard Time</option>
				<option value='UP1275'>(UTC +12:45) Chatham Islands Standard Time</option>
				<option value='UP13'>(UTC +13:00) Phoenix Islands Time, Tonga</option>
				<option value='UP14'>(UTC +14:00) Line Islands</option>
			</select>
		</form>


	当你的站点允许用户选择自己的本地时区时，这个菜单会很有用。

	第一个参数为菜单默认选定的时区，例如，要设置太平洋时间为默认值，你可以这样::

		echo timezone_menu('UM8');

	菜单中的值请参见下面的时区参考。

	第二个参数用于为菜单设置一个 CSS 类名。

	第四个参数用于为生成的 select 标签设置一个或多个属性。

	.. note:: 菜单中的文本可以在语言文件 `language/<your_lang>/date_lang.php` 中找到。

时区参考
==================

下表列出了每个时区和它所对应的位置。

注意，为了表述清晰和格式工整，有些位置信息做了适当的删减。

===========     =====================================================================
时区            位置
===========     =====================================================================
UM12            (UTC - 12:00) 贝克岛、豪兰岛
UM11            (UTC - 11:00) 萨摩亚时区、纽埃
UM10            (UTC - 10:00) 夏威夷-阿留申标准时间、库克群岛
UM95            (UTC - 09:30) 马克萨斯群岛
UM9             (UTC - 09:00) 阿拉斯加标准时间、甘比尔群岛
UM8             (UTC - 08:00) 太平洋标准时间、克利珀顿岛
UM7             (UTC - 07:00) 山区标准时间
UM6             (UTC - 06:00) 中部标准时间
UM5             (UTC - 05:00) 东部标准时间、西加勒比
UM45            (UTC - 04:30) 委内瑞拉标准时间
UM4             (UTC - 04:00) 大西洋标准时间、东加勒比
UM35            (UTC - 03:30) 纽芬兰标准时间
UM3             (UTC - 03:00) 阿根廷、巴西、法属圭亚那、乌拉圭
UM2             (UTC - 02:00) 南乔治亚岛、南桑威奇群岛
UM1             (UTC -1:00) 亚速尔群岛、佛得角群岛
UTC             (UTC) 格林尼治标准时间、西欧时间
UP1             (UTC +1:00) 中欧时间、西非时间
UP2             (UTC +2:00) 中非时间、东欧时间
UP3             (UTC +3:00) 莫斯科时间、东非时间
UP35            (UTC +3:30) 伊朗标准时间
UP4             (UTC +4:00) 阿塞拜疆标准时间、萨马拉时间
UP45            (UTC +4:30) 阿富汗
UP5             (UTC +5:00) 巴基斯坦标准时间、叶卡捷琳堡时间
UP55            (UTC +5:30) 印度标准时间、斯里兰卡时间
UP575           (UTC +5:45) 尼泊尔时间
UP6             (UTC +6:00) 孟加拉国标准时间、不丹时间、鄂木斯克时间
UP65            (UTC +6:30) 可可岛、缅甸
UP7             (UTC +7:00) 克拉斯诺亚尔斯克时间、柬埔寨、老挝、泰国、越南
UP8             (UTC +8:00) 澳大利亚西部标准时间、北京时间
UP875           (UTC +8:45) 澳大利亚中西部标准时间
UP9             (UTC +9:00) 日本标准时间、韩国标准时间、雅库茨克
UP95            (UTC +9:30) 澳大利亚中部标准时间
UP10            (UTC +10:00) 澳大利亚东部标准时间、海参崴时间
UP105           (UTC +10:30) 豪勋爵岛
UP11            (UTC +11:00) 中科雷姆斯克时间、所罗门群岛、瓦努阿图
UP115           (UTC +11:30) 诺福克岛
UP12            (UTC +12:00) 斐济、吉尔伯特群岛、堪察加半岛、新西兰
UP1275          (UTC +12:45) 查塔姆群岛标准时间
UP13            (UTC +13:00) 凤凰岛、汤加
UP14            (UTC +14:00) 莱恩群岛
===========	=====================================================================