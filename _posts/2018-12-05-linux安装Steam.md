---
layout: post
title:  "linux安装Steam"
date:   2018-12-05
tags:
      - it
---


<https://negativo17.org/steam/>



> dnf config-manager
> \--add-repo=https://negativo17.org/repos/fedora-steam.repo

To make "Big Picture" work in Fedora or CentOS/RHEL, enable this SELinux
boolean as root:

> setsebool -P allow_execheap 1

[英雄无敌3地图的中英文对照      
](https://www.cnblogs.com/findumars/p/4733534.html)

[ 英雄无敌3死亡阴影怎么知道地图对应的英文名称](https://www.cnblogs.com/findumars/p/4733534.html)

[英雄无敌3开源引擎vcmi的编译安装](https://www.cnblogs.com/findumars/p/6275440.html)

How to build VCMI
(Linux)  <https://wiki.vcmi.eu/How_to_build_VCMI_(Linux)>

sudo yum install cmake gcc-c++ SDL2-devel SDL2_image-devel
SDL2_ttf-devel SDL2_mixer-devel boost boost-devel boost-filesystem
boost-system boost-thread boost-program-options boost-locale zlib-devel
ffmpeg-devel ffmpeg-libs qt5-qtbase-devel

> mkdir build && cd build cmake ../vcmi
>
> cmake \--build . \-- -j2
>
> make install

编译的库放在/usr/local/lib, 所以需要 加库路径：

> cat \> /etc/ld.so.conf.d/vcmi-x86_64.conf \<\<EOF
>
> /usr/local/lib64/vcmi
> /usr/local/lib
> 
> EOF

ldconfig



> vcmiclient -v
>
> sudo ln -sf /mnt/lenovo/GAME/Heroes3/Data .
>
> sudo ln -sf /mnt/lenovo/GAME/Heroes3/Mp3 .
> sudo ln -sf /mnt/lenovo/GAME/Heroes3/Maps .
> 
> 

Fedora29 没有 ffmpeg-devel ffmpeg-libs， 用源码编译：

Get the packages  <https://ffmpeg.org/download.html#get-sources>

魔法门之英雄无敌3-死亡阴影英文版光盘 <https://pan.baidu.com/share/link?uk=587557272&shareid=1666116402>

Heroes of Might and Magic III-The Shad
<https://pan.baidu.com/share/link?shareid=264692&uk=3390017774>



[FFmpeg在Linux下编译使用 ](https://www.cnblogs.com/CoderTian/p/6655568.html)

 

> git clone https://git.ffmpeg.org/ffmpeg.git ffmpeg
>
> 
>
> ./configure
> \--prefix=host \--enable-shared \--disable-static \--disable-doc
>
> ./configure \--enable-gpl \--disable-optimizations \--enable-shared \--enable-static \--enable-ffplay \--disable-x86asm
>
> make
>
> make install



            steam有linux版的免费游戏暂时名单

Sunrider: First Arrival

Reversion - The Escape (1st Chapter)(有简体中文版）
Floating Point

World of Guns: Gun Disassembly（枪支世界:枪械拆解）
Toribash
Fistful of Frags（西部牛仔半条命2，必玩）
You Have to Win the Game
The Plan
Cannons Lasers Rockets
8BitMMO
No More Room in Hell地狱已满（联机杀僵尸，必玩）
Champions of Regnum
Star Conflict星际争端（俄国出太空机战游戏，要有一定配置才能玩）
Dwarfs - F2P

TF2（不解释，必玩）
Dota2（不解释,必玩）
Narcissu 1st & 2nd（水仙1\\2,神作必玩）
            

