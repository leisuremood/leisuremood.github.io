---
layout: post
title:  "Rust的borrow checke及生命周期"
date:   2022-06-17
tags:
      - it
---


# Table of Contents

1.  [the NLL borrow checker](#org1a0d293)
2.  [lifetime](#org75b33c5)


<a id="org1a0d293"></a>

# the NLL borrow checker

the NLL borrow checker finally got fully enabled by default <https://jackh726.github.io/rust/2022/06/10/nll-stabilization.html>

有3中checker

-   AST borrowck, (已弃)
-   MIR borrowck,
-   and polonius (well, in progress).

简单来说就是现在的错误提示信息更加准确了。


<a id="org75b33c5"></a>

# lifetime


## lifetime标注问题

<https://rustcc.cn/article?id=566ae19b-a476-42be-8194-3c98e6b38dd5>

还有这标注。

    pub fn live_regions<'a>(
        &'a self,
        live_bits: BitSlice<'a>,   //@ BitSlice 是 内部有引用成员。 &'a [Word] 。
    ) -> impl Iterator<Item = repr::RegionName> + 'a {
        self.regions_set(live_bits).into_iter()
    }

网友的提问回答都没有到点子上，为什么返回的 **impl Iterator<Item = repr::RegionName> + 'a** ，这里类型好像并没有引用，为什么要标注'a?
原因是impl Iterator表示返回的是实现了trait的末个类，它有可能是带引用的，如果带引用，那么生命周期i依赖与'a. 我是这样理解。（好像也不对。如果RegionName带引用，应该写成RegionName<'a>）

    struct my<'a> {
        str: &'a repr::RegionName,
    }
    impl Iterator for my {
        type Item = repr::RegionName;
        fn next(&self) {
    	self.str;
        }
    }

网页里提到一个palyground:直接Item用的是i32.

    #![allow(unused)]
    #![allow(clippy::redundant_clone)]
    
    use std::collections::BTreeSet;
    
    struct A;
    struct B<'b>(&'b [i32]);
    
    impl A {
        fn iter(&self, b: B) -> impl Iterator<Item = i32> { self.inner(b).into_iter() }
    
        fn iter_over_constrained<'a>(&'a self, b: B<'a>) -> impl Iterator<Item = i32> + 'a {
    	self.inner(b).into_iter()
        }
    
        fn inner(&self, b: B) -> BTreeSet<i32> { b.0.iter().cloned().collect() }
    
        fn use_mut(&mut self) {}
    }
    
    fn main() {
        let (mut a, v) = (A, vec![1, 2, 3]);
    
        {
    	let v = v.clone();
    	let b = B(&v);
    	let mut iter = a.iter(b);
    	iter.next();
    	drop(iter);
    	drop(v);
        }
        {
    	let v = v.clone();
    	let b = B(&v);
    	let mut iter = a.iter_over_constrained(b);
    	iter.next();
    	drop(iter);
    	drop(v);
        }
    
        {
    	let v = v.clone();
    	let b = B(&v);
    	let mut iter = a.iter(b);
    	iter.next();
    	drop(v);
    	drop(iter);
        }
        {
    	let v = v.clone();
    	let b = B(&v);
    	let mut iter = a.iter_over_constrained(b);
    	iter.next();
    	// drop(v); // Error: cannot move out of `v` because it is borrowed
    	drop(iter);
        }
    
        {
    	let v = v.clone();
    	let b = B(&v);
    	let mut iter = a.iter(b);
    	iter.next();
    	a.use_mut();
    	iter.next();
        }
        {
    	let v = v.clone();
    	let b = B(&v);
    	let mut iter = a.iter_over_constrained(b);
    	iter.next();
    	// a.use_mut(); // cannot borrow `a` as mutable because it is also borrowed as immutable
    	iter.next();
        }
    }  

**结论** 'a是一个纽带，把两个东西关联起来。

