---
layout: post
title:  "C++14的returntypededuction&"
date:   2015-01-26
tags:
      - it
---


https://www.newsmth.net/nForum/#!article/CPlusPlus/374960

若干年前实现了一个 C++
序列化库，以前一直纠结于一种优化无法（自动）实现： 

struct A { int a1, a2; }; 

struct B { int b1, b2; }; 

struct C { A ca; B cb; }; 

struct D { std::set d; }; 

DATA_IO_LOAD_SAVE_E(A, &a1 &a2) // 序列化 A 的 a1, a2 成员，下同 

DATA_IO_LOAD_SAVE_E(B, &b1 &b2) 

DATA_IO_LOAD_SAVE_E(C, &ca &cb) 

DATA_IO_LOAD_SAVE_E(D, &d)      // stl 容器也可以序列化 

  

如果使用 NativeDataInput/Output, 只能自动推断出 A, B 可以直接  

memcpy，无法推断出 C 也可以 memcpy，当然，任何情况下 D 肯定都是 

无法 memcpy 的 

  

现在有了 return type deduction, C memcpy 的问题就解决了 

  

参考链接： https://www.nfabo.cn/p/?p=65 

  



