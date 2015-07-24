#################################
编写 CodeIgniter 的文档
#################################

CodeIgniter 使用 Sphinx 来生成多种不同格式的文档，并采用 reStructuredText 语法来编写。
如果你熟悉 Markdown 或 Textile ，你会很快上手 reStructuredText 。我们的目标是可读性
以及对用户的友好性，尽管是非常技术性的文档，但读它的永远是人类！

每一页都应该包含该页的一个目录，就像下面这样。它是通过下面的代码自动创建的：

::

	.. contents::
		:local:

	.. raw:: html

  	<div class="custom-index container"></div>

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

其中的 <div> 标签采用了原始的 HTML 语法，它是文档中的一个占位符，用于 JavaScript
动态的添加当前页面上的任何方法和函数。

**************
所需工具
**************

要生成 HTML、ePub、PDF 等等这些格式的文档，你需要先安装 Sphinx 和 Sphinx 的 phpdomain
扩展，并确保你已经安装了 Python 。然后安装 CI Lexer for Pygments ，它可以正确的高亮
页面中的代码。

.. code-block:: bash

	easy_install "sphinx==1.2.3"
	easy_install sphinxcontrib-phpdomain

然后按照 :samp:`cilexer` 目录下的 README 文件的提示，来安装 CI Lexer 。

*****************************************
页面标题、小节标题 和 子标题
*****************************************

在一个页面中标题可以用于对内容进行排序，将内容分成章节，而且还可以用于自动生成
页面目录以及整个文档的目录。可以使用特定的某些字符作为下划线来表示标题，主标题
（例如页面标题和小节标题）还需要使用上划线，其他的标题只需要使用下划线即可。
层次结构如下::

	# 页面标题（带上划线）
	* 小节标题（带上划线）
	= 子标题
	- 子子标题
	^ 子子子标题
	" 子子子子标题 (!)

使用 :download:`TextMate ELDocs Bundle <./ELDocs.tmbundle.zip>` 可以用下面这些 tab 
快捷键快速创建这些标题::

	title->

		##########
		Page Title
		##########

	sec->

		*************
		Major Section
		*************

	sub->

		Subsection
		==========

	sss->

		SubSubSection
		-------------

	ssss->

		SubSubSubSection
		^^^^^^^^^^^^^^^^

	sssss->

		SubSubSubSubSection (!)
		"""""""""""""""""""""""

********************
为方法编写文档
********************

当你为其他开发者编写类或方法的文档时，Sphinx 提供了一些指令可以帮你简单快速的完成。
例如，看下面的 ReST 语法：

.. code-block:: rst

	.. php:class:: Some_class

		.. php:method:: some_method ( $foo [, $bar [, $bat]])

			This function will perform some action. The ``$bar`` array must contain
			a something and something else, and along with ``$bat`` is an optional
			parameter.

			:param int $foo: the foo id to do something in
			:param mixed $bar: A data array that must contain a something and something else
			:param bool $bat: whether or not to do something
			:returns: FALSE on failure, TRUE if successful
			:rtype: bool

			::

				$this->load->library('some_class');

				$bar = array(
					'something'		=> 'Here is this parameter!',
					'something_else'	=> 42
				);

				$bat = $this->some_class->should_do_something();

				if ($this->some_class->some_method(4, $bar, $bat) === FALSE)
				{
					show_error('An Error Occurred Doing Some Method');
				}

			.. note:: Here is something that you should be aware of when using some_method().
					For real.

			See also :meth:`Some_class::should_do_something`


		.. php:method:: should_do_something()

			:returns: Whether or not something should be done
			:rtype: bool


它生成的文档如下所示：

.. php:class:: Some_class


	.. php:method:: some_method ( $foo [, $bar [, $bat]])

		This function will perform some action. The ``$bar`` array must contain
		a something and something else, and along with ``$bat`` is an optional
		parameter.

		:param int $foo: the foo id to do something in
		:param mixed $bar: A data array that must contain a something and something else
		:param bool $bat: whether or not to do something
		:returns: FALSE on failure, TRUE if successful
		:rtype: bool

		::

			$this->load->library('some_class');

			$bar = array(
				'something'		=> 'Here is this parameter!',
				'something_else'	=> 42
			);

			$bat = $this->some_class->should_do_something();

			if ($this->some_class->some_method(4, $bar, $bat) === FALSE)
			{
				show_error('An Error Occurred Doing Some Method');
			}

		.. note:: Here is something that you should be aware of when using some_method().
				For real.

		See also :meth:`Some_class::should_do_something`


	.. php:method:: should_do_something()

		:returns: Whether or not something should be done
		:rtype: bool