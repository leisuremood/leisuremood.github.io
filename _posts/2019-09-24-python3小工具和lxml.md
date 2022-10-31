---
layout: post
title:  "python3小工具和lxml"
date:   2019-09-24
tags:
      - it
---


# PDF转word

> pip install popdf
> import popdf
> 
> popdf.pdf2docx(file<sub>path</sub>='程序员晚枫.pdf')


# 给图片添加水印

> pip install poimage
> import poimage
> 
> poimage.add<sub>watermark</sub>(file='程序员晚枫.jpg', mark='你的水印')


# 多个Excel表格的关联查询

根据身份证号码，从100个Excel文件里面，找到这个人的所有信息。

> pip install poexcel
> 
> import poexcel
> poexcel.find<sub>excel</sub><sub>data</sub>(search<sub>key</sub>='你要搜索的内容', target<sub>dir</sub>='存放excel的文件夹位置')


# 简易爬虫下载图片

> pip install poimage
> import poimage
> 
> poimage.down4img(
>     url='https://python-office-1300615378.cos.ap-chongqing.myqcloud.com/2-free-group.jpg',
>     output<sub>name</sub>='程序员晚枫',
>     type='jpg')


# 翻译

> pip install wftools
> 
> import wftools  
> 
> wftools.transtools(to<sub>lang</sub>='Chinese', content='hello world')

以上功能，都来自python-office这个自动化办公的专用库，更多功能和视频教程，可以访问官网：www.python-office.com


# [lxml 学习笔记](//www.jb51.net/article/142670.htm)

如果某个属性的值有多个时，我们可以使用contains()函数来获取

result1=html.xpath('//li[contains(@class,"aaa")]/a/text()')

同时匹配多个属性，此时可用运用and运算符来连接

result1=html.xpath('//li[contains(@class,"aaa") and
@name="fore"]/a/text()')

<https://stackoverflow.com/questions/7474972/python-lxml-append-element-after-another-element>

contentdiv = contentnav.getparent()

<https://stackoverflow.com/questions/23282241/lxml-difference-between-element-addnext-and-insert-in-handling-tail>

xml[0].tail.find("italic")

