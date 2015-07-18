##########################
管理你的应用程序
##########################

默认情况下，CodeIgniter 假设你只有一个应用程序，被放置在
*application/* 目录下。但是，你完全可以拥有多个程序并让
它们共享一份 CodeIgniter 。你甚至也可以对你的应用程序目录
改名，或将其移到其他的位置。

重命名应用程序目录
==================================

如果你想重命名应用程序目录，你只需在重命名之后打开 index.php
文件将 ``$application_folder`` 变量改成新的名字::

	$application_folder = 'application';

移动应用程序目录
=====================================

你可以将你的应用程序目录移动到除 Web 根目录之外的其他位置，
移到之后你需要打开 index.php 文件将 ``$application_folder``
变量改成新的位置（使用**绝对路径**）::

	$application_folder = '/path/to/your/application';

在一个 CodeIgniter 下运行多个应用程序
===============================================================

如果你希望在一个 CodeIgniter 下管理多个不同的应用程序，只需简单的
将 application 目录下的所有文件放置到每个应用程序独立的子目录下即可。

譬如，你要创建两个应用程序："foo" 和 "bar"，你可以像下面这样组织你的目录结构::

	applications/foo/
	applications/foo/config/
	applications/foo/controllers/
	applications/foo/libraries/
	applications/foo/models/
	applications/foo/views/
	applications/bar/
	applications/bar/config/
	applications/bar/controllers/
	applications/bar/libraries/
	applications/bar/models/
	applications/bar/views/

要选择使用某个应用程序时，你需要打开 index.php 文件然后设置 ``$application_folder``
变量。譬如，选择使用 "foo" 这个应用，你可以这样::

	$application_folder = 'applications/foo';

.. note:: 你的每一个应用程序都需要一个它自己的 index.php 文件来调用它，
	你可以随便对 index.php 文件进行命名。