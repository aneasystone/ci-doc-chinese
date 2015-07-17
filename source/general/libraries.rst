###########################
使用 CodeIgniter 类库
###########################

所有的系统类库都位于 *system/libraries/* 目录下，大多数情况下，在使用之前，
你要先在 :doc:`控制器 <controllers>` 中初始化它，使用下面的方法::

	$this->load->library('class_name');

'class_name' 是你想要调用的类库名称，譬如，要加载 :doc:`表单验证类库 
<../libraries/form_validation>`，你可以这样做::

	$this->load->library('form_validation');

一旦类库被载入，你就可以根据该类库的用户指南中介绍的方法去使用它了。

另外，多个类库可以通过一个数组来同时加载。

譬如::

	$this->load->library(array('email', 'table'));

创建你自己的类库
===========================

请阅读用户指南中关于 :doc:`创建你自己的类库 <creating_libraries>` 部分。