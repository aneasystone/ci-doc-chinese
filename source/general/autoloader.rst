######################
自动加载资源
######################

CodeIgniter 的"自动加载"特性可以允许系统每次运行时自动初始化类库、辅助函数和模型。
如果你需要在整个应用程序中全局使用某些资源，为方便起见可以考虑自动加载它们。

支持自动加载的有下面这些：

-  *libraries/* 目录下的核心类
-  *helpers/* 目录下的辅助函数
-  *config/* 目录下的用户自定义配置文件
-  *system/language/* 目录下的语言文件
-  *models/* 目录下的模型类

要实现自动加载资源，你可以打开 **application/config/autoload.php** 文件，然后将
你需要自动加载的项添加到 autoload 数组中。你可以在该文件中的每种类型的 autoload 
数组的注释中找到相应的提示。

.. note:: 添加 autoload 数组时不用包含文件扩展名（.php）

另外，如果你想让 CodeIgniter 使用 `Composer <https://getcomposer.org/>`_ 的自动加载，
只需将 **application/config/config.php** 配置文件中的 ``$config['composer_autoload']`` 
设置为 ``TRUE`` 或者设置为你自定义的路径。