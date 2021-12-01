---
layout: post
title:  "lisp谓词eq,eql,equal"
date:   2019-08-14
tags:
      - 随笔
---

# lisp谓词eq,eql,equal


真正严谨的是 Common Lisp，CL 里有三种通用的等价谓词：eq, eql, equal
CL-USER> (eq (cons 1 1) \'(1 . 1)) 
NIL
CL-USER> (eql (cons 1 1) \'(1 . 1)) 
NIL
CL-USER> (equal (cons 1 1) \'(1 . 1)) 
T
你觉得我的理解有错误，或者不准确？ 

eq 的含义是指 两个对象是否存在于同一个内存区域。 
equal 的含义是指 两个对象类型相同，而且两个对象看起来一样。
eql 是什么，是 
    如果两个对象都是 number ，那么就用 equal 否则就用 eq   
The eql predicate is true if its arguments are eq, or if they are
numbers of the same type with the same value, or if they are character
objects that represent the same character. For example:   

除了整数以外，其他都是指针。 
 因此 (eq 1 1) 返回 t , (eq 1.0 1.0) 返回 nil  
  
 \"eq 是比较指针， equal 是比较内容。\" 
 简化成这样，就很好理解，利于人脑记忆了。 

elisp 对象的内容包含两部分，一部分表示类型，另一部分表示值， 
 因此内容相同，首先类型要相同，其次值相同。 
 这样(equal 1 1.0) 就返回 nil , 就就不让人很吃惊了 



> 用eq表示我想比较两个指针 
>  用eql表示我想比较两个相同基本类型的值 
>  用equal表示我想比较两个相同复杂类型的值 
>
> 

我觉得你应该看看 CLTL2 中对于这三种等价谓词的精确定义和举例，对于理解
elisp 里的相同特性应该有一定指导作用： 
  
 \[Function\] 
eq x y
  
 (eq x y) is true if and only if x and y are the same identical
object. (Implementationally, x and y are usually eq if and only if they
address the same identical memory location.) 
  
It should be noted that things that print the same are not necessarily
eq to each other. Symbols with the same print name usually are eq to
each other because of the use of the intern function. However, numbers
with the same value need not be eq, and two similar lists are usually
not eq. For example: 
  
 (eq \'a \'b) is false.  
 (eq \'a \'a) is true.  
 (eq 3 3) might be true or false, depending on the implementation.  
 (eq 3 3.0) is false.  
 (eq 3.0 3.0) might be true or false, depending on the
implementation.  
 (eq #c(3 -4) #c(3 -4))  
   might be true or false, depending on the implementation.  
 (eq #c(3 -4.0) #c(3 -4)) is false.  
 (eq (cons \'a \'b) (cons \'a \'c)) is false.  
 (eq (cons \'a \'b) (cons \'a \'b)) is false.  
 (eq \'(a . b) \'(a . b)) might be true or false.  
 (progn (setq x (cons \'a \'b)) (eq x x)) is true.  
 (progn (setq x \'(a . b)) (eq x x)) is true.  
 (eq #\\A #\\A) might be true or false, depending on the
implementation.  
 (eq \"Foo\" \"Foo\") might be true or false.  
 (eq \"Foo\" (copy-seq \"Foo\")) is false.  
 (eq \"FOO\" \"foo\") is false. 
  
 \[Function\] 
eql x y
  
The eql predicate is true if its arguments are eq, or if they are
numbers of the same type with the same value, or if they are character
objects that represent the same character. For example: 
  
 (eql \'a \'b) is false.  
 (eql \'a \'a) is true.  
 (eql 3 3) is true.  
 (eql 3 3.0) is false.  
 (eql 3.0 3.0) is true.  
 (eql #c(3 -4) #c(3 -4)) is true.  
 (eql #c(3 -4.0) #c(3 -4)) is false.  
 (eql (cons \'a \'b) (cons \'a \'c)) is false.  
 (eql (cons \'a \'b) (cons \'a \'b)) is false.  
 (eql \'(a . b) \'(a . b)) might be true or false.  
 (progn (setq x (cons \'a \'b)) (eql x x)) is true.  
 (progn (setq x \'(a . b)) (eql x x)) is true.  
 (eql #\\A #\\A) is true.  
 (eql \"Foo\" \"Foo\") might be true or false.  
 (eql \"Foo\" (copy-seq \"Foo\")) is false.  
 (eql \"FOO\" \"foo\") is false. 
  
 \[Function\] 
equal x y
  
The equal predicate is true if its arguments are structurally similar
(isomorphic) objects. A rough rule of thumb is that two objects are
equal if and only if their printed representations are the same. 
  
Numbers and characters are compared as for eql. Symbols are compared as
for eq. This method of comparing symbols can violate the rule of thumb
for equal and printed representations, but only in the infrequently
occurring case of two distinct symbols with the same print name. 
  
Certain objects that have components are equal if they are of the same
type and corresponding components are equal. This test is implemented in
a recursive manner and may fail to terminate for circular structures. 
  
For conses, equal is defined recursively as the two car\'s being equal
and the two cdr\'s being equal. 
  
Two arrays are equal only if they are eq, with one exception: strings
and bit-vectors are compared element-by-element. If either argument has
a fill pointer, the fill pointer limits the number of elements examined
by equal. Uppercase and lowercase letters in strings are considered by
equal to be distinct. (In contrast, equalp ignores case distinctions in
strings.) 
  
Compatibility note: In Lisp Machine Lisp, equal ignores the difference
between uppercase and lowercase letters in strings. This violates the
rule of thumb about printed representations, however, which is very
useful, especially to novices. It is also inconsistent with the
treatment of single characters, which in Lisp Machine Lisp are
represented as fixnums. 
  
Two pathname objects are equal if and only if all the corresponding
components (host, device, and so on) are equivalent. (Whether or not
uppercase and lowercase letters are considered equivalent in strings
appearing in components depends on the file name conventions of the file
system.) Pathnames that are equal should be functionally equivalent. 
  
change_begin
X3J13 voted in June 1989 (EQUAL-STRUCTURE)   to clarify that equal never
recursively descends any structure or data type other than the ones
explicitly described above: conses, bit-vectors, strings, and pathnames.
Numbers and characters are compared as if by eql, and all other data
objects are compared as if by eq. 
change_end
  
 (equal \'a \'b) is false.  
 (equal \'a \'a) is true.  
 (equal 3 3) is true.  
 (equal 3 3.0) is false.  
 (equal 3.0 3.0) is true.  
 (equal #c(3 -4) #c(3 -4)) is true.  
 (equal #c(3 -4.0) #c(3 -4)) is false.  
 (equal (cons \'a \'b) (cons \'a \'c)) is false.  
 (equal (cons \'a \'b) (cons \'a \'b)) is true.  
 (equal \'(a . b) \'(a . b)) is true.  
 (progn (setq x (cons \'a \'b)) (equal x x)) is true.  
 (progn (setq x \'(a . b)) (equal x x)) is true.  
 (equal #\\A #\\A) is true.  
 (equal \"Foo\" \"Foo\") is true.  
 (equal \"Foo\" (copy-seq \"Foo\")) is true.  
 (equal \"FOO\" \"foo\") is false. 
  
 \[Function\] 
equalp x y
  
Two objects are equalp if they are equal; if they are characters and
satisfy char-equal, which ignores alphabetic case and certain other
attributes of characters; if they are numbers and have the same
numerical value, even if they are of different types; or if they have
components that are all equalp. 
  
Objects that have components are equalp if they are of the same type and
corresponding components are equalp. This test is implemented in a
recursive manner and may fail to terminate for circular structures. For
conses, equalp is defined recursively as the two car\'s being equalp and
the two cdr\'s being equalp. 
  
Two arrays are equalp if and only if they have the same number of
dimensions, the dimensions match, and the corresponding components are
equalp. The specializations need not match; for example, a string and a
general array that happens to contain the same characters will be equalp
(though definitely not equal). If either argument has a fill pointer,
the fill pointer limits the number of elements examined by equalp.
Because equalp performs element-by-element comparisons of strings and
ignores the alphabetic case of characters, case distinctions are
therefore also ignored when equalp compares strings. 
  
Two symbols can be equalp only if they are eq, that is, the same
identical object. 
  
change_begin
X3J13 voted in June 1989 (EQUAL-STRUCTURE)   to specify that equalp
compares components of hash tables (see below), and to clarify that
otherwise equalp never recursively descends any structure or data type
other than the ones explicitly described above: conses, arrays
(including bit-vectors and strings), and pathnames. Numbers are compared
for numerical equality (see =), characters are compared as if by
char-equal, and all other data objects are compared as if by eq. 
  
Two hash tables are considered the same by equalp if and only if they
satisfy a four-part test: 
  
     \* They must be of the same kind; that is, equivalent :test
arguments were given to make-hash-table when the two hash tables were
created. 
  
     \* They must have the same number of entries (see
hash-table-count). 
  
     \* For every entry (key1, value1) in one hash table there must be a
corresponding entry (key2, value2) in the other, such that key1 and key2
are considered to be the same by the :test function associated with the
hash tables. 
  
     \* For every entry (key1, value1) in one hash table and its
corresponding entry (key2, value2) in the other, such that key1 and key2
are the same, equalp must be true of value1 and value2.  
  
The four parts of this test are carried out in the order shown, and if
some part of the test fails, equalp returns nil and the other parts of
the test are not attempted. 
  
If equalp must compare two structures and the defstruct definition for
one used the :type option and the other did not, then equalp returns
nil. 
  
If equalp must compare two structures and neither defstruct definition
used the :type option, then equalp returns t if and only if the
structures have the same type (that is, the same defstruct name) and the
values of all corresponding slots (slots having the same name) are
equalp. 
  
As part of the X3J13 discussion of this issue the following observations
were made. Object equality is not a concept for which there is a
uniquely determined correct algorithm. The appropriateness of an
equality predicate can be judged only in the context of the needs of
some particular program. Although these functions take any type of
argument and their names sound very generic, equal and equalp are not
appropriate for every application. Any decision to use or not use them
should be determined by what they are documented to do rather than by
any abstract characterization of their function. If neither equal nor
equalp is found to be appropriate in a particular situation, programmers
are encouraged to create another operator that is appropriate rather
than blame equal or equalp for \`\`doing the wrong thing.\'\' 
  
Note that one consequence of the vote to change the rules of
floating-point contagion
(CONTAGION-ON-NUMERICAL-COMPARISONS)   (described in section 12.1) is to
make equalp a true equivalence relation on numbers. 
change_end
  
 (equalp \'a \'b) is false.  
 (equalp \'a \'a) is true.  
 (equalp 3 3) is true.  
 (equalp 3 3.0) is true.  
 (equalp 3.0 3.0) is true.  
 (equalp #c(3 -4) #c(3 -4)) is true.  
 (equalp #c(3 -4.0) #c(3 -4)) is true.  
 (equalp (cons \'a \'b) (cons \'a \'c)) is false.  
 (equalp (cons \'a \'b) (cons \'a \'b)) is true.  
 (equalp \'(a . b) \'(a . b)) is true.  
 (progn (setq x (cons \'a \'b)) (equalp x x)) is true.  
 (progn (setq x \'(a . b)) (equalp x x)) is true.  
 (equalp #\\A #\\A) is true.  
 (equalp \"Foo\" \"Foo\") is true.  
 (equalp \"Foo\" (copy-seq \"Foo\")) is true.  
 (equalp \"FOO\" \"foo\") is true. 
  


