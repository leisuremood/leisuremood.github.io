---
layout: post
title:  "use-package专题"
date:   2019-08-17
tags:
      - it
---

# Table of Contents

1.  [package的引入和管理](#orgf066cc1)
2.  [Eval after load](#org08c283c)
3.  [use-package manual](#orgc4d2ccf)
4.  [暴增 Emacs 生产力的十大最佳插件](#org294a027)
5.  [emacs操作 - 插件篇](#org35fdf01)
6.  [超简单emacs配置](#org6e7aa1b)
7.  [emacs配置](#org20ff9f8)
8.  [How to exclude files from Projectile?](#orgb28a563)



<a id="orgf066cc1"></a>

# [package的引入和管理](https://longlycode.github.io/post/emacs%E5%AE%8C%E5%85%A8%E8%A1%A5%E5%AE%8C%E8%AE%A1%E5%88%92-%E4%B8%89/)

Auto-loading 可以向emacs中注册一个函数，只有当调用或者使用这个函数的时候，包含这个函数的文件才会加载。这么用：

(autoload'some-function”some-file”)

当调用some-function 时，加载some-file.el，再执行这个函数。autoload完整参数：  
(autoload FUNCTION FILE &optional DOCSTRING INTERACTIVE TYPE)，  
可以看出它除了提供加载文件地址外，还可以编写说明文档，在不加载文件时也能够查看它的用法。

autoload这种方法可以写入的到emacs配置文件里面，但明显不好管理，为什么不在函数定义处就指明它是一个autoload形式的函数呢？事实上可以用所谓的”魔术”注释来装饰一个函数的开头，让它autoload。
`~` elisp ;;;###autoload (defun my-function ()  ;; Source code&#x2026;  )
`~`
;;;###autoload是一种神奇的注释。当然autoload这种机制不局限于函数，可以用在一切可以定义的东西上面，比如宏、主模式、次模式等等。

类似c++的虚函数，先在symbol的位置放上autoloader, 实际使用是动态载入实际的函数。？？


<a id="org08c283c"></a>

# Eval after load

当我们加载一个package的时候想配置它，比如绑定一个自定义的函数等等。为了能够让我们的自定义的代码也autoload，使用with-eval-after-load。

    (with-eval-after-load 'helm
     ;; Some-Code
     )

Use-package是一个第三方的package，使用前需要安装。Use-package可以说是为了解决之前提到的所有问题而出现的。Use-package是一个宏，来看看它是怎么使用的：

(use-package helm :defer t))

:defer是个关键字，如果为t，则表示helm的里面的凡是被autoload的函数、宏、模式等都成立。
`~` elisp (use-package helm  :defer t  :init  ;; Code to execute
before Helm is loaded  :config  ;; Code to execute after Helm is
loaded  ) `~`
关键字:init和:config，顾名思义，针对package加载之前和package加载之后添加一些自己的配置。在:init中可以加载一些入口设定，比如定义一些按键调用这个package里面的函数。:config关键字后面的代码和with-eval-after-load功用是一样的，都是package实际被加载之后才会执行。use-package还有很多关键字，比如指定某些条件下才会加载package，可以指定某些特定文件才加载这个package，可以进[官方文档](https://github.com/jwiegley/use-package)自行查看。


<a id="orgc4d2ccf"></a>

# [use-package manual](https://phenix3443.github.io/notebook/emacs/modes/use-package-manual.html#org2b8768b)

:init 关键字设置 package 加载前执行的代码，可用来设置影响 package 加载过程的变量。 即使配置的 package 不存在， :init 部分的代码也会执行，所以该部分代码应该保证 package 不存在也可以执行成功。

:config 关键字设置 package 加载后执行的代码，主要用来对 package 进行配置。在 package 自动加载（autoload）的情况下，这些代码直到加载时才会执行。

:ensure t  ;;表示一定要安装 

:demand ;;强制性加载。

use-package渐渐一统以前古老的配置方式，许多知名的emacser用这个包将自己的配置又重新写了一遍。因为它既包含了现有的所有定义方式又提供了许多关键字，让配置一个package简单易行，而且可以将所有的包都变成了延时加载，只有使用的时候再加载，大大加速了emacs的启动速度。懂得以上所有的emacs加载机制后，基本上自己动手配置一个emacs配置不成问题。

[自定义Emacs时,在加载包之前或之后是否设置相应的变量？](https://codeday.me/bug/20190520/1139996.html)

`~` elisp (setq some-variable some-value) (require 'some-package) `~`
要么
`~` elisp (require 'some-package) (setq some-variable some-value) `~`

在我看来,除非某些软件包在加载后明确要求init,否则大部分时间都可以工作.对吗？
如果变量是用户选项,请在加载包之前执行此操作.该软件包包含一个定义选项的defcustom,但如果变量已经有一个值(例如,来自你的setq),defcustom将不会设置该值.

类似地,如果变量是不是用户选项的全局定义变量,即由包使用defvar定义.与defcustom一样,defvar不会覆盖现有值.

:command 关键字会为命令创建自动加载（autoload），他的参数是 symbol 或 symbol 列表。

:hook 关键字允许将函数添加到钩子上，这里只需要钩子的基本名称。因此，以下所有内容都是等同的：
`~` elisp (use-package ace-jump-mode  :hook prog-mode) ;然而经过测试这种写法并没有生效

(use-package ace-jump-mode  :hook (prog-mode . ace-jump-mode))

(use-package ace-jump-mode  :commands ace-jump-mode  :init  (add-hook
'prog-mode-hook #'ace-jump-mode)) `~`

同样，如果命令添加到多个钩子，以下内容也是等同的：

    (use-package ace-jump-mode
     :hook (prog-mode text-mode)) ;没有生效
    
    (use-package ace-jump-mode
     :hook ((prog-mode text-mode) . ace-jump-mode))
    
    (use-package ace-jump-mode
     :hook ((prog-mode . ace-jump-mode)
     (text-mode . ace-jump-mode)))
    
    (use-package ace-jump-mode
     :commands ace-jump-mode
     :init
     (add-hook 'prog-mode-hook #'ace-jump-mode)
     (add-hook 'text-mode-hook #'ace-jump-mode))

:bind 关键字用来设置键绑定：

(use-packageace-jump-mode:bind ("C-." . ace-jump-mode)) 

这会做两件事情：

-   首先,为 ace-jump-mode 命令创建自动加载。

-   其次，将 C-. 绑定到该命令。加载之后，可以使用 M-x
    describe-personal-keybindings 查看.emacs 文件中设置的所有键绑定。
    
    :bind 作用基本等同于以下代码：

    (use-package ace-jump-mode
     :commands ace-jump-mode
     :init
     (bind-key "C-." 'ace-jump-mode))

此外， :bind 的配置可以是 cons 或 cons 的列表：

    (use-package color-moccur
     :commands (isearch-moccur isearch-all)
     :bind (("M-s O" . moccur)
     :map isearch-mode-map
     ("M-o" . isearch-moccur)
     ("M-O" . isearch-moccur-all))
     :init
     (setq isearch-lazy-highlight t)
     :config
     (use-package moccur-edit))

本例中，来自 color-moccur.el 的 isearch-moccur 和 isearch-all
会延迟加载（autoload），并在全局和 isearch-mode-map 中进行了键绑定。当该
package
真正加载时（通过使用其中一个命令），moccur-edit 也会加载，以允许编辑
moccur buffer。

如果 pacakge 的加载需要将一个目录添加到 load-path
中，使用 :load-path 。参数可以是一个符号、函数、字符串或字符串列表。如果是相对路径，基于
user-emacs-directory 进行扩展。

    (use-package ess-site
     :load-path "site-lisp/ess/lisp/"
     :commands R)

注意，如果使用一个符号或函数来动态生成路径列表，必须通知字节编译器该定义，以保证该值在编译时可用。这通过使用特殊形式（special
form）eval-and-compile (而不是
eval-when-compile)实现。进一步说，该值固定为编译期间确定的值，避免在每次启动时再次查找相同的信息：

    (eval-and-compile
     (defun ess-site-load-path ()
     (shell-command "find \~ -path ess/lisp")))
    
    (use-package ess-site
     :load-path (lambda () (list (ess-site-load-path)))
     :commands R)

在.emacs 中声明的 package
都会自动下载。 :ensure 关键字会自动安装系统没有的 package。

(use-packagemagit:ensure t)

如果希望所有包 package 都这样处理，需要设置

(setq use-package-always-ensure t)

可以这样指定 安装 package 的别名：

(use-packagetex-site:ensure auctex)

:ensure 会安装系统上没有的
package，但不会进行主动更新。如果希望主动更新，可以使用
auto-package-update，如 `~` elisp (use-package auto-package-update
 :config  (setq auto-package-update-delete-old-versions t)  (setq
auto-package-update-hide-results t)  (auto-package-update-maybe)) `~`
24.4 或更高版本，use-package 可以将一个包 pin 到特定的
archive，允许混合和匹配来自不同 archive 的 package。主要使用场景是从 gnu
和 melpa-stable 选择 package（混合选择）

    (use-package company
     :ensure t
     :pin melpa-stable)
    
    (use-package evil
     :ensure t)
     ;; no :pin needed, as package.el will choose the version in melpa
    
    (use-package adaptive-wrap
     :ensure t
     ;; as this package is available only in the gnu archive, this is
     ;; technically not needed, but it helps to highlight where it
     ;; comes from
     :pin gnu)
    
    (use-package org
     :ensure t
     ;; ignore org-mode from upstream and use a manually installed
    version
     :pin manual)

通常 :bind 绑定的命令是 package
中自动加载的函数。然而，如果命令实际上是键映射（keymap）的话，这就有所不同了，因为键映射不是函数，不能使用
Emacs 的自动加载机制进行自动加载。

为了处理这种情况，use-package 提供了 :bind-keymap ，它是 :bind 一个特殊受限的变体。 二者之间的唯一区别是： :bind-keymap 绑定的命令必须是
package
中定义的键映射，而不是命令函数。这通过生成一段定制代码来处理：这段代码首先加载含有键映射的
package，加载之后再次执行后续的按键，也就说，将 :bind-keymap
绑定的按键解释为一个前缀键来处理。

    (use-package projectile
     :bind-keymap
     ("C-c p" . projectile-command-map))

C-h c C-c p 可以查看到 C-c p 绑定到了 lambda 函数。

类似 :bind ,可以使用 :mode 和 :interpreter 在变量 auto-mode-alist 和
interpreter-mode-alist 中建立延迟绑定。这些关键字的参数可以是
cons、 列表、字符串或正则表达式。

    (use-package ruby-mode
     :mode "\\.rb\\'"
     :interpreter "ruby")
    
    ;; The package is "python" but the mode is "python-mode":
    (use-package python
     :mode ("\\.py\\'" . python-mode)
     :interpreter ("python" . python-mode))

如果没有使用 :commands, :bind, :bind\*, :bind-keymap, :bind-keymap\*, :mode,
or :interpreter （这些都意味着 :defer ; 参阅 use-package
中的文档对每个都简单了解一下），也可以使用 :defer 关键字实现延迟加载：

    (use-package ace-jump-mode
     :defer t
     :init
     (autoload 'ace-jump-mode "ace-jump-mode" nil t)
     (bind-key "C-." 'ace-jump-mode))
    
    这等同于：
    
    (use-package ace-jump-mode
     :bind ("C-." . ace-jump-mode))

即使使用 :bind
，使用 :demand 也会强制立即加载，不会为绑定键建立任何自动加载。

有时，只有在另一个 package 加载之后，加载当前 package
才有意义，因为有些变量或函数此前不在作用域内。这可以通过 :after 关键字来实现，该关键字允许设置相当丰富的加载条件。这里有一个例子：

    (use-package hydra
     :load-path "site-lisp/hydra")
    
    (use-package ivy
     :load-path "site-lisp/swiper")
    
    (use-package ivy-hydra
     :after (ivy hydra))

这种情况下，所有的 package
都按照它们出现的顺序进行了加载，使用 :after 关键字不是绝对必要的。但是，通过使用它，上面的代码可以变得与顺序无关，init 文件本质上隐含了这种顺序依赖关系。

使用 :defines 和 :functions 关键字只为字节编译器引入虚拟变量和函数声明。

    (use-package texinfo
     :defines texinfo-section-list
     :commands texinfo-mode
     :init
     (add-to-list 'auto-mode-alist '("\\.texi\$" . texinfo-mode)))

如果需要消除一个缺失函数的警告，可以使用 :function :

    (use-package ruby-mode
     :mode "\\.rb\\'"
     :interpreter "ruby"
     :functions inf-ruby-keys
     :config
     (defun my-ruby-mode-hook ()
     (require 'inf-ruby)
     (inf-ruby-keys))
    
     (add-hook 'ruby-mode-hook 'my-ruby-mode-hook))

如果安装 diminsh 和 delight，use-package
内置对它们的支持。它的目的是删除或更改 mode line 中的字符串。

通过 :diminish 关键字调用 diminish，该关键字需要一个 minor mode symbol
或符号组成的 cons
以及替代字符串；也可以仅是替代符串，这种情况下，通过末尾带有”-mode”的
package 名猜 minor modes symbol。

    (use-package abbrev
     :diminish abbrev-mode
     :config
     (if (file-exists-p abbrev-file-name)
     (quietly-read-abbrev-file)))

通过 :delight 关键字调用 delight，该关键字需要 minor mode symbol
和替换字符串，或带引号的 mode-line
数据（这种情况下，通过末尾带有”-mode”的 package 名猜 minor modes
symbol）。这两种用法，或两种用法组成的
list，都可以作为关键字的参数。如果没有提供参数，默认该模式名字完全隐藏。

    ;; Don't show anything for rainbow-mode.
    (use-package rainbow-mode
    :delight)
    
    ;; Don't show anything for auto-revert-mode, which doesn't match
    ;; its package name.
    (use-package autorevert
    :delight auto-revert-mode)
    
    ;; Remove the mode name for projectile-mode, but show the project
    name.
    (use-package projectile
    :delight '(:eval (concat " " (projectile-project-name))))
    
    ;; Completely hide visual-line-mode and change auto-fill-mode to "
    AF".
    (use-package emacs
    :delight
    (auto-fill-function " AF")
    (visual-line-mode))

[一名Clojurian的Emacs配置](https://www.jianshu.com/p/eba1f0e892e4)

    (use-package clojure-mode 
      :config  
      (add-hook 'clojure-mode-hook #'rainbow-delimiters-mode)
      (add-hook 'clojure-mode-hook #'subword-mode)  
      (add-hook 'clojure-mode-hook #'midje-mode)  
      (add-hook 'clojure-mode-hook #'my-clj-refactor-mode-hook)    
      (add-hook 'clojure-mode-hook #'enable-paredit-mode))

\#' 是一个读取宏 (read-macro)，功能上相当于
symbol-function，也就是取出一个符号在函数名字空间里的值，也就是以那个符号为名字的函数对象。 


<a id="org294a027"></a>

# [暴增 Emacs 生产力的十大最佳插件](https://www.imooc.com/article/251525)

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


<a id="org35fdf01"></a>

# [emacs操作 - 插件篇](https://www.jianshu.com/p/ab585e1ef365)

使用emacs默认软件源安装软件速度比较慢，国内的用户一般需要替换软件源，这里我将软件源替换为清华镜像。

    (require'package)
    (setq package-enable-at-startup nil)
    (setq package-archives '(("gnu" . "https://mirrors.tuna.tsinghua.edu.cn/elpa/gnu/") ("melpa" . "https://mirrors.tuna.tsinghua.edu.cn/elpa/melpa/")))
    (package-initialize) 


<a id="org6e7aa1b"></a>

# [超简单emacs配置](https://www.jianshu.com/p/250301ebb7f9)

    (use-package company
      :ensure t
      :bind (("C-c /". company-complete))
      :diminish company-mode
      :commands company-mode
      :init  (setq company-dabbrev-ignore-case nil
    	       company-dabbrev-code-ignore-case nil
    	       company-dabbrev-downcase nil
    	       company-idle-delay 0  
    	       company-minimum-prefix-length 4)
      :config  ;; disables TAB in ; commentmpany-mode, freeing it for yasnippet  (global-company-mode)
      (define-key company-active-map [tab] nil)
      (define-key company-active-map (kbd "TAB") nil))


<a id="org20ff9f8"></a>

# [emacs配置](https://blog.csdn.net/xh_acmagic/article/details/78939246)

复杂配置， 参考了doom-emacs

projectile-globally-ignored-file-suffixes '(".elc"".pyc"".o")


<a id="orgb28a563"></a>

# [How to exclude files from Projectile?](https://emacs.stackexchange.com/questions/16497/how-to-exclude-files-from-projectile)

亭麻烦的。

[projectile-globally-ignored-directories
not working with native indexing.](https://github.com/bbatsov/projectile/issues/1250)

[\`projectile-globally-ignored-directories' only ignores top level directory.](https://github.com/bbatsov/projectile/issues/1119#)

[Emacs远程开发及projectile安装](https://blog.csdn.net/u010164190/article/details/79578994)

简单的projectile配置

[專案管理 : Projectile](https://ithelp.ithome.com.tw/articles/10201149) 

    (use-package projectile
      :ensure t
      :bind-keymap ("\C-c p" . projectile-command-map)
      :config
      (projectile-mode t)
      (setq projectile-completion-system 'ivy)
      (use-package counsel-projectile :ensure t)  )
    
    (use-package ag  :ensure t) 

我改的：

    (use-package projectile
      :ensure t
      :diminish projectile-mode
      ;;即不让projectile-mode显示在modeline上?
      :bind-keymap ("\C-p" . projectile-command-map) ;;default is?.
      :init
      (setq projectile-use-git-grep t
    	projectile-require-project-root nil) ; make projectile usable for every directory  :config
      (projectile-mode 1)  ;(projectile-global-mode) ;;obsolete
      (setq projectile-cache-file (concat mage-cache-dir "projectile.cache")   
    	projectile-enable-caching t
    	projectile-indexing-method 'alien
    	projectile-known-projects-file (concat mage-cache-dir "projectile.projects")
    	projectile-globally-ignored-file-suffixes '(".elc" ".pyc" ".o" ".Po")
    	;;projectile-globally-ignored-directories '(".deps")
    	;;By default Projectile uses Ido for completion. https://stackoverflow.com/questions/54043789/how-can-i-get-emacs-projectile-to-operate-like-vscodes-c-p
    	;;(Check the value of the variable projectile-completion-system to see if this has been configured differently in your Emacs.)  
    	projectile-completion-system 'helm ;; 经常用ivy， https://www.jianshu.com/p/250301ebb7f9  )
    	(append '(".deps" "tmp" "log" "temp" "obj" "build" "Library" "StreamingAssets") projectile-globally-ignored-directories)
    	(use-package helm-projectile :ensure t))

现需要安装git， <https://git-scm.com/download/win>

因为我们打开了projectile-use-git-grep 。

[emacs中projectile快捷键](https://blog.sina.com.cn/s/blog_822426570102vmzp.html)

[Exploring large projects with Projectile and Helm Projectile](https://tuhdo.github.io/helm-projectile.html)

helm projectile 配合 小demo动画。

[use-package, 使用use-package简化你的.emacs](https://www.helplib.com/GitHub/article_85425)

use-package 的使用方法。

[use-package manual](https://phenix3443.github.io/notebook/emacs/modes/use-package-manual.html)

[My Emacs
Configuration](https://www.pengmeiyu.com/blog/my-emacs-configuration/)

一些例子, 包括使用git

\`M-x helm-find-file\`(已绑定为\`C-x C-f\`)  search files
 **\* \`C-f\` 返回到上级目录  \*** \`C-j\` 进入目录 \`M-x
helm-M-x\`(已绑定为\`M-x\`)  enhanced M-x command \`M-x helm-recentf\` 
search for recently edited files \`M-x helm-mini\` 集成了buffer 和 recentf
\`M-x helm-buffers-list\` 列出缓冲区文件

[helm使用总结](https://blog.csdn.net/hedu135790/article/details/16851077)

(define-key helm-map (kbd "<tab>") 'helm-execute-persistent-action) 
 (define-key helm-map
(kbd "C-i") 'helm-execute-persistent-action)   (define-key
helm-map (kbd "C-z") 'helm-select-action)  \`\`\` 

这几个命令让helm更适合我们的操作，例如查找文件时直接tab进行文件名、路径补齐。

> 但是 没有 helm-execute-persistent-action 啊！！
> 
> 用空格 接着输入关键字

[如何在目录内更改emacs helm-find-file默认操作而不是在dired中打开？](https://codeday.me/bug/20181229/476966.html)

[Emacs Helm: 使用关键字搜索、获取、执行任何东西](https://www.cnblogs.com/astropeak/p/6219857.html)

例如：

M-x package 这时输入空格 可以接着输入关键字 list， candidates
可以进一步narrowing。

[helm使用总结](https://blog.csdn.net/hedu135790/article/details/16851077)

