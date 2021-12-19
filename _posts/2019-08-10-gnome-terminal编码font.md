---
layout: post
title:  "gnome-termilalfont"
date:   2019-08-10
tags:
      - it
---


# Table of Contents

1.  [设置gnome-terminal font](#org1a8c573)
2.  [key编码](#orgf2917e7)


<a id="org1a8c573"></a>

# 设置gnome-terminal font

    cp /mnt/d/Windows/Fonts/simsun.ttf ~/.fonts
    fc-cache
    
    sudo fc-cache -fv .fonts #(.fonts为你的字体目)
    
    fc-list
    gnome-terminal

screenshot

![img](https://imglf3.lf127.net/img/TE0ySjlkTnYxRnRCM05JTVlma1djV0ZSaEVSaEhvdk11b0pUeEp4cmZ5L2ZVRnoyUlRxOEJnPT0.png?=imageView&thumbnail=500x0&quality=96&stripmeta=0&type=jpg%7Cwatermark&type=2)


<a id="orgf2917e7"></a>

# key编码

<https://blog.csdn.net/cean1024/article/details/72903069>

第一，为什么在运行程序时，在终端按下箭头up down left right 箭头会出现
^{［A}［B^{［C}［D

这个跟换键码有关，"escape sequence"实际上是用来生成换码符的关键字的顺序。换码符会告诉打印机将不再打印后面的字符，但是还要将这些字符解释为一类打印机控制码或其他。需要 Escape Sequences 顺序的应用程序通常是早期的 DOS 应用程序、UNIX (R) 应用程序或非常特殊的应用程序。

up - "\\033[A" down - "\\033[B" left - "\\033[D" right - "\\033[C"

\\033 = 0x1b = 27 = ^[= Esc 键 ， 也就是说up down left right
这些键，每个都由三个字符组成。

读到键值为 读到27时，再读一个是'[' = 0x5B = 91, 然后再读一个键，若是'A'
= 0x41 = 65则表示向上的箭头。其他的类似。

