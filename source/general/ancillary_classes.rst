##########################
创建附属类
##########################

有些时候，你可能想在你的控制器之外新建一些类，但同时又希望
这些类还能访问 CodeIgniter 的资源。下面你会看到，这其实很简单。

get_instance()
==============

.. php:function:: get_instance()

	:returns:	Reference to your controller's instance
	:rtype:	CI_Controller

任何在你的控制器方法中初始化的类都可以简单的通过 ``get_instance()``
函数来访问 CodeIgniter 资源。这个函数返回一个 CodeIgniter 对象。

通常来说，调用 CodeIgniter 的方法需要使用 ``$this`` ::

	$this->load->helper('url');
	$this->load->library('session');
	$this->config->item('base_url');
	// etc.

但是 ``$this`` 只能在你的控制器、模型或视图中使用，如果你想在
你自己的类中使用 CodeIgniter 类，你可以像下面这样做：

首先，将 CodeIgniter 对象赋值给一个变量::

	$CI =& get_instance();

一旦你把 CodeIgniter 对象赋值给一个变量之后，你就可以使用这个变量
来 *代替* ``$this`` ::

	$CI =& get_instance();

	$CI->load->helper('url');
	$CI->load->library('session');
	$CI->config->item('base_url');
	// etc.

如果你在类中使用``get_instance()`` 函数，最好的方法是将它赋值给
一个属性 ，这样你就不用在每个方法里都调用 ``get_instance()`` 了。

例如::

	class Example {

		protected $CI;

		// We'll use a constructor, as you can't directly call a function
		// from a property definition.
		public function __construct()
		{
			// Assign the CodeIgniter super-object
			$this->CI =& get_instance();
		}

		public function foo()
		{
			$this->CI->load->helper('url');
			redirect();
		}

		public function bar()
		{
			$this->CI->config->item('base_url');
		}
	}

在上面的例子中， ``foo()`` 和 ``bar()`` 方法在初始化 Example 
类之后都可以正常工作，而不需要在每个方法里都调用 ``get_instance()`` 函数。
