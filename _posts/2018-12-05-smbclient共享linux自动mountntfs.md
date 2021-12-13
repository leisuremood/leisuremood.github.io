---
layout: post
title: "smbclient共享linux自动mountntfs"
date: 2018-12-05
tags: 
    - it
---


# Table of Contents

1.  [smbclient共享](#orgf07c381)
2.  [自动mount ntfs](#orgda0feb4)


<a id="orgf07c381"></a>

# smbclient共享

win7下先share目录ISO

Linux：

smbclient -L 192.168.1.101

<p class="verse">
<br />
&#xa0;&#xa0;&#xa0;&#xa0;&#xa0;&#xa0;Sharename       Type      Comment<br />
&#xa0;&#xa0;&#xa0;&#xa0;&#xa0;&#xa0;----&#x2013;&#x2014;       -&#x2014;      --&#x2013;&#x2014;<br />
&#xa0;&#xa0;&#xa0;&#xa0;&#xa0;&#xa0;ADMIN$          Disk      远程管理<br />
&#xa0;&#xa0;&#xa0;&#xa0;&#xa0;&#xa0;C$              Disk      默认共享<br />
&#xa0;&#xa0;&#xa0;&#xa0;&#xa0;&#xa0;D$              Disk      默认共享<br />
&#xa0;&#xa0;&#xa0;&#xa0;&#xa0;&#xa0;IPC$            IPC       远程<br />
&#xa0;&#xa0;&#xa0;&#xa0;&#xa0;&#xa0;IPC       ISO-ghost       Disk<br />
</p>

smbclient //192.168.1.101/ISO-ghost -U username%password

<p class="verse">
Enter SAMBA\gsdj's password:  Try "help" to get a list of possible<br />
commands. smb: \\> ls<br />
<br />
smb: \\> get Win10PE\_17763.107x64精简.iso<br />
smb: \\> q<br />
</p>

还可以挂载 

mount -t cifs -o username=administrator,password=123456 //192.168.0.1/tmp /mnt/tmp


<a id="orgda0feb4"></a>

# 自动mount ntfs

<p class="verse">
/etc/fstab<br />
<br />
UUID="60CA0B46CA0B17C4" /mnt/lenovo  ntfs defaults,noauto,user,exec 0 0<br />
</p>

ntfs-3g 需要一些设置： <https://www.tuxera.com/community/ntfs-3g-faq/#useroption>

**Why do I get "fusermount: option blkdev is privileged" error?**

Unprivileged block device mounts work only if all the below requirements
are met:

1.  ntfs-3g is compiled with integrated FUSE support

2.  the ntfs-3g binary is at least version 1.2506

3.  the ntfs-3g binary is set to setuid-root

4.  the user has access right to the volume

5.  the user has access right to the mount point

The root user can make an ntfs-3g binary setuid-root as shown below  
chown root $(which ntfs-3g)   chmod 4755 $(which ntfs-3g) In such case
the driver will also be able

-   to fix common FUSE kernel module loading problems

-   to create the required but sometimes incorrectly removed or missing
    FUSE device file

Please note that using setuid-root can result unforeseen privilege
escalation and its usage is discouraged. Only the absolutely trusted
users must be granted such access. Below is an example how this can be
done for users in the ntfsuser group to be able to mount any NTFS volume
if they have also the needed volume access rights.   addgroup ntfsuser  
chown root:ntfsuser $(which ntfs-3g)   chmod 4750 $(which ntfs-3g)  
usermod -aG ntfsuser allowed-user The setuid-root ntfs-3g driver applies
the principle of least privilege during its lifetime as a safety
measure.

