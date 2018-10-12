---
title: 关于新手购买域名和选择服务器的说明
tags: IP
categories: 域名和服务器
abbrlink: 21811
date: 2018-10-11 08:31:01
description:
image:
---
<blockquote class="blockquote-center">最近身边的好多小伙伴都购买了域名，但是不知道该怎么部署才能实现装逼效果，下面我就简单的写一点相关的说明和我遇到的一些坑。写的不好请见谅。</blockquote> 

<!-- <img src="https://" alt="" style="width:100%" /> -->

<!-- more -->

---
### 必读 写在前面

本文的内容是购买域名和相对应域名商的解析设置，然后搭建的过程会贴出大佬的文章链接。

---
### 首先当然是考虑先买一个域名啦。服务器什么的慌什么，也跑不掉。
<div class="note info no-icon"><p>现在好多的服务商都提供域名购买服务，其实买到的域名都是一样的，就是各大服务商的政策有一点区别。</p></div>

<span id="inline-toc">1.</span>首先各大服务商基本都有域名购买服务。比如阿里云，腾讯云，百度云···等

<span id="inline-toc">2.</span>这里说一下购买步骤。

> 在云服务商主页，这里采用的是腾讯云的例子。一般找不到的话上面都有搜索，输入域名。如下图

> 后面的步骤这里就不演示了，花钱买就行了。不过要说一下，要是第一个域名的话，腾讯云的会有一个活动，不是的话就算了。

![image](https://ws1.sinaimg.cn/large/0068ZTOjly1fw40k9nfsnj30mj092aah.jpg)
	

### 服务器提供商和博客框架的选择

{% note info %} 域名购买之后就是要选择服务器提供商了。虽然说国内各大网站卖的肯定是各种服务器都有，但是**1.贵，2.都需要备案**。所有暂时都不考虑 {% endnote %}

{% note success no-icon %} **服务器**：这里只说一下我比较了解的两个没有以上两点问题的服务器：**1.[GitHub Pages的部署相关](#GitHub-Pages); 2.[Nc主机的部署相关](#Nc主机)** {% endnote %}

{% note success no-icon %} **博客框架**：这里也只说两个，一个是无后台的**[Hexo](https://hexo.io/zh-cn/)**，一个是有后台管理界面的**[WordPress](https://cn.wordpress.org/)**。 {% endnote %}


<div class="note warning"><p>选择前须知：要是喜欢部署时稍微简单点的，建议先选择[nc主机](http://idc.ncshu.com/)，然后搭配[WordPress框架](https://cn.wordpress.org/)，但其实我感觉难易程度总体来说是差不多的。反正个人也就是我现在用的是[GitHub Pages](https://pages.github.com/)搭配的[Hexo框架](https://hexo.io/zh-cn/)。理论上说，各个服务器能搭配的框架都是很多的，但还是建议根据上面的搭配来搞。</p></div>



<h4 id="GitHub-Pages">GitHub Pages的部署相关</h4>

<div class="note primary no-icon"><p>**开始搭建博客**：在本地安装HEXO，建议阅读一遍[官方文档](https://hexo.io/zh-cn/docs/)，当然不想看了，就看下面的步骤也行.</p></div>

* 安装前需要的必须组件：**Git和Node.js**。这两个都直接去官网下载安装就行了，这里就不写步骤了，毕竟网上有好多。而且只需要一直下一步就行了。

* **Node.js安装时一定要注意**

<span id="inline-purple">Custom Setup这一步要选`Add to PATH`添加到环境变量</span>

<h5 id="开始安装">开始安装Hexo：</h5>

```python 所在目录：~/blog/
# 安装hexo
npm install hexo-cli g
# 初始化博客文件夹
hexo init 
# 安装hexo的扩展插件
npm install
# 安装其它插件
...
```
<div class="note info no-icon">上面的步骤，每一步都需要耐心等待。暂时还没发现什么好方法。如果正常走完的话，恭喜你这个框架已经安装完成了！<Br/>--hexo init时，如果出现橙色的<span id="inline-yellow">WARN</span>请无视，如果出现红色的<span id="inline-red">EERROR</span>，请自行查找方法。</div>

```python 所在目录: ~/blog/
# 测试是否安装完成：
hexo g 	# 生成一个包含所有静态网页所需东西的文件夹public
hexo s  # 通过自带的API接口，让你可以本地预览
```
再在本地打开[http://localhost:4000/](http://localhost:4000/)，如果现实有欢迎页面，恭喜你。第一步搞定了。

<h5 id="开始绑定域名">本地部署好了，当然要开始绑定自己的域名啦。</h5>

<div class="note primary no-icon"><p>GitHub Pages是由[GitHub](https://github.com/)提供的静态网页服务。官网地址为[https://pages.github.com/](https://pages.github.com/)，有兴趣的可以去瞅瞅。不过人家是全英的。。。</p></div>

注册GitHub 帐号和创建 Repository仓库请看这篇文章：[基于Hexo+github+coding搭建个人博客——基础篇(从菜鸟到放弃)](http://yangbingdong.com/2017/build-blog-hexo-base/#GitHub)，只看这两步就行了，不想再写一遍了。两步走完之后在接下来出现的页面找到我第一个箭头的位置点一下，改成如下图所示，把网址复制出来复制到下面接着的第二步里。
![image](https://ws1.sinaimg.cn/large/0068ZTOjly1fw4aiv1j8oj30s606bjrz.jpg)

<span id="inline-blue">言归正传：</span>继续讲怎么部署到GitHub。
* 1.首先在下面目录打开Git Bash安装下面这个插件
```python 所在目录: ~/blog/
npm install hexo-deployer-git --save	# 用来部署到git的插件
```

* 2.然后，打开站键文件夹根目录的配置文件_config.yml，在大概是最后的位置更改：
```python 文件位置：~/blog/_config.yml
省略。。。

# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: git
  repo:
  	# 下面写你自己的github的地址。也就是刚才让复制的地址。 
    github: git@github.com:yueliangxia/yueliangxia.github.io.git 
  branch: master
```

* 3.为了不让自己每一次部署都输入账号密码，就需要下面的步骤，也就是绑定SSH Key。对的没错，还是上一篇文章，不过我帮你你换了个索引以便于快速定位到这个地方。请按照人家的步骤绑定一下。
[基于Hexo+github+coding搭建个人博客——基础篇(从菜鸟到放弃)](http://yangbingdong.com/2017/build-blog-hexo-base/#生成SSH-Key
)

* 4.上面步骤完成之后，就算是大功告成了。
```python 所在目录：~/blog/
hexo g # 生成静态文件
hexo d # 部署到github
```
<div class="note info">hexo d第一次部署的时候会弹出一个输入框，输入yes就行了。</div>

<div class="note success">之后请进入github并找到自己的用户名.github.io的仓库打开，查看是否有文件。如果有文件请点击setting。</div>

![image](https://ws2.sinaimg.cn/large/0068ZTOjly1fw4b2pw050j30sk038mxa.jpg)
<div class="note success">进去后，请在第一个框里写上自己的域名，并把第二个启用HTTPS的框勾上，如下图</div>

![image](https://wx3.sinaimg.cn/large/0068ZTOjly1fw4b3qryxqj30ki0gf3zf.jpg)

<span id="inline-red">特别注意：</span> 关于部署完成之后服务器和域名跳转问题。

<div class="note success no-icon">需要在source文件夹里新建一个名为**CNAME**的文件，注意这里没有.txt后缀，什么后缀都没有，里面只需要写上自己的域名就行了，另存为utf-8格式。重新hexo d一下吧！</div>


<h4 id="hexo优化">Hexo 优化相关</h4>

<h5>关于主题的更换：</h5>

<div class="note info no-icon">首先当然说一下自带的主题。。来看一下自带的[主题官网](https://hexo.io/themes/)，不翻墙可能有点慢，不过也能看。这里面带有几乎所有支持的主题，点击图片可以查看预览。如果想要应用的话，就需要点击主题的名字，进入github的界面。因为这些主题都是存放在github的</div>

**更换主题的命令**
```python 所在目录：~/blog/
git clone github提供的下载地址 themes/主题的名字
# 下面是其中一个的栗子
git clone https://github.com/probberechts/hexo-theme-cactus.git themes/cactus
```
**然后更改站点配置文件**
```python 文件位置：~/blog/_config.yml
省略。。。

## Themes: https://hexo.io/themes/
# 把下面的主题名字改成自己想用的
theme: 主题的名字
```
<div class="note primary">这里推荐几个不错的主题：<br/>1.我当前在用的[next](https://github.com/iissnan/hexo-theme-next)<br/>2.[yilia](https://github.com/litten/hexo-theme-yilia)<br/>3.[indigo](https://github.com/yscoder/hexo-theme-indigo/tree/card)<br/>4.[cactus](https://github.com/probberechts/hexo-theme-cactus),选择主题就说到这里，就靠自己的喜好了。</div>


<h4 id="Nc主机">Nc主机的部署相关</h4>

<div class="note info"><p>这个主机嘛，提供的有免费虚拟机和的一个免费的大小还凑合数据库。说的是距离近，但是不花钱，访问速度也就比github提供的稍微快一点点。不明显！</p></div>


* <span id="inline-toc">1.</span> 首先去人家的官网[http://idc.ncshu.com/](http://idc.ncshu.com/)会员中心自己注册个账号。

* <span id="inline-toc">2.</span> 接下来去官网选择免费主机，然后选香港免费主机，点击下图立即订购
![image](https://wx1.sinaimg.cn/large/0068ZTOjly1fw43ccbednj30qw0apabj.jpg)

* <span id="inline-toc">3.</span> **配置账户界面** ：这个控制面板密码将会是你的ftp访问和数据库访问的密码。建议浏览器自动保存一下，不然忘了又麻烦。 
<span id="inline-yellow">这里建议先写上域名</span>不然的话就自己去控制面板选择绑定域名。
![image](https://ws2.sinaimg.cn/large/0068ZTOjly1fw43dn74i1j30nj0dr75j.jpg)

* <span id="inline-toc">4.</span> **控制面板**：搞好之后进入会员中心，然后选择已购产品-主机管理--然后进入管理面板

* <span id="inline-toc">5.</span> **上传文件**：
	* 先去WordPress官网下载这个框架的包。当然，下载地址我贴出来了[https://wordpress.org/latest.zip](https://wordpress.org/latest.zip)。下载完成后记住地址一会儿把这个框架上传到NC主机
	* 回到NC主机的控制面板--选择FTP/文件管理--在线文件管理器--然后在右侧选择上传文件--选择刚才下载的压缩包文件上传
	* 上传完成后，目录结构如下图，选择解压，解压目录写上~~/wwwroot~~,密码写控制面板的密码![image](https://ws1.sinaimg.cn/large/0068ZTOjly1fw445x370bj30ni04r3ys.jpg)

* <span id="inline-toc">6.</span> 回到基本功能--默认文件--文件名写上index.php，顺序写100--提交

* <span id="inline-toc">7.</span> **绑定域名**：
	
	* 首先按照提示 **增加域名绑定，在添加绑定之前请先解析域名：A记录到IP xxxx**。这一点请查看下面[域名绑定相关](#域名绑定相关)，绑定好后进行下一步
	* 域名填写自己买的域名，目录写 wwwroot/wordpress/。---确定
	* 如果没什么问题的话，你刚才填上的域名就能访问了。
	* 访问自己的域名
	* <span id="inline-blue">下面就是正常的WordPress安装界面</span>

* <span id="inline-toc">7.</span> **WordPress的安装**：
	* 选择简体中文--然后继续
	* 这里的用户名密码，是你的网站的后台管理员的用户名密码，这个一定要记着。
	* 准备好上面的东西，然后--现在就开始
	* 然后让你输入数据库名，用户名密码什么的。实际上需要输入的也就只有这两个---不知道的话，打开主机控制面板首页--右边那一列有数据库名，数据库用户名
	* 然后选--现在安装，后面的话访问域名，基本的网页就出来了。。如果需要定制，后面再说，或者自己去找相关教程。


---
<h3 id="域名绑定相关">域名绑定相关</h3>

pass


{% btn https://www.baidu.com, 点击下载百度, download fa-lg fa-fw %}

<hr />