################
创建驱动器
################

驱动器目录及文件结构
===================================

下面是驱动器目录和文件结构布局的简单例子:

-  /application/libraries/Driver_name

   -  Driver_name.php
   -  drivers

      -  Driver_name_subclass_1.php
      -  Driver_name_subclass_2.php
      -  Driver_name_subclass_3.php

.. note:: 为了在大小写敏感的文件系统下保证兼容性，Driver_name 目录必须以 
	``ucfirst()`` 函数返回的结果格式进行命名。

.. note:: 由于驱动器的架构是子驱动器并不继承主驱动器，因此在子驱动器里
	无法访问主驱动器中的属性或方法。

译者补充
-------------------------------------------------

鉴于这篇文档并没有详细介绍什么是驱动器（driver），驱动器的用途，以及如何
创建驱动器，下面列出一些外部资源供参考：

 - `Usage of drivers in CodeIgniter <http://sysmagazine.com/posts/132494/>`_
 - `Guide to CodeIgniter Drivers <http://tominator.comper.sk/2011/01/guide-to-codeigniter-drivers/>`_
 - `Codeigniter Drivers Tutorial <http://www.kevinphillips.co.nz/news/codeigniter-drivers-tutorial/>`_