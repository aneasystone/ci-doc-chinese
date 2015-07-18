#########################
使用 CodeIgniter 驱动器
#########################

驱动器是一种特殊类型的类库，它有一个父类和任意多个子类。子类可以访问父类，
但不能访问兄弟类。在你的 :doc:`控制器 <controllers>` 中，驱动器为你的类库提供了
一种优雅的语法，从而不用将它们拆成很多离散的类。

驱动器位于 *system/libraries/* 目录，每个驱动器都有一个独立的目录，目录名和
驱动器父类的类名一致，在该目录下还有一个子目录，命名为 drivers，用于存放
所有子类的文件。

要使用一个驱动器，你可以在控制器中使用下面的方法来进行初始化::

	$this->load->driver('class_name');

class_name 是你想要调用的驱动器类名，譬如，你要加载名为 Some_parent 的驱动器，
可以这样::

	$this->load->driver('some_parent');

然后就可以像下面这样调用该类的方法::

	$this->some_parent->some_method();

而对于那些子类，我们不用初始化，可以直接通过父类调用了::

	$this->some_parent->child_one->some_method();
	$this->some_parent->child_two->another_method();

创建你自己的驱动器
=========================

请阅读用户指南中关于如何 :doc:`创建你自己的驱动器 <creating_drivers>` 部分。
