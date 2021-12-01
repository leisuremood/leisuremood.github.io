---
layout: post
title:  "helm合use-package合projectile"
date:   2019-08-12
tags:
      - 随笔
---

#helm合use-package合projectile


[暴增 Emacs 生产力的十大最佳插件](https://www.imooc.com/article/251525)

1.  Ido-mode

2.  smex

3.  auto-complete

4.  YASnippet

5.  org-mode

6.  helm

7.  ace-jump-mode

8.  find-file-in-project

9.  flymake

10. electric-pair

[emacs操作 - 插件篇](https://www.jianshu.com/p/ab585e1ef365)

使用emacs默认软件源安装软件速度比较慢，国内的用户一般需要替换软件源，这里我将软件源替换为清华镜像。

(require\'package)(setq package-enable-at-startup nil)(setq
package-archives
\'((\"gnu\"   . \"https://mirrors.tuna.tsinghua.edu.cn/elpa/gnu/\")                         (\"melpa\" . \"https://mirrors.tuna.tsinghua.edu.cn/elpa/melpa/\")))(package-initialize)  

[超简单emacs配置](https://www.jianshu.com/p/250301ebb7f9)

(use-package company
  :ensure t
  :bind ((\"C-c /\". company-complete))
  :diminish company-mode
  :commands company-mode
  :init
  (setq
   company-dabbrev-ignore-case nil
   company-dabbrev-code-ignore-case nil
   company-dabbrev-downcase nil
   company-idle-delay 0
   company-minimum-prefix-length 4)
  :config
  ;; disables TAB in company-mode, freeing it for yasnippet
  (global-company-mode)
  (define-key company-active-map \[tab\] nil)
  (define-key company-active-map (kbd \"TAB\") nil))

[emacs配置](https://blog.csdn.net/xh_acmagic/article/details/78939246)

复杂配置， 参考了doom-emacs

projectile-globally-ignored-file-suffixes \'(\".elc\"\".pyc\"\".o\")

[How to exclude files from
Projectile?](https://emacs.stackexchange.com/questions/16497/how-to-exclude-files-from-projectile)

亭麻烦的。

[projectile-globally-ignored-directories not working with native
indexing.](https://github.com/bbatsov/projectile/issues/1250)

[\`projectile-globally-ignored-directories\' only ignores top level
directory.](https://github.com/bbatsov/projectile/issues/1119#)

[Emacs远程开发及projectile安装](https://blog.csdn.net/u010164190/article/details/79578994)

简单的projectile配置

[專案管理 :
Projectile](https://ithelp.ithome.com.tw/articles/10201149)  

(use-package projectile
  :ensure t
  :bind-keymap (\"\\C-c p\" . projectile-command-map)
  :config
  (projectile-mode t)
  (setq projectile-completion-system \'ivy)
  (use-package counsel-projectile
    :ensure t)
  )

(use-package ag
  :ensure t)                  

我改的：

> (use-package projectile
>   :ensure t
>   :diminish projectile-mode ;;即不让projectile-mode显示在modeline上?
>   :bind-keymap (\"\\C-p\" . projectile-command-map)  ;;default is?.
>   :init (setq projectile-use-git-grep t
>           projectile-require-project-root nil) ; make projectile
> usable for every directory
>   :config
>   (projectile-mode 1)
>   ;(projectile-global-mode) ;;obsolete
>   (setq ;projectile-cache-file (concat mage-cache-dir
> \"projectile.cache\") 
>     projectile-enable-caching t
>     projectile-indexing-method \'alien
>     ;projectile-known-projects-file (concat mage-cache-dir
> \"projectile.projects\")
>     projectile-globally-ignored-file-suffixes
> \'(\".elc\" \".pyc\" \".o\" \".Po\")
>     ;;projectile-globally-ignored-directories \'(\".deps\")
>     ;;By default Projectile uses Ido for completion.
> https://stackoverflow.com/questions/54043789/how-can-i-get-emacs-projectile-to-operate-like-vscodes-c-p
>     ;;(Check the value of the variable projectile-completion-system to
> see if this has been configured differently in your Emacs.)
>     projectile-completion-system \'helm ;; 经常用ivy，
> https://www.jianshu.com/p/250301ebb7f9
>     )
>   (append
> \'(\".deps\" \"tmp\" \"log\" \"temp\" \"obj\" \"build\" \"Library\" \"StreamingAssets\")
> projectile-globally-ignored-directories)
>   (use-package helm-projectile :ensure t))
> 
> 
> 

现需要安装git， <https://git-scm.com/download/win>

因为我们打开了projectile-use-git-grep 。

[emacs中projectile快捷键](https://blog.sina.com.cn/s/blog_822426570102vmzp.html)

[Exploring large projects with Projectile and Helm
Projectile](https://tuhdo.github.io/helm-projectile.html)

helm projectile 配合 小demo动画。

[use-package, 使用use-package简化你的.emacs](https://www.helplib.com/GitHub/article_85425)

use-package 的使用方法。

[use-package
manual](https://phenix3443.github.io/notebook/emacs/modes/use-package-manual.html)

[My Emacs
Configuration](https://www.pengmeiyu.com/blog/my-emacs-configuration/)

一些例子, 包括使用git



\`M-x helm-find-file\`(已绑定为\`C-x C-f\`)  search files
  \*\* \`C-f\` 返回到上级目录
  \*\* \`C-j\` 进入目录
\`M-x helm-M-x\`(已绑定为\`M-x\`)  enhanced M-x command
\`M-x helm-recentf\`  search for recently edited files
\`M-x helm-mini\` 集成了buffer 和 recentf
\`M-x helm-buffers-list\` 列出缓冲区文件

 [helm使用总结](https://blog.csdn.net/hedu135790/article/details/16851077)

(define-key helm-map
(kbd \"\<tab>\") \'helm-execute-persistent-action)    
 (define-key helm-map
(kbd \"C-i\") \'helm-execute-persistent-action)        
 (define-key helm-map (kbd \"C-z\") \'helm-select-action)
 \`\`\` 

这几个命令让helm更适合我们的操作，例如查找文件时直接tab进行文件名、路径补齐。

> 但是 没有 helm-execute-persistent-action 啊！！
>
> 用空格 接着输入关键字

[如何在目录内更改emacs
helm-find-file默认操作而不是在dired中打开？](https://codeday.me/bug/20181229/476966.html)



[Emacs
Helm: 使用关键字搜索、获取、执行任何东西](https://www.cnblogs.com/astropeak/p/6219857.html)

例如：

M-x package 这时输入空格 可以接着输入关键字 list， candidates
可以进一步narrowing。



[helm使用总结](https://blog.csdn.net/hedu135790/article/details/16851077)



