---
title: "Bios Gpt Pxe Bcdboot Winpe Linux引导"
date: 2017-08-23
draft: false
tags:
    - it
---

# Table of Contents

1.  [bios + gpt 折腾](#org3e5eeae)
2.  [reference :](#org7939984)
3.  [引导Fedora.iso简记](#org3a50409)
4.  [pxe安装linux26尝试](#org5d7091a)
5.  [用qemu修复win10 bcdboot](#org93efaf0)
6.  [自用万能启动U盘FBA版2016-12-20 最新增强版](#orgb9dc8ec)
7.  [一个撒手不管pe](#org838e35e)

<a id="org3e5eeae"></a>

# bios + gpt 折腾

一点注意：

grub2-install /dev/sda

把core.img写入Boot Partition.不是sda1.

references:

<https://www.lightofdawn.org/wiki/wiki.cgi/BIOSBootGPT>

<https://blog.csdn.net/sqhxhg/article/details/8456689>


## BIOS系统：

1、GRUB（2）：需要2MiB”BIOS启动分区”（requires a 2 MiB "BIOS Boot Partition" (EF02 type code in gdisk and bios<sub>grub</sub> flag in GNU Parted) in BIOS systems to embed itscore.img file due to lack of post-MBR embed gap in GPT disks. Runtime GPT support in GRUB(2) is provided by thepart<sub>gpt</sub> module. See[GRUB#GPT<sub>specific</sub><sub>instructions</sub>](https://wiki.archlinux.org/index.php/GRUB#GPT_specific_instructions) for more information）;

<https://wiki.archlinux.org/index.php/GRUB>

On a BIOS/[GPT](https://wiki.archlinux.org/index.php/GPT) configuration a [BIOS boot partition](https://www.gnu.org/software/grub/manual/html_node/BIOS-installation.html) is required. GRUB embeds its core.img into this partition.

**Note:**

-   Before attempting this method keep in mind that not all systems will be able to support this partitioning scheme. Read more on [GUID partition tables](https://wiki.archlinux.org/index.php/GUID_Partition_Table#BIOS_systems)[[broken link](https://wiki.archlinux.org/index.php/ArchWiki:Requests#Broken_section_links): invalid section].

-   This additional partition is only needed on a GRUB, BIOS/GPT partitioning scheme. Previously, for a GRUB, BIOS/MBR partitioning scheme, GRUB used the Post-MBR gap for the embedding the core.img). GRUB for GPT, however, does not use the Post-GPT gap to conform to GPT specifications that require 1<sub>megabyte</sub>/2048<sub>sector</sub> disk boundaries.

-   For [UEFI](https://wiki.archlinux.org/index.php/UEFI) systems this extra partition is not required, since no embedding of boot sectors takes place in that case. However, UEFI systems still require an [ESP](https://wiki.archlinux.org/index.php/ESP).


## grub 安装到c盘。

grub2-install &#x2013;boot-directory=/mnt/c /dev/sda

syslinux/memdisk &#x2013;> /mnt/c/syslinux/memdisk 

> menuentry 'Windows 7 (from VHD)' &#x2013;class windows &#x2013;class os
> $menuentry<sub>id</sub><sub>option</sub> 'osprober-chain-082608C02608B0AC' {
> 
> insmod part<sub>gpt</sub>
> 
> insmod ntfs
> 
> set root='hd0,gpt3'
> 
> if [ x$feature<sub>platform</sub><sub>search</sub><sub>hint</sub> = xy ]; then
> 
>           search
> &#x2013;no-floppy &#x2013;fs-uuid &#x2013;set=root &#x2013;hint-bios=hd0,gpt3 &#x2013;hint-efi=hd0,gpt3 &#x2013;hint-baremetal=ahci0,gpt3 
> 082608C02608B0AC
> 
> else
> 
> search &#x2013;no-floppy &#x2013;fs-uuid &#x2013;set=root 082608C02608B0AC
> 
> fi
> 
> linux16 /syslinux/memdisk harddisk
> 
> initrd16 /cwbootmgr.vhd
> 
> }


## Hybrid 方法：

<https://www.rodsbooks.com/gdisk/hybrid.html>

<https://www.rodsbooks.com/gdisk/bios.html>


<a id="org7939984"></a>

# reference :

<https://blog.csdn.net/littlesmallless/article/details/59173072>

<https://bbs.pcbeta.com/viewthread-1573292-1-1.html>


<a id="org3a50409"></a>

# 引导Fedora.iso简记

grub2-install &#x2013;target=i386-pc &#x2013;boot-directory=/mnt/usb /dev/sdc  

我的custom.cfg位于(usb)/boot/grub2/custom.cfg

网上的可以试试：

[fedora 16 livecd硬盘u盘 安装](https://blog.csdn.net/bingo_boy/article/details/6957698) 新建一个目录, 比如fedora, 然后将iso解压到此文件夹中. 则grub.cfg需要修改成如下

    menuentry "Fedora Live CD"{
    set root="(hd0,msdos1)"
     linux/fedora/isolinux/vmlinuz0 root=UUID=0C02-A283 live_dir=/fedora/LiveOS/ liveimg quiet rhgb rootfstype=auto
     initrd /fedora/isolinux/initrd0.img
     }

    vgscan
    
    vgchange -a y fedora
    
    mount /dev/mapper/fedora-root  /mnt
    
    mount /dev/sda8 /mnt/boot
    
    mount /dev/sda2 /mnt/boot/efi
    mount --bind /dev/ /mnt/dev
    mount --bind /dev/pts /mnt/dev/pts
    mount --bind /proc /mnt/proc
    mount --bind /sys /mnt/sys
    
    chroot /mnt
    
    grub-install --target=x86_64-efi /dev/sda
    
    # 然后系统报错
    # grub2-install: error: /usr/lib/grub/x86_64-efi/modinfo.sh
    # doesn't exist. Please specify --target or --directory
    
    dnf reinstall  grub2-efi-x64.x86_64
    
    dnf install grub2-efi-x64-modules.noarch
    
    grub2-mkconfig -o /boot/efi/EFI/fedora/grub.cfg
    
    exit&&sudo umount/mnt/sys &&sudo umount/mnt/proc &&sudo umount/mnt/dev/pts &&sudo umount/mnt/dev &&sudo umount/mnt
    
    grub2-mkconfig -o /boot/efi/EFI/fedora/grub.cfg


<a id="org5d7091a"></a>

# pxe安装linux26尝试

1.配置dnsmasq(dns, dhcp,tftp)

2.配置ftp

似乎直接用grub2也可以？

    grub2-mkimage  -O i386-pc -d  /usr/lib/grub/i386-pc/ -c first.cfg -p '(pxe)/boot/grub' -o i386-pc-core.img -v echo biosdisk blocklist newc cpio fat iso9660 part_msdos ext2 ntfs loopback reboot halt

设置prefix=(tftp,10.9.9.1)/boot/grub 没有试过。

cat /usr/lib/grub/i386-pc/pxeboot.img i386-pc-core.img > grub2pxe.0

boot放置grub2需要的stage2文件。

find boot | cpio -o -H newc > winly.pkg

\#cat first.cfg

loopback winly
/winly.pkg  ##指令启动的第一时间，首先回放winly.pkg到虚拟的winly磁盘

set prefix=(winly)/boot/grub
 ##指定启动模块的位置到这个虚拟winly磁盘的/boot/grub

set pager=1  ##设定手动翻页

这里root应该设成(tftp,10.9.9.1) ?

3.使用syslinux

<https://docs.fedoraproject.org/en-US/Fedora/26/html/Installation_Guide/pxe-bootloader.html>

Create a boot menu for BIOS clients at /var/lib/tftpboot/pxelinux.cfg/default.

> default vesamenu.c32
> prompt 1
> timeout 600
> 
> label linux
> menu label ^Install Fedora 26 64-bit
> menu default
> kernel f26/vmlinuz
> append initrd=f26/initrd.img inst.stage2=<http://download.fedoraproject.org/pub/fedora/linux/releases/26/Server/x86_64/os/> ip=dhcp
> 
> label server
> menu label ^Install Fedora 26 ( Minimal Image )
> menu default
> kernel f26/vmlinuz
> append initrd=f26/initrd.img inst.stage2=<http://download.fedoraproject.org/pub/fedora/linux/releases/26/Server/x86_64/os/> ip=dhcp ks=<https://example.com/fedora/kickstarts/minimal.ks>
> 
> label rescue
> menu label ^Rescue installed system
> kernel f26/vmlinuz
> append initrd=f26initrd.img ip=dhcp root=live:<http://download.fedoraproject.org/pub/fedora/linux/releases/26/Server/x86_64/os/LiveOS/squashfs.img> rescue
> 
> label local
> menu label Boot from ^local drive
> localboot 0xffff

1.  pxe + UEFI 使用shim。

<https://docs.fedoraproject.org/en-US/Fedora/26/html/Installation_Guide/pxe-bootloader.html>

cp shim.efi, grubx64.efi -> *tftpboot*

1.  让x64 +UEFI 的机器使用shim

主要是这个

dhcp-match=set:efi-x86<sub>64,option</sub>:client-arch,7

dhcp-match=set:efi-x86<sub>64,option</sub>:client-arch,9

dhcp-match=set:efi-x86,option:client-arch,6

dhcp-match=set:bios,option:client-arch,0

dhcp-boot=tag:efi-x86<sub>64,shim.efi</sub>

dhcp-boot=tag:efi-x86,"efi32/syslinux.efi"

dhcp-boot=tag:bios,pxelinux.0

cat /etc/dnsmasq.conf

> server=127.0.0.1
> 
> server=114.114.114.114
> 
> server=8.8.8.8
> 
> address=/bogon/127.0.0.1
> 
> except-interface=wlp2s2
> 
> dhcp-range=10.9.9.10,10.9.9.150,12h
> 
> dnsmasq
> 
> dhcp-match=set:efi-x86<sub>64,option</sub>:client-arch,7
> 
> dhcp-match=set:efi-x86<sub>64,option</sub>:client-arch,9
> 
> dhcp-match=set:efi-x86,option:client-arch,6
> 
> dhcp-match=set:bios,option:client-arch,0
> 
> dhcp-boot=tag:efi-x86<sub>64,shim.efi</sub>
> 
> dhcp-boot=tag:efi-x86,"efi32/syslinux.efi"
> 
> dhcp-boot=tag:bios,pxelinux.0
> 
> enable-tftp
> 
> tftp-root=/tftpboot
> 
> conf-dir=/etc/dnsmasq.d,.rpmnew,.rpmsave,.rpmorig

6.最后是shim 调用grub2的配置文件。

cat /tftpboot/EFI/fedora

> function load<sub>video</sub> {
> 
> insmod efi<sub>gop</sub>
> 
> insmod efi<sub>uga</sub>
> 
> insmod video<sub>bochs</sub>
> 
> insmod video<sub>cirrus</sub>
> 
> insmod all<sub>video</sub>
> 
> }
> 
> load<sub>video</sub>
> 
> set gfxpayload=keep
> 
> insmod gzio
> 
> menuentry 'Install Fedora 64-bit'  &#x2013;class fedora &#x2013;class gnu-linux
> &#x2013;class gnu &#x2013;class os {
> 
> linuxefi f26/vmlinuz ip=dhcp inst.repo=<ftp://10.9.9.1/pub/LiveOS/>
> 
> initrdefi f26/initrd.img
> 
> }
> 
> menuentry 'Rescue installed system'  &#x2013;class fedora &#x2013;class gnu-linux
> &#x2013;class gnu &#x2013;class os {
> 
> linuxefi f26/vmlinuz ip=dhcp
> root=live:<ftp://10.9.9.1/pub/LiveOS/squashfs.img>
> 
> initrdefi f26/initrd.img
> 
> }


<a id="org93efaf0"></a>

# 用qemu修复win10 bcdboot

> qemu-img create -f qcow2 fedora.img 1G
> qemu-system-x86<sub>64</sub> -m 50 fedora.img -cdrom ./ReactOS-LiveCD.iso
> 
> grep -E 'vmx|svm' /proc/cpuinfo；# 检查KVM是否可用：
> 
> lsmod | grep kvm
> 
> qemu-system-x86<sub>64</sub> -m 2048 -enable-kvm fedora.img -cdrom ./Fedora-Live-Desktop-x86<sub>64</sub>
> qemu-img create -f qcow2 fedora.img 1G
> 
> qemu-system-x86<sub>64</sub> -m 50 fedora.img -cdrom ./ReactOS-LiveCD.iso
> 
> grep -E 'vmx|svm' /proc/cpuinfo；# 检查KVM是否可用：
> 
> lsmod | grep kvm
> 
> qemu-system-x86<sub>64</sub> -m 2048 -enable-kvm fedora.img -cdrom ./Fedora-Live-Desktop-x86<sub>64</sub>-20-1.iso

可以运行，但是没有增加磁盘。

> qemu-system-x86<sub>64</sub> -M pc &#x2013;enable-kvm -m 2048 -cdrom Win10PEx64.iso
> -drive file=test.img,if=none,id=drive-ide0-0-0,format=raw
> 
> qemu-system-x86<sub>64</sub> -m 2048 -name tst -smp 2 -blockdev
> driver=raw,node-name=disk,file.driver=file,file.filename=test.img
> -cdrom Win10PEx64.iso
> 
> qemu-system-x86<sub>64</sub> -M pc &#x2013;enable-kvm -m 2048 -hda test.img -cdrom
> Win10PEx64.iso

实际上是有的：用win10安装盘可以看到硬盘。

> qemu-kvm -m 2048 -name tst -smp 2 -hda test.img -cdrom win10.iso
> 
> sudo qemu-system-x86<sub>64</sub> &#x2013;M pc -m memsize &#x2013;enable-kvm -hda imgname -net nic,model=virtio,vlan=0,macaddr=00:e0:fc:00:00:01 -net tap,vlan=0,ifname=tap0,script=no -cdrom virtio-win-0.1-100.iso -usbdevice tablet -vga std -boot c 

定义一个虚拟机：可以用gnome-boxes创建一个，然后virsh dumpxml win10 > win10.xml 做参考。  

virsh define win10.xml       //创建虚拟机  

> <domain type='kvm'> //如果是Xen，则type='xen'   
> <name>win10</name> //虚拟机名称，同一物理机唯一   
> <uuid>fd3535db-2558-43e9-b067-314f48211343</uuid> //同一物理机唯一，可用uuidgen生成   
> <memory>524288</memory> //内存，单位K 1048576 = 1G, 524288 = 500M  
> <currentMemory>524288</currentMemory> //memory这两个值最好设成一样   
> <vcpu>2</vcpu> //虚拟机可使用的cpu个数，查看物理机可用CPU个数：cat /proc/cpuinfo |grep processor | wc -l <os>   
> <type arch='x86<sub>64</sub>' machine='pc'>hvm</type> //arch指出系统架构类型，machine 则是机器类型，查看机器类型：qemu-system-x86<sub>64</sub> -M help?   
> <boot dev='hd'/> //启动介质，第一次需要装系统可以选择cdrom光盘启动   
> <boot dev='cdrom'/>
> <bootmenu enable='yes'/> //表示启动按F12进入启动菜单   
> </os> 
> <features>   
> <acpi/> //Advanced Configuration and Power Interface,高级配置与电源接口   
> <apic/> //Advance  
> </features>  
>   <cpu mode='custom' match='exact'>  
>     <model fallback='allow'>Westmere</model>  
>   </cpu>  
>   <clock offset='utc'>  
>     <timer name='rtc' tickpolicy='catchup'/>  
>     <timer name='pit' tickpolicy='delay'/>  
>     <timer name='hpet' present='no'/>  
>   </clock>  
>   <on<sub>poweroff</sub>>destroy</on<sub>poweroff</sub>>  
>   <on<sub>reboot</sub>>restart</on<sub>reboot</sub>>  
>   <on<sub>crash</sub>>restart</on<sub>crash</sub>>  
>   <pm>  
>     <suspend-to-mem enabled='no'/>  
>     <suspend-to-disk enabled='no'/>  
>   </pm>  
> <devices>  
>     <emulator>/usr/bin/qemu-kvm</emulator>      #模拟器：qemu-kvm  
>     <disk type='file' device='disk'>                #disk 磁盘  
>       <driver name='qemu' type='raw'/>  
>       <source file='/home/gsdj/test.img'/>  
>       <target dev='hdb' bus='ide'/>
> 
>      #[https://blog.51cto.com/yinweiai/2109033
>  ](https://blog.51cto.com/yinweiai/2109033)  
>       <address type='drive' controller='0' bus='1' target='0'
> unit='0'/>  
> 
> function='0x0'/>  
>     </disk>  
>     <disk type='file' device='cdrom'>              #cdrom  
>       <driver name='qemu' type='raw'/>  
>       <source file='/home/gsdj/win10.iso'/> //光盘镜像路径  
> 
>       <target dev='hda' bus='ide'/>  
>       <readonly/>  
>       <address type='drive' controller='0' bus='0' target='0'
> unit='0'/>  
>     </disk>  
> <graphics type='vnc' port='-1' autoport='yes' listen = '0.0.0.0'
> keymap='en-us'/>//vnc方式登录，端口号自动分配，自动加1，可以通过virsh
> vncdisplay来查询  
>    
> </devices>  
> </domain>  

还是没有硬盘。说明，生成一块新的raw格式的空盘 <https://qzc.iteye.com/blog/1820985>

> \#qemu-img create -f raw test<sub>add.img</sub> 10G  
> #virsh attach-disk test /var/lib/libvirtd/images/test<sub>add.img</sub> vdb &#x2014;cache none  
> virsh list &#x2013;all
> 
> Id   Name    State    
> ---------------------  
> 
> -   win10   关闭
> 
>   
> 
> virsh start  win10    //启动虚拟机  
> 
> virsh vncdisplay win10
>   //查看虚拟机的vnc端口， 然后就可以通过vnc登录来完成虚拟机的安装
> 
> virsh undefine win10 //删除它。  

挂载真实分区，在qemu-sys  tem-x86<sub>64启动虚拟机的命令里面直接添加参数</sub>

> -hdb
> /dev/sda4  //不能使用hda，这个已经被虚拟机的系统盘使用，目前可以使用的是好的hdc
> hdd，在虚拟机内对应的就是D、E、F盘  

附录：<https://www.jianshu.com/p/3890c45b3645>

virt-install -n oel1 &#x2013;ram 512 &#x2013;vcpus=1 &#x2013;disk path=/data/vm/oel1/oel1<sub>raw.img,format</sub>=raw,size=20,bus=virtio &#x2013;accelerate &#x2013;cdrom /data/ios/OracleLinux-R6-U6-Server-x86<sub>64</sub>-dvd.iso &#x2013;vnc &#x2013;vncport=5910 &#x2013;vnclisten=0.0.0.0 &#x2013;network bridge=br0,model=virtio &#x2013;noautoconsole
  
virsh console centos1 <https://blog.51cto.com/7424593/1735600>  

\*<https://blog.csdn.net/chenmoshashou2/article/details/80622064>  

\*

\*定义\*：virsh definexxx.xml
xxx为xml文件所在的路径及文件名称，在当前目录下则不写路径

\*启动\*：virsh start xyz
xyz为虚拟机xml配置文件中虚拟机的名字<name>rhel6.2<sub>2</sub></name>

\*停止\*：virsh shutdownxyz 此方法为正常关机方法，需要一段才能关机

\*下电\*：virsh destroy xyz 此方法为暴力下电，虚拟机立即关闭

\*删除\*：virsh undefinexxx
关闭了的虚拟机，只是不在运行状态而已，通过virsh undefine
xxx就能从virsh列表里面（virsh
list查看当前系统中的虚拟机列表，详见第2.4节）将其删除，undefine命令不会删除镜像文件和xml文件。运行状态的虚拟机是不能删除的。

\*临时起虚拟机：\*virsh create xxx.xml
此方法为方便开发调试等临时需求，不会持久化，虚拟机关机后就消失了，不推荐生产系统使用。

\*查看VNC端口：\*virshvncdisplay xx 查看VNC端口，其中xx可通过virsh
list查看


## 运行qemu并加载磁盘

qemu-system-x86<sub>64</sub> -kernel /usr/src/linux-4.6.2-x86<sub>64</sub>/arch/x86/boot/bzImage -initrd ~/Downloads/initramfs.img ~/Downloads/disk.img -append "console=ttyS0" -nographic 


<a id="orgb9dc8ec"></a>

# 自用万能启动U盘FBA版2016-12-20 最新增强版

<http://bbs.wuyou.net/forum.php?mod=viewthread&tid=385360>


<a id="org838e35e"></a>

# 一个撒手不管pe

<https://bbs.wuyou.net/forum.php?mod=viewthread&tid=397953&extra=page%3D1>

