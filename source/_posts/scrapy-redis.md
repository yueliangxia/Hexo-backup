---
title: 分布式爬虫
date: '2018/9/25 16:03'
tags: 'scrapy,redis'
categories: scrapy
copyright: true
abbrlink: 19872
---
### 1. 概念
将同一个爬虫程序放在多台电脑上(或者同一个电脑中的多个虚拟机环境)，并且在多台电脑上同时启动这个爬虫。异态电脑运行一个爬虫程序成为单机爬虫。

---

<!--more-->

### 2. 作用
可以利用多台电脑的带宽，处理器等资源提高爬虫的爬取速度；

### 3. 原理 
分布式需要解决的问题（共用的队列Queue和共用的去重集合set(),这两个是scrapy自身无法解决的问题。只能通过第三方组件。）

 > 需要保证多台电脑中的爬虫代码完全一致；

 > 多台电脑操作统一网站，如何管理url去重？

> scrapy如何做去重的？
  > scrapy会将所有的Request对象(request.url,request.method,request.body)进行加密，生成一个指纹对象fingerprint；然后将指纹对象放入set()集合中进行对比，如果set()集合中已经存在这个对象，那么scrapy就会将Request抛弃。如果set()集合中不存在这个指纹对象，就将这个Request添加到调度队列queue中，等待被调度器调度；
 >> 多台电脑就会有多个set()对象。能否使用各自的set()做去重？
  >  不能。这个set()对象中的数据都是保存在电脑内存中的，电脑的内存空间是不能共享的。
  >  这个set()对象随着程序的启动而创建，程序的退出而销毁
  >> 所以，要解决这个问题需要让所有电脑公用同一个set()集合，不再使用scrapy内置的set()对象，而是使用scrapy-redis蒋政set()集合在redis中创建。然后多台电脑访问同一个redis就可以实现set集合的共用。

> 用于存放合法Request请求对象的对象，在scrapy中也是默认存放在内存中的，也是无法实现多台电脑的共享队列。如果是多台电脑的话，就需要保证多台电脑有共用的队列queue，这样可以保证所有电脑都是从同一队列中获取Request对象进行调度，多有set()过滤出来的Request都放到同一队列中。
>> 所以，要解决这个问题，需要让所有电脑公用同一个队列，不再使用scrapy内置的queue，而是使用scrapy-redis将这个queue在redis中创建，然后多台电脑访问同一个redis就可以使用queue的共用。
> 如何将不同电脑上获取的数据，保存在同一个数据库中。
      
### 4. 配置 
> 必须配置
```python
# Enables scheduling storing requests queue in redis.
SCHEDULER = "scrapy_redis.scheduler.Scheduler"
# Ensure all spiders share same duplicates filter through redis.
DUPEFILTER_CLASS = "scrapy_redis.dupefilter.RFPDupeFilter"
```
> 可选配置
```python
# Store scraped item in redis for post-processing.
ITEM_PIPELINES = {
    'scrapy_redis.pipelines.RedisPipeline': 300
}
```
> 必须配置
```python
# 配置REDIS_URL = '(redis://redis数据库用户(默认是root)：redis数据库连接密码(默认为空))@(redis的连接地址):(redis的端口号)'
# Specify the full Redis URL for connecting (optional).
# If set, this takes precedence over the REDIS_HOST and REDIS_PORT settings.
# hostname：redis的链接地址，由于多台电脑要连接同一个redis数据库，所以，这个链接地址可以是其中一台电脑的IP地址。注意：这个IP地址对应的电脑必须启动redis，
# 如果是阿里云上购买的redis数据库服务器，这个hostname就填写阿里云的redis服务器的公网IP
#REDIS_URL = 'redis://root@ip:6379'
#REDIS_HOST = 'localhost'
#REDIS_PORT = 6379
```
> 必须配置
* 配置redis服务，默认只支持localhost的本地链接，不允许远程链接，需要配置redis服务，开启远程连接功能。找到本地安装包，修改redis.windows.conf文件
  1. 将protected-mode yes 这个保护模式改为 no
  2. 将bind 127.0.0.1 修改成REDIS_URL中设置的IP地址(****)

{% note primary %}注意：分布式爬虫程序中设置的所有的key，都是"xxx:xxx"结构，对应的是容器的名称（类似于MySQL中的表名），并不是容器内容中key：value的这个key。{% endnote %}

> (必须配置)
	from ..scrapy_redis.spiders import RedisSpider
	class BoleSpider(RedisSpider):
		name = 'bole'
		allowed_domains = ['jobbole.com']
		# start_urls = ['http://blog.jobbole.com/all-posts/']

		# 分布式就不需要再设置起始的url，需要通过redis进行添加起始的url。起始的url添加到哪去了？被添加到了公共队列queue中。让多台机器中的其中一台从公共的reids队列queue中，获取起始的url，并对这个起始的url进行请求和解析，获取更多的url，然后将所有的url构造成Request，还放入公共队列queue中，让其他机器获取这些Request请求。
		redis_key = 'jobbole:start_urls'

> 向redis_key = 'jobbole:start_urls'这个键中，添加起始url.
		lpush jobbole:start_urls http://blog.jobbole.com/all-posts/
		
> 修改MySQL数据库为远程连接，让所有电脑连接同一个数据库，爬取出来的数据都保存在同一个数据库的表中。
	默认情况下，MySQL分配的root用户只允许本地连接localhost，如果需要通过IP建立数据库的连接，需要创建一个具有远程连接权限的用户：
	*.*: 所有连接地址都可以使用，比如：localhost,192.168.1.121,117.56.23.4
	rootuser: 新创建的用户名
	'%': 表示所有权限
	'123456': 连接密码
	grant all privileges on *.* to rootuser@'%' identified by '123456';(需要从cmd先进入mysql，再输入这个命令)


### scrapy去重：
{% note warning %} 
1. start_url = ['http://blog.jobbole.com/all-posts/', 'http://blog.jobbole.com/all-posts/'] 这个列表中的所有的url都不会去重。
{% endnote %}

```python
  # dont_filter的默认值是False
  def start_request():
    for start_requests:
      yield Request(dont_filter=True)
```
```python
# 开始去重
def enqueue_request():
  # 此函数位于core/scheduler文件中。
  if not dont_filter and self.request_fingerprint(request):
    return False
  else:
    # 将request添加到队列中
```

{% note success %}
2. 除了start_urls之外的这些请求都是要去重的。
{% endnote %}

```python
# scrapy-redis去重：和scrapy默认的去重一致。
```
