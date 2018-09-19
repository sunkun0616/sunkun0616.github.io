---
title: hexo+github搭建属于自己的博客
date: 2017-09-18 16:25:54
tags: 
- hexo
---
这两天一直在弄hexo+github搭建博客，遇到好多坑，真是从一个坑跳到另一坑里。但现在总算是搭好了一个，也算是给这两天的自己一点小小的安慰吧！！废话不多说，接下来，我再把流程锊一遍。

<!--more-->


把需要的环境配置好：
--

* 安装node(必须)-作用是生成静态页面以及安装一些依赖包
* 安装GIT(必须)-作用是把本地的hexo内容提交到github上
* 申请github账户(必须)-作用是用来做博客的远程仓库，域名，服务器之类等

*注：github账号申请后，有一个ssh需要配置，不配置也可以，每次提交都得输入账号密码，看自己的喜好吧*


正式在本地安装:
--

1.首先在电脑本地随便建个文件夹，比如在d盘下建个文件夹，起名为blog，然后用cmd命令进入blog目录：

	cd blog

2.进入以后用命令安装hexo：

	npm install -g hexo

3.接着初始化hexo，输入命令：

	hexo init

这个时候基本全部的安装工作已经完成，以后关于博客的所有操作都在这个目录下。

4.生成静态页面，输入命令：

	hexo generate (直接输入 hexo g 也可以)

5.在本地启动，输入命令：

	hexo server

然后在浏览器里输入http://localhost:4000就可以访问到在本地装好的个人博客。

配置github:
--

1.建立仓库Repository

仓库名必须为：_your _ user _ name.github.io_ 【固定写法】

我起的名字是：sunkun0616.github.io

2.建立与本地博客项目的关联：

在根目录下(也就是blog目录下)找到_config.yml文件，编辑，找到里面的最下面有一个deploy字段的区域,修改为以下内容：

	deploy:
		type: git
		repo: https://github.com/sunkun0616/sunkun0616.github.io.git
		branch: master

注：这儿有个坑，yaml的编码格式，<font color=#FF4500>每个冒号后面一定要跟个空格。</font>

3.接着安装hexo自带的命令提交到github：

	npm install hexo-deployer-git --save

4.安装好后执行以下命令，在本地生成静态页面，然后提交到github：

	hexo generate (直接输入 hexo g 也可以)

	hexo deploy   (直接输入 hexo d 也可以)

然后在浏览器输入[http://sunkun0616.github.io](http://sunkun0616.github.io)就可以访问了，大功告成。


后续：
--

一些常用的基本命令有：
	
	hexo clean				--->清除数据

	hexo generate			--->在本地生成静态页面至public目录

	hexo deploy				--->提交到相关联的github账户上

	hexo new "博客文章标题"	--->新建文章

	hexo new page "页面名字"	--->新建页面

	hexo server				--->开启本地访问服务(默认端口是4000，直接在本地访问：http://localhost:4000)

	hexo help				--->查看关于hexo的命令帮助

	hexo -v					--->查看hexo的当前版本

后续（1）：
--

查看git分支（本地和远程）

	git branch -l		//查看本地分支
	git branch -r		//查看远程分支
	git branch -a		//查看全部分支（远程和本地）

后续（2）：
--

如果换电脑了，怎么用hexo+github更新博客？

1.在现有的项目上(xxx.github.io)新建个分支hexo，然后克隆xxx.github.io项目文件到本地
	
	git clone https://github.com/yourname/xxx.github.io

2.删除文件夹里除了.git的其他文件
3.把hexo文件夹下面的所有文件拷贝过来
4.里面应该有个.gitignore文件，如果没有就输入touch .gitignore创建一个
5..gitignore文件里的内容如下：
	
	.DS_Store 

	Thumbs.db 

	db.json 

	*.log 

	node_modules/ 

	public/ 

	.deploy*/

6.然后创建一个叫hexo的分支并切换到这个分支

	git checkout -b hexo

7.提交拷贝过来的文件到暂存区

	git add --all

8.提交

	git commit -m "提交的备注信息"

9.推送分支到github

	git push --set-upstream origin hexo
	
---------------

到这一步基本上就部署完毕，以后再更新博客就直接`git push`就可以，hexo更新博客的操作和以前的一样。

10.往后如果想在其他的电脑上写博客，只需要把创建的hexo的分支克隆下来，`npm install`安装依赖之后就可以用了。

	git clone -b hexo https:github.com/yourname/xxx.github.io.git

11.最后需要注意的是，每次写完博客用hexo发布后不要忘了还要git push把源文件推到分支上。

over....