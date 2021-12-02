---
layout: post
title:  "emacsggtagsglobal"
date:   2019-08-14
tags:
      - it
---


[Mac上emacs使用ggtags](https://blog.csdn.net/u010164190/article/details/79520054)

[global, ggtags](https://www.jianshu.com/p/652b384589cf)

执行 gtags 命令。可以看到生成了3个文件：GPATH, GRTAGS, GTAGS。执行 ls
-alh命令，查看生成的文件大小.

<https://www.cnblogs.com/instinct-em/p/10628709.html>

<https://www.newsmth.net/nForum/#!article/Emacs/63629>



看到很多人在讨论 ecb,
etags, \... ，后来发现其实不是很好用.最近我发现一个东西叫 GNU Global

如果使用cygwin，则解压global的zip包到/usr/local下边。如果不用cygwin的话，只要复制global中bin目录的内容到系统路径就可以了，比如c:/windows，不过这样就无法方便的阅读manual了。

<https://www.gnu.org/software/global/download.html>

解压出来，相应路径放到windows的PATH里面。让终端可执行global



让emacs缺省使用ggtags, 而不是etags.

(ggtags-mode 1) 要hook到c-common-hook中才能生效，好像。

> (use-package ggtags
>   :config (ggtags-mode 1) ;;好像没啥用, 必须加到
> c-mode-common-hook里面
>   )
>
> 

> ;; https://tuhdo.github.io/c-ide.html
> (use-package ggtags
>   :ensure t
>   :bind (:map ggtags-mode-map
>   (\"C-c g s\" . \'ggtags-find-other-symbol)
>   (\"C-c g h\" . \'ggtags-view-tag-history)
>   (\"C-c g r\" . \'ggtags-find-reference)
>   (\"C-c g f\" . \'ggtags-find-file)
>   (\"C-c g c\" . \'ggtags-create-tags)
>   (\"C-c g u\" . \'ggtags-update-tags))
>   :config  ;;(ggtags-mode 1) 好像没啥用, 必须加到
> c-mode-common-hook里面
>   (add-hook \'c-mode-common-hook
>         (lambda ()
>           (when
> (derived-mode-p \'c-mode \'c++-mode \'java-mode \'asm-mode)
>         (ggtags-mode 1)))))
> 
> 

