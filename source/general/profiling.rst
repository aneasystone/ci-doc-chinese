##########################
程序分析
##########################

分析器类会在页面下方显示基准测试结果，运行过的 SQL 语句，
以及 $_POST 数据。这些信息有助于开发过程中的调试和优化。

初始化类
======================

.. important:: 这个类无须初始化，如果已按照下面的方式启用，
	他将被 :doc:`输出类 <../libraries/output>` 自动加载。

启用分析器
=====================

要启用分析器，你可以在你的 :doc:`控制器 <controllers>`
方法的任何位置添加一行下面的代码::

	$this->output->enable_profiler(TRUE);

当启用之后，将会生成一份报告插入到页面的最底部。

使用下面的方法禁用分析器::

	$this->output->enable_profiler(FALSE);

设置基准测试点
========================

为了让分析器编译并显示你的基准测试数据，你必须使用特定的语法
来命名基准点。

请阅读 :doc:`基准测试类 <../libraries/benchmark>` 中关于设置基准点的资料。

启用和禁用分析器中的字段
========================================

分析器中的每个字段都可以通过设置相应的控制变量为 TRUE 或 FALSE
来启用或禁用。有两种方法来实现，其中的一种方法是：
在 *application/config/profiler.php* 文件里设置全局的默认值。

例如::

	$config['config']          = FALSE;
	$config['queries']         = FALSE;

另一种方法是：在你的控制器里通过调用 :doc:`输出类 <../libraries/output>`
的 set_profiler_sections()  函数来覆盖全局设置和默认设置::

	$sections = array(
		'config'  => TRUE,
		'queries' => TRUE
	);

	$this->output->set_profiler_sections($sections);

下表列出了可用的分析器字段和用来访问这些字段的 key 。

======================= =================================================================== ========
Key                     Description                                                         Default
======================= =================================================================== ========
**benchmarks**          在各个计时点花费的时间以及总时间           TRUE
**config**              CodeIgniter 配置变量                                        TRUE
**controller_info**     被请求的控制器类和调用的方法                           TRUE
**get**                 请求中的所有 GET 数据                                  TRUE
**http_headers**        本次请求的 HTTP 头部                            TRUE
**memory_usage**        本次请求消耗的内存（单位字节）          TRUE
**post**                请求中的所有 POST 数据                                 TRUE
**queries**             列出所有执行的数据库查询，以及执行时间  TRUE
**uri_string**          本次请求的 URI                                      TRUE
**session_data**        当前会话中存储的数据                                  TRUE
**query_toggle_count**  指定显示多少个数据库查询，剩下的则默认折叠起来   25
======================= =================================================================== ========

.. note:: 在你的数据库配置文件中禁用 :doc:`save_queries </database/configuration>` 参数
	也可以禁用数据库查询相关的分析器，上面说的 'queries' 字段就没用了。
	你可以通过 ``$this->db->save_queries = TRUE;`` 来覆写该设置。
	另外，禁用这个设置也会导致你无法查看查询语句以及
	`last_query <database/helpers>` 。