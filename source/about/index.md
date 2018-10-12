---
title: about
date: 2018-09-06 22:10:24
comments: false
---

<center>关于我的一些基本信息</center>


> 有人以为我会写点什么吗? 不存在的
---
> 其实还真有一点。。背景音乐确实有点烦，来打我啊，哈哈！！



```python
print('Hello,World!')
```

---

* 自带的 blockquote 标签测试,算了都说私有的格式兼容问题很严重，还是研究固有的格式吧

<!-- HTML方式: 直接在 Markdown 文件中编写 HTML 来调用 -->
<!-- 其中 class="blockquote-center" 是必须的 -->
<blockquote class="blockquote-center">blah blah blah</blockquote>

<!-- 标签 方式，要求版本在0.4.5或以上 -->
{% centerquote %}blah blah blah{% endcenterquote %}

<!-- 标签别名 -->
{% cq %} blah blah blah {% endcq %}
<!-- 标签带网址 -->
{% blockquote @本网址 https://blog.52sifang.cn %}
这东西还是挺麻烦的还要写开始和结束的符号。 http://test.52sifang.cn
{% endblockquote %}

#### Bootstrap Callout

`{% note class_name %} Content (md partial supported) {% endnote %}`

{% note warning %} warning-content (md partial supported) {% endnote %}

其中，class_name 可以是以下列表中的一个值：
这个倒是挺好玩的
1.default
2.primary
3.success
4.info
5.warning
6.danger

`{% block title %}{{ config.title }}{% if theme.index_with_subtitle and config.subtitle %} - {{config.subtitle }}{% endif %}{% endblock %}`

<font size=5 color="#66ccff" class="blockquote-center"> 这里: END</font> 
