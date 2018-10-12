---
title: 用twisted异步写入
date: '2018/9/11 19:43'
tags: 'scrapy,mysql'
categories: 爬虫
copyright: true
abbrlink: 16883
---

## 异步写入MySQL数据库,如果scrapy解析数据的速度远远超过数据库的写入速度,那么很容易就造成数据的丢失.这里用twisted保证数据的异步多线程写入,提高写入速度.详情请看正文

@本文相关源码: https://github.com/Loveyueliang/yuke/tree/master/9-11/jobbolespider

<!--more-->

### 首先需要先导入模块
```python
from twisted.enterprise import adbapi
```
### 接着在自定义的pipeline里面重构from_settings函数
```python
@classmethod
def from_settings(cls, settings):
    """
    这个函数名称是固定的，当爬虫启动的时候，scrapy会自动调用这些函数，加载配置数据。
    """
```
### 创建一个数据库连接池对象，这个连接池中可以包含多个connect链接对象。
```python
# 参数1：操作数据库的包名
# 参数2：链接数据库的参数
db_connect_pool = adbapi.ConnectionPool('pymysql', **params)
```
### 初始化这个类的对象
```python
obj = cls(db_connect_pool)
```
### 在process_item这个函数里执行数据的多线程写入操作
```python
    # 参数1：在线程中被执行的sql语句
    # 参数2：要保存的数据
    result = self.dbpool.runInteraction(self.insert, item)
    # 给result绑定一个回调函数，用于监听错误信息
    result.addErrback(self.error)
```
* 定义一个打印错误信息的函数
```python
    def error(self, reason):
        print('--------', reason)
```
* 定义一个执行插入语句的函数,用twisted的好处其中一点就是不用一步步的手动提交
```python
    def insert(self, cursor, item):
        insert_sql = "INSERT INTO bole(bole_title, bole_date, bole_tag, bole_content, bole_dz, bole_sc, bole_pl, bole_img_src) VALUES (%s, %s, %s, %s, %s, %s, %s, %s)"
        cursor.execute(insert_sql, (item['bole_title'], item['bole_date'], item['bole_tag'], item['bole_content'], item['bole_dz'], item['bole_sc'], item['bole_pl'], item['bole_img_path']))
        # 不需要commit()
```