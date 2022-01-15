title: php的數組操作
categories: php
tags: [php, 数组]
date: 2015-06-07 11:35:20
description: 关于PHP数组的操作
list_number: false
---


#### 1. 合并数组
**array_merge()** 函数将数组合并到一起，返回一个联合的数组。所得到的数组以第一个输入数组参数开始，按后面数组参数出现的顺序依次追加。其形式为：
`array array_merge (array array1, array2…, arrayN)`
这个函数将一个或多个数组的单元合并起来，一个数组中的值附加在前一个数组的后面。返回作为结果的数组。
```php
如果输入的数组中有相同的字符串键名，则该键名后面的值将覆盖前一个值。
$a1=array("a"=>"Horse","b"=>"Dog");
$a2=array("c"=>"Cow","b"=>"Cat");
print_r(array_merge($a1,$a2));
// output
// Array ([a] => Horse, [b] => Cat, [c] => Cow)
如果数组包含数字键名，后面的值将不会覆盖原来的值，而是附加到后面。
$a1=array("1"=>"Horse","2"=>"Dog");
$a2=array("3"=>"Cow","2"=>"Cat");
print_r(array_merge($a1,$a2));
// output
// Array ([0] => Horse, [1] => Dog, [2] => Cow, [3] => Cat)
$a1=array("a"=>"Horse","2"=>"Dog");
$a2=array("c"=>"Cow","2"=>"Cat");
print_r(array_merge($a1,$a2));
// output
// Array ([a] => Horse, [0] => Dog, [c] => Cow, [1] => Cat)
如果只给了一个数组并且该数组是数字索引的，则键名会以连续方式重新索引。
$a=array(3=>"Horse",4=>"Dog");
print_r(array_merge($a));
// output
// Array ([0] => Horse, [1] => Dog )
```

#### 2. 追加数组
函数**array_merge_recursive()**与**array_merge()**相同，可以将两个或多个数组合并在一起，形成一个联合的数组．两者之间的区别在于，当某个输入数组中的某个键己经存在于结果数组中时该函数会采取不同的处理方式。
1.array_merge()会覆盖前面存在的键/值对，替换为当前输入数组中的键/值对
2.array_merge_recursive()将把两个值合并在一起，形成一个新的数组，并以原有的键作为数组名
```php
$fruit1 = array("apple" => "red", "banana" => "yellow");
$fruit2 = array("pear" => "yellow", "apple" => "green");
$result1 = array_merge_recursive($fruit1, $fruit2);
$result2 = array_merge($fruit1, $fruit2);
print_r($result1);
print_r($result2);
// output
// Array ([apple] => Array ( [0] => red [1] => green ), [banana] => yellow, [pear] => yellow)
// 现在键 apple 指向一个数组，这个数组由两个颜色值组成的索引数组。
// Array ([apple] => green, [banana] => yellow, [pear] => yellow)
```

#### 3. 连接数组
**array_combine()** 函数会得到一个新数组，它由一组提交的键和对应的值组成。其形式为：
` array array_combine(array keys,array values) `
注意，两个输入数组必须 **大小相同** ，不能为空。示例如下：
```php
$name = array("apple", "banana", "orange");
$color = array("red", "yellow", "orange");
$fruit = array_combine($name, $color);
print_r($fruit);
// output
// Array ([apple] => red, [banana] => yellow, [orange] => orange)
```

#### 4. 拆分数组
**array_slice()** 函数将返回数组中的一部分，从键offset开始，到offset+length位置结束。其形式：
`array array_slice (array array, int offset[,int length])`
如果offset 为正值，拆分从距数组开头的offset 位置开始；
如果offset 为负值，拆分从距数组末尾的offset 位置开始。
如果省略了可选参数length，则拆分将从offset 开始，一直到数组的最后一个元素。
如果给出了length 且为正数，则会在距数组开头的offset+length 位置结束。考虑一个例子：
    ```
    $fruits = array("Apple", "Banana", "Orange", "Pear", "Grape", "Lemon", "Watermelon");
    $subset = array_slice($fruits, 3);
    print_r($subset);
    // output
    // Array ( [0] => Pear [1] => Grape [2] => Lemon [3] => Watermelon )
    ```
 如果给出了length且为负数，则在距数组开头的count(input_array)-|length|位置结束。
    ```
    $fruits = array("Apple", "Banana", "Orange", "Pear", "Grape", "Lemon", "Watermelon");
    $subset = array_slice($fruits, 2, -2);
    print_r($subset);
    // output
    // Array ( [0] => Orange [1] => Pear [2] => Grape )
    ```

#### 5. 接合数组
**array_splice()** 函数会删除数组中从offset开始到 offset+length 结束的所有元素，并以数组的形式返回所删除的元素。其形式为：
`array array_splice ( array array , int offset[,length[,array replacement]] )`
offset 为正值时，接合将从距数组开头的offset 位置开始，
offset 为负值时，接合将从距数组末尾的offset 位置开始。
如果忽略可选的length 参数，则从offset 位置开始到数组结束之间的所有元素都将被删除。
如果给出了length且为正值，则接合将在距数组开头的offset + length 位置结束。
如果给出了length且为负值，则结合将在距数组开头的count(input_array)-length的位置结束。实例如下：
```php
$fruits = array("Apple", "Banana", "Orange", "Pear", "Grape", "Lemon", "Watermelon");
$subset = array_splice($fruits, 4);
print_r($fruits);
print_r($subset);
// output
// Array ( [0] => Apple, [1] => Banana, [2] => Orange, [3] => Pear )
// Array ( [0] => Grape, [1] => Lemon, [2] => Watermelon )
```
 可以使用可选参数replacement来指定取代目标部分的数组。实例如下：
```php
$fruits = array("Apple", "Banana", "Orange", "Pear", "Grape", "Lemon", "Watermelon");
$subset = array_splice($fruits, 2, -1, array("Green Apple", "Red Apple"));
print_r($fruits);
print_r($subset);
// output
// Array ( [0] => Apple, [1] => Banana, [2] => Green Apple, [3] => Red Apple, [4] => Watermelon )
// Array ( [0] => Orange, [1] => Pear, [2] => Grape, [3] => Lemon )
```

#### 6. 数组的交集
**array_intersect()** 函数返回一个保留了键的数组，这个数组只由第一个数组中出现的, 且在其他每个输入数组中 **都** 出现的值组成。只有在两个元素相等且具有相同的数据类型时，array\_intersect()函数才会认为它们是相同的。其形式如下：
`array array_intersect(array array1,array array2[,arrayN…])`
下面这个例子将返回在$fruit1数组中出现的且在$fruit2和$fruit3中也出现的所有的水果：
```php
$fruit1 = array("Apple","Banana","Orange");
$fruit2 = array("Pear","Apple","Grape");
$fruit3 = array("Watermelon","Orange","Apple");
$intersection = array_intersect($fruit1, $fruit2, $fruit3);
print_r($intersection);
// output
// Array ( [0] => Apple )
```
**array_intersect_assoc()** 函数与array_intersect()基本相同，只不过它在比较中还考虑了数组的键。因此，只有在第一个数组中出现，且在所有其他输入数组中也出现的键/值对才返回到结果数组中。
`array array_intersect_assoc(array array1,array array2[,arrayN…])`
下面的例子返回了出现在$fruit1数组中，也同时出现在$fruit2与$fruit3中的所有键/值对：
```php
$fruit1 = array("red"=>"Apple","yellow"=>"Banana","orange"=>"Orange");
$fruit2 = array("yellow"=>"Pear","red"=>"Apple","purple"=>"Grape");
$fruit3 = array("green"=>"Watermelon","orange"=>"Orange","red"=>"Apple");
$intersection = array_intersect_assoc($fruit1, $fruit2, $fruit3);
print_r($intersection);
// output
// Array ( [red] => Apple )
```

#### 7. 数组的差集

函数 **array_diff()** 返回出现在第一个数组中但其他输入数组中没有的值。这个功能与array\_intersect()相反。
`array array_diff(array array1,array array2[,arrayN…])`
实例如下：
```php
$fruit1 = array("Apple","Banana","Orange");
$fruit2 = array("Pear","Apple","Grape");
$fruit3 = array("Watermelon","Orange","Apple");
$intersection = array_diff($fruit1, $fruit2, $fruit3);
print_r($intersection);
// output
// Array ( [1] => Banana )
```

函数 **array_diff_assoc()** 与array_diff()基本相同，只是它在比较时还考虑了数组的键。因此，只在第一个数组中出现而不再其他输入数组中出现的键/值对才会返回到结果数组中。其形式如下：
`array array_diff_assoc(array array1,array array2[,arrayN…])`

下面的例子只返回了[yellow] => Banana,因为这个特殊的键/值对出现在$fruit1中，而在$fruit2和$fruit3中都不存在。
```php
$fruit1 = array("red"=>"Apple","yellow"=>"Banana","orange"=>"Orange");
$fruit2 = array("yellow"=>"Pear","red"=>"Apple");
$fruit3 = array("green"=>"Banana","orange"=>"Orange");
$intersection = array_diff_assoc($fruit1, $fruit2, $fruit3);
print_r($intersection);
// output
// Array ( [yellow] => Banana )
```
<br/>
使用数组的过程中经常要遍历数组。通常需要遍历数组并获得各个键或值（或者同时获得键和值），所以毫不奇怪，PHP为此提供了一些函数来满足需求。许多函数能完成两项任务，不仅能获取当前指针位置的键或值，还能将指针移向下一个适当的位置。
#### 8. 获取当前数组键
**key()** 函数返回input_array中当前指针所在位置的键。其形式如下：
`mixed key(array array)`
下面的例子通过迭代处理数组并移动指针来输出$fruits数组的键：
```php
$fruits = array("apple"=>"red", "banana"=>"yellow");
while ($key = key($fruits)) {
    printf("%s <br />", $key);
    next($fruits);
}
// apple
// banana
```
注意，每次调用key()时不会移动指针。为此需要使用`next()`函数，这个函数的唯一作用就是完成推进指针的任务。

#### 9. 获取当前数组值
**current()** 函数返回数组中当前指针所在位置的数组值。其形式如下：
`mixed current(array array)`
下面修改前面的例子，这一次我们要获取数组值：
```php
$fruits = array("apple"=>"red", "banana"=>"yellow");
while ($fruit = current($fruits)) {
    printf("%s <br />", $fruit);
    next($fruits);
}
// red
// yellow
```
#### 10. 获取当前数组键和值
**each()** 函数返回input_array的当前键/值对，**并将指针推进一个位置**。其形式如下：
`array each(array array)`
返回的数组包含四个键，键0和key包含键名，而键1和value包含相应的数据。如果执行each()前指针位于数组末尾，则返回false。
**注**：所以，使用完each，记得要用reset()，将数组指针复位
```php
$fruits = array("apple", "banana", "orange", "pear");
print_r ( each($fruits) );
// Array ( [1] => apple [value] => apple [0] => 0 [key] => 0 )
```
each() 经常和 list() 结合使用来遍历数组。本例与上例类似，不过循环输出了整个数组：
```php
$fruits = array("apple", "banana", "orange", "pear");
while (list($key, $val) = each($fruits))
    {
        echo "$key => $val<br />";
    }
// 0 => apple
// 1 => banana
// 2 => orange
// 3 => pear
reset($fruits); //把数组的内部指针指向第一个元素，并返回这个元素的值
while ($element = each($fruits)) {
    echo $element['key'];
    echo '-';
    echo $element['value'];
    echo '<br />';
}
// 0-apple
// 1-banana
// 2-orange
// 3-pear
```
因为将一个数组赋值给另一个数组时会重置原来的数组指针，因此在上例中如果我们在循环内部将 $fruits 赋给了另一个变量的话将会导致无限循环。
这就完成了数组的遍历。
<br/>
查找、筛选与搜索数组元素是数组操作的一些常见功能。下面来介绍一下几个相关的函数。
#### 11. 在数组中搜索特定的值
**in_array()** 函数在一个数组汇总搜索一个特定值，如果找到这个值返回true，否则返回false。
`boolean in_array(mixed needle,array haystack[,boolean strict]);`
来看下面的例子，查找变量apple是否已经在数组中，如果在，则输出一段信息：
```php
$fruit = "apple";
$fruits = array("apple","banana","orange","pear");
if( in_array($fruit,$fruits) )
    echo "$fruit 已经在数组中";
```
第三个参数可选，它强制in_array()在搜索时考虑类型。

**array_search()** 函数在一个数组中搜索一个指定的值，如果找到则返回相应的键，否则返回false。其形式如下：
`mixed array_search(mixed needle,array haystack[,boolean strict])`
下面的例子在$fruits中搜索一个特定的日期（December 7），如果找到，则返回相应州的有关信息：
```php
$fruits["apple"] = "red";
$fruits["banana"] = "yellow";
$fruits["watermelon"]="green";
$founded = array_search("green", $fruits);
if($founded)
    printf("%s was founded on %s.",$founded, $fruits[$founded]);
//watermelon was founded on green.
```
#### 12. 在数组中搜索特定的键
**array_key_exists()** 函数如果在一个数组中找到一个指定的键，返回true，否则返回false。其形式如下：
`boolean array_key_exists(mixed key,array array);`
下面的例子将在数组键中搜索apple，如果找到，将输出这个水果的颜色：
```php
$fruit["apple"] = "red";
$fruit["banana"] = "yellow";
$fruit["pear"] = "green";
if(array_key_exists("apple", $fruit)){
    printf("apple's color is %s",$fruit["apple"]);
}
//apple's color is red
```
**array_keys()** 函数返回一个数组，其中包含所搜索数组中找到的所有键。其形式如下：
`array array_keys(array array[,mixed search_value])`
如果包含可选参数search_value，则只会返回与该值匹配的键。下面的例子将输出$fruit数组中找到的所有数组：
```php
$fruits["apple"] = "red";
$fruits["banana"] = "yellow";
$fruits["watermelon"]="green";
$keys = array_keys($fruits);
$key = array_keys($fruits, "red");
print_r($keys);
print_r($key);
//Array ( [0] => apple [1] => banana [2] => watermelon )
//Array ( [0] => apple )
```
#### 13. 返回数组所有的值
**array_values()** 函数返回一个数组中的所有值，并自动为返回的数组提供数值索引。其形式如下：
`array array_values(array array)`
下面的例子将获取$fruits中找到的各元素的值：
```php
$fruits["apple"] = "red";
$fruits["banana"] = "yellow";
$fruits["watermelon"]="green";
$values = array_values($fruits);
print_r($values);
//Array ( [0] => red [1] => yellow [2] => green )
```
<br/>
有时候我们需要扩展一个数组，或者删掉数组的一部分，PHP为扩展和缩小数组提供了一些函数。对于那些希望模仿各种队列实现（FIFO、LIFO）的程序员来说，这些函数可以提供便利。顾名思义，从这些函数的函数名（push、pop、shift和unshift）就清楚地反映出其作用。
PS：传统的队列是一种数据结构，删除元素与加入元素的顺序相同，就称为先进先出，或FIFO。相反，栈是另外一种数据结构，其中删除元素的顺序与加入时的顺序相反，这成为后进先出，或LIFO。
#### 14. 在数组头添加元素
**array_unshift()** 函数在数组头添加元素。所有己有的数值键都会相应地修改，以反映其在数组中的新位置，但是关联键不受影响。其形式如下：
`int array_unshift(array array,mixed variable[,mixed variable])`
下面这个例子在$fruits数组前面添加了两种水果：
```php
$fruits = array("apple","banana");
array_unshift($fruits,"orange","pear")
// $fruits = array("orange","pear","apple","banana");
```
#### 15. 在数组尾添加元素
**array_push()** 函数的返回值是int型，是压入数据后数组中元素的个数，可以为此函数传递多个变量作为参数，同时向数组压入多个变量。其形式为：
`int array_push(array array,mixed variable [,mixed variable...])`
下面这个例子在$fruits数组中又添加了两个水果：
```php
$fruits = array("apple","banana");
array_push($fruits,"orange","pear")
//$fruits = array("apple","banana","orange","pear")
```
#### 16. 从数组头删除值
**array_shift()** 函数删除并返回数组中找到的元素。其结果是，如果使用的是数值健，则所有相应的值都会下移，而使用关联键的数组不受影响。其形式为
`mixed array_shift(array array)`
下面的例子删除了$fruits数组中的第一个元素apple：
```php
$fruits = array("apple","banana","orange","pear");
$fruit = array_shift($fruits);
// $fruits = array("banana","orange","pear")
// $fruit = "apple";
```
#### 17. 从数组尾删除元素
**array_pop()** 函数删除并返回数组的最后一个元素。其形式为：
`mixed array_pop(aray target_array);  `
下面的例子从$states数组删除了最后的一个州：
```php
$fruits = array("apple","banana","orange","pear");
$fruit = array_pop($fruits);
//$fruits = array("apple","banana","orange");
//$fruit = "pear";
```
### 18. 数组排序
**sort(array, sorttype)** 对数组进行排序。默认都是从小到大的排序。(**注**: 所有的大写字母都在小写字母前面，即Z小于a)。
对于，第二个参数：
SORT_REGULAR - 默认。以它们原来的类型进行处理（不改变类型）。
SORT_NUMERIC - 把值作为数字来处理
SORT_STRING - 把值作为字符串来处理
**asort()**根据数组的每个元素值进行排序。
**ksort()**根据数组的每个关键字进行排序。
####用户自定义排序：
**usort(array, compare)**第一个参数是要比较的数组，第二个参数是比较的函数。（可用于二维数组排序）
例子:
```php
    $products = array(array('TIR', 'Tires', 100),
                     array('OIL', 'Oil', 10),
                     array('SPK', 'Spark Plugs', 4));
    function compare($x, $y) {
      if ($x[2] == $y[2]) {
        return 0;
      } elseif ($x[2] < $y[2]) {
        return -1;
      } else {
        return 1;
      }
    }
    usort($products, 'compare');
    //按照价格进行排序

    $products = array(array('Code'=>'TIR', 'Description'=>'Tires', 'pride'=>100),
                     array('Code'=>'OIL', 'Description'=>'Oil', 'pride'=>10),
                     array('Code'=>'SPK', 'Description'=>'Spark Plugs', 'pride'=>4));
    function compare($x, $y) {
      if ($x['pride'] == $y['pride']) {
        return 0;
      } elseif ($x['pride'] < $y['pride']) {
        return -1;
      } else {
        return 1;
      }
    }
    usort($products, 'compare');
    //按照价格进行排序
```
**uasort()**和**uksort()**则是用于非数字索引数组的排序。其余类似与**usort()**
**shuffle(array)**对数组进行随机排序

###19. 对数组中的每一个元素应用任何函数
**array_walk(array arr, string func, [mixed userdata])** 第一个是要处理的数组，第二个是用户自定义并作用于数组每个元素的函数，第三个是可选的，传一个参数给自己的函数。
例子：

    function my_print($value) {
      echo "$value<br />";
    }
    array_walk($array, 'my_print');

    function my_multiply(&$value, $key, $factor) {
      $value *= $factor;
    }
    array_walk(&$array, 'my_multiply', 3);

###20. 统计数组的基数集
**array_count_values()** 函数用于统计数组中每个特定的值出现的次数，返回一个关联数组。数组的关键字是原数组中的所有值，对应的数值是关键字出现的次数。

