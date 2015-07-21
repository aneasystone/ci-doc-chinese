#################
创建新闻条目
#################

现在你已经知道如何通过 CodeIgniter 从数据库中读取数据了，但是你还没有
向数据库中写入数据。在这一节，你将继续完善前文中创建的 News 控制器和模型，
添加上这一功能。

创建一个表单
-------------

为了向数据库中写入数据，你需要先创建一个表单用来填写要存储的信息，这意味着
你的表单里需要包含两项：一项代表标题，另一项代表内容。你可以在模型中通过代码
从标题中提取出 slug 。在文件 application/views/news/create.php 中创建一个新视图。

::

    <h2><?php echo $title; ?></h2>

    <?php echo validation_errors(); ?>

    <?php echo form_open('news/create'); ?>

        <label for="title">Title</label> 
        <input type="input" name="title" /><br />

        <label for="text">Text</label>
        <textarea name="text"></textarea><br />

        <input type="submit" name="submit" value="Create news item" /> 

    </form>

这里有两个地方你可能还不熟悉：form_open() 函数 以及 validation_errors() 函数。

第一个函数是由 :doc:`表单辅助库 <../helpers/form_helper>` 提供的，用于生成 form
元素，并添加一些额外的功能，如添加一个隐藏的 :doc:`CSRF 保护字段 <../libraries/security>` , 
第二个函数用于显示表单验证的错误信息。

回到你的 News 控制器，你将要在这里做两件事：检查表单是否被提交，以及提交的数据是否能通过验证规则。
你可以使用 :doc:`表单验证类 <../libraries/form_validation>` 来做到这一点。

::

    public function create()
    {
        $this->load->helper('form');
        $this->load->library('form_validation');
        
        $data['title'] = 'Create a news item';
        
        $this->form_validation->set_rules('title', 'Title', 'required');
        $this->form_validation->set_rules('text', 'text', 'required');
        
        if ($this->form_validation->run() === FALSE)
        {
            $this->load->view('templates/header', $data);   
            $this->load->view('news/create');
            $this->load->view('templates/footer');
            
        }
        else
        {
            $this->news_model->set_news();
            $this->load->view('news/success');
        }
    }

上面的代码添加了不少功能，前几行代码加载了 表单辅助库 和 表单验证类。然后，设置了表单验证规则，
set\_rules() 方法有三个参数：表单中字段的名称，错误信息中使用的名称，以及验证规则。在这个例子中，
规则为 title 和 text 字段是必填的。

CodeIgniter 有一个强大的表单验证类，像上面示例中那样。你可以在 :doc:`这里 <../libraries/form_validation>` 
阅读更多。

接下来，你可以看到一个判断条件检查表单验证是否成功通过，如果没有通过，将显示出表单，
如果通过了验证，则会调用模型。然后，加载视图显示出成功信息。新建一个视图文件
application/views/news/success.php 并写上成功的信息。

模型
-----

最后只剩下一件事情了，那就是写一个方法将数据保存到数据库中，你将会使用 输入类 获取用户提交的数据，
并使用 查询构造器类 向数据库中插入数据。打开之前创建的模型文件，添加以下代码：

::

    public function set_news()
    {
        $this->load->helper('url');
        
        $slug = url_title($this->input->post('title'), 'dash', TRUE);
        
        $data = array(
            'title' => $this->input->post('title'),
            'slug' => $slug,
            'text' => $this->input->post('text')
        );
        
        return $this->db->insert('news', $data);
    }

新加的这个方法用于向数据库插入数据，第三行有一个新方法 url\_title() ，
这个方法由 :doc:`URL 辅助库 <../helpers/url_helper>` 提供，用于将字符串
中的所有空格替换成连接符（-），并将所有字符转换为小写。
这样其实就生成了一个 slug ，可以很好的用于创建 URI 。

然后我们继续准备将要被插入到数据库中的记录，我们将其赋值给 $data 数组，
数组中的每一项都对应之前创建的数据库表中的一列，这里你应该看到又出现了一个新方法，
来自 :doc:`输入类 <../libraries/input>` 的 post() 方法，这个方法可以对数据进行过滤，
防止其他人的恶意攻击。输入类默认已经加载了。最后，将 $data 数组插入到我们的数据库中。

路由
-------

在你开始向 CodeIgniter 程序中添加新闻条目之前，你需要到 config/routes.php 
文件中去添加一条新的路由规则，确保你的文件中包含了下面的代码。这样可以让
CodeIgniter 知道 'create' 将作为一个方法被调用，而不是一个新闻条目的 slug 。

::

    $route['news/create'] = 'news/create';
    $route['news/(:any)'] = 'news/view/$1';
    $route['news'] = 'news';
    $route['(:any)'] = 'pages/view/$1';
    $route['default_controller'] = 'pages/view';

现在在你的浏览器中输入你安装好的 CodeIgniter 的本地开发地址，然后在 URL 
后面添加上 index.php/news/create 。恭喜你，你刚刚完成了你的第一个 CodeIgniter 
程序！添加些新闻来看看这些你创造的页面吧！
