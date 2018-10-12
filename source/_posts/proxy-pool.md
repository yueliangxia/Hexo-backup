---
title: zhihu的一些评论
tags: scrapy
categories: 爬虫
copyright: true
abbrlink: 7459
date: 2018-09-12 14:13:59
---

### 代理IP:

* 1.免费的代理IP,可用IP少,时效性较短,大部分的代理IP可能在访问网站都是失效的.
* 2.收费的代理IP,讯代理.
* 3.爬取国外的网站,VPN代理服务器.

### proxy_pool:	
> @项目地址：https://github.com/jhao104/proxy_pool.git
* 1.它将国内的代理IP网站都进行了爬取;
* 2.代理IP爬取完毕之后,会进行检测,可用的IP会保存到数据库redis中;
* 3.会定期将数据库中的代理IP拿出来检测,失效的IP从数据库中删除。
* 4.支持扩展

---

<!--more-->

### 练习项目:

	1. 解决登陆问题；
	2. 使用downloadmiddleware, ua, 代理、池;
	3. 使用itemloader完成数据的提取和解析；
	4. 提取所有问题的信息：标题、点赞数、评论数、关注者数量、浏览数量；将列表页数据单独
		保存至一个表中；
	5. 提取每一个问题的所有答案：回答用户、回答内容、回答时间、回答
		点赞数、回答评论数；将所有的答案保存至另外一个表中；
	6. 数据库的写入要求使用异步Twisted框架完成；
	
#### 首先需要先设置好代理IP和请求头,也就是自定义下载中间件

#### 然后根据需求知道要先获得登录后的Cookie
* 这个设置在middlewares里
* cookie登录需要设置成键值对的方式
	
```python
	request.cookies = {
        '_xsrf':'BKORzjqUj35ax0DdrwKnVn25RkNRaahM',
        '_zap':'4dc36192-e267-44d1-b8f4-074822456258',
        'd_c0':"AFAlC-xzMw6PTlUrgV6b_Bu4DtkilyXo-Z4=|1536751108",
        'capsion_ticket':"2|1:0|10:1536756874|14:capsion_ticket|44:MDRlODI3NDk5ZjhlNGRlOThiNzdiN2Q1NGM3OWEyNjQ=|7b1b74e68e3ccc4440577f394f8e1ded1efc6a5bca537a1a0d459b71d36877c2",
        'q_c1':'cd8e72dfa9384e70af51bc5c90b10f8a|1536751149000|1536751149000',
        'l_n_c':'1',
        'l_cap_id':"NGRjMmM4OTZkOTBlNGQ0ZTg2MTQ2YTk4YTNiODBhMGI=|1536756854|ae8c593677b5d5d7301941fa6e133c0c4f4f1022",
        'r_cap_id':"YTM4YTAyZjQ2ZDAwNDhiNjlmYTYxNmFmODU2NTMwMWU=|1536756854|b164129500acaa359cfc585c4c1d5d32b8529bf2",
        'cap_id':"MjQ0ZWZhOGQwZDlhNGQ5MGE0ZWRlMTNlMWYyZDcwMWQ=|1536756854|a8f854ab2430dbb1dc0f0ee8b9cf89e9f316b556",
        'n_c':'1',
        'z_c0':"2|1:0|10:1536756886|4:z_c0|92:Mi4xU2JyYkF3QUFBQUFBVUNVTDdITXpEaVlBQUFCZ0FsVk5sbHFHWEFEMGZOMm5qMmItXzJlUElVWEpCWHZGWkFzMkt3|8e0843029fb42283f58a63ec8765ba96c10b936e8775f14293cf3b12501e0f13",
        'tgw_l7_route':'53d8274aa4a304c1aeff9b999b2aaa0a'
        }
```
### 登录成功之后,获取问题详情,
* 1.发现获取详情的网址是xhr请求,其中一条为https://www.zhihu.com/api/v3/feed/topstory?action_feed=True&limit=7&session_token=b36c08db4577ef8d563788f5337b283c&action=down&after_id=13&desktop=true
* 2.经解析参数发现session_tokon这个参数,每次启动项目都会不一样,所以尝试在主页面的response中寻找.最终解析该值得代码为:
```python
		data = response.xpath('//div[@id="data"]/@data-state').extract_first('')
		pattern = re.compile(r'sessionToken":"(.*?)"',re.S)
		session_token = re.findall(pattern,data)
		print(session_token)
```
* 3.参数after_id的值为每一次加7,所以设置range循环,步进为7
* yield回调解析json数据的函数.经测试发现有的问题,没有question的值.--这个问题是因为:
	* 有的是文章,有的是问题.--这里暂时只解析那些是问题的页面.
	* 网址都是跟据id拼接出来的
	* 然后发现https://www.zhihu.com/question/41435689/answer/465837326拼接出这样的网址是无法获取到全部的回答的.所以..还是考虑把后面的回答者去掉
	* 变成https://www.zhihu.com/question/41435689这样
	
### 解析问题页面的评论

* 1.https://www.zhihu.com/api/v4/questions/41435689/answers?发现xhr请求中类似这样的get请求是获取的评论.
* 2.进去后经简单解析,发现没个请求有四个,所以就用从0开始步进为5的循环.--以后可以考虑获取一下评论的总数,然后按总数获取.
	
	* 这一点参数里有两个值offset=5&limit=5这两个控制每次获取的评论数.暂时不需要更改
* 3.include=data%5B*%5D这个参数,好像没个问题的也都不一样.
	* 关于这一点,参数里面需要这些东西的都是用[*]代替的,写出[*]也能正常获取评论
* 4.scrapy获取的json数据我用的解析方式,这样就能获取到字典了
```python
response_data = response.body.decode('utf-8')
json_data = json.loads(str(response_data))
```
* 5.关于评论帖子的时间这一块,返回的好像是个时间戳,需要反序列化一下
```python
import time
if 'updated_time' in data:
	# 有更新时间就获取更新时间
	date = data.get('updated_time','')
else:
	# 没有就获取创建时间
	date = data.get('created_time','')
timeArray = time.localtime(date)
date = time.strftime("%Y-%m-%d %H:%M:%S", timeArray)

```

### 关于遇到的一些错误
```python
	MySQLdb TypeError: %d format: a number is required, not str问题解决
```
	
* 这个其实就是传参数的时候占位符填错了,一般都是因为传int类型时不需要用%d,直接用%s就搞定了,亲测成功~!
* 还有一点就是最好要把str类型的占位符改成'%s',就是带引号的这种,防止1146错误..

