---
layout: post
title:  "ld链接器脚本内的locationcounter"
date:   2018-04-09
tags:
      - 随笔
---


https://www.newsmth.net/nForum/#!article/KernelTech/74604



. 是指 memory layout，表示当前偏移量。

对于reloadeble， 。 代表了相邻的session和当前位置的偏移。0-offset



https://forum.osdev.org/viewtopic.php?f=1&t=32840



        . = 0x1111111;
        /\* We are still in the previous section right now.
           Although if orphan sections are not discarded,
           we would be in one of those orphan sections instead. \*/
        R_in_same_section = .;
        /\* Relative address in another section by itself is still
           a relative address in that section, no matter where we use
it. \*/
        S_in_first_section = E_inside_current;
        /DISCARD/ : { \*(\*); }

The results for relocatable output.

0000000000000011        2       R_in_same_section
0000000000000011        1       S_in_first_section



按照作者的解释  R_in_same_section是在 another_section，偏移量. =
0x1111100;

后来又赋值  . = 0x1111111;， 所以R_in_same_section记录相对偏移量 = 

0x1111111 - 0x1111100 = 0x0000011

.section .text 

         .globl \_start

\_start: 

         movq \$1, %rax 

         movq \$0, %rbx 

         int\$0x80

 \-\-\-\-\--test.lds\-\-\-\-\-- 

SECTIONS

 { 

         . = 0x10; 

         label_1 = .; 

         custom_section : { 

                 . = 0x20; 

                 label_2 = . ; 

                 label_3 = ABSOLUTE(.) ; 

                  label_4 =  label_2 \* 2；

                 \*(.text) ; 

         } 

 }



gcc -c test.s

ld test.o -T test.lds -o test

readelf -s test 和 nm test 输出类似。

label_2 =0x30,  (0x20+0x10)

label_4=0x50,   (0x20\*2 +0x10)

可见ld之后test中 label_2, label_4从relative offset转变成了virtual
address。

