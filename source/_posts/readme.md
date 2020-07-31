---
title: makedown语法
tags:
  - hexo
  - linux
categories: 运维
date: 2060-07-30 10:52:44
---
# readme.md

如果想写文章, 又不希望被看到, 那么可以  
`hexo new draft newdraft`  
这样会在source/_draft中新建一个newdraft.md文件, 如果你的草稿文件写的过程中, 想要预览一下, 那么可以使用  
`hexo server --draft`

如果你的草稿文件写完了, 想要发表到post中  
`hexo publish draft newdraft`
就会自动把newdraft.md发送到post中  
最后`hexo clean && hexo g && gulp && hexo d`就可以了.

写作请使用:  
`hexo new post --path 无极OPS/docker3 "docker--容器创建后添加端口映射"`  

---
分割线三个减号--- 上下空行即可  

---

[md小技巧](https://www.runoob.com/markdown/md-tutorial.html)  
`&emsp;&emsp;  空白格, 用于中文段落首行缩进`
<!-- # 一级标题 这样写右侧目录不显示一级标题.-->

---
# 一级标题  

&emsp;&emsp;如果配置不行, 建议增加上下行空行, 标题尾部双空格等  

## 二级标题
<!-- # 一级标题 -->
<!-- ## 一级标题 -->

段落的换行是使用两个以上空格加上回车 或者空一行表示换行

------
```
*斜体文本* &emsp;&emsp;**粗体文本** &emsp;&emsp; ~~删除线~~ \_\_init__显示本体 不转义
```
*斜体文本* &emsp;&emsp;**粗体文本** &emsp;&emsp; ~~删除线~~&emsp;&emsp;\_\_init__  

------
```
>这是引用的内容  
>>这是引用的内容  
```
>这是引用的内容  
>>这是引用的内容  

---
```
下载地址书写:[download](https://wujiops.coding.net/p/xuexibiji/d/xuexibiji/git/raw/master/类和方法.zip "类和方法.zip") 
```
下载地址书写:[download](https://wujiops.coding.net/p/xuexibiji/d/xuexibiji/git/raw/master/类和方法.zip "类和方法.zip") 

---
`![图片alt](图片地址 ''图片title'')`  
`![](/media/hdp/5.jpg)`
`![](/media/sourcetree/0.jpg)`  
图片alt 就是显示在图片下面的文字, 相当于对图片内容的解释  
图片title 是图片的标题, 当鼠标移到图片上时显示的内容。title可加可不加  

`[超链接名](超链接地址 "超链接title")`  
title可加可不加  

示例  
[简书](http://jianshu.com)  
[百度](http://baidu.com)  

---
无序列表用 - + * 任何一种都可以  
注意: - + * 跟内容之间都要有一个空格   

```
- 列表内容
- 列表内容
- 列表内容
```

- 列表内容
- 列表内容
- 列表内容
---
有序列表  
注意: 序号跟内容之间要有空格   

```
1. 列表内容  
2. 列表内容  
3. 列表内容  
```

1. 列表内容  
2. 列表内容  
3. 列表内容  
列表嵌套   
上一级和下一级之间敲三个空格即可   

```
1. 列表1  
2. 列表2  
   * 嵌套1  
```

1. 列表1  
2. 列表2  
   * 嵌套1  

---
表格对齐方式

-: 设置内容和标题栏居右对齐。  
:- 设置内容和标题栏居左对齐。  
:-: 设置内容和标题栏居中对齐。  
实例如下：  

|左|居中|右|
|:-|:-:|-:|
|abc|bcd|cde|
|abc|bcd|cde|
|abc|bcd|cde|

```bash
<font face="黑体">我是黑体字</font>
<font face="微软雅黑">我是微软雅黑</font>
<font face="STCAIYUN">我是华文彩云</font>
<font color=red>我是红色</font>
<font color=#008000>我是绿色</font>
<font color=Blue>我是蓝色</font>
<font size=5>我是尺寸</font>
<font face="黑体" color=green size=5>我是黑体, 绿色, 尺寸为5</font>
<center>居中</center>  
```

<font face="黑体">我是黑体字</font>  

<font face="微软雅黑">我是微软雅黑</font>  

<font face="STCAIYUN">我是华文彩云</font>  

<font color=red>我是红色</font>  

<font color=#008000>我是绿色</font>  

<font color=Blue>我是蓝色</font>  

<font size=5>我是尺寸</font>  

<center>居中</center>  

<font face="黑体" color=green size=5>我是黑体, 绿色, 尺寸为5</font>  

---
参考[链接](https://blog.csdn.net/heimu24/article/details/81189700)

脚注是对文本的补充说明。  
[^要注明的文本]  
创建脚注格式类似这样 [^RUNOOB]。  
[^RUNOOB]: 菜鸟教程 -- 学的不仅是技术, 更是梦想！！！  

TODO 待完成  
FIXME 代码需要修正和原因说明  
XXX 虽然实现功能，但是代码需要改进和说明  
HACK 补锅踩雷填坑  
BUG 丢锅埋雷挖坑  

`Ctrl+G`快速去某一行