#########################
安装说明
#########################

通过下面四步来安装 CodeIgniter：

#. 解压缩安装包；
#. 将 CodeIgniter 文件夹及里面的文件上传到服务器，通常 index.php
   文件将位于网站的根目录；
#. 使用文本编辑器打开 application/config/config.php 文件设置你网站的
   根 URL，如果你想使用加密或会话，在这里设置上你的加密密钥；
#. 如果你打算使用数据库，打开 application/config/database.php 文件
   设置数据库参数。

如果你想通过隐藏 CodeIgniter 的文件位置来增加安全性，你可以将 system
和 application 目录修改为其他的名字，然后打开主目录下的 index.php 文件
将 $system_path 和 $application_folder 两个变量设置为你修改的名字。

为了达到更好的安全性，system 和 application 目录都应该放置在 Web 
根目录之外，这样它们就不能通过浏览器直接访问。CodeIgniter 
默认在每个目录下都包含了一个 .htaccess 文件，用于阻止直接访问，
但是最好还是将它们移出能公开访问的地方，防止出现 Web 服务器配置
更改或者 .htaccess 文件不被支持这些情况。

如果你想让 views 目录保持公开，也可以将你的 views 目录移出 application 
目录。

移动完目录之后，打开 index.php 文件，分别设置好 $system_path、
$application_folder 和 $view_folder 三个变量的值，最好设置成绝对路径，
譬如：'/www/MyUser/system' 。

在生产环境还要额外再多一步，就是禁用 PHP 错误报告以及所有其他仅在
开发环境使用的功能。在 CodeIgniter 中，可以通过设置 ENVIRONMENT
常量来做到这一点，这在 :doc:`安全 <../general/security>` 
这篇指南中有着更详细的介绍。

以上就是全部安装过程！

如果你刚刚接触 CodeIgniter，请阅读用户指南的 :doc`开始 <../overview/getting_started>` 
部分，学习如何构造动态的 PHP 应用，开始享受吧！

.. toctree::
	:hidden:
	:titlesonly:

	downloads
	self
	upgrading
	troubleshooting

