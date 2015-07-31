###################
数据库驱动器参考
###################

这是一个平台无关的数据库实现基类，该类不会被直接调用，
而是通过特定的数据库适配器类来继承和实现该类。

关于数据库驱动器，已经在其他几篇文档中介绍过，这篇文档将作为它们的一个参考。

.. important:: 并不是所有的方法都被所有的数据库驱动器所支持，
	当不支持的时候，有些方法可能会失败（返回 FALSE）。

.. php:class:: CI_DB_driver

	.. php:method:: initialize()

		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		初始化数据库配置，建立对数据库的连接。

	.. php:method:: db_connect($persistent = TRUE)

		:param	bool	$persistent: Whether to establish a persistent connection or a regular one
		:returns:	Database connection resource/object or FALSE on failure
		:rtype:	mixed

		建立对数据库的连接。

		.. note:: 返回值取决于当前使用的数据库驱动器，例如 ``mysqli`` 实例将会返回 'mysqli' 驱动器。

	.. php:method:: db_pconnect()

		:returns:	Database connection resource/object or FALSE on failure
		:rtype:	mixed

		建立对数据库的连接，使用持久连接。

		.. note:: 该方法其实就是调用 ``db_connect(TRUE)`` 。

	.. php:method:: reconnect()

		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		如果超过服务器的超时时间都没有发送任何查询请求，
		使用该方法可以让数据库连接保持有效，或重新连接数据库。

	.. php:method:: db_select([$database = ''])

		:param	string	$database: Database name
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		切换到某个数据库。

	.. php:method:: db_set_charset($charset)

		:param	string	$charset: Character set name
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		设置客户端字符集。

	.. php:method:: platform()

		:returns:	Platform name
		:rtype:	string

		当前使用的数据库平台（mysql、mssql 等）。

	.. php:method:: version()

		:returns:	The version of the database being used
		:rtype:	string

		数据库版本。

	.. php:method:: query($sql[, $binds = FALSE[, $return_object = NULL]]])

		:param	string	$sql: The SQL statement to execute
		:param	array	$binds: An array of binding data
		:param	bool	$return_object: Whether to return a result object or not
		:returns:	TRUE for successful "write-type" queries, CI_DB_result instance (method chaining) on "query" success, FALSE on failure
		:rtype:	mixed

		执行一个 SQL 查询。

		如果是读类型的查询，执行 SQL 成功后将返回结果对象。

		有以下几种可能的返回值：

		   - 如果是写类型的查询，执行成功返回 TRUE 
		   - 执行失败返回 FALSE 
		   - 如果是读类型的查询，执行成功返回 ``CI_DB_result`` 对象

		.. note: 如果 'db_debug' 设置为 TRUE ，那么查询失败时将显示一个错误页面，
			而不是返回 FALSE ，并终止脚本的执行

	.. php:method:: simple_query($sql)

		:param	string	$sql: The SQL statement to execute
		:returns:	Whatever the underlying driver's "query" function returns
		:rtype:	mixed

		``query()`` 方法的简化版，当你只需要简单的执行一个查询，并不关心查询的结果时，
		可以使用该方法。

	.. php:method:: trans_strict([$mode = TRUE])

		:param	bool	$mode: Strict mode flag
		:rtype:	void

		启用或禁用事务的严格模式。

		在严格模式下，如果你正在运行多组事务，只要有一组失败，所有组都会被回滚。
		如果禁用严格模式，那么每一组都被视为独立的组，
		这意味着其中一组失败不会影响其他的组。

	.. php:method:: trans_off()

		:rtype:	void

		实时的禁用事务。

	.. php:method:: trans_start([$test_mode = FALSE])

		:param	bool	$test_mode: Test mode flag
		:rtype:	void

		开启一个事务。

	.. php:method:: trans_complete()

		:rtype:	void

		结束事务。

	.. php:method:: trans_status()

                	:returns:	TRUE if the transaction succeeded, FALSE if it failed
		:rtype:	bool

		获取事务的状态，用来判断事务是否执行成功。

	.. php:method:: compile_binds($sql, $binds)

		:param	string	$sql: The SQL statement 
		:param	array	$binds: An array of binding data
		:returns:	The updated SQL statement
		:rtype:	string

		根据绑定的参数值编译 SQL 查询。

	.. php:method:: is_write_type($sql)

		:param	string	$sql: The SQL statement 
		:returns:	TRUE if the SQL statement is of "write type", FALSE if not
		:rtype:	bool

		判断查询是写类型（INSERT、UPDATE、DELETE），还是读类型（SELECT）。

	.. php:method:: elapsed_time([$decimals = 6])

		:param	int	$decimals: The number of decimal places
		:returns:	The aggregate query elapsed time, in microseconds
		:rtype:	string

		计算查询所消耗的时间。

	.. php:method:: total_queries()

		:returns:	The total number of queries executed
		:rtype:	int

		返回当前已经执行了多少次查询。

	.. php:method:: last_query()

		:returns:	The last query executed
		:rtype:	string

		返回上一次执行的查询。

	.. php:method:: escape($str)

		:param	mixed	$str: The value to escape, or an array of multiple ones
		:returns:	The escaped value(s)
		:rtype:	mixed

		根据输入数据的类型进行数据转义，包括布尔值和空值。

	.. php:method:: escape_str($str[, $like = FALSE])

		:param	mixed	$str: A string value or array of multiple ones
		:param	bool	$like: Whether or not the string will be used in a LIKE condition
		:returns:	The escaped string(s)
		:rtype:	mixed

		转义字符串。

		.. warning:: 返回的字符串没有用引号引起来。

	.. php:method:: escape_like_str($str)

		:param	mixed	$str: A string value or array of multiple ones
		:returns:	The escaped string(s)
		:rtype:	mixed

		转义 LIKE 字符串。

		和 ``escape_str()`` 方法类似，但同时也对 LIKE 语句中的 ``%`` 和 ``_`` 
		通配符进行转义。

	.. php:method:: primary($table)

		:param	string	$table: Table name
		:returns:	The primary key name, FALSE if none
		:rtype:	string

		获取一个表的主键。

		.. note:: 如果数据库不支持主键检测，将假设第一列就是主键。

	.. php:method:: count_all([$table = ''])

		:param	string	$table: Table name
		:returns:	Row count for the specified table
		:rtype:	int

		返回表中的总记录数。

	.. php:method:: list_tables([$constrain_by_prefix = FALSE])

		:param	bool	$constrain_by_prefix: TRUE to match table names by the configured dbprefix
		:returns:	Array of table names or FALSE on failure
		:rtype:	array

		返回当前数据库的所有表。

	.. php:method:: table_exists($table_name)

		:param	string	$table_name: The table name
		:returns:	TRUE if that table exists, FALSE if not
		:rtype:	bool

		判断某个数据库表是否存在。

	.. php:method:: list_fields($table)

		:param	string	$table: The table name
		:returns:	Array of field names or FALSE on failure
		:rtype:	array

		返回某个表的所有字段名。

	.. php:method:: field_exists($field_name, $table_name)

		:param	string	$table_name: The table name
		:param	string	$field_name: The field name
		:returns:	TRUE if that field exists in that table, FALSE if not
		:rtype:	bool

		判断某个字段是否存在。

	.. php:method:: field_data($table)

		:param	string	$table: The table name
		:returns:	Array of field data items or FALSE on failure
		:rtype:	array

		获取某个表的所有字段信息。

	.. php:method:: escape_identifiers($item)

		:param	mixed	$item: The item or array of items to escape
		:returns:	The input item(s), escaped
		:rtype:	mixed

		对 SQL 标识符进行转义，例如列名、表名、关键字。

	.. php:method:: insert_string($table, $data)

		:param	string	$table: The target table
		:param	array	$data: An associative array of key/value pairs
		:returns:	The SQL INSERT statement, as a string
		:rtype:	string

		生成 INSERT 语句。

	.. php:method:: update_string($table, $data, $where)

		:param	string	$table: The target table
		:param	array	$data: An associative array of key/value pairs
		:param	mixed	$where: The WHERE statement conditions
		:returns:	The SQL UPDATE statement, as a string
		:rtype:	string

		生成 UPDATE 语句。

	.. php:method:: call_function($function)

		:param	string	$function: Function name
		:returns:	The function result
		:rtype:	string

		使用一种平台无关的方式执行一个原生的 PHP 函数。

	.. php:method:: cache_set_path([$path = ''])

		:param	string	$path: Path to the cache directory
		:rtype:	void

		设置缓存路径。

	.. php:method:: cache_on()

		:returns:	TRUE if caching is on, FALSE if not
		:rtype:	bool

		启用数据库结果缓存。

	.. php:method:: cache_off()

		:returns:	TRUE if caching is on, FALSE if not
		:rtype:	bool

		禁用数据库结果缓存。

	.. php:method:: cache_delete([$segment_one = ''[, $segment_two = '']])

		:param	string	$segment_one: First URI segment
		:param	string	$segment_two: Second URI segment
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		删除特定 URI 的缓存文件。

	.. php:method:: cache_delete_all()

		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		删除所有缓存文件。

	.. php:method:: close()

		:rtype:	void

		关闭数据库的连接。

	.. php:method:: display_error([$error = ''[, $swap = ''[, $native = FALSE]]])

		:param	string	$error: The error message
		:param	string	$swap: Any "swap" values
		:param	bool	$native: Whether to localize the message
		:rtype:	void
		:returns:	Displays the DB error screensends the application/views/errors/error_db.php template

		显示一个错误信息，并终止脚本执行。

		错误信息是使用 *application/views/errors/error_db.php* 文件中的模板来显示。

	.. php:method:: protect_identifiers($item[, $prefix_single = FALSE[, $protect_identifiers = NULL[, $field_exists = TRUE]]])

		:param	string	$item: The item to work with
		:param	bool	$prefix_single: Whether to apply the dbprefix even if the input item is a single identifier
		:param	bool	$protect_identifiers: Whether to quote identifiers
		:param	bool	$field_exists: Whether the supplied item contains a field name or not
		:returns:	The modified item
		:rtype:	string

		根据配置的 *dbprefix* 参数，给列名或表名（可能是表别名）添加一个前缀。

		为了处理包含路径的列名，必须要考虑一些逻辑。

		例如下面的查询::

			SELECT * FROM hostname.database.table.column AS c FROM hostname.database.table

		或者下面这个查询，使用了表别名::

			SELECT m.member_id, m.member_name FROM members AS m

		由于列名可以包含四段（主机、数据库名、表名、字段名）或者有一个表别名的前缀，
		我们需要做点工作来判断这一点，才能将 *dbprefix* 插入到正确的位置。

		该方法在查询构造器类中被广泛使用。
