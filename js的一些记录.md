title: js的一些记录
categories: js
tags: js
date: 2016-09-07 17:35:20
description: 记录一些我平时写JS常用的知识点和技巧
---
###要点记录
1. window是一个对象，它包含了加载了的所有的js文件里的函数，使用`window[function]`可以调用相关函数。
2. `console.log(object)`可以查看object的相关内容。
3. 来源：[js 函数参数个数问题](http://jackielieu.blog.51cto.com/5586910/1158118)
	1、调用函数时，如果参数多于定义时的个数，则多余的参数将会被忽略，如果少于定义时的个数则缺失的参数数会被自动赋予undefined值。
	2、如果是用function语句声明的函数定义则不可以出现在循环或条件语句中，但是如果是用函数直接量方法的函数定义则可以出现在任何js表达式中。
	3、arguments对象
    函数的arguments对象就像一个数组，里面保存着函数调用时的实际参数，可以用arguments[0]、arguments[1]、arguments[2]…等来引用这些参数，即使这些参数在定义函数时是没有的。但arguments并非真正的数组对象。
	   function a(x,y){
        arguments[0]   //表示第一个参数x
        arguments[1]   //表示第一个参数y
        arguments[2]   //表示第三个参数，前提是在调用函数时传入了三个参数 
        …
        arguments.length   //表示实际传入参数的个数
        arguments.callee(x,y)   //调用自身 }
	   arguments对象具有length属性，表示的是函数调用时实际传入的参数的个数。
	   arguments对象还具有callee属性，用来引用当前正在执行的函数，这个在匿名函数中尤其有用。
	4、函数的length属性（没错，函数也是具有length属性的）
	    与arguments.length不同，函数的length属性表示的是在定义函数时的形参个数，而不是函数调用时的实际参数个数。可以用arguments.callee.length来调用函数的length属性。
4. `typeof()`, 可以用来检测变量的类型 (返回值：number, string, bool, object, function, undefined)
  检查undefined typeof(value) == "undefined"
5. 在each函数中，不能使用break和continue，用`return false`代替break，`return true`代替continue
6. NaN “Not a Number”。出现这个数值比较少见，以至于我们可以不理它。当运算无法返回正确的数值时，就会返回 "NaN" 值。NaN 值非常特殊，因为它“不是数字”，所以任何数跟它都不相等，甚至 **NaN** 本身也不等于 **NaN** 。使用`isNaN(value)`来判断value，当value是NaN, 字符串或对象时，也返回true。
7. **字符串截取**
  substr 方法 返回一个从指定位置开始的指定长度的子字符串。
  string.substr(start [, length ])
  参数
  string 必选项。要提取子字符串的字符串文字或 String 对象。
  start 必选项。所需的子字符串的起始位置。字符串中的第一个字符的索引为 0。
  length 可选项。在返回的子字符串中应包括的字符个数。
  说明
  如果 length 为 0 或负数，将返回一个空字符串。如果没有指定该参数，则子字符串将延续到 string 的最后。

  substring 方法
  返回位于 String 对象中指定位置的子字符串。
  string.substring(start[, end ])
  "String Literal".substring(start[, end ])
  参数
  start
  指明子字符串的起始位置，该索引从 0 开始起算。
  end
  指明子字符串的结束位置，该索引从 0 开始起算。
  说明
  substring 方法将返回一个包含从 start 到最后（不包含 end ）的子字符串的字符串。
  substring 方法使用 start 和 end 两者中的较小值作为子字符串的起始点。例如， strvar.substring(0, 3) 和 strvar.substring(3, 0) 将返回相同的子字符串。
  如果 start 或 end 为 NaN 或者负数，那么将其替换为0。
  子字符串的长度等于 start 和 end 之差的绝对值。例如，在 strvar.substring(0, 3) 和 strvar.substring(3, 0) 返回的子字符串的的长度是 3。
8. **indexOf**
  stringObject.indexOf(searchvalue,fromindex), 返回某个指定的字符串值在字符串中首次出现的位置。
9. **字符串替换** str.replace("s", "t");//可以使用正则表达式
  将字符串s替换成字符串t
10. JS有个Math对象，要试着去使用。
11. JS采用的是IEEE-754浮点数表示法，这是一种二进制表示法。意味着不能精确表示1/10, 1/100这样的分数。例如x = 0.3 - 0.2; y = 0.2 - 0.1; x == y(返回是false); 由于舍入误差这两个近似值，实际上并不相等。
12. **静态语言和动态语言**
  动态(类型)语言和静态(类型)语言，动态类型语言是指在运行期间才去做数据类型检查的语言，在语言编程时，永远也不用给任何变量指定数据类型，该语言会在第一次赋值给变量时，在内部将数据类型记录下来。Python、Ruby和JavaScript就是典型的动态类型语言。静态类型语言是在编译其间检查的，在写程序时要声明所有变量的类型。比如C/C++、C#和JAVA。
13. 强/弱类型是指类型检查的严格程度的。
14. 面向过程与面向对象
13. **bind()函数**（弃用，改用on）
  $(selector).bind(event, eventdata, function)
  event是事件名称，是字符串。
  eventdata是事件数据，可以用来传递参数给function
  function是回调函数
  回调函数的参数function(para), para是一个mouseEvent对象，不仅包含了传递的参数，还有一些其他的数据
  bind第二种参数形式
  $(selector).bind({event:function, event:function, ...})
  -------------------------------------------------------------------
  **on(events,[selector],[data],fn)**
  events:一个或多个用空格分隔的事件类型和可选的命名空间，如"click"或"keydown.myPlugin" 。
  selector:一个选择器字符串用于过滤器的触发事件的选择器元素的后代。如果选择的< null或省略，当它到达选定的元素，事件总是触发。
  data:当一个事件被触发时要传递event.data给事件处理函数。
  fn:该事件被触发时执行的函数。 false 值也可以做一个函数的简写，返回false。
14. 方括号[]是数组，花括号{}是对象。
15. bool类型的转换，只有以下几个才会转换为false
  undefined, null, 0(数字0), -0, NaN, ""(空字符串)
  字符串"false", "0"都会转换成true，而不是false
16. **in** 运算符。判断右侧的对象是否拥有一个名为左操作数值的属性名。
17. **避免使用with(with会创建自己的作用域，会增加作用域链长度)**
  with(object)
  statement
  用于临时拓展作用域链
  使用with会使得JS代码非常难于优化，而且运行得更慢。
  不过在对象嵌套层次很深的时候，可以使用with简化代码编写。
  比如: document.forms[0].address.value
  可以使用with语句将form对象添加至作用域链的顶层：
  ```
  with(document.forms[0]) {
  //直接访问表单元素，例如：
  name.value = "";
  address.value = "";
  email.value = "";
  }
  ```
  这个对象临时挂载在作用域链上，当JS需要解析诸如address的标识符时，就会自动在这个对象中查找。
  注：摘自犀牛书P113。

18. **逻辑与&&**
  在JS中，"&&"并不总是返回true和false。它可能会返回"真值"或者"假值"。而这些"真值"和"假值"，就是其他类型的值。真值，比如，非空字符串，非零整数，对象等。假值就是空字符串""，数字0，null，undefined，NaN。在计算"&&"的值时，先计算左侧表达式，如果表达式值为假，那么整个表达式的结果为假，那么"&&"就返回左侧操作数的值，并且不计算右操作数。如果左侧表达式为真，那么计算右侧表达式，并返回右侧表达式的结果。
  比如
  ```
  var o = {x:2};
  var p = null;
  o && o.x //输出2，因为操作数结果都为真
  p && p.x //输出null，切不会报错。因为p为假，直接输出p，不计算p.x
  ```
  **"||"**同理，但是返回的结果跟"&&"相反。如果都为真，返回第一个操作数的结果，为假，返回右操作数的结果。

19. 作用域链
  当定义一个函数时，它实际上保存一个作用域链。当调用这个函数时，它创建一个新的对象来存储它的局部变量，并将这个对象添加至保存的那个作用域链上，同时创建一个新的更长的表示函数调用作用域的“链”。摘自"犀牛书"P59-3.10.3

  别人的答案：
  当执行一段JavaScript代码(全局代码或函数)时，JavaScript引擎会创建为其创建一个作用域又称为执行上下文(Execution Context)，在页面加载后会首先创建一个全局的作用域，然后每执行一个函数，会建立一个对应的作用域，从而形成了一条作用域链。每个作用域都有一条对应的作用域链，链头是全局作用域，链尾是当前函数作用域。
  作用域链的作用是用于解析标识符，当函数被创建时(不是执行)，会将this、arguments、命名参数和该函数中的所有局部变量添加到该当前作用域中，当JavaScript需要查找变量X的时候(这个过程称为变量解析)，它首先会从作用域链中的链尾也就是当前作用域进行查找是否有X属性，如果没有找到就顺着作用域链继续查找，直到查找到链头，也就是全局作用域链，仍未找到该变量的话，就认为这段代码的作用域链上不存在x变量，并抛出一个引用错误(ReferenceError)的异常。

20. ECMAScript 5中的数组方法
  **forEach()** 遍历数组，并可以根据指定函数，修改调用数组的值
  **map()** 遍历数组，根据指定函数，返回一个新的数组
  **filter()** 遍历数组，并可以根据指定函数，筛选出需要的数值，返回新的数组，并且会跳过缺少的元素
  **every()和some()** 遍历数组，并可以根据指定函数，对数组进行判定，返回true或false，every()是所有元素，some()是存在元素
  **reduce()和reduceRight()** 遍历数组，对数组的元素进行折叠合并。
  **indexOf()和lastIndexOf()** 搜索数组中给定的值的元素，返回索引

21. **跨域**
  (1) 利用CORS跨域
  "跨域资源共享"(Cross-origin resource sharing，简称CORS)。CORS是是在XHR2中拓展的能力，使用方法很简单，在服务端设置：
  `Header set Access-Control-Allow-Origin *`
  这种设置将接受所有域名的跨域请求，也可以指定具体网址，也可以对域名进行限定：
  `Header set Access-Control-Allow-Origin http://www.test.com`
  然而，这种方式的局限性在于要求客户端支持，并且服务端进行相关设置。在这两者满足的情况下，通过CORS进行跨域不仅支持所有类型的HTTP请求，而且开发者可以使用普通的XMLHttpRequest发起请求和获得数据，比起JSONP有更好的错误处理。

  (2) JSONP
  我们知道在网页中通过 `<script>` 元素的src指定加载目标脚本时是不受同源策略的影响的,因此可以使用它们从其他服务器请求数据，这种利用 `<script>`元素作为Ajax传输的技术就称为JSONP。
  实现： 
  
  ```
    function getJSONP(url, callback){
      var funcName = getUniqueName()；//利用时间戳或指自增计数器获得唯一函数名
      url += "?callback=" + funcName; //将函数名作为参数添加至url中
      var script = document.createElement('script');//动态构建script标签
      //回调函数
      getJSONP[funcName] = function(response){
        try{
          callback(response); //处理响应数据
        }
        finally{
          //即使回调函数或响应抛出错误，清除动态增加内容
          delete getJSONP[funcName];
          script.parentNode.removeChild(scirpt);
        }
      }
      //触发HTTP请求
      script.src = url;
      document.body.appendChild(script);
    }
  ```
  JSONP也存在一些弊端，首先JSONP支持GET不支持POST方法，另外使用 <script> 元素进行Ajax请求，这意味着允许Web页面可以执行远程服务器发送过来的任何JavaScript代码，因此对于不信任的服务器，不应该采用该技术。

  (3) 利用'window.name'进行跨域
  window对象有个name属性，该属性有个特征：即在一个窗口(window)的生命周期内,窗口载入的所有的页面都是共享一个'window.name'的，每个页面对'window.name'都有读写的权限，'window.name'是持久存在一个窗口载入过的所有页面中的，并不会因新页面的载入而进行重置，因此可以借助'window.name'在不同源的页面中传递数据。
    如 www.a.com/a.html 想要获得 www.b.com/b.html 中的数据,步骤如下：
    (a) 在 b.html 中将数据存储在 window.name 中
    (b) 在 a.html 中构建一个隐藏（display：none）的iframe标签，假设id设为proxy，src设置为和a.html同源即可。
    (c) 通过如下代码在 a.html 中获取到data
      ```
      var proxy = document.getElementById('proxy');
      proxy.onload = function(){
        var data = proxy.contentWindow.name;//获取到数据
      }
      ```
    (d) 移除相关元素

  (4) 使用 window.postMessage 进行跨域
  这种方式比较简单，在a页面中利用windowObj.postMessage(message, targetOrigin)向目标页面发送信息，而在b页面中通过监听message事件获取信息。这种方式是在HTML5中新增的方法，对于IE6、IE7等老版本浏览器无法使用。
21. 在函数定义中，赋值据有最高权限，即fn=function(){}。而function fn(){}可以提升。
    例子：
    ```JavaScript
    {//1
    var m= 1, j = k = 0;
    //y = add(m);
    //console.log(y);
    add=function (n) {  return n = n + 1;  }
    add=function (n) {  return n = n + 3;  }
    z = add(m);
    console.log(z)
    }
    {//2
      var m= 1, j = k = 0;
      y = add(m);
      console.log(y);
      function add(n) {  return n = n + 1; }
      function add(n) {  return n = n + 3; }
      z = add(m);
      console.log(z)
    }
    {//3
      var m= 1, j = k = 0;
      y = add(m);
      console.log(y);
      add=function (n) {  return n = n + 1; }
      function add(n) {  return n = n + 3;  }
      z = add(m);
      console.log(z)
    }
    {//4
      var m= 1, j = k = 0;
      y = add(m);
      console.log(y);
      function add(n) {  return n = n + 1; }
      add=function(n) {  return n = n + 3; }
      z = add(m);
      console.log(z)
    }
    {//5
      var m= 1, j = k = 0;
      add=function (n) {  return n = n + 1; }
      y = add(m);
      console.log(y);
      add=function (n) {  return n = n + 3; }
      z = add(m);
      console.log(z)
    }
    {//6
      var m= 1, j = k = 0;
      function add(n) {  return n = n + 1; }
      y = add(m);
      console.log(y);
      function add(n) {  return n = n + 3; }
      z = add(m);
      console.log(z)
    }
    {//7
      var m= 1, j = k = 0;
      add=function (n) {  return n = n + 1; }
      y = add(m);
      console.log(y);
      function add(n) {  return n = n + 3;  }
      z = add(m);
      console.log(z)
    }
    {//8
      var m= 1, j = k = 0;
      function add(n) {  return n = n + 1; }
      y = add(m);
      console.log(y);
      add=function(n) {  return n = n + 3; }
      z = add(m);
      console.log(z)
    }
    ```
21. =>是es6语法中的arrow function

22. **前端模块化**
  [前端模块化, 作者:Samaritans](http://www.cnblogs.com/dolphinX/p/4381855.html)
  [JavaScript 模块化历程, 作者:吕大豹](http://www.cnblogs.com/lvdabao/p/js-modules-develop.html)

###问题
2. **getter** 和 **setter** 方法的实际作用
3. **命名空间的函数** 还不太懂实际作用
4. **闭包** 

参考文献:
1. 《JavaScript权威指南》第6版
2. [前端面试题及答案整理（一）,作者:vicfeel](http://www.cnblogs.com/vicfeel/p/5702815.html)
