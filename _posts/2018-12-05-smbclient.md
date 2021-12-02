---
layout: post
title:  "smbclient"
date:   2018-12-05
tags:
      - it
---


win7下先share目录ISO



Linux：

smbclient -L 192.168.1.101



> 
>     Sharename       Type      Comment
>     \-\-\-\-\-\-\-\--       \-\-\--      \-\-\-\-\-\--
>     ADMIN\$          Disk      远程管理
>     C\$              Disk      默认共享
>     D\$              Disk      默认共享
>     IPC\$            IPC       远程 IPC
>     ISO-ghost       Disk      




smbclient //192.168.1.101/ISO-ghost -U username%password

> Enter SAMBA\\gsdj\'s password: 
> Try \"help\" to get a list of possible commands.
> smb: \\\> ls
> 
> smb: \\\> get Win10PE_17763.107x64精简.iso
> 
>
> smb: \\\> q

还可以挂载 

mount -t cifs -o username=administrator,password=123456
//192.168.0.1/tmp /mnt/tmp 

