---
title: Django的学习记录
tags: Django
categories: Django
abbrlink: 3399
date: 2018-09-28 21:04:13
description:
image:
---
<blockquote class="blockquote-center">Django的一些基础知识，例如Django框架的基本结构</blockquote> 

<!-- <img src="https://" alt="" style="width:100%" /> -->

<!-- more -->

### (1)为什么要选择Django
#### 1.一站式的解决方案（ORM，Session，Admin等）
* 可以做后台的定时任务，执行相关的任务；也可以做运维的管理后台，可以快速的搭建起一个后台的模型
* Django的ORM是值得肯定的。因为有了它的ORM模型，我们可以方便的调用modles，直接去操作数据库，这样省去了手写SQL，或者SQL安全方面的一些问题

#### 2.成熟的Python Web框架（Django社区、丰富模块、稳定）
* 相对于现在主流的其他几款Python Web框架而言，Django开源于05年，所以相对于现在来说比较早，就因为这一点，他有着丰富的Django社区。对于出现的问题可以快速的查找到、定位到相应的问题，找到解决方案，对于新手而言，也可以快速的上手，进行学习。
* 对于新手主要要去浏览Django的官方文档 [官方文档：https://www.djangoproject.com/](https://www.djangoproject.com/)，里面有Django的整套不同版本的文档，主要是英文的。但是文档里从基础一直讲到相当细节的内容。对于开发者而言是相当方便的。当然了要想看中文的可以去百度搜索Django中文文档。
* 模块：有了丰富的模块，可以开发出一些多样的内容，省去了需要自己从零开发代码和相关的一些底层的设计所需要的耗时。

### (2)Django工程结构和建立
* 首先安装好Django后，通过Django-admin startproject 项目名 新建一个项目。
* 应用的建立(scanhosts：应用名)
`Ubuntu: ./manage.py startapp scanhosts`
`Windows: python manage.py startapp scanhosts`
* 下面是Django的目录的大体结构：
![目录结构](https://wx2.sinaimg.cn/large/0068ZTOjly1fvpl88hpgaj30k8092409.jpg)

> 首先最外层的是容器项目名--注意：这个容器项目，没有什么实际的作用，修改容器项目的名字，并不会影响后面项目的运行。
>> **manage.py**：是一个命令行工具，会接收对于自己的Django工程的管理，比如Django工程的启动和关闭，或者进行相应的调试，或者是数据模型的迁移等等操作，都是用这个文件进行操作。--里面的内容很简单就是从终端接收对应的参数去执行对应的命令，所以起到的是一个Django的管理工具的作用。
>>> **setting.py**：~~[详细配置](#settings)~~**非常重要！**这个里面有Django工程里面初始化的所有的相应的默认设置，也有后面我们要对Django工程对应的核心模块的一些设置都会集中在settings这个模块里面。Django初始化启动的服务也都是通过settings里面对应的配置去读取
>>> **urls.py**: 用于做Django工程的url路由的——我们要做自己的Web后台服务的时候，要生成自己的http的url，urls
.py就是url的路径进行对应的路由的。
>>> **wsgi.py**：这个是Django自己的一个与wsgi兼容Web服务的接口。不用做详细的了解
>> **其他文件夹**：然后下面就是包含的应用项目，一个容器项目里面可以包含多个应用项目
>> **scanhosts**：自己建立的应用
>>> **models.py**：**重点了解** Django的ORM模型建立model这个模型的一些配置都会放到这里面,默认里面只有下面这一行代码。这里是指导入了Django的模型，我们在定义数据库模型的时候，就要在这个文件里建立。
```python
from django.db import models
# Create your models here.
```
>>> **migrations文件夹**：默认是空的，这里是要做数据迁移用的。我们在models这个文件里进行对应的数据库模型的建立和更改以后，为了进行一个对应的迁移操作，会生成一些临时文件（一些临时的但是非常重要的中间文件）在migrations里面。会保存对应的文件的操作信息在这个文件夹里面。
>>> **views.py**： 是一个视图文件，同样也是在我们如果需要进行Web后台或者接口服务的话，对应相应的逻辑处理的逻辑层的代码书写都会放在这个文件里。






### (3)第一个DevOPS工程

* 启动Django工程的命令
`python manage.py sunserver`

<h4 id="settings">settings文件详细配置</h4>

```python
# Build paths inside the project like this: os.path.join(BASE_DIR, ...)
# BASE_DIR 是一个路径，工程启动的初始化基础路径，后面会调用基础路径去取其他的文件
BASE_DIR = os.path.dirname(os.path.dirname(os.path.abspath(__file__)))
```
```python
# 新建的应用如果没有加到settings文件里是不会随着Django工程一起启动的。所以首先要做的就是把自己的项目添加到INSTALLED_APPS这个列表里，最好直接添加到最后面。

# Application definition

INSTALLED_APPS = [
	# 前面都是Django的一起默认的基础模块
    'django.contrib.admin', # 后台模块
    'django.contrib.auth', # 认证相关的模块
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'scanhosts',
]
```
```python
# 数据库的连接，也是需要配置的。默认使用的是sqlite的数据库
# Database
# https://docs.djangoproject.com/en/2.1/ref/settings/#databases

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
}
```


### (4)Django日志logging模块

### (5)Django邮件发送

<hr />


极验验证：
1. 抓取极验参数:

	任何一个网站，如果在登录时网站接入的极验的接口，那么该网站就可以使用极验验证码进行登录。此时，极验验证码API就会返回两个极验参数(gt和challenge)。这两个参数只跟极验验证码API相关，跟这个网站没有任何关系。

	注意： 有的网站是直接调用极验官方提供的验证码接口，比如：极验的官方后台：https://auth.geetest.cn/api/ ；有的网站又对极验验证吗接口封装了一个API接，比如：魅族登录：https://login.flyme.cn/sec/geetest?

2. 将获取的极验参数，提交给极验破解网站的识别接口。会得到新的返回值：
```JSON
{
    status: "ok",
    challenge: "3d033f099597f5ae63e2e2c902301d183z",
    validate: "8f6ebd56291ed6569ac40c1d74780985"
}
```
3. 将上述参数challenge换个validate，混合着网站自己的提交的参数向网站自己的url发生POST请求，即可。