title: 使用GitHub和Hexo写博客
date: 2015-05-29 11:20:30
tags: [Hexo, 博客, Github]
description: 使用HEXO3.1.1来搭建博客的详细步骤，和一些问题的解决方法
toc: false
---

注意我**安装的HEXO版本是3.1.1**，2.x.x和3.x.x安装的稍有不同

**注**：以下教程搬运__cnFeat__[《如何搭建一个独立博客——简明Github Pages与Hexo教程》](http://cnfeat.com/2014/05/10/2014-05-11-how-to-build-a-blog/)的。
##为什么选择GitHub Pages？
很多人用wordpress，你为什么要用github pages来搭建？
* 1、github pages有300M免费空间，资料自己管理，保存可靠；
* 2、学着用github，享受github的便利，上面有很多大牛，眼界会开阔很多；
* 3、顺便看看github工作原理，最好的团队协作流程；
* 4、github是趋势；
* 5、你不觉得一个文科生用github很geek吗？瞬间跻身技术界；
* 6、就算github被墙了，我可以搬到国内的gitcafe中去。
![](/images/bg2012082502.jpg "git hub")

##GitHub Pages是什么？
GitHub Pages本用于介绍托管在GitHub的项目， 不过，由于他的空间免费稳定，用来做搭建一个博客再好不过了。
>GitHub Pages可以被认为是用户编写的、托管在github上的静态网页。

![](/images/1.png "git hub")

##安装准备软件
依次下载安装。
* 1、[Node.js](https://nodejs.org/)
* 2、[Git](http://git-scm.com/)

顺带去[注册一个GitHub帐号](https://github.com/ "注册Git Hub")
##怎么打开Git？
* 1、开始菜单Git Bash。
 ![](/images/9.jpg)
* 2、鼠标右键打开Git Bash。
 ![](/images/10.jpg)

##配置SSH keys
我们如何让本地git项目与远程的github建立联系呢？用SSH keys。
###检查SSH keys的设置
首先我们需要检查你电脑上现有的ssh key:

	$ cd ~/.ssh 检查本机的ssh密钥

如果提示：No such file or directory 说明你是第一次使用git。
###生成新的SSH Key：
	$ ssh-keygen -t rsa -C "邮件地址@youremail.com"
	Generating public/private rsa key pair.
	Enter file in which to save the key (/Users/your_user_directory/.ssh/id_rsa):<回车就好> 

**注意1:** 此处的邮箱地址，你可以输入自己的邮箱地址；**注意2:** 此处的「-C」的是大写的「C」
然后系统会要你输入密码：

	Enter passphrase (empty for no passphrase):<输入加密串>
	Enter same passphrase again:<再次输入加密串>
在回车中会提示你输入一个密码，这个密码会在你提交项目时使用，如果为空的话提交项目时则不用输入。这个设置是防止别人往你的项目里提交内容。
**注意：**输入密码的时候没有*字样的，你直接输入就可以了。
最后看到这样的界面，就成功设置ssh key了：
	![](/images/11.png)
###添加SSH Key到GitHub
在本机设置SSH Key之后，需要添加到GitHub上，以完成SSH链接的设置。
* 1、打开本地C:\Documents and Settings\Administrator.ssh\id_rsa.pub文件。此文件里面内容为刚才生成的密钥。如果看不到这个文件，你需要设置显示隐藏文件。准确的复制这个文件的内容，才能保证设置的成功。
* 2、登陆github系统。点击右上角的 Settings—->SSH keys —-> Add SSH key
![](/images/12.png "Account Settings") ![](/images/13.png "SSH Public")  ![](/images/14.jpg "Add SSH key")
* 3、把你本地生成的密钥复制到里面（key文本框中）， 点击 add key 就ok了
![](/images/15.jpg "密钥")

###测试
可以输入下面的命令，看看设置是否成功，git@github.com的部分**不要修改**：

	$ ssh -T git@github.com
如果是下面的反馈: 

	The authenticity of host 'github.com (207.97.227.239)' can't be established.
	RSA key fingerprint is 16:27:ac:a5:76:28:2d:36:63:1b:56:4d:eb:df:a6:48.
	Are you sure you want to continue connecting (yes/no)?
不要紧张，输入yes就好，然后会看到：
	
	Hi cnfeat! You've successfully authenticated, but GitHub does not provide shell access.
###设置用户信息
现在你已经可以通过SSH链接到GitHub了，还有一些个人信息需要完善的。

Git会根据用户的名字和邮箱来记录提交。GitHub也是用这些信息来做权限的处理，输入下面的代码进行个人信息的设置，把名称和邮箱替换成你自己的，名字必须是你的真名，而不是GitHub的昵称。

	$ git config --global user.name "cnfeat"//用户名
	$ git config --global user.email  "cnfeat@gmail.com"//填写自己的邮箱
###SSH Key配置成功
本机已成功连接到github。
若有问题，请重新设置。常见错误请参考：
[GitHub Help - Generating SSH Keys](http://help.github.com/articles/generating-ssh-keys)
[GitHub Help - Error Permission denied (publickey)](http://help.github.com/articles/error-permission-denied-publickey)
##使用GitHub Pages建立博客
与GitHub建立好链接之后，就可以方便的使用它提供的Pages服务，GitHub Pages分两种，一种是你的GitHub用户名建立的username.github.io这样的用户&组织页（站），另一种是依附项目的pages。
想建立个人博客是用的第一种，形如cnfeat.github.io这样的可访问的站，每个用户名下面只能建立一个。
###github上建立仓库
登录后系统，在github首页，点击页面右下角「New Repository」
![](/images/16.jpg "New Repository")
**注**：Github Pages的Repository名字是特定的，比如我Github账号是taichushouwang，那么我Github Pages Repository名字就是taichushouwang.github.io。(帐号就是图中红色框中/前面那个)
点击「Create Repository」 完成创建。

##安装[HEXO](http://hexo.io/docs/)
**注**: 以下教程搬运__Forsweet__[《用Hexo 3 搭建github blog》](http://forsweet.github.io/hexo/%E7%94%A8Hexo%E6%90%AD%E5%BB%BAGithub%E5%8D%9A%E5%AE%A2/)

前置准备完成后, 打开[git bash](#怎么打开Git？), 输入
	
	$ npm install -g hexo-cli
>在OS X上面安装完毕之运行hexo时可能会提示这样的错误:
>
	[SyntaxError: Unexpected token ILLEGAL]
	{ [Error: Cannot find module './build/default/DTraceProviderBindings'] code: 'MODULE_NOT_FOUND' }
	{ [Error: Cannot find module './build/Debug/DTraceProviderBindings'] code: 'MODULE_NOT_FOUND' }
安装时,可以用npm install hexo --no-optional来解决
详情参见[issues1055](https://github.com/hexojs/hexo/issues/1055)

##部署HEXO
安装完成之后运行如下命令

	$ hexo init <folder>
	$ cd <folder>
	$ npm install
* <folder>为要存放Hexo的位置（以后你写的博客也都存在这）
* 若npm install 安装过程卡住不动
	修改 npm 的安装目录下的 npmrc文件 增加一条 registry=http://registry.cnpmjs.org, 即输入
	
		$ npm config set registry http://registry.cnpmjs.org
* 出现 "error network npm getaddrinfo ENOTFOUND"
	将 C:\Users\用户名\文件夹下的 .npmrc文件删掉，然后重新运行即可

在Hexo3中, hexo-server独立出来了,如要本地调试,需要先安装

	$ npm install hexo-server --save
其余独立出来的还包括下面这些,特别是**hexo-deployer-git**这个模块,不安装的话后面在运行hexo deploy时会提示找不到git,参见这个[issues1040](https://github.com/hexojs/hexo/issues/1040).
详细的[Hexo2to3](https://github.com/hexojs/hexo/wiki/Migrating-from-2.x-to-3.0)信息

	npm install hexo-generator-index --save
	npm install hexo-generator-archive --save
	npm install hexo-generator-category --save
	npm install hexo-generator-tag --save
	npm install hexo-server --save
	npm install hexo-deployer-git --save
	npm install hexo-renderer-marked@0.2 --save
	npm install hexo-renderer-stylus@0.2 --save
	npm install hexo-generator-feed@1 --save
	npm install hexo-generator-sitemap@1 --save
可以到**\node_modules**目录下面看自己是不是已经安装了这些模块.