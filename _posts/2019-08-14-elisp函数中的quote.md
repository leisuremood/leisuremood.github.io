---
layout: post
title:  "elisp函数中的quote"
date:   2019-08-14
tags:
      - 随笔
---

# elisp函数中的quote


(defun dummy() 
   (list \'(\"first\" . 1) 
         \'(\"second\" .  2))) 
  
 如果认为在任何时候 (dummy) 都会返回 
 ((\"first\" . 1 ) (\"second\" . 2))  
 那你就错了。 
  
 看下面的例子 
 (let\* ((m (dummy)) 
        n
                                   ;; \-\-\--给n赋 一个nil值。
        (m1 (assoc \"first\" m))) 
   (setcdr m1 100) 
   (setq n (dummy)) 
   (message \"\\n%S %S %S\\n\" m m1 n)) 
  
n 的值居然是 ((\"first\" . 100) (\"second\" . 2)) 
  
 只有这样写 
 (defun dummy() 
   (list (cons \"first\"  1) 
         (cons \"second\"  2))) 
  
 (dummy) 才会永远返回 ((\"first\" . 1) (\"second\" . 2)) 
  
 这是一个有意思的现象。 
 估计是 elisp 为了优化，quote 产生的对象是返回的是一个类似与引用 
 的东西。 任何 side-effect 的 set\* 函数都可以修改这个一个值。 



那合理的解释是：函数的返回值跟函数本身可能根本就是一回事情。修改函数的返回值有可能导致函数本身被修改？



function is nothing more than a list. 
  
 所谓函数，本质上也是一个数据，就是 list
 (lambda () \'(1 2 4) 也就是一个简单的list  
 函数的返回值也就是 list 中的一部分的 evaluation. 
 只不过这个特殊的例子，eval 之后，得到的是函数本身的 
 一部分， \'(1 2 4) , 
  
 修改返回值，也就是修改原来 list （函数）中的一部分。 



Common Lisp 里也有同样的问题。Paul Graham 在《On
Lisp》一书里给出一个规则： 
 \"functions shouldn\'t return quoted
lists.\" 并且指出除了宏展开函数以外，其他 
 函数返回值中含有引用的列表都可能不安全。(具体位置在该书的 3.3 节) 
  
 下列书中的例子更加明显： 
  
 (defun exclaim (expression) 
   (append expression \'(oh my))) 
  
Then any later destructive modification of the return value
  
 > (exclaim \'(lions and tigers and bears)) 
 (LIONS AND TIGERS AND BEARS OH MY) 
 > (nconc
\* \'(goodness))   ;\-\--concatenate 连在一起,改变了函数中的\'(oh my)
 (LIONS AND TIGERS AND BEARS OH MY GOODNESS) 
  
could alter the list within the function: 
  
 > (exclaim \'(fixnums and bignums and floats)) 
 (FIXNUMS AND BIGNUMS AND FLOATS OH MY GOODNESS) 
  
To make exclaim proof against such problems, it should be written: 
  
 (defun exclaim (expression) 
   (append expression (list \'oh \'my))) 
  




