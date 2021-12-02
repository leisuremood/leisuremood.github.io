---
layout: post
title:  "用qemu修复win10 bcdboot"
date:   2016-03-09
tags:
      - it
---

## 用qemu修复win10 bcdboot {#用qemu修复win10-bcdboot .tit .js-630307}

> qemu-img create -f qcow2 fedora.img 1G

> qemu-system-x86_64 -m 50 fedora.img -cdrom ./ReactOS-LiveCD.iso
>
>  检查KVM是否可用：grep -E \'vmx\|svm\' /proc/cpuinfo；
>
> lsmod \| grep kvm
>
> \
>
> qemu-system-x86_64 -m 2048 -enable-kvm fedora.img
> -cdrom ./Fedora-Live-Desktop-x86_64\...
:::

::: {.txt .js-630307 style="display: block;"}
> qemu-img create -f qcow2 fedora.img 1G

> qemu-system-x86_64 -m 50 fedora.img -cdrom ./ReactOS-LiveCD.iso
>
>  检查KVM是否可用：grep -E \'vmx\|svm\' /proc/cpuinfo；
>
> lsmod \| grep kvm
>
> \
>
> qemu-system-x86_64 -m 2048 -enable-kvm fedora.img
> -cdrom ./Fedora-Live-Desktop-x86_64-20-1.iso
>
> **qemu-system-x86_64-M pc
> \--enable-kvm -m**size**-hda**filename**-cdrom**iso_name**-boot c -k
> en-us -smp**cpu_number**-usbdevice tablet**，在空白磁盘中安装OS镜像。

可以运行，但是没有增加磁盘。\

> qemu-system-x86_64 -M pc \--enable-kvm -m 2048 -cdrom Win10PEx64.iso
> -drive file=test.img,if=none,id=drive-ide0-0-0,format=raw
>
> qemu-system-x86_64 -m 2048 -name tst -smp 2 -blockdev
> driver=raw,node-name=disk,file.driver=file,file.filename=test.img
> -cdrom Win10PEx64.iso\
>
> qemu-system-x86_64 -M pc \--enable-kvm -m 2048 -hda test.img -cdrom
> Win10PEx64.iso

实际上是有的：用win10安装盘可以看到硬盘。

> qemu-kvm -m 2048 -name tst -smp 2 -hda test.img -cdrom win10.iso
>
> \

\

> **sudo qemu-system-x86_64 --M pc -m**memsize**\--enable-kvm -hda
> imgname -net nic,model=virtio,vlan=0,macaddr=00:e0:fc:00:00:01 -net
> tap,vlan=0,ifname=tap0,script=no -cdrom virtio-win-0.1-100.iso
> -usbdevice tablet -vga std -boot
> c**，启动已经安装好Windows操作系统的虚拟机。\

定义一个虚拟机：可以用gnome-boxes创建一个，然后virsh dumpxml win10 \>
win10.xml 做参考。\

virsh define win10.xml       //创建虚拟机\

> \<domain type=\'kvm\'\> //如果是Xen，则type='xen' \
> \<name>win10\</name> //虚拟机名称，同一物理机唯一 \
> \<uuid>fd3535db-2558-43e9-b067-314f48211343\</uuid> //同一物理机唯一，可用uuidgen生成 \
> \<memory>524288\</memory> //内存，单位K 1048576 = 1G, 524288 = 500M\
> \<currentMemory>524288\</currentMemory> //memory这两个值最好设成一样 \
> \<vcpu>2\</vcpu> //虚拟机可使用的cpu个数，查看物理机可用CPU个数：cat
> /proc/cpuinfo \|grep processor \| wc -l \<os> \
> \<type arch=\'x86_64\'
> machine=\'pc\'\>hvm\</type> //arch指出系统架构类型，machine
> 则是机器类型，查看机器类型：qemu-system-x86_64 -M help? \
> //\<boot
> dev=\'hd\'/> //启动介质，第一次需要装系统可以选择cdrom光盘启动 \
> \<boot dev=\'cdrom\'/>\
> \<bootmenu enable=\'yes\'/> //表示启动按F12进入启动菜单 \
> \</os> \
> \<features> \
> \<acpi/> //Advanced Configuration and Power
> Interface,高级配置与电源接口 \
> \<apic/> //Advance\
> \</features>\
>   \<cpu mode=\'custom\' match=\'exact\'\>\
>     \<model fallback=\'allow\'\>Westmere\</model>\
>   \</cpu>\
>   \<clock offset=\'utc\'\>\
>     \<timer name=\'rtc\' tickpolicy=\'catchup\'/>\
>     \<timer name=\'pit\' tickpolicy=\'delay\'/>\
>     \<timer name=\'hpet\' present=\'no\'/>\
>   \</clock>\
>   \<on_poweroff>destroy\</on_poweroff>\
>   \<on_reboot>restart\</on_reboot>\
>   \<on_crash>restart\</on_crash>\
>   \<pm>\
>     \<suspend-to-mem enabled=\'no\'/>\
>     \<suspend-to-disk enabled=\'no\'/>\
>   \</pm>\
> \<devices>\
>     \<emulator>/usr/bin/qemu-kvm\</emulator>      #模拟器：qemu-kvm\
>     \<disk type=\'file\' device=\'disk\'\>                #disk 磁盘\
>       \<driver name=\'qemu\' type=\'raw\'/>\
>       \<source file=\'/home/gsdj/test.img\'/>\
>       \<target dev=\'hdb\' bus=\'ide\'/>
>
>      #[https://blog.51cto.com/yinweiai/2109033
>  ](https://blog.51cto.com/yinweiai/2109033)\
>       \<address type=\'drive\' controller=\'0\' bus=\'1\' target=\'0\'
> unit=\'0\'/>\
> #      \<address type=\'pci\' domain=\'0x0000\' bus=\'0x00\'
> slot=\'0x04\' function=\'0x0\'/>\
>     \</disk>\
>     \<disk type=\'file\' device=\'cdrom\'\>              #cdrom\
>       \<driver name=\'qemu\' type=\'raw\'/>\
>       \<source file=\'/home/gsdj/win10.iso\'/> //光盘镜像路径\
> #      \<source file=\'/home/gsdj/Win10PEx64.iso\'/> //光盘镜像路径\
>       \<target dev=\'hda\' bus=\'ide\'/>\
>       \<readonly/>\
>       \<address type=\'drive\' controller=\'0\' bus=\'0\' target=\'0\'
> unit=\'0\'/>\
>     \</disk>\
> \<graphics type=\'vnc\' port=\'-1\' autoport=\'yes\' listen
> = \'0.0.0.0\'
> keymap=\'en-us\'/>//vnc方式登录，端口号自动分配，自动加1，可以通过virsh
> vncdisplay来查询\
>  \
> \</devices>\
> \</domain>\
> \
>
>   

还是没有硬盘。

　　//说明，生成一块新的raw格式的空盘 <https://qzc.iteye.com/blog/1820985>

       #qemu-img create -f raw test_add.img 10G\
　　#virsh attach-disk test /var/lib/libvirtd/images/test_add.img vdb
---cache none\
virsh list \--all

> \
>
> Id   Name    State  \
> \-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--\
>  -    win10   关闭   \
> \

virsh start  win10    //启动虚拟机\

virsh vncdisplay win10
  //查看虚拟机的vnc端口， 然后就可以通过vnc登录来完成虚拟机的安装

virsh undefine win10 //删除它。\

挂载真实分区，在qemu-sys  tem-x86_64启动虚拟机的命令里面直接添加参数

> -hdb
> /dev/sda4  //不能使用hda，这个已经被虚拟机的系统盘使用，目前可以使用的是好的hdc
> hdd，在虚拟机内对应的就是D、E、F盘\

\

附录：<https://www.jianshu.com/p/3890c45b3645>

//virt-install -n oel1 \--ram 512 \--vcpus=1 \--disk
path=/data/vm/oel1/oel1_raw.img,format=raw,size=20,bus=virtio
\--accelerate \--cdrom /data/ios/OracleLinux-R6-U6-Server-x86_64-dvd.iso \--vnc \--vncport=5910 \--vnclisten=0.0.0.0 \--network
bridge=br0,model=virtio \--noautoconsole\
\
virsh console centos1 <https://blog.51cto.com/7424593/1735600>\

**<https://blog.csdn.net/chenmoshashou2/article/details/80622064>\
**

**定义**：virsh definexxx.xml
xxx为xml文件所在的路径及文件名称，在当前目录下则不写路径

**启动**：virsh start xyz
xyz为虚拟机xml配置文件中虚拟机的名字\<name>rhel6.2_2\</name>

**停止**：virsh shutdownxyz 此方法为正常关机方法，需要一段才能关机

**下电**：virsh destroy xyz 此方法为暴力下电，虚拟机立即关闭

**删除**：virsh undefinexxx
关闭了的虚拟机，只是不在运行状态而已，通过virsh undefine
xxx就能从virsh列表里面（virsh
list查看当前系统中的虚拟机列表，详见第2.4节）将其删除，undefine命令不会删除镜像文件和xml文件。运行状态的虚拟机是不能删除的。

**临时起虚拟机：**virsh create xxx.xml
此方法为方便开发调试等临时需求，不会持久化，虚拟机关机后就消失了，不推荐生产系统使用。

**查看VNC端口：**virshvncdisplay xx 查看VNC端口，其中xx可通过virsh
list查看

\

运行qemu并加载磁盘\

qemu-system-x86_64
-kernel /usr/src/linux-4.6.2-x86_64/arch/x86/boot/bzImage -initrd \~/Downloads/initramfs.img \~/Downloads/disk.img -append \"console=ttyS0\" -nographic 
