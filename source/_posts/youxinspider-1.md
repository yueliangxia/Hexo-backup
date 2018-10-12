---
title: youxinSpider
tags: xin.com
categories: scrapy
abbrlink: 21872
date: 2018-09-20 16:59:56
description:
image:
---
<p class="description">关于某信二手车的验证码问题。</p>

<img src="https://wx1.sinaimg.cn/large/0068ZTOjly1fvg47kq4bcj30ec092t96.jpg" alt="" style="width:100%" />

<!-- more -->

### 这个页面的请求其实不多，也没遇见什么加密 所以几乎是一瞬间就发现了请求的网址 -> [validate](http://www.xin.com/checker/validate/)，还顺便看到了它的两个参数
* 1. vcode：后面跟的是四位验证码
* 2. t：后面跟的是网页中图片验证码的链接里面的

### 于是乎，乐呼呼的填上了，但是直接请求失败。403状态码。还是接着研究吧。
<hr />

### scrapy采用跟的是多线程模式，十个线程，每一个都遇到了验证码，所以十个线程肯定都会去刷新验证码图片，要是能保证最新的验证码给服务器进行认证也行，但是没办法获取到最新的啊。


##

<hr />