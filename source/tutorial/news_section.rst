############
读取新闻条目
############

在上一节中，我们通过写出一个包含静态页面的类了解了一些框架的基本概念，
我们也根据自定义路由规则来重定向 URI 。现在是时候向大家介绍动态内容
和如何使用数据库了。

创建你的数据模型
---------------------

数据库的查询操作应该放在模型里，而不是写在控制器里，这样可以很方便的重用它。
模型正是用于从数据库或者其他存储中获取、新增、更新数据的地方。它就代表你的数据。

打开 *application/models/* 目录，新建一个文件 *News_model.php* ，然后写入下面的代码。
确保你的 :doc:`数据库配置 <../database/configuration>` 正确。

::

	<?php
	class News_model extends CI_Model {

		public function __construct()
		{
			$this->load->database();
		}
	}

这个代码和之前的控制器的代码有点类似，它通过继承 ``CI_Model`` 创建了一个新模型，
并加载了数据库类。数据库类可以通过 ``$this->db`` 对象访问。

在查询数据库之前，我们要先创建一个数据库表。连接你的数据库，运行下面的 SQL 语句
（MySQL），并添加一些测试数据。

::

	CREATE TABLE news (
		id int(11) NOT NULL AUTO_INCREMENT,
		title varchar(128) NOT NULL,
		slug varchar(128) NOT NULL,
		text text NOT NULL,
		PRIMARY KEY (id),
		KEY slug (slug)
	);

现在，数据库和模型都准备好了，你需要一个方法来从数据库中获取所有的新闻文章。
为实现这点，我们使用了 CodeIgniter 的数据库抽象层 :doc:`查询构造器 <../database/query_builder>` ，
通过它你可以编写你的查询代码，并在 :doc:`所有支持的数据库平台 <../general/requirements>` 上运行。
向你的模型中添加如下代码。

::

	public function get_news($slug = FALSE)
	{
		if ($slug === FALSE)
		{
			$query = $this->db->get('news');
			return $query->result_array();
		}

		$query = $this->db->get_where('news', array('slug' => $slug));
		return $query->row_array();
	}

通过这个代码，你可以执行两种不同的查询，一种是获取所有的新闻条目，另一种
是根据它的 `slug <#>`_ 来获取新闻条目。你应该注意到，``$slug`` 变量在执行查询之前
并没有做检查， :doc:`查询构造器 <../database/query_builder>` 会自动帮你检查的。

显示新闻
----------------

现在，查询已经写好了，接下来我们需要将模型绑定到视图上，向用户显示新闻条目了。
这可以在之前写的 ``Pages`` 控制器里来做，但为了更清楚的阐述，我们定义了一个新的 
``News`` 控制器，创建在 *application/controllers/News.php* 文件中。

::

	<?php
	class News extends CI_Controller {

		public function __construct()
		{
			parent::__construct();
			$this->load->model('news_model');
			$this->load->helper('url_helper');
		}

		public function index()
		{
			$data['news'] = $this->news_model->get_news();
		}

		public function view($slug = NULL)
		{
			$data['news_item'] = $this->news_model->get_news($slug);
		}
	}

阅读上面的代码你会发现，这和之前写的代码有些相似之处。首先是 ``__construct()`` 
方法，它调用父类（``CI_Controller``）中的构造函数，并加载模型。这样模型就可以
在这个控制器的其他方法中使用了。另外它还加载了 :doc:`URL 辅助库 <../helpers/url_helper>` ，
因为我们在后面的视图中会用到它。

其次，有两个方法用来显示新闻条目，一个显示所有的，另一个显示特定的。
你可以看到第二个方法中调用模型方法时传入了 ``$slug`` 参数，模型根据这个 slug 
返回特定的新闻条目。

现在，通过模型，控制器已经获取到数据了，但还没有显示。下一步要做的就是，
将数据传递给视图。

::

	public function index()
	{
		$data['news'] = $this->news_model->get_news();
		$data['title'] = 'News archive';

		$this->load->view('templates/header', $data);
		$this->load->view('news/index', $data);
		$this->load->view('templates/footer');
	}

上面的代码从模型中获取所有的新闻条目，并赋值给一个变量，另外页面的标题赋值给了
``$data['title']`` 元素，然后所有的数据被传递给视图。现在你需要创建一个视图文件来
显示新闻条目了，新建 *application/views/news/index.php* 文件并添加如下代码。

::

	<h2><?php echo $title; ?></h2>
	
	<?php foreach ($news as $news_item): ?>

		<h3><?php echo $news_item['title']; ?></h3>
		<div class="main">
			<?php echo $news_item['text']; ?>
		</div>
		<p><a href="<?php echo site_url('news/'.$news_item['slug']); ?>">View article</a></p>

	<?php endforeach; ?>

这里，通过一个循环将所有的新闻条目显示给用户，你可以看到我们在 HTML 模板中混用了 PHP ，
如果你希望使用一种模板语言，你可以使用 CodeIgniter 的 :doc:`模板解析类 <../libraries/parser>` ，
或其他的第三方解析器。

新闻的列表页就做好了，但是还缺了显示特定新闻条目的页面，之前创建的模型可以很容易的
实现该功能，你只需要向控制器中添加一些代码，然后再新建一个视图就可以了。回到 ``News``
控制器，使用下面的代码替换掉 ``view()`` 方法：

::

	public function view($slug = NULL)
	{
		$data['news_item'] = $this->news_model->get_news($slug);

		if (empty($data['news_item']))
		{
			show_404();
		}

		$data['title'] = $data['news_item']['title'];

		$this->load->view('templates/header', $data);
		$this->load->view('news/view', $data);
		$this->load->view('templates/footer');
	}

我们并没有直接调用 ``get_news()`` 方法，而是传入了一个 ``$slug`` 参数，
所以它会返回相应的新闻条目。最后剩下的事是创建视图文件
*application/views/news/view.php* 并添加如下代码 。

::

	<?php
	echo '<h2>'.$news_item['title'].'</h2>';
	echo $news_item['text'];

路由
-------

由于之前创建的通配符路由规则，你需要新增一条路由来显示你刚刚创建的控制器，
修改你的路由配置文件（*application/config/routes.php*）添加类似下面的代码。
该规则可以让请求访问 ``News`` 控制器而不是 ``Pages`` 控制器，第一行可以让
带 slug 的 URI 重定向到 ``News`` 控制器的 ``view()`` 方法。

::

	$route['news/(:any)'] = 'news/view/$1';
	$route['news'] = 'news';
	$route['(:any)'] = 'pages/view/$1';
	$route['default_controller'] = 'pages/view';

把浏览器的地址改回根目录，在后面加上 index.php/news 来看看你的新闻页面吧。
