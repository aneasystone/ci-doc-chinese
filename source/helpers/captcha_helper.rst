##############
验证码辅助库
##############

验证码辅助库文件包含了一些帮助你创建验证码图片的函数。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

加载辅助库
===================

该辅助库通过下面的代码加载::

	$this->load->helper('captcha');

使用验证码辅助库
========================

辅助库加载之后你可以像下面这样生成一个验证码图片::

	$vals = array(
		'word'		=> 'Random word',
		'img_path'	=> './captcha/',
		'img_url'	=> 'http://example.com/captcha/',
		'font_path'	=> './path/to/fonts/texb.ttf',
		'img_width'	=> '150',
		'img_height'	=> 30,
		'expiration'	=> 7200,
		'word_length'	=> 8,
		'font_size'	=> 16,
		'img_id'	=> 'Imageid',
		'pool'		=> '0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ',

		// White background and border, black text and red grid
		'colors'	=> array(
			'background' => array(255, 255, 255),
			'border' => array(255, 255, 255),
			'text' => array(0, 0, 0),
			'grid' => array(255, 40, 40)
		)
	);

	$cap = create_captcha($vals);
	echo $cap['image'];

-  验证码辅助函数需要使用 GD 图像库。
-  只有 **img_path** 和 **img_url** 这两个参数是必须的。
-  如果没有提供 **word** 参数，该函数将生成一个随机的 ASCII 字符串。
   你也可以使用自己的词库，从里面随机挑选。
-  如果你不设置 TRUE TYPE 字体（译者注：是主要的三种计算机矢量字体之一）的路径，将使用 GD 默认的字体。
-  "captcha" 目录必须是可写的。
-  **expiration** 参数表示验证码图片在删除之前将保留多久（单位为秒），默认保留 2 小时。
-  **word_length** 默认值为 8， **pool** 默认值为 '0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ'
-  **font_size** 默认值为 16，GD 库的字体对大小有限制，如果字体大小需要更大一点的话可以设置一种 TRUE TYPE 字体。
-  **img_id** 将会设置为验证码图片的 "id" 。
-  **colors** 数组中如果有某个颜色未设置，将使用默认颜色。

添加到数据库
-----------------

使用验证码函数是为了防止用户胡乱提交，要做到这一点，你需要将 ``create_captcha()`` 函数返回的信息保存到数据库中。
然后，等用户提交表单数据时，通过数据库中保存的数据进行验证，并确保它没有过期。

这里是数据表的一个例子::

	CREATE TABLE captcha (  
		captcha_id bigint(13) unsigned NOT NULL auto_increment,  
		captcha_time int(10) unsigned NOT NULL,  
		ip_address varchar(45) NOT NULL,  
		word varchar(20) NOT NULL,  
		PRIMARY KEY `captcha_id` (`captcha_id`),  
		KEY `word` (`word`)
	);

这里是使用数据库的示例。在显示验证码的那个页面，你的代码类似于下面这样::

	$this->load->helper('captcha');
	$vals = array(     
		'img_path'	=> './captcha/',     
		'img_url'	=> 'http://example.com/captcha/'     
	);

	$cap = create_captcha($vals);
	$data = array(     
		'captcha_time'	=> $cap['time'],     
		'ip_address'	=> $this->input->ip_address(),     
		'word'		=> $cap['word']     
	);

	$query = $this->db->insert_string('captcha', $data);
	$this->db->query($query);

	echo 'Submit the word you see below:';
	echo $cap['image'];
	echo '<input type="text" name="captcha" value="" />';

然后在处理用户提交的页面，处理如下::

	// First, delete old captchas
	$expiration = time() - 7200; // Two hour limit
	$this->db->where('captcha_time < ', $expiration)
		->delete('captcha');

	// Then see if a captcha exists:
	$sql = 'SELECT COUNT(*) AS count FROM captcha WHERE word = ? AND ip_address = ? AND captcha_time > ?';
	$binds = array($_POST['captcha'], $this->input->ip_address(), $expiration);
	$query = $this->db->query($sql, $binds);
	$row = $query->row();

	if ($row->count == 0)
	{     
		echo 'You must submit the word that appears in the image.';
	}

可用函数
===================

该辅助库有下列可用函数：

.. php:function:: create_captcha([$data = ''[, $img_path = ''[, $img_url = ''[, $font_path = '']]]])

	:param	array	$data: Array of data for the CAPTCHA
	:param	string	$img_path: Path to create the image in
	:param	string	$img_url: URL to the CAPTCHA image folder
	:param	string	$font_path: Server path to font
	:returns:	array('word' => $word, 'time' => $now, 'image' => $img)
	:rtype:	array

	根据你提供的一系列参数生成一张验证码图片，返回包含此图片信息的数组。

	::

		array(
			'image'	=> IMAGE TAG
			'time'	=> TIMESTAMP (in microtime)
			'word'	=> CAPTCHA WORD
		)

	**image** 就是一个 image 标签::

		<img src="http://example.com/captcha/12345.jpg" width="140" height="50" />

	**time** 是一个毫秒级的时间戳，作为图片的文件名（不带扩展名）。就像这样：1139612155.3422

	**word** 是验证码图片中的文字，如果在函数的参数中没有指定 word 参数，这将是一个随机字符串。