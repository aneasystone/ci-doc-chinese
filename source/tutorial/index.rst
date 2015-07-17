################
教程 - 内容提要
################

通过由浅入深，前后连贯的章节安排，本教程将向你介绍 CodeIgniter
框架基础以及MVC程序架构的基本理论。

在这篇教程中，你将创建一个 **简单新闻系统**。首先，你将学会：
如何编写代码让程序能够加载静态内容页面；紧接着，你将学会：
从数据库中读取新闻条目并将其显示出来；最后，你将学会：
通过创建表单将新闻条目添加进数据库中。

教程将主要介绍以下技术要点：

-  模型-视图-控制器（Model-View-Controller）基础知识
-  URI 路由基础
-  表单验证
-  使用 "查询构造器" 执行基本的数据库操作

整个教程会被分拆成多个章节，每个章节会介绍涉及到 CodeIgniter
的不同功能点。具体章节安排如下：

-  内容提要（本页面）：介绍教程将覆盖的内容要点。
-  :doc:`加载静态内容 <static_pages>`：此节主要介绍控制器（Controllers），
   视图（Views）和路由（Routing）的基础知识。
-  :doc:`读取新闻条目 <news_section>`：此节开始介绍模型（Models）的相关知识，
   以及在模型中执行一些基本的数据库操作。
-  :doc:`创建新闻条目 <create_news_items>`：此节主要介绍在 CodeIgniter 
   中执行高级数据库操作，以及表单验证的相关知识。
-  :doc:`结束语 <conclusion>`：总结整个教程，给出深入学习 CodeIgniter
   框架的一些建议和参考资源链接。

开始你的 CodeIgniter 框架探索之旅吧。

.. toctree::
	:glob:
	:hidden:
	:titlesonly:
	
	static_pages
	news_section
	create_news_items
	conclusion