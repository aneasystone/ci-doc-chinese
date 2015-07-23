################
迁移类
################

迁移是一种非常方便的途径来组织和管理你的数据库变更，当你编写了一小段 SQL
对数据库做了修改之后，你就需要告诉其他的开发者他们也需要运行这段 SQL ，
而且当你将应用程序部署到生产环境时，你还需要记得对数据库已经做了哪些修改，
需要执行哪些 SQL 。

在 CodeIgniter 中，**migration** 表记录了当前已经执行了哪些迁移，所以
你需要做的就是，修改你的应用程序文件然后调用 ``$this->migration->current()``
方法迁移到当前版本，当前版本可以在 **application/config/migration.php**
文件中进行设置。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

********************
迁移文件命令规则
********************

每个迁移都是根据文件名中的数字顺序向前或向后运行，有两种不同的数字格式：

* **序列格式：** 每个迁移文件以数字序列格式递增命名，从 **001** 开始，每个数字都需要占三位，
  序列之间不能有间隙。（这是 CodeIgniter 3.0 版本之前的命令方式）
* **时间戳格式：** 每个迁移文件以创建时间的时间戳来命名，格式为：**YYYYMMDDHHIISS** （譬如：
  **20121031100537**），这种方式可以避免在团队环境下以序列命名可能造成的冲突，而且也是
  CodeIgniter 3.0 之后版本中推荐的命名方式。

可以在 *application/config/migration.php* 文件中的 ``$config['migration_type']`` 参数设置命名规则。

无论你选择了哪种规则，将这个数字格式作为迁移文件的前缀，并在后面添加一个下划线，
再加上一个描述性的名字。如下所示：

* 001_add_blog.php (sequential numbering)
* 20121031100537_add_blog.php (timestamp numbering)

******************
创建一次迁移
******************

这里是一个新博客站点的第一次迁移的例子，所有的迁移文件位于 **application/migrations/** 目录，
并命名为这种格式：*20121031100537_add_blog.php* 。
::

	<?php

	defined('BASEPATH') OR exit('No direct script access allowed');

	class Migration_Add_blog extends CI_Migration {

		public function up()
		{
			$this->dbforge->add_field(array(
				'blog_id' => array(
					'type' => 'INT',
					'constraint' => 5,
					'unsigned' => TRUE,
					'auto_increment' => TRUE
				),
				'blog_title' => array(
					'type' => 'VARCHAR',
					'constraint' => '100',
				),
				'blog_description' => array(
					'type' => 'TEXT',
					'null' => TRUE,
				),
			));
			$this->dbforge->add_key('blog_id', TRUE);
			$this->dbforge->create_table('blog');
		}

		public function down()
		{
			$this->dbforge->drop_table('blog');
		}
	}

然后在 **application/config/migration.php** 文件中设置：``$config['migration_version'] = 20121031100537;``

*************
使用范例
*************

在这个例子中，我们在 **application/controllers/Migrate.php** 文件中添加如下的代码来更新数据库::

	<?php
	
	class Migrate extends CI_Controller
	{

		public function index()
		{
			$this->load->library('migration');

			if ($this->migration->current() === FALSE)
			{
				show_error($this->migration->error_string());
			}
		}

	}

*********************
迁移参数
*********************

下表为所有可用的迁移参数。

========================== ====================== ========================== =============================================
参数                 默认值                可选项                    描述
========================== ====================== ========================== =============================================
**migration_enabled**      FALSE                  TRUE / FALSE               启用或禁用迁移
**migration_path**         APPPATH.'migrations/'  None                       迁移目录所在位置
**migration_version**      0                      None                       当前数据库所使用版本
**migration_table**        migrations             None                       用于存储当前版本的数据库表名
**migration_auto_latest**  FALSE                  TRUE / FALSE               启用或禁用自动迁移
**migration_type**         'timestamp'            'timestamp' / 'sequential' 迁移文件的命名规则
========================== ====================== ========================== =============================================

***************
类参考
***************

.. php:class:: CI_Migration

	.. php:method:: current()

		:returns:	TRUE if no migrations are found, current version string on success, FALSE on failure
		:rtype:	mixed

		迁移至当前版本。（当前版本通过 *application/config/migration.php* 文件的 ``$config['migration_version']`` 参数设置）

	.. php:method:: error_string()

		:returns:	Error messages
		:rtype:	string

		返回迁移过程中发生的错误信息。

	.. php:method:: find_migrations()

		:returns:	An array of migration files
		:rtype:	array

		返回 **migration_path** 目录下的所有迁移文件的数组。

	.. php:method:: latest()

		:returns:	Current version string on success, FALSE on failure
		:rtype:	mixed

		这个方法和 ``current()`` 类似，但是它并不是迁移到 ``$config['migration_version']`` 参数所对应的版本，而是迁移到迁移文件中的最新版本。

	.. php:method:: version($target_version)

		:param	mixed	$target_version: Migration version to process
		:returns:	TRUE if no migrations are found, current version string on success, FALSE on failure
		:rtype:	mixed

		迁移到特定版本（回退或升级都可以），这个方法和 ``current()`` 类似，但是忽略 ``$config['migration_version']`` 参数，而是迁移到用户指定版本。
		::

			$this->migration->version(5);
