---
title: scrapy部署爬虫项目
tags: scrapy
categories: scrapy
abbrlink: 38441
date: 2018-09-27 14:16:24
description:
image:
---
<blockquote  size=5 color="#66ccff" class="blockquote-center">功能：它就相当于是一个服务器，用于将自己本地的爬虫代码，打包上传到服务器上，让这个爬虫在服务器上运行，可以实现对爬虫的远程管理。(远程启动爬虫，远程关闭爬虫，远程查看爬虫的一些日志。)</blockquote> 


<!-- <img src="https://" alt="" style="width:100%" /> -->

<!-- more -->

### 1.scrapy的安装

`pip install scrapy`

### 2.如何将本地的爬虫项目Deloying(打包)，上传至scrapyd这个服务中。

* a> 提供了一个客户端工具，就是scrapyd-client，使用这个工具对scrapyd这个服务进行操作，比如向scrapy这个服务打包上传项目。scrapy-client类似于redis-cli.exe,mongodb数据库的client。
`pip install scrapyd-client==1.2.0a1`

### 3.上述服务和客服端安装好之后，就可以启动scrapyd这个服务了。服务启动之后，不要关闭，
![image](https://wx2.sinaimg.cn/large/0068ZTOjly1fvo3wznucwj30lj09879a.jpg)
访问 <http://127.0.0.1:6800/>
* 出现下面的页面正常
![image](https://ws3.sinaimg.cn/large/0068ZTOjly1fvo413bep2j30nf0b6q3d.jpg)


### 4.配置爬虫项目，完成以后，再通过addversion.json进行打包。
![image](https://ws4.sinaimg.cn/large/0068ZTOjly1fvo48d6dbwj30vi0dhwio.jpg)
### 5.上述的scrapyd服务窗口cmd不要关闭，再心打开一个cmd窗口，用于使用scrapyd-client客户端连接scrapyd服务。
* 进入项目根目录，然后输入scrapyd-delopy命令，查看scrapyd-client客户端命令是否正常可用
```
输入：scrapyd-deploy
返回：Unknown target: default

```
* 查看当前可用于打包上传的爬虫项目：
```
输入：scrapyd-deploy -l
返回：bole(scrapy.cfg里添加的爬虫名称)      http://localhost:6800/(scrapy.cfg里解注释的url)
```
* 使用scrapy-deploy命令打包项目：
`scrapyd-deploy bole -p jobbolespider`
> 参数：
> Status:"ok"/"error" 项目上传状态
> Project:  上传的项目名称
> Version:  项目的版本号，值是时间戳
> Spiders:  项目project包含的爬虫个数


* 通过API接口，查看已经上传至scrapyd服务项目。
```
命令：curl http://localhost:6800/listprojects.json`
```
键值：

Projects:[]所有已经上传的爬虫项目，都会显示在这个列表中。

* 通过API接口，查看某一个项目中的所有的爬虫名称；
```
命令： $ curl http://localhost:6800/listspiders.json?project=myproject
```

* 通过API接口，启动爬虫项目：
```
命令： curl http://localhost:6800/schedule.json -d project=爬虫项目名称名称 -d spider=项目中某一个爬虫名称 
```
键值：


jobid：是根据项目(jobbolespider)和爬虫(bole)生成的一个id，将来用于取消爬虫任务。
```
取消：curl http://localhost6800/cancel.json -d project=jobbolespider -d job=**
```

```
删除命令：$ curl http://localhost:6800/delproject.json -d project=myproject
```

<hr />

{% note warning %}注意：如果项目上传失败，需要先将爬虫项目中打包生成的文件删除(build、project.egg-info、setup.py){% endnote %}

{% note warning %} 注意：删除前一定要先取消！{% endnote %}

