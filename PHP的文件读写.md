title: PHP的文件读写
categories: PHP
tags: [php, 文件读写] 
date: 2015-09-07 14:59:01
description: PHP的文件读写相关函数
toc:
---

###打开文件
**resource fopen ( string $filename , string $mode [, bool $use_include_path = false [, resource $context ]] )**
$filename = 路径+文件名
$mode = **r(只读)** &nbsp; **w(只写)** &nbsp; **a(追加)**
**注**: 为移植性考虑，强烈建议在用 fopen() 打开文件时总是使用 'b' 标记。
第三个参数是可选的。如果要在include_path中搜索一个文件，就设置为true，且不用提供目录名或路径
第四个参数是可选的。fopen()函数允许文件名称以协议名称开始 (例如，http://) 并且在一个远程位置打开文件。就要设置该参数

如果fopen()成功打开一个文件，该函数将返回一个指向这个文件的文件指针。这个文件指针可以用来访问文件，进行读写。如果打开失败将返回**false**;
###[写文件](http://www.w3school.com.cn/php/func_filesystem_fwrite.asp)
**int fwrite(resource $fp, string $outputstring [, int length]);**
$fp是文件指针，$outputstring存储将要写入文件的内容，写入的最大字符数
###关闭文件
文件访问完毕之后，一定要记得**关闭文件**。
使用`fclose($fp);`成功返回true，失败返回false。
###读文件
**feof($fp)**用来判断文件指针是否已经指向了文件末尾
**fgets($fp, 999);**从文件中每次读取一行内容。直到最后一个换行字符(\n)、或者文件结束符EOF，或者读取了998B
**fgetss($fp, length [, allowable\_tags]);**过滤字符串中包含的PHP和HTML标记，包含在$allowable\_tags中用','隔开，就不会被过滤
**fgetcsv($fp, length [, string delimiter, string enclosure])**$delimiter界定符，将读到的内容进行分行。$enclosure封闭每个域的字符，默认是"(双引号)。
【不常用】**readfile($filename)**将文件读到输出缓存中，且不需要fopen
【不常用】**fpassthru($fp)**将文件读取到输出缓存，需要fopen
**file($filename)**将文件的每行作为数组的一个元素保存，返回一个数组
**fgetc($fp)**读取一个字符
**fread($fp, $length)**读取文件任意长度的字节
###其他文件函数
**file_exists($filename)**检查文件是否存在
**filesize($filename)**查看文件的大小
**unlink($filename)**删除文件，访问权限不够或者文件不存在，返回false
**rewind($fp)**将文件指针复位到文件的开始
**ftell($fp)**以字节为单位报告文件指针当前在文件中的位置
**fseek($fp, $offset [, $whence])**将文件指针从whence位置移动offset个字节，默认为文件开始处。
**flock($fp, $operation [, $wouldblock])**文件锁定。 
		
| 操作值 | 意义 |
| ------ | ---- |
| LOCK_SH | 读操作锁定 |
|	LOCK_EX | 写操作锁定 |
|	LOCK_UN | 释放已有的锁定|
|	LOCK_NB | 防止在请求加锁时发生阻塞|

  **下面这一段代码，本意是用table标签做一个表格出来的，但是，不知什么原因，使用table，会在前面加入很多个<br>标签，具体原因有待发现**
		
		<table>
			<thead>
				<tr>
					<th>操作值</th>
					<th>意义</th>
				</tr>
			</thead>
			<tbody>
				<tr>
					<td>LOCK_SH</td>
					<td>读操作锁定</td>
				</tr>
				<tr>
					<td>LOCK_EX</td>
					<td>写操作锁定</td>
				</tr>
				<tr>
					<td>LOCK_UN</td>
					<td>释放已有的锁定</td>
				</tr>
				<tr>
					<td>LOCK_NB</td>
					<td>防止在请求加锁时发生阻塞</td>
				</tr>
			</tbody>
		</table>
