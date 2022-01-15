title: php的一些记录
categories: php
tags: php
date: 2015-05-29 11:35:20
---

### 使用技巧
1. 超级全局变量：$_SERVER
   'DOCUMENT_ROOT': 文档根目录
2. 单引号和双引号区别——变量会被解析，一般用单引号，在mysql语句中用双引号。在HTML的时候，用单引号
3. header('Location: ' . $_SERVER['HTTP_REFERER']);
4. 數組操作，見[php的數組操作](/2015/06/07/php的數組操作)
5. 使用路径时，尽量使用'/'(正斜线), 可以在Windows和UNIX上运行，而'\'(反斜线)只能在Windows上运行
6. [cURL的使用]('http://www.cnblogs.com/languoliang/archive/2013/03/13/php_curl.html')
### 基本语法
1. 所有 php 的脚本都是以 <?php 开始，以 ?> 结束，编译器根据这两个标签来判断 php 程序。

2. php直接使用变量，不需要经过声明，所以使用的时候，尽可能使用不同的变量名

3. 封装：直接require once".php"，就可以使用里面的函数或者变量

4. 数组实际上是一个有序映射，可以是各种结构。key可以是integer或者string（会被强制类型转换）。value可以是任意类型。重复键名使用最后一个。

5. 输出语句：
	5.1 var_dump($array)
	可以输出变量的类型和值
	5.2 
	``` bash
	echo($string); //输出字符串。
	echo json_encode(array('status' => $result)); //在网站传递数据中传递json格式
	```
	5.3 
	``` bash
	print_r($array); //输出数组内容
	```
注：print比echo速度要慢
6. PHP中全局变量在函数中使用时必须声明为 global。
	```php
	<?php
	$a = 1;  /* global scope */
	function Test()
	{
	    echo  $a ;  /* reference to local scope variable */
	}
	Test ();
	//无输出

	$a  =  1 ;
	include  'b.inc' ;
	//这里变量 $a 将会在包含文件 b.inc 中生效。

	$a  =  1 ;
	$b  =  2 ;
	function  Sum ()
	{
	    global  $a ,  $b ;
	     $b  =  $a  +  $b ;
	}
	Sum ();
	echo  $b ;
	?> 
	//有输出
	```

7. 设置常量, 最好用`define('CONST_VAR', var);`

8. foreach
	```php
	foreach (array_expression_r_r as $value)
	  statement 
	//遍历给定的array_expression_r_r 数组。每次循环中，当前单元的值被赋给 $value 并且数组内部的指针向前移一步（因此下一次循环中将会得到下一个单元）。
	foreach (array_expression_r_r as $key => $value)
	  statement
	//同上，只除了当前单元的键值也会在每次循环中被赋给变量 $key。 
	注1: 当 foreach 开始执行时，数组内部的指针会自动指向第一个单元。
	注2：foreach只能遍历数组，而不能改变数组
	```
9. **trim()**这个函数作用是去除字符串首尾的空格，有时候用户误在头尾多敲了空格，如果不去除的话就会出现关键字无法匹配，比如“你好 ”如果不去掉尾部空格就无法匹配关键字“你好”。
	**substr(string, start, [end]);** 字符串截取
	**str_replace(tag, replace, $a_tag);** 字符串替换

10. **implode()**函数的使用方法为，implode(分隔符，数组)，将数组内元素转化成字符串，并将分隔符插入元素之间

11. **explode()**函数的使用方法为，explode(分隔符，字符串 [，最大块数])，与implode相反，将字符串按分隔符分割成一个个小块，返回一个数组

12. **printf(string format [, mixed data, ...])**跟C语言的是类似的。另外对于转换说明，有如下格式: `%[ 'padding_character][-][width][.precision]type` 
	* 参数padding_character是可选的。它将被用来填充变量直至所指定的宽度。该参数的作用就像使用计算器那样在数字前面加零。默认的填充字符是一个空格，如果指定了一个空格或0，就不需要使用"'"作为前缀。对于任何其他填充字符，必须指定"'"作为前缀。
	* 字符"-"是可选的。它指明该域中的数据应该左对齐，而不是默认的右对齐。
	* 参数width告诉printf()函数在这里为将被替换的变量留下多少空间（按字符计算）。
	* 参数precision表示必须是以一个小数点开始。它指明了小数点后面要显示的位数。
	* 转换说明的最后一部分是一个类型码。
	* 可以使用带序号的参数方式。`%2\$.2f`表示使用列表中的第二个参数替换。

13.
			/*
			从外部直接访问属性是糟糕的想法。
			可以通过__get()和__set()来实现对属性的访问。
			并不会直接访问这些函数。
			而是依旧使用 $class->attribute来访问。
			但是变成了使用一段代码来访问特定的属性。
			*/
			public function __set($name, $value)
			{
				$this->$name = $value;
			}

11. 注释：包含大量内容的 web 应用程序会影响加载时间（比如文本、链接、图像和脚本等等）。如果您不希望在内部链接页面，请使用外部文件：
```bash
<a href="externalfile.html">转到外部页面</a>
```
12. **isset($v1)**判断v1是否赋值
	**empty($v1)**判断v1是否为0，空串，false,和null
	注：用if判断变量的时候，只要变量有值且不为0，空串，false,和null，就会认为是对的
	**gettype(mixed var)**返回变量的类型

13. 保护应用：
		- 一切表单提交的数据都要验证
		- 使用一些php的内置函数，比如strip_tags(), 删除所有的HTML标记
		- 尽可能的限制输入表单的内容

注：
	教材参考《Head First PHP&MySQL》、《PHP和MySQL Web开发 第四版》
	最后一章关于如何订阅RSS和拉取YOUTUBE的视频
