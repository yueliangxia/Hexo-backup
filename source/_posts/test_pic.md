---
title: 关于51job的爬虫说明
tags: scrapy
categories: 爬虫
copyright: true
abbrlink: 24762
date: 2018-09-10 17:51:00
---

@本文源码：https://github.com/Loveyueliang/yuke/tree/master/9-10

<!--more-->

### 错误点:
* 感觉唯一的坑就是刚学的sql插入,一开始定义的表的字段名和item定义的不一样,所以就各种报KeyError.
```angular2html
insert_sql = "INSERT INTO job(zwmc, zwxz, gzdd, gzyq)VALUE ('%s', '%s', '%s', '%s')"%(item['zwmc'],item['zwxz'],item['gzdd'],item['gzyq'])
            self.cursor.execute(insert_sql)
```

### 最终结果还不错,暂时就爬取这一点信息吧.

> ![image](https://wx3.sinaimg.cn/large/0068ZTOjgy1fvex5hxnrbj30wy0ecadp.jpg)
