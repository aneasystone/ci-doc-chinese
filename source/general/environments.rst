##############################
处理多环境
##############################

开发者常常希望当系统运行在开发环境或生产环境中时能有不同的行为，
譬如，在开发环境如果程序能输出详细的错误信息将非常有用，但是在
生产环境这将造成一些安全问题。

ENVIRONMENT 常量
========================

CodeIgniter 默认使用 ``$_SERVER['CI_ENV']`` 的值作为 ENVIRONMENT 常量，
如果 $_SERVER['CI_ENV'] 的值没有设置，则设置为 'development'。在 index.php
文件的顶部，你可以看到::

	define('ENVIRONMENT', isset($_SERVER['CI_ENV']) ? $_SERVER['CI_ENV'] : 'development');

$_SERVER['CI_ENV'] 的值可以在 .htaccess 文件或 Apache 的配置文件中
使用 `SetEnv <https://httpd.apache.org/docs/2.2/mod/mod_env.html#setenv>`_
命令进行设置，Nginx 或其他 Web 服务器也有类似的设置方法。
或者你可以直接删掉这个逻辑，根据服务器的 IP 地址来设置该常量。

使用这个常量，除了会影响到一些基本的框架行为外（见下一节），
你还可以在开发过程中使用它来区分当前运行的是什么环境。

对默认框架行为的影响
=====================================

CodeIgniter 系统中有几个地方用到了 ENVIRONMENT 常量。这一节将描述
它对框架行为有哪些影响。

错误报告
---------------

如果将 ENVIRONMENT 常量设置为 'development' ，当发生 PHP 
错误时错误信息会显示到浏览器上。与之相对的，如果将常量设置为
'production' 错误输出则会被禁用。在生产环境禁用错误输出是个
:doc:`不错的安全实践 <security>`。

配置文件
-------------------

另外，CodeIgniter 还可以根据不同的环境加载不同的配置文件，
这在处理譬如不同环境下有着不同的 API key 的情况时相当有用。
这在 :doc:`配置类 <../libraries/config>` 文档中的“环境”这一节
有着更详细的介绍。
