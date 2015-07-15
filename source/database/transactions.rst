############
事务
############

CodeIgniter 允许你在支持事务安全的表上使用事务。在 MySQL 中，你需要将
表的存储引擎设置为 InnoDb 或 BDB，而不是通常我们使用的 MyISAM 。大多数
其他数据库平台都原生支持事务。

如果你对事务还不熟悉，我们推荐针对你正在使用的数据库，先在网上寻找一些
在线资源学习一下。下面将假设你已经明白事务的基本概念。

CodeIgniter 的事务方法
======================================

CodeIgniter 使用的事务处理方法与流行的数据库类 ADODB 的处理方法非常相似。
我们选择这种方式是因为它极大的简化了事务的处理过程。大多数情况下，你只需
编写两行代码就行了。

传统的事务处理需要实现大量的工作，你必须随时跟踪你的查询，并根据查询的成功
或失败来决定提交还是回滚。当遇到嵌套查询时将会更加麻烦。相比之下，我们实现了
一个智能的事务系统，它将自动的为你做这些工作。（你仍然可以选择手工管理你的
事务，但这实在是没啥好处）

运行事务
====================

要使用事务来运行你的查询，你可以使用 $this->db->trans_start() 和
$this->db->trans_complete() 两个方法，像下面这样::

	$this->db->trans_start();
	$this->db->query('AN SQL QUERY...');
	$this->db->query('ANOTHER QUERY...');
	$this->db->query('AND YET ANOTHER QUERY...');
	$this->db->trans_complete();

在 start 和 complete 之间，你可以运行任意多个查询，根据查询执行
成功或失败，系统将自动提交或回滚。

严格模式 （Strict Mode）
============================

CodeIgniter 默认使用严格模式运行所有的事务，在严格模式下，如果你正在
运行多组事务，只要有一组失败，所有组都会被回滚。如果禁用严格模式，那么
每一组都被视为独立的组，这意味着其中一组失败不会影响其他的组。

严格模式可以用下面的方法禁用::

	$this->db->trans_strict(FALSE);

错误处理
===============

如果你的数据库配置文件 config/database.php 中启用了错误报告（db_debug = TRUE），
当提交没有成功时，你会看到一条标准的错误信息。如果没有启用错误报告，
你可以像下面这样来管理你的错误::

	$this->db->trans_start();
	$this->db->query('AN SQL QUERY...');
	$this->db->query('ANOTHER QUERY...');
	$this->db->trans_complete();
	
	if ($this->db->trans_status() === FALSE)
	{
		// generate an error... or use the log_message() function to log your error
	}

启用事务
=====================

当执行 $this->db->trans_start() 方法时，事务将自动启用，如果
你要禁用事务，可以使用 $this->db->trans_off() 方法来实现::

	$this->db->trans_off();
	
	$this->db->trans_start();
	$this->db->query('AN SQL QUERY...');
	$this->db->trans_complete();

当事务被禁用时，你的查询会自动提交，就跟没有使用事务一样。

测试模式（Test Mode）
======================

你可以选择性的将你的事务系统设置为 “测试模式”，这将导致你的所有
查询都被回滚，就算查询成功执行也一样。要使用 “测试模式”，你只需要
将 $this->db->trans_start() 函数的第一个参数设置为 TRUE 即可::

	$this->db->trans_start(TRUE); // Query will be rolled back
	$this->db->query('AN SQL QUERY...');
	$this->db->trans_complete();

手工运行事务
=============================

如果你想手工运行事务，可以像下面这样做::

	$this->db->trans_begin();
	
	$this->db->query('AN SQL QUERY...');
	$this->db->query('ANOTHER QUERY...');
	$this->db->query('AND YET ANOTHER QUERY...');
	
	if ($this->db->trans_status() === FALSE)
	{
		$this->db->trans_rollback();
	}
	else
	{
		$this->db->trans_commit();
	}

.. note:: 手动运行事务时，请务必使用 $this->db->trans_begin() 方法，
	**而不是** $this->db->trans_start() 方法。
