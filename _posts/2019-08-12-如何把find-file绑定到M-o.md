---
layout: post
title:  "如何把find-file绑定到M-o"
date:   2019-08-12
tags:
      - 随笔
---


[一些有用的 Emacs
配置（窗口快速切换、一键透明效果、任意位置删除整行等）](https://www.cnblogs.com/yjie/p/4059145.html)   

上文提到的 （emacs操作 global-set-key）

> 以org-mode为例，怎么知道这个模式所定义的按键map是什么呢？非常简单，C-h
> f \<RET> org-mode
> \<RET>查看org-mode的帮助文档，就会出现org-mode代码所在的el文件，进入这个文件，搜索：-map，就会找到org-mode-map这个变量。

或者

C-x C-q 取消buffer的只读性质。 然后粘贴输入

(lookup-key global-map (kbd \"M-o\")) ;; 用C-x C-e 执行这一行。

> facemenu-keymap

(local-unset-key (kbd \"C-x p\"))

(define-key global-map \"M-o\" nil)

\^上面是错的。 直接就行。

;;(global-unset-key (kbd \"M-o\"))
(global-set-key (kbd \"M-o\") \'find-file)



