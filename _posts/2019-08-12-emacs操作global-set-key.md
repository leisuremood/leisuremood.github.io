---
layout: post
title:  emacs操作global-set-key
date:   2019-08-12
tags:
      - 随笔
---
::: txtcont
[Key Bind
Commands](https://www.masteringemacs.org/article/mastering-key-bindings-emacs)

There are several ways you can define (or undefine) keys, as the table
below shows, but in reality there are dozens of ways you can hack the
keymaps.

(define-key KEYMAP KEY DEF) 

Defines a key against a keyboard map. Use this if you want to change a
keymap that isn't the current buffer map.

(local-set-key KEY COMMAND) 

Binds a key to the local keymap used by the active buffer, unlike
define-key which takes an explicit keymap to bind a key against.

(global-set-key KEY COMMAND) 

Binds a key to the global keymap, making it available in all buffers
(with a caveat -- see below.)

(global-unset-key KEY) 

Removes KEY from the global keymap

(local-unset-key KEY) 

Removes KEY from the active, local keymap.

\

[global-set-key
干了什么](https://blog.csdn.net/rodneyzhaonet/article/details/44004549)

很多人绑定一些比较特殊的键的时候，都搞不清楚在\
\
(global-set-key \... \'my-funtion)\
\
里写些什么。特别是在 xterm
里的时候就更不知所措了。其实有一个万无一失的办法保证你一定写对。这个办法就是：\
\
    M-x global-set-key RET 交互式的绑定你的键。\
    C-x Esc Esc 调出上一条"复杂命令"。\
\
好了，你现在就能在 minibuffer 里看到你应该写在 .emacs 的东西了。\
如果你还是失败了......\
\
如果你在第1步的时候发现 Emacs
根本对你的按键没有反应，那么应该怀疑是你的窗口管理器拦截了这个按键。比如，我的
FVWM 设置把 C-f3 设定成了打开一个 FvwmCommand, 所以Emacs
接收不到这个按键。如果我要绑定一个函数到 C-f3, 我必须让 FVWM 放过
C-f3。\
绑定新的前缀键\
\
其实上面的办法只能让你绑定一个已有的前缀。你有可能想绑定一个 save-buffer
到 \"C-c C-w C-b a\"。上面的办法就不灵了。我们必须使用另外的办法：\
\
(global-set-key (kbd \"C-c C-w C-b a\") \'save-buffer) 

> C-c是一个已有的前缀\

一点解释\
\
上面的那个 \"C-c C-w C-b a\" 是自动把 \"C-c C-w\", \"C-c C-w
C-b\" 都定义成了一个 prefix-command. 你可以这样看到它们：\
\
    C-h C-b 显示绑定\
    C-x o 切换到显示绑定的窗口\
    C-x C-q 消除这个窗口的只读属性\
    M-x delete-non-matching-lines RET prefix RET
删除所有不含\"prefix\" 字样的行。\
\
现在你清楚的看到了 \"C-c C-w\", \"C-c C-w C-b\" 都是 prefixcommand 吧？

上面这个办法只对开头的键已经是 prefix command
的键序列起作用，如果你的第一个键不是一个
prefix，那么就会出错。你可以试试：\
\
(global-set-key (kbd \"C-z C-c C-w b\") \'find-file)\
\
出现错误：(error \"Key sequence C-z C-c C-w b uses invalidprefix
characters\")\
\
所以你必须事先把第一个键设定为 prefix:\
\
(define-prefix-command \'ctl-z-map)\
(global-set-key (kbd \"C-z\") \'ctl-z-map)\
\
然后再用\
\
(global-set-key (kbd \"C-z C-c C-w b\") \'find-file)\
\
就行了。\"C-z C-c\" 和 \"C-z C-c C-w\" 都会自动被定义为 prefixcommand.

[绑定中文命令](https://blog.csdn.net/rodneyzhaonet/article/details/44004549)\
\
现在我举一个例子来说明 prefix command
是如何工作的。我们可以把中文的 存盘 两个字绑定到save-buffer. 这样你用中文输入法敲入"存盘"两个字时，就可以把当前
buffer 保存起来。\
\
(define-prefix-command \'存-map)\
(global-set-key (kbd \"存\") \'存-map)\
(define-key 存-map (kbd \"盘\") \'save-buffer)\
\
有趣吧？你可以猜到这里面是怎么回事吧？太简单了是不是？当你输入"存"的时候，看到
minibuffer 是这样：\
\
![](https://docs.huihoo.com/homepage/shredderyin/images/bind-cun.png){style="max-width:500px;"}\
\
这是因为我们把"存"这个字绑定到了 存-map
这个prefix-command. 当读到"存"的时候，Emacs 就会等待下一条命令，这个命令是定义在 存-map 这个
map 里的。它读到"盘"，就会执行 save-buffer 了。\
\
不过注意，你真的要在文档里输入"存盘"两个字就得先打 C-q
了。刚才我就打了好多次
C-q，真累啊。还是用一些不常用的词组比较好，或者加一个 ctrl
什么的前缀，就像这个，\"C-z 存盘\"。\
\
(define-prefix-command \'ctl-z-map)\
(global-set-key (kbd \"C-z\") \'ctl-z-map)\
(define-key ctl-z-map (kbd \"存盘\") \'save-buffer)

define-key 会自动建立很多 prefix command. 不过自己显式用
define-prefix-command 定义前缀命令有一个好处，就是你可以在你的 prefix
里再方便的定义更多的命令，而不用把整个前缀都写一遍。\
\
(define-prefix-command \'ctl-z-map)\
(define-prefix-command \'存-map)\
(define-prefix-command \'盘-map)\
\
(global-set-key (kbd \"C-z\") \'ctl-z-map)\
(define-key ctl-z-map (kbd \"存\") \'存-map)\
(define-key 存-map (kbd \"盘\") \'盘-map)\
\
(define-key 盘-map (kbd \"！\") \'save-buffer)\
(define-key 盘-map (kbd \"到\") \'write-file)\
(define-key 盘-map (kbd \"退出\") \
  (lambda ()\
    (interactive)\
    (save-buffer)\
    (kill-emacs)))\
\
这样，到了"C-z 存盘-" 这个时候，我们定义了3个分支：\
\
    "C-z 存盘！"，表示保存这个文件；\
    "C-z 存盘到"，表示保存到另一个文件；\
    "C-z 存盘退出"，这个不用解释了吧。\
\
自定义 prefix command 的另外一个更大的好处就是：你可以修改最上层对
prefix command
的绑定，从而修改许多键的绑定。比如，我们可以把 "存盘" 轻而易举的改成 "保存"：\
\
(define-key ctl-z-map (kbd \"保存\") \'盘-map)\
\
这样一来， "C-z
保存！" ，"C-z 保存到" ， "C-z 保存退出"就分别有了 "C-z 存盘！" ， "C-z 存盘到" 和 "C-z 存盘退出" 的含义了。

\

[Custom
Prefixes](https://www.masteringemacs.org/article/mastering-key-bindings-emacs)

Purpose

Creating a prefix is easy nowadays as you don't have to explicitly
create your own prefix keymaps, provided you use local-set-key or
global-set-key. Use custom prefixes to group or categorize your
commands.

Definition(global-set-key (kbd \"subkey_1 \...
endkey_1\") \'my-command-1)(global-set-key (kbd \"subkey_1 \...
endkey_2\") \'my-command-2)Example

Global keys that will insert either the time of the day, or the current
date. Type C-c i d to insert the date; and C-c i t to insert the time.
Type C-c i C-h to list all bound keys under the C-c i prefix.

(defun mp-insert-date
()  (interactive)  (insert (format-time-string \"%x\")))(defun
mp-insert-time
()  (interactive)  (insert (format-time-string \"%X\")))(global-set-key (kbd \"C-c
i d\") \'mp-insert-date)(global-set-key (kbd \"C-c i
t\") \'mp-insert-time) 

\

\

 \

Emacs中\`global-set-key\`和\`define-key global-map\`有什么区别

global-set-key在subr.el中定义为：(define-key (current-global-map) key
command))

global-set-key是基于define-key的交互式功能

<https://www.jianshu.com/p/07996a1af7b0>\

按键绑定的可见范围分类

全局绑定 > 主模式（major-mode）绑定 > 副模式（minor-mode）绑定 > 局部绑定（在某个缓冲区中通过执行\"M-x\"所做的绑定）

在更小可见范围中定义的按键绑定将会覆盖掉在更大可见范围内已经定义的相同按键的绑定。

查看某个按键（序列）和哪些函数进行了绑定 

C-h c \[按键序列\]

查看某个函数和哪些按键（序列）进行了绑定 

C-h w \[函数名\] \<RET>

使用global-set-key和kbd给函数绑定全局按键 

常用用法如下：

(global-set-key (kbd\"C-z\") \'shell) ;; 将shell函数绑定到按键\"C-z\"上(global-set-key (kbd\"C-c
y\") \'clipboard-yank) ;; 将clipboard-yank函数绑定到按键\"C-c
y上\"(global-set-key
(kbd\"C-M-q\") \'query-replace)(global-set-key (kbd\"\<f5>\") \'flyspell-mode)(global-set-key (kbd\"C-\<f5>\") \'linum-mode)(global-set-key (kbd\"C-\<right>\") \'forward-sentence)使用add-hook给指定的主模式绑定按键 

使用add-hook可以对指定的主模式进行按键绑定，而不影响其他模式。大部分的主模式都定义了自己的至少一个hook，用法示例：

;; 仅在org-mode模式改变\"C-z\"的按键绑定，将其绑定为打开shell命令行的\"shell\"函数，注意这里要使用local-set-key(add-hook \'org-mode-hook      (lambda ()        (local-set-key (kbd\"C-z\") \'shell))) 

注：1. 给主模式绑定快捷键后，要重启主模式才能生效（kill掉对应的buffer然后重新打开）2. 查看当前emacs中的所有已定义的hook：M-x
apropos-variable \<RET> -hook \<RET>

使用define-key给指定的模式绑定按键 

使用define-key函数也可以给指定的模式绑定快捷键，前提是这个模式定义了按键map，所以第一件事就是要找到这个模式所定义的按键map。

以org-mode为例，怎么知道这个模式所定义的按键map是什么呢？非常简单，C-h f
\<RET> org-mode
\<RET>查看org-mode的帮助文档，就会出现org-mode代码所在的el文件，进入这个文件，搜索：-map，就会找到org-mode-map这个变量。

然后使用define-key来为org-mode定义一个简单的按键绑定，如下：

(defun my-org-func ()  (interactive)  (message\"hello,
org!\"))(define-key org-mode-map (kbd\"C-c d\") \'my-org-func) 

加载上述代码后，在org-mode中按C-c
d快捷键，就会执行my-org-func函数，显示出来一个字符串：hello, org!

 <https://jixiuf.github.io/blog/00005-emacs-keybind.html/>\

M-x global-set-key RET 交互式的绑定你的键

比如 我想绑定 Alt-Return 到 forward-char 这个命令\
M-x:global-set-key RET 然后 按下 Alt-Return
按键 ，然后输入forward-char 然后回来\
再然后 按下 C-x Esc Esc 就会在minibuffer 里显示出\

(global-set-key \[27 13\] (quote forward-char)) 

\

[Duplicating prefix keys](https://www.emacswiki.org/emacs/PrefixKey)

Since I'm a Windows user and use CUA, I copy C-x functionality onto the
\<C-lwindow> key. This way I can reach C-x prefixed functions
consistantly even when I've  got text selected. The same technique could
copy them to just about any other  available key.

  (global-set-key (kbd \"\<C-lwindow>\") (lookup-key global-map
(kbd \"C-x\"))) 

**NOTE** This only copies the PREFIX KEY not any followup key. Meaning
the C-x C-x function can now be reached using \<C-lwindow> C-x  but
unless C-x \<C-lwindow> is mapped to
something, \<C-lwindow> \<C-lwindow> won't be either.
:::
