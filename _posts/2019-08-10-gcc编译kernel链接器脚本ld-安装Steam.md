---
layout: post
title: "gcc编译kernel链接器脚本ld-安装Steam"
date: 2019-08-10
tags:
    - it
---


# Table of Contents

1.  [升级gcc版本9.1.0](#org983aa45)
2.  [编译kernel](#org194aa43)
3.  [ld链接器脚本内的locationcounter](#orgafc2f7e)
4.  [linux安装Steam](#org227826d)
5.  [linux安装英雄无敌3](#org4f3bdd8)


<a id="org983aa45"></a>

# 升级gcc版本9.1.0

<https://mamicode.com/info-detail-2688872.html>

wget <https://ftp.gnu.org/gnu/gcc/gcc-9.1.0/gcc-9.1.0.tar.gz>

    tar -xzvf gcc-9.1.0.tar.gz
    
    cd gcc-9.1.0
    
    ./contrib/download_prerequisiles
    
    mkdir ../build-gcc
    
    cd ../build-gcc/
    
    ../gcc-9.1.0/configure  --enable-bootstrap --enable-languages=c,c++ --prefix=/tools --enable-gnu-unique-object --enable-linker-build-id --with-gcc-major-version-only --with-linker-hash-style=gnu --enable-plugin --enable-initfini-array --with-isl --enable-libmpx --enable-offload-targets=nvptx-none --without-cuda-driver --enable-gnu-indirect-function --enable-cet --with-tune=generic --with-arch_32=i686 --build=x86_64-redhat-linux
    
    # 这个不行啊。
    
    ../gcc-9.1.0/configure
    -enable-checking=release -prefix=/tools -enable-languages=c,c++ -disable-multilib

<https://blog.csdn.net/sonicling/article/details/6706152>

而GCC的词法分析是手工构造的，实现在libcpp/lex.c文件中，其中最重要的那个函数是<sub>cpp</sub><sub>lex</sub><sub>direct</sub>，他反应了GCC词法分析器的核心结构。


## 例如修改c语言中的=赋值为←

<https://www.mysmth.net/nForum/#!article/CPlusPlus/407534>

      int main(int argc, char **argv)  
    {  
         int i ← 0;
         unsigned char *str ← "1234中";
    
         while(str[i])
         {
    	 printf("%02x ",str[i]);
    	 i++;
         }
         printf("\n");
         if ( 1 ?= 1)
    	 printf("%s\n", str);
         return 0;  
    } 

> $ diff -u lex.c.old  lex.c
> &#x2014; lex.c.old    2019-08-10 13:27:04.354956122 <del>0800
> ++</del> lex.c    2019-08-10 13:57:50.698128173 +0800
> @@ -3099,11 +3099,18 @@
> 
> case '\*': IF<sub>NEXT</sub><sub>IS</sub> ('`', CPP_MULT_EQ, CPP_MULT); break;
>       case '`': IF<sub>NEXT</sub><sub>IS</sub> ('=', CPP<sub>EQ</sub><sub>EQ</sub>, CPP<sub>EQ</sub>); break;
> 
> -   case 0xE2:
> -   if (\*buffer->cur `= 0x86 && buffer->cur[1] =` 0x90)
> -   buffer->cur+= 2, result->type = CPP<sub>EQ</sub>;
> -   else
> -   cpp<sub>error</sub> (pfile, CPP<sub>DL</sub><sub>WARNING</sub>, "not support");
> -   break;
>     case '!': IF<sub>NEXT</sub><sub>IS</sub> ('`', CPP_NOT_EQ, CPP_NOT); break;
>           case '^': IF_NEXT_IS ('`', CPP<sub>XOR</sub><sub>EQ</sub>, CPP<sub>XOR</sub>); break;
>     case '#': IF<sub>NEXT</sub><sub>IS</sub> ('#', CPP<sub>PASTE</sub>, CPP<sub>HASH</sub>); result->val.token<sub>no</sub> = 0; break;
> 
> -   case '?': result->type = CPP<sub>QUERY</sub>; break;
> -   //case '?': result->type = CPP<sub>QUERY</sub>; break;
> -   case '?': IF<sub>NEXT</sub><sub>IS</sub> ('=', CPP<sub>EQ</sub><sub>EQ</sub>, CPP<sub>QUERY</sub>); break;
>     case '~': result->type = CPP<sub>COMPL</sub>; break;
>     case ',': result->type = CPP<sub>COMMA</sub>; break;
>     case '(': result->type = CPP<sub>OPEN</sub><sub>PAREN</sub>; break;


## 使用?=代替if判断中的==

    #include <stdio.h>
    int main(int argc, char **argv)  
    {  
         int i = 0;
         unsigned char *str = "1234中";
    
         while(str[i])
         {
    	 printf("%02x ",str[i]);
    	 i++;
         }
         printf("\n");
         if ( 1 ?= 1)
    	 printf("%s\n", str);
         return 0;  
    }   

> $ diff -u lex.c.old lex.c
> &#x2014; lex.c.old    2019-08-10 13:27:04.354956122 <del>0800
> ++</del> lex.c    2019-08-10 13:28:31.970443943 +0800
> @@ -3103,7 +3103,8 @@
>       case '^': IF<sub>NEXT</sub><sub>IS</sub> ('=', CPP<sub>XOR</sub><sub>EQ</sub>, CPP<sub>XOR</sub>); break;
>       case '#': IF<sub>NEXT</sub><sub>IS</sub> ('#', CPP<sub>PASTE</sub>, CPP<sub>HASH</sub>); result->val.token<sub>no</sub> = 0; break;
> 
> -   case '?': result->type = CPP<sub>QUERY</sub>; break;
> -   //case '?': result->type = CPP<sub>QUERY</sub>; break;
> -   case '?': IF<sub>NEXT</sub><sub>IS</sub> ('=', CPP<sub>EQ</sub><sub>EQ</sub>, CPP<sub>QUERY</sub>); break;
>     case '~': result->type = CPP<sub>COMPL</sub>; break;
>     case ',': result->type = CPP<sub>COMMA</sub>; break;
>     case '(': result->type = CPP<sub>OPEN</sub><sub>PAREN</sub>; break;


<a id="org194aa43"></a>

# 编译kernel

[How to compile and install Linux Kernel 5.1.2 from source code](https://www.cnblogs.com/qccz123456/p/11009502.html)

下载 linux-5.4.1

> 
> 
> cp *boot/config-5.3.13-300.fc31.x86<sub>64</sub> linux-5.4.1*.config
> 
> make xconfig  或者 (defconfig, oldconfig, config, gconfig)
> 
> make -j 4
> 
> sudo make modules<sub>install</sub>
> 
> sudo make install
> 
> sudo grubby &#x2013;set-default /boot/vmlinuz-5.1.2   #安装自己定义的名称来设置
> 
> gtags -v
> 
> htags -DfFnva -t 'kernel 5.4.1 Ref'
> 
> htags-server   # 默认<https://127.0.0.1:8000>


<a id="orgafc2f7e"></a>

# ld链接器脚本内的locationcounter

<https://www.newsmth.net/nForum/#!article/KernelTech/74604>

. 是指 memory layout，表示当前偏移量。

对于reloadeble， 。 代表了相邻的session和当前位置的偏移。0-offset

<https://forum.osdev.org/viewtopic.php?f=1&t=32840>

> SECTIONS
> {
>         *\* Relative address by itself results in relative address.
>            '.' at this point holds a negative offset to the start of custom<sub>section</sub>,
>            which is yet to be determined by a future assignment to '.'
>            The absolute address would be 0, although if this is not the last
>            linker invocation, future invocations move this symbol along with the section. \**
>         A<sub>outside</sub><sub>current</sub> = .;
>         . = 0x111000;
>         *\* Relative address by itself results in relative address. Still negative offset. \**
>         B<sub>outside</sub><sub>current</sub> = .;
>         *\* Numbers treated as absolute addresses outside of sections.
>            Absolute address by itself results in absolute address.
>            This changes if LD<sub>FEATURE</sub> ("SANE<sub>EXPR</sub>") is requested at the start of the script. \**
>         C<sub>outside</sub><sub>direct</sub> = 0x111000;
>         *\* Numbers treated as absolute addresses outside of sections.
>            Arithmetic between absolute address and relative address, converts the
>            relative address to absolute one and produces absolute address as well. \**
>         D<sub>outside</sub><sub>current</sub><sub>doubled</sub> = . \* 2;
>         . = 0x111100;
>         custom<sub>section</sub> : {
>                 . = 0x11;
>                 *\* Relative address by itself results in relative address. 0 offset. \**
>                 E<sub>inside</sub><sub>current</sub> = .;
>                 F<sub>inside</sub><sub>direct</sub> = 0x11;
>                 *\* Numbers treated as numbers inside of sections.
>                    Arithmetic between number and relative address uses the offset of the
>                    relative address and produces relative address. \**
>                 G<sub>inside</sub><sub>current</sub><sub>doubled</sub> = . \* 2;
>                 *\* The following are fairly clear, since they request conversion of the
>                    address type explicitly. \**
>                 H<sub>inside</sub><sub>absolute</sub><sub>current</sub> = ABSOLUTE(.);
>                 I<sub>inside</sub><sub>absolute</sub><sub>current</sub><sub>doubled</sub> = ABSOLUTE(.) \* 2;
>                 J<sub>inside</sub><sub>absolute</sub><sub>E</sub> = ABSOLUTE(E<sub>inside</sub><sub>current</sub>);
>                 K<sub>inside</sub><sub>absolute</sub><sub>H</sub> = ABSOLUTE(H<sub>inside</sub><sub>absolute</sub><sub>current</sub>);
>                 *\* Absolute addresses are treated as numbers within sections.
>                    Numbers produce section relative symbols in assignments. \**
>                 L<sub>inside</sub><sub>H</sub> = H<sub>inside</sub><sub>absolute</sub><sub>current</sub>;
>                 **(.text);
>                 . = 0x1000;
>         }
>         /** Arithmetic between relative addresses in the same section
>            produces absolute address, but does not convert the operands.
>            That is, it operates on the offset parts of the addresses. **/
>         M<sub>outside</sub><sub>current</sub><sub>squared</sub> = . \* .;
>         /** Relative address in the same section produces relative address. **/
>         N<sub>outside</sub><sub>E</sub> = E<sub>inside</sub><sub>current</sub>;
>         /** Absolute address produces absolute address as expected. **/
>         O<sub>outside</sub><sub>H</sub> = H<sub>inside</sub><sub>absolute</sub><sub>current</sub>;
>         . = 0x1111100;
>         /** Relative address to the following section. 0 offset. **/
>         P<sub>in</sub><sub>another</sub><sub>section</sub> = .;
>         another<sub>section</sub> : {
>                 /** This is a surprise, although it is by the rules.
>                    Mixing relative addresses from different sections converts them to their
>                    absolute address value first, but produces a relative address in the end. **/
>                 Q<sub>mixed</sub><sub>F</sub><sub>P</sub> = F<sub>inside</sub><sub>direct</sub> \* P<sub>in</sub><sub>another</sub><sub>section</sub>;
>                 LONG(0)
>         }
>         . = 0x1111111;
>         /** We are still in the previous section right now.
>            Although if orphan sections are not discarded,
>            we would be in one of those orphan sections instead. **/
>         R<sub>in</sub><sub>same</sub><sub>section</sub> = .;
>         /** Relative address in another section by itself is still
>            a relative address in that section, no matter where we use it. **/
>         S<sub>in</sub><sub>first</sub><sub>section</sub> = E<sub>inside</sub><sub>current</sub>;
>         *DISCARD* : { \*(**); }
> }

The results for relocatable output.

> ffffffffffeeef00        1       A<sub>outside</sub><sub>current</sub>
> ffffffffffffff00        1       B<sub>outside</sub><sub>current</sub>
> 0000000000111000        ABS     C<sub>outside</sub><sub>direct</sub>
> 0000000000222000        ABS     D<sub>outside</sub><sub>current</sub><sub>doubled</sub>
> 0000000000000011        1       E<sub>inside</sub><sub>current</sub>
> 0000000000000011        1       F<sub>inside</sub><sub>direct</sub>
> 0000000000000022        1       G<sub>inside</sub><sub>current</sub><sub>doubled</sub>
> 0000000000111111        ABS     H<sub>inside</sub><sub>absolute</sub><sub>current</sub>
> 0000000000222222        ABS     I<sub>inside</sub><sub>absolute</sub><sub>current</sub><sub>doubled</sub>
> 0000000000111111        ABS     J<sub>inside</sub><sub>absolute</sub><sub>E</sub>
> 0000000000111111        ABS     K<sub>inside</sub><sub>absolute</sub><sub>H</sub>
> 0000000000111111        1       L<sub>inside</sub><sub>H</sub>
> 0000000001000000        ABS     M<sub>outside</sub><sub>current</sub><sub>squared</sub>
> 0000000000000011        1       N<sub>outside</sub><sub>E</sub>
> 0000000000111111        ABS     O<sub>outside</sub><sub>H</sub>
> 0000000000000000        2       P<sub>in</sub><sub>another</sub><sub>section</sub>
> 0000123455432100        2       Q<sub>mixed</sub><sub>F</sub><sub>P</sub>
> 0000000000000011        2       R<sub>in</sub><sub>same</sub><sub>section</sub>
> 0000000000000011        1       S<sub>in</sub><sub>first</sub><sub>section</sub>

按照作者的解释  R<sub>in</sub><sub>same</sub><sub>section是在</sub> another<sub>section</sub>，偏移量. = 0x1111100;

后来又赋值  . = 0x1111111;， 所以R<sub>in</sub><sub>same</sub><sub>section记录相对偏移量</sub> = 0x1111111 - 0x1111100 = 0x0000011

    -----test.s---------
    section .text
    	.globl _start
    _start:
    	movq $1, %rax
    	movq $0, %rbx
    	int $0x80
    
    ------test.lds------
    SECTIONS
    {
    	. = 0x10;
    	label_1 = .;
    	custom_section : {
    		. = 0x20;
    		label_2 = . ;
    		label_3 = ABSOLUTE(.) ;
    		*(.text) ;
    	}
    }	  

> gcc -c test.s
> ld test.o -T test.lds -o test
> 
> label<sub>2</sub> =0x30,  (0x20+0x10)
> label<sub>4</sub>=0x50,   (0x20\*2 +0x10)

可见ld之后test中 label<sub>2</sub>, label<sub>4从relative</sub> offset转变成了virtual address。


<a id="org227826d"></a>

# linux安装Steam

<https://negativo17.org/steam/>

> dnf config-manager &#x2013;add-repo=<https://negativo17.org/repos/fedora-steam.repo>

To make "Big Picture" work in Fedora or CentOS/RHEL, enable this SELinux
boolean as root:

> setsebool -P allow<sub>execheap</sub> 1

steam有linux版的免费游戏暂时名单

> Sunrider: First Arrival
> 
> Reversion - The Escape (1st Chapter)(有简体中文版） Floating Point
> 
> World of Guns: Gun Disassembly（枪支世界:枪械拆解）
> Toribash Fistful of Frags（西部牛仔半条命2，必玩）
> You Have to Win the Game The Plan Cannons Lasers Rockets 8BitMMO No More Room in Hell地狱已满（联机杀僵尸，必玩）
> Champions of Regnum Star Conflict星际争端（俄国出太空机战游戏，要有一定配置才能玩）
> Dwarfs - F2P
> TF2（不解释，必玩）
> Dota2（不解释,必玩）
> Narcissu 1st & 2nd（水仙1\\2,神作必玩）             


<a id="org4f3bdd8"></a>

# linux安装英雄无敌3

[魔法门之英雄无敌3-死亡阴影英文版光盘 [[https://pan.baidu.com/share/link?uk=587557272&shareid=1666116402](https://www.cnblogs.com/findumars/p/4733534.html)

Heroes of Might and Magic III-The Shad
<https://pan.baidu.com/share/link?shareid=264692&uk=3390017774>

[英雄无敌3地图的中英文对照      ](https://www.cnblogs.com/CoderTian/p/6655568.html)

[ 英雄无敌3死亡阴影怎么知道地图对应的英文名称](https://www.cnblogs.com/findumars/p/4733534.html)

[英雄无敌3开源引擎vcmi的编译安装](https://www.cnblogs.com/findumars/p/6275440.html)


## How to build VCMI (Linux)  <https://wiki.vcmi.eu/How_to_build_VCMI_(Linux)>

    sudo yum install cmake gcc-c++ SDL2-devel SDL2_image-devel
    SDL2_ttf-devel SDL2_mixer-devel boost boost-devel boost-filesystem
    boost-system boost-thread boost-program-options boost-locale zlib-devel
    ffmpeg-devel ffmpeg-libs qt5-qtbase-devel
    
    mkdir build && cd build cmake ../vcmi
    
    cmake --build . -- -j2
    
    make install
    # 编译的库放在/usr/local/lib, 所以需要 加库路径：
    
    cat > /etc/ld.so.conf.d/vcmi-x86_64.conf <<EOF
    
      /usr/local/lib64/vcmi /usr/local/lib
    
      EOF
    
    ldconfig
    
    vcmiclient -v
    
    sudo ln -sf /mnt/lenovo/GAME/Heroes3/Data .
    
    sudo ln -sf /mnt/lenovo/GAME/Heroes3/Mp3 . sudo ln
    -sf /mnt/lenovo/GAME/Heroes3/Maps .


## FFmpeg在Linux下编译

Fedora29 没有 ffmpeg-devel ffmpeg-libs， 用源码编译：

Get the packages  <https://ffmpeg.org/download.html#get-sources>

> git clone <https://git.ffmpeg.org/ffmpeg.git> ffmpeg
> 
> ./configure
> &#x2013;prefix=host &#x2013;enable-shared &#x2013;disable-static &#x2013;disable-doc
> 
> ./configure &#x2013;enable-gpl &#x2013;disable-optimizations &#x2013;enable-shared &#x2013;enable-static &#x2013;enable-ffplay &#x2013;disable-x86asm
> 
> make
> 
> make install

