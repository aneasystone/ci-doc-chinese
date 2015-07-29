######################
数据库工具类
######################

数据库工具类提供了一些方法用于帮助你管理你的数据库。

.. contents::
    :local:
    :depth: 2

******************************
初始化工具类
******************************

.. important:: 由于工具类依赖于数据库驱动器，为了初始化工具类，你的数据库驱动器必须已经运行。

加载工具类的代码如下::

	$this->load->dbutil();

如果你想管理的不是你正在使用的数据库，你还可以传另一个数据库对象到数据库工具类的加载方法::

	$this->myutil = $this->load->dbutil($this->other_db, TRUE);

上例中，我们通过第一个参数传递了一个自定义的数据库对象，第二个参数表示方法将返回 dbutil 对象，
而不是直接赋值给 ``$this->dbutil`` 。

.. note:: 两个参数都可以独立使用，如果你只想传第二个参数，可以将第一个参数置空。

一旦初始化结束，你就可以使用 ``$this->dbutil`` 对象来访问它的方法::

	$this->dbutil->some_method();

****************************
使用数据库工具类
****************************

获取数据库名称列表
================================

返回一个包含所有数据库名称的列表::

	$dbs = $this->dbutil->list_databases();

	foreach ($dbs as $db)
	{
 		echo $db;
	}


判断一个数据库是否存在
==============================

有时我们需要判断某个数据库是否存在，可以使用该方法。方法返回布尔值 TRUE/FALSE 。例如::

	if ($this->dbutil->database_exists('database_name'))
	{
		// some code...
	}

.. note:: 使用你自己的数据库名替换 *database_name* ，该方法区分大小写。

优化表
================

根据你指定的表名来优化表，根据成败返回 TRUE 或 FALSE ::

	if ($this->dbutil->optimize_table('table_name'))
	{
		echo 'Success!';
	}

.. note:: 不是所有的数据库平台都支持表优化，通常使用在 MySQL 数据库上。

修复表
==============

根据你指定的表名来修复表，根据成败返回 TRUE 或 FALSE ::

	if ($this->dbutil->repair_table('table_name'))
	{
		echo 'Success!';
	}

.. note:: 不是所有的数据库平台都支持表修复。

优化数据库
===================

允许你优化数据库类当前正在连接的数据库。返回一个数组，包含数据库状态信息，失败时返回 FALSE 。

::

	$result = $this->dbutil->optimize_database();

	if ($result !== FALSE)
	{
		print_r($result);
	}

.. note:: 不是所有的数据库平台都支持数据库优化，通常使用在 MySQL 数据库上。

将查询结果导出到 CSV 文档
===================================

允许你从查询结果生成 CSV 文档，第一个参数必须是查询的结果对象。例如::

	$this->load->dbutil();

	$query = $this->db->query("SELECT * FROM mytable");

	echo $this->dbutil->csv_from_result($query);

第二、三、四个参数分别为分隔符、换行符和每个字段包围字符，默认情况下，分隔符为逗号，换行符为 "\n" ，
包围字符为双引号。例如::

	$delimiter = ",";
	$newline = "\r\n";
	$enclosure = '"';

	echo $this->dbutil->csv_from_result($query, $delimiter, $newline, $enclosure);

.. important:: 该方法并不写入 CSV 文档，它只是简单的返回 CSV 内容，如果你需要写入到文件中，
	你可以使用 :doc:`文件辅助库 <../helpers/file_helper>` 。

将查询结果导出到 XML 文档
========================================

允许你从查询结果生成 XML 文档，第一个参数为查询的结果对象，第二个参数可选，可以包含一些的配置参数。例如::

	$this->load->dbutil();

	$query = $this->db->query("SELECT * FROM mytable");

	$config = array (
		'root'		=> 'root',
		'element'	=> 'element',
		'newline'	=> "\n",
		'tab'		=> "\t"
	);

	echo $this->dbutil->xml_from_result($query, $config);

.. important:: 该方法并不写入 XML 文档，它只是简单的返回 XML 内容，如果你需要写入到文件中，
	你可以使用 :doc:`文件辅助库 <../helpers/file_helper>` 。

********************
备份你的数据库
********************

数据备份说明
=====================

允许你备份完整的数据库或指定的表。备份的数据可以压缩成 Zip 或 Gzip 格式。

.. note:: 该功能只支持 MySQL 和 Interbase/Firebird 数据库。

.. note:: 对于 Interbase/Firebird 数据库，只能提供一个备份文件名参数。

		$this->dbutil->backup('db_backup_filename');

.. note:: 限于 PHP 的执行时间和内存限制，备份非常大的数据库应该不行。如果你的数据库非常大，
	你可以直接使用命令行进行备份，如果你没有 root 权限的话，让你的管理员来帮你备份。

使用示例
=============

::

	// Load the DB utility class
	$this->load->dbutil();

	// Backup your entire database and assign it to a variable
	$backup = $this->dbutil->backup();

	// Load the file helper and write the file to your server
	$this->load->helper('file');
	write_file('/path/to/mybackup.gz', $backup);

	// Load the download helper and send the file to your desktop
	$this->load->helper('download');
	force_download('mybackup.gz', $backup);

设置备份参数
==========================

备份参数为一个数组，通过第一个参数传递给 ``backup()`` 方法，例如::

	$prefs = array(
		'tables'	=> array('table1', 'table2'),	// Array of tables to backup.
		'ignore'	=> array(),			// List of tables to omit from the backup
		'format'	=> 'txt',			// gzip, zip, txt
		'filename'	=> 'mybackup.sql',		// File name - NEEDED ONLY WITH ZIP FILES
		'add_drop'	=> TRUE,			// Whether to add DROP TABLE statements to backup file
		'add_insert'	=> TRUE,			// Whether to add INSERT data to backup file
		'newline'	=> "\n"				// Newline character used in backup file
	);

	$this->dbutil->backup($prefs);

备份参数说明
=================================

======================= ======================= ======================= ========================================================================
参数                      默认值           选项                 描述
======================= ======================= ======================= ========================================================================
**tables**               empty array             None                    你要备份的表，如果留空将备份所有的表。
**ignore**               empty array             None                    你要忽略备份的表。
**format**               gzip                    gzip, zip, txt          导出文件的格式。
**filename**             the current date/time   None                    备份文件名。如果你使用了 zip 压缩这个参数是必填的。
**add_drop**             TRUE                    TRUE/FALSE              是否在导出的 SQL 文件里包含 DROP TABLE 语句
**add_insert**           TRUE                    TRUE/FALSE              是否在导出的 SQL 文件里包含 INSERT 语句
**newline**              "\\n"                   "\\n", "\\r", "\\r\\n"  导出的 SQL 文件使用的换行符
**foreign_key_checks**   TRUE                    TRUE/FALSE              导出的 SQL 文件中是否继续保持外键约束
======================= ======================= ======================= ========================================================================

***************
类参考
***************

.. php:class:: CI_DB_utility

	.. php:method:: backup([$params = array()])

		:param	array	$params: An associative array of options
		:returns:	raw/(g)zipped SQL query string
		:rtype:	string

		根据用户参数执行数据库备份。

	.. php:method:: database_exists($database_name)

		:param	string	$database_name: Database name
		:returns:	TRUE if the database exists, FALSE otherwise
		:rtype:	bool

		判断数据库是否存在。

	.. php:method:: list_databases()

		:returns:	Array of database names found
		:rtype:	array

		获取所有的数据库名称列表。

	.. php:method:: optimize_database()

		:returns:	Array of optimization messages or FALSE on failure
		:rtype:	array

		优化数据库。

	.. php:method:: optimize_table($table_name)

		:param	string	$table_name:	Name of the table to optimize
		:returns:	Array of optimization messages or FALSE on failure
		:rtype:	array

		优化数据库表。

	.. php:method:: repair_table($table_name)

		:param	string	$table_name:	Name of the table to repair
		:returns:	Array of repair messages or FALSE on failure
		:rtype:	array

		修复数据库表。

	.. php:method:: csv_from_result($query[, $delim = ','[, $newline = "\n"[, $enclosure = '"']]])

		:param	object	$query:	A database result object
		:param	string	$delim: The CSV field delimiter to use
		:param	string	$newline: The newline character to use
		:param	string	$enclosure: The enclosure delimiter to use
		:returns:	The generated CSV file as a string
		:rtype:	string

		将数据库结果对象转换为 CSV 文档。

	.. php:method:: xml_from_result($query[, $params = array()])

		:param	object	$query: A database result object
		:param	array	$params: An associative array of preferences
		:returns:	The generated XML document as a string
		:rtype:	string

		将数据库结果对象转换为 XML 文档。
