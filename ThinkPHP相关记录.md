title: ThinkPHP相关记录
categories: php
tags: php thinkphp
date: 2016-04-06 09:35:20
---

###命名规范
1. 类文件都是以.class.php为后缀（这里是指的ThinkPHP内部使用的类库文件，不代表外部加载的类库文件），使用驼峰法命名，并且首字母大写，例如 DbMysql.class.php；
2. 类的命名空间地址和所在的路径地址一致，例如 Home\Controller\UserController类所在的路径应该是 Application/Home/Controller/UserController.class.php；
3. **确保文件的命名和调用大小写一致** ，是由于在类Unix系统上面，对大小写是敏感的（而ThinkPHP在调试模式下面，即使在Windows平台也会严格检查大小写）；
4. 类名和文件名一致（包括上面说的大小写一致），例如 UserController类的文件命名是UserController.class.php， InfoModel类的文件名是InfoModel.class.php， 并且不同的类库的类命名有一定的规范；
5. 函数、配置文件等其他类库文件之外的一般是以.php为后缀（第三方引入的不做要求）；
6. 函数的命名使用 **小写字母** 和 **下划线** 的方式，例如 get_client_ip；
7. 函数的命名使用小写字母和下划线的方式，例如 get_client_ip；
8. 方法的命名使用驼峰法，并且首字母小写或者使用下划线“_”，例如 getUserName，_parseType，通常下划线开头的方法属于私有方法；
**函数和方法的区别是什么？？？**
9. 属性的命名使用驼峰法，并且首字母小写或者使用下划线“_”，例如 tableName、_instance，通常下划线开头的属性属于私有属性；
10. 以双下划线“__”打头的函数或方法作为魔法方法，例如 __call 和 __autoload；
11. 常量以大写字母和下划线命名，例如 HAS_ONE和 MANY_TO_MANY；
12. 配置参数以大写字母和下划线命名，例如HTML_CACHE_ON；
13. 语言变量以大写字母和下划线命名，例如MY_LANG，以下划线打头的语言变量通常用于系统语言变量，例如 _CLASS_NOT_EXIST_；
14. 对变量的命名没有强制的规范，可以根据团队规范来进行；
15. ThinkPHP的模板文件默认是以.html 为后缀（可以通过配置修改）；
16. 数据表和字段采用小写加下划线方式命名，并注意字段名不要以下划线开头，例如 think_user 表和 user_name字段是正确写法，类似 _username 这样的数据表字段可能会被过滤。
17. **特例：** 在ThinkPHP里面，有一个函数命名的特例，就是单字母大写函数，这类函数通常是某些操作的快捷定义，或者有特殊的作用。例如：A、D、S、L 方法等等，他们有着特殊的含义，后面会有所了解。
18. **注意：** 由于ThinkPHP默认全部使用UTF-8编码，所以请确保你的程序文件采用UTF-8编码格式保存，并且去掉BOM信息头（去掉BOM头信息有很多方式，不同的编辑器都有设置方法，也可以用工具进行统一检测和处理），否则可能导致很多意想不到的问题。

###其他要点
1. 配置加载
  1. 配置文件加载顺序：(后面的配置会覆盖之前的同名配置)
    __惯例配置__（位于ThinkPHP/Conf/convention.php）
    ->__应用配置__（默认位于Application/Common/Conf/config.php）
    ->__模式配置__（如果使用了普通应用模式之外的应用模式的话，还可以为应用模式（后面会有描述）单独定义配置文件，文件命名规范是： Application/Common/Conf/config_应用模式名称.php（仅在运行该模式下面才会加载））
    ->__调试配置__（如果开启调试模式的话，则会自动加载框架的调试配置文件（位于ThinkPHP/Conf/debug.php）和应用调试配置文件（位于Application/Common/Conf/debug.php））
    ->__状态配置__（每个应用都可以在不同的情况下设置自己的状态（或者称之为应用场景），并且加载不同的配置文件。
    举个例子，你需要在公司和家里分别设置不同的数据库测试环境。那么可以这样处理，在公司环境中，我们在入口文件中定义：
    `define('APP_STATUS','office');`
    那么就会自动加载该状态对应的配置文件（位于Application/Common/Conf/office.php）。
    如果我们回家后，我们修改定义为：
    `define('APP_STATUS','home');`
    那么就会自动加载该状态对应的配置文件（位于Application/Common/Conf/home.php）。）
    ->__模块配置__（每个模块会自动加载自己的配置文件（位于Application/当前模块名/Conf/config.php））
    ->__扩展配置__
    ->__动态配置__
  2. 
2. Application和Home下的Common区别是什么, 一个叫公共模块，一个叫公共函数
  Common模块是一个特殊的模块，是应用的公共模块，访问所有的模块之前都会首先加载公共模块下面的配置文件（Conf/config.php）和公共函数文件（Common/function.php）。但Common模块本身不能通过URL直接访问，公共模块的其他文件则可以被其他模块继承或者调用。
3. URL模式
  ThinkPHP支持的URL模式有四种：普通模式、PATHINFO、REWRITE和兼容模式，可以设置URL_MODEL参数改变URL模式。
  而且可以重写规则，去掉URL中的index.php，但是目前没效果(2016.4.6-10:35)
4. `function C()`//读取或设置配置
   `function D()`//数据模型的实例化操作, 需要定义模型类
   `function M()`//数据模型的实例化操作, 无具体模型类
  单个大写字母函数？？？？
5. **命名空间** 
  3.2版本全面采用命名空间方式定义和加载类库文件，有效的解决多个模块之间的冲突问题，并且实现了更加高效的类库自动加载机制。
  命名空间的概念必须了解，否则会成为3.2版本开发的重大障碍。
6. **路由** 系统会按定义的顺序依次匹配路由规则，一旦匹配到的话，就会定位到路由定义中的控制器和操作方法去执行（可以传入其他的参数），并且后面的规则不会继续匹配。
7. **伪静态** 可以通过设置`URL_HTML_SUFFIX`参数随意在URL的最后增加你想要的静态后缀，而不会影响当前操作的正常执行。
  可以设置禁止访问的URL后缀，例如：
  ```'URL_DENY_SUFFIX' => 'pdf|ico|png|gif|jpg', // URL禁止访问的后缀设置
8. [数据的输入](http://document.thinkphp.cn/manual_3_2/input_var.html)
9. **模型类** 模型类的作用大多数情况是操作数据表的，如果按照系统的规范来命名模型类的话，大多数情况下是可以自动对应数据表。
  **命名** 模型类的命名规则是除去表前缀的数据表名称，采用驼峰法命名，并且首字母大写，然后加上模型层的名称（默认定义是Model），例如：`UserModel think_user`
10. 

参考来源：[ThinkPHP3.2完全开发手册](http://document.thinkphp.cn/manual_3_2.html)
