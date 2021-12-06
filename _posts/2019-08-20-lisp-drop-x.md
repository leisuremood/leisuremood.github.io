---
layout: post
title:  "从lisp的列表中删除一个元素"
date:   2019-08-20
tags:
      - it
---

http://cn.voidcc.com/question/p-vvrdqcpc-na.html

两个函数 有参考意义。

~~~ elisp
(defun list_member (x L)
    (cond ((null L) nil)    ;if list L is empty, return NIL
    ((equal x (car L)) T)   ;if element x is in L, return T
    (T (list_member x (cdr L))))) ;else, recursively check remainder of L

(defun rem_dup (L)
    (cond ((null L) nil)       ; if list L is empty, return NIL to user
     ((list_member (car L) (cdr L)) (rem_dup (cdr L))) ; Skip duplicate element
     (T (cons (car L) (rem_dup (cdr L)))))) ; else include it, and check rest of list recursively
~~~

emacs 里面可以

(print before-save-hook )  
(delete 'delete-trailing-whitespace before-save-hook )  
