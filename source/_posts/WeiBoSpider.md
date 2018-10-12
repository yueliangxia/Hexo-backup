---
title: 新浪微博爬虫
tags: scrapy
categories: 爬虫
copyright: true
abbrlink: 23444
date: 2018-09-14 08:59:59
---

爬取新浪微博时遇到的一些小问题汇总，但是由于现在还没有养成，报错就截图的好习惯，所以现在只能捡能想起来的先写点。

<!--more-->

### 先说一下一开始遇到的这个错误，经过查找原因，发现可能是由的网址的拼接规则不对
```python
ValueError: Missing scheme in request url: 
2018-09-16 17:19:40 [scrapy.core.scraper] ERROR: Spider error processing <GET https://weibo.cn/search/mblog?hideSearchFrame=&keyword=%E8%B5%B5%E4%B8%BD%E9%A2%96&page=2> (referer: None)
Traceback (most recent call last):
  File "F:\ENVS\scrapy_two\lib\site-packages\scrapy\utils\defer.py", line 102, in iter_errback
    yield next(it)
  File "F:\ENVS\scrapy_two\lib\site-packages\scrapy\spidermiddlewares\offsite.py", line 30, in process_spider_output
    for x in result:
  File "F:\ENVS\scrapy_two\lib\site-packages\scrapy\spidermiddlewares\referer.py", line 339, in <genexpr>
    return (_set_referer(r) for r in result or ())
  File "F:\ENVS\scrapy_two\lib\site-packages\scrapy\spidermiddlewares\urllength.py", line 37, in <genexpr>
    return (r for r in result or () if _filter(r))
  File "F:\ENVS\scrapy_two\lib\site-packages\scrapy\spidermiddlewares\depth.py", line 58, in <genexpr>
    return (r for r in result or () if _filter(r))
  File "E:\yuke\test\9-13\XinLangSpider\XinLangSpider\spiders\weibo.py", line 18, in parse
    yield scrapy.Request(url=url,callback=self.parse_info)
  File "F:\ENVS\scrapy_two\lib\site-packages\scrapy\http\request\__init__.py", line 25, in __init__
    self._set_url(url)
  File "F:\ENVS\scrapy_two\lib\site-packages\scrapy\http\request\__init__.py", line 62, in _set_url
    raise ValueError('Missing scheme in request url: %s' % self._url)
```
* 发现一个可能是错误的原因的地方
	* 修改之前
	```python
	for every_article in page:
		url = every_article.xpath('div/a[@class="cc"]/@href').extract_first('')
		yield scrapy.Request(url=url,callback=self.parse_info)
	```
	* 修改之后
	```python
url_list = page.xpath('div/a[@class="cc"]/@href').extract()
for url in url_list:
	yield scrapy.Request(url=url,callback=self.parse_info)
	```
> 其实也就是等于以前在for循环里整理网址,修改后在for循环之外整理网址,然后遍历网址的列表.猜测可能是因为scrapy爬虫框架要求的高效率吧,这样处理会让爬虫可以同步进行爬取列表中的网址.


### 关于转发数、赞数和评论数的提取，下面是item里面的一部分定义函数，使数量存在时返回，不存在时返回'0'
```python
def process_zhuanfa(value):
    if value[3:-1] is None or value[3:-1] == '':
        return '0'
    else:
        return value[3:-1]
def process_zan(value):
    return value[2:-1]
def process_pinglun(value):
    if value[4:-2] is None or value[4:-2] == '':
        return '0'
    else:
        return value[4:-2]
```

### 关于cookie池的问题
> 采用的是别人写好的开源的一个项目，输入账号密码后，自动生成cookie，并通过@http://127.0.0.1:5000/weibo/random　来提取生成
生成的值获取到的结果为str，需要反序列化成字典
    
    request.cookies = requests.get('http://127.0.0.1:5000/weibo/random').json()

### 关于发布时间转成统一格式的问题
* 现在发现有三种格式的时间
	* **分钟之前(一小时之内的会这样显示)
	* 今天 **:**(超过一小时就会这样显示)
	* 09月13日 22:33 (不是今天的)
* 现在考虑的是,分别格式化为时间戳,然后再从时间戳转换成要求的统一格式
```python
import time
def process_time(value):
    value = value.split(' ')[0:2]
    date = 'no_time'
    if '分钟' in value[0]:
        time_stamp = time.time() - int(value[0].split('分钟')[0]) * 60
        date = time.strftime("%Y-%m-%d %H:%M:%S", time.localtime(time_stamp))
        return date
    if '今天' in value[0]:
        local_time = time.localtime()
        time_string = "%s年%s月%s日 %s"%(local_time.tm_year,local_time.tm_mon,local_time.tm_mday,value[1])

        date = time.strftime('%Y-%m-%d %H:%M:%S', time.strptime(time_string, '%Y年%m月%d日 %H:%M'))
        return date
    if '月' in value[0]:
        local_time = time.localtime()
        time_string = "%s年%s %s" % (local_time.tm_year, value[0], value[1])
        date = time.strftime('%Y-%m-%d %H:%M:%S', time.strptime(time_string, '%Y年%m月%d日 %H:%M'))
        return date
```

### 最后的最后，准备接入微博的图床。也就是用人家的相册做些事情。。
* 下面是测试用的图片
* 还是不要了，现在已经改完了。。
<!-- ![image](https://wx3.sinaimg.cn/large/0068ZTOjgy1fvdy2edu3sj319u0ukn5s.jpg) -->