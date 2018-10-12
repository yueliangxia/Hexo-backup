---
title: 天眼查的字体加密
tags: []
abbrlink: 8131
date: 2018-10-09 19:27:54
categories:
description:
image:
---
<blockquote class="blockquote-center">关于一些网站的字体加密方面，目前发现的也就只有两种：一种是起点网的那个总字数那种的给的是是禁止的数据，转换成十六进制之后再在woff文件转换成的xml文件中寻找对应的关系。另一种就是类似于天眼查的，获取到的源码是字体的位置信息，然后需要根据位置信息取出对应的数据。</blockquote> 

<!-- <img src="https://" alt="" style="width:100%" /> -->

<!-- more -->

### 前提：
---
<div class="note success no-icon"><p>这里我用了两种方式来搞定这种相对应位置的字体加密。</p></div>


### 1.第一种的思路是：主要采用PIL图像处理模块来根据1-0这个十个源码里获得的数字取出字体文件里真正要显示的数字，并画在画布上保存成文件，然后利用图片识别(tesseract-orc)技术来取出保存的图像中包含的数字，从而获得它们的对应关系。
---
* 1.1：首先当然是需要安装必须的工具，俗话说“工欲善其事，必先利其器”嘛。
	* Win系统下首先要安装这个 tesseract-ocr.exe 软件，下载地址就靠自由发挥了。(这个软件需要手动把目录添加到环境变量)
	* 对于Python而言，需要装一个中间模块，用来在py文件中调用上面的软件。（这个好装：pip install pytesseract）

* 1.2：下面是这种思路用到的代码

```python  
def getmapping(font_url):
    a_time = time.time()
    # content = requests.get('')
    # 保存woff文件
    # 请求字体文件的url，获取字体文件的内容。
    font_response = requests.get(font_url, headers={
        'User-Agent': 'Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36'}).content
    with open('tianyan.woff', 'wb') as f:
        f.write(font_response)
    # 1.先定义位置数字
    position_number = ['1','2','3','4','5','6','7','8','9','0']
    # 2.遍历位置数字列表
    map_dict = {}
    for p_num in position_number:
        # 3.创建一个图片对象，用于后续数字图片的存储
        img = Image.new('RGB',(300,500),(255,255,255))
        # 4.根据这个img对象，创建一个画布，用于后续将数字画到画布上
        draw = ImageDraw.Draw(img)
        # 5.根据位置数字p_num和字体文件woff，找出p_num在woff文件中的真实映射关系。
        # 参数1：指定字体文件
        # 参数2：这个数字在画布上，展示的大小
        # truetype()返回一个字体对象
        font = ImageFont.truetype('tianyan.woff',400)
        draw.text((10,10), text=p_num, font=font, fill=ImageColor.getcolor('black','RGB'))
        # img.show()
        img.save('tianyan.png')
        # 6.利用pytesseract包识别图片中的数字
        # pip install pytesseract
        # a> 打开图片
        font_image = Image.open('tianyan.png')
        # b> 调用识别函数

        num = pytesseract.image_to_string(font_image,config='--psm 6')
        if num == 'A' or num == '/':
            num = pytesseract.image_to_string(font_image,config='--psm 8')
            map_dict[p_num] = num
        elif num == "":
            # 有的字体文件中是缺少部分数字的。那么得到的图片就是一个空白图片，也就是位置数字对应的真实数字不存在。
            # 凡是字体不存在的，都有一个特征：就是位置数字和真实数字是相等的。
            map_dict[p_num] = p_num
        else:
            map_dict[p_num] = num
        # print('位置：{}，真实：{}'.format(p_num, num))
    print('tesseract处理时间：',time.time()-a_time)
    return map_dict
```


### 2.第二种思路就是还是解析字体转换成的xml文件，从中找到它们之间的对应关系。
<div class="note primary"><p>这次就主要先写这一个吧。[代码](#parse_font_xml)</p></div>


<div class="note info">我还是直接在代码中注释出来吧。虽然不一定适合其他网站，但是思路还是不错滴。</div>

<h4 id="parse_font_xml">解析转换成xml的woff文件代码</h4>

```python 
from fontTools.ttLib import TTFont
from xml.etree import ElementTree

map_dict = {}

def parse_font_xml(font_url):
    # 首先 全局定义的有一个空字典，用于存放已经解析过的字体规则。如果已经解析过，则直接取出并返回，提升运算速度。
    if font_url in map_dict:
        return map_dict[font_url]
    
    # position_number这个列表定义的是映射前的字符，也就是指源代码中可能获取到的数字。
    position_number = ['1', '2', '3', '4', '5', '6', '7', '8', '9', '0']
    
    
    # 请求字体文件的url，获取字体文件的内容并存储到本地。
    font_response = requests.get(font_url, headers={
        'User-Agent': 'Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36'}).content
    with open('tianyan.woff', 'wb') as f:
        f.write(font_response)
    
    # 存好之后，用TTFont打开，并保存成xml文件
    f = TTFont('tianyan.woff')
    f.saveXML('tianyan.xml')
    
    # 解析xml标签结构，并取出一个含有所有数字的长度为十的列表命名为maps_element.(最下面有此段的xml代码，但只是众多规则中的一种，仅供参考)
    root_obj = ElementTree.parse('tianyan.xml').getroot()
    maps_element = root_obj.find('GlyphOrder').findall('GlyphID')[2:12]

    # 有的规则会获取不到完整的十个数字，因为有的映射关系没有改变（比如3-3），这样的情况在上面的列表中就不会显示，所以要进行下面的两个步骤
    # 1.先遍历maps_element判断取出的值是否为数字，并把是数字的存到新的列表sort_num中。
    sort_num = []
    for i in range(len(maps_element)):
        if maps_element[i].attrib['name'] in position_number:
            sort_num.append(maps_element[i].attrib['name'])
    # 2.然后判断sort_num这个列表长度是否为十，为十的话，就根本不用处理了
    if len(sort_num)!=10:
    	# 如果不为十，就要在映射前的列表中取值，看看映射后的列表也就是sort_num中缺少了什么用insert插入相对应的位置
        for num in position_number:
            if num not in sort_num:
                sort_num.insert(int(num),num)
    
    # 处理完成之后，映射后的数字就按照正确的顺序存在了sort_num这个列表中
    # 最后一步就是把值和对应的索引取出来并放在字典中，返回出去
    sort_num_dict = {}
    for key,value  in enumerate(sort_num):
        sort_num_dict[str(value)] = key
        
    # 当然不能忘了把字体url和对应的解析规则存放起来，以便下次重复时调用
    map_dict[font_url] = sort_num_dict
    
    return sort_num_dict
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ttFont sfntVersion="\x00\x01\x00\x00" ttLibVersion="3.30">

  <GlyphOrder>
    <!-- The 'id' attribute is only for humans; it is ignored when parsed. -->
    <GlyphID id="0" name="glyph00000"/>
    <GlyphID id="1" name="x"/>
    <GlyphID id="2" name="8"/>
    <GlyphID id="3" name="4"/>
    <GlyphID id="4" name="9"/>
    <GlyphID id="5" name="0"/>
    <GlyphID id="6" name="2"/>
    <GlyphID id="7" name="5"/>
    <GlyphID id="8" name="7"/>
    <GlyphID id="9" name="1"/>
    <GlyphID id="10" name="_"/>
    <GlyphID id="11" name="_#1"/>
    <GlyphID id="12" name="_#2"/>
    ...
```

<hr />