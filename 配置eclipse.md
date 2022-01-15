title: 配置eclipse
tags: JAVA eclipse
date: 2016-05-12 12:35:20
---
Eclipse版本Version: Mars.2 Release (4.5.2)
来源于自己的移动硬盘

第一步，先配置JAVA环境。下载JDK或者JRE。我是用的JRE因为比JDK小很多。(一定要下载最新版的，因为是向下兼容。如果拿到别人的代码，版本不对就会各种问题。我就是下了1.7的版本，结果没发现别人的是1.8的环境，搞出了各种问题。换了1.8之后，就什么问题也没有了)。安装完之后，就要配环境变量。首先增加一个JAVA_HOME的变量名，变量值是你安装JRE的路径，(比如我的是C:\Program Files (x86)\Java\jre1.8.0_91)，一般的默认值都是在C盘的Program Files中。然后在path变量中，增加```%JAVA_HOME%\bin;```完成之后，在cmd中输入JAVA，如果弹出一堆说明，说明JAVA环境配置成功。
第二步，安装eclipse。(如果需要汉化的，http://www.eclipse.org/babel/downloads.php, 在这里选择对应版本，进行语言包的下载，如果需要多国语言，就全部下载了，如果不需要，可以点击红框位置，选择对应版本
![](/images/eclipse_1.png)
![](/images/eclipse_2.jpg)
进行下载。下载之后解压出文件夹后，在eclipse中，点击help->install new software->add->local->解压的文件夹)
第三步，去下载一个tomcat，然后进行对应的配置。
未完待续。。。
