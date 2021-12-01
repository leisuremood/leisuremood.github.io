---
layout: post
title:  "python3的lxml"
date:   2019-09-24
tags:
      - 随笔
---

#python3的lxml


[lxml 学习笔记](https://www.jb51.net/article/142670.htm)

如果某个属性的值有多个时，我们可以使用contains()函数来获取

result1=html.xpath(\'//li\[contains(\@class,\"aaa\")\]/a/text()\')

同时匹配多个属性，此时可用运用and运算符来连接

result1=html.xpath(\'//li\[contains(\@class,\"aaa\") and
\@name=\"fore\"\]/a/text()\')



<https://stackoverflow.com/questions/7474972/python-lxml-append-element-after-another-element>

contentdiv = contentnav.getparent()



<https://stackoverflow.com/questions/23282241/lxml-difference-between-element-addnext-and-insert-in-handling-tail>

xml\[0\].tail.find(\"italic\")

