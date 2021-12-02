---
layout: post
title:  "自动mountntfs"
date:   2018-12-16
tags:
      - it
---


/etc/fstab

UUID=\"60CA0B46CA0B17C4\" /mnt/lenovo        ntfs   
defaults,noauto,user,exec 0 0



ntfs-3g
需要一些设置： <https://www.tuxera.com/community/ntfs-3g-faq/#useroption>

**Why do I get "fusermount: option blkdev is privileged" error?**

Unprivileged block device mounts work only if all the below requirements
are met:

1.  ntfs-3g is compiled with integrated FUSE support

2.  the ntfs-3g binary is at least version 1.2506

3.  the ntfs-3g binary is set to setuid-root

4.  the user has access right to the volume

5.  the user has access right to the mount point

The root user can make an ntfs-3g binary setuid-root as shown below
  chown root \$(which ntfs-3g)
  chmod 4755 \$(which ntfs-3g)
In such case the driver will also be able

-   to fix common FUSE kernel module loading problems

-   to create the required but sometimes incorrectly removed or missing
    FUSE device file

Please note that using setuid-root can result unforeseen privilege
escalation and its usage is discouraged. Only the absolutely trusted
users must be granted such access. Below is an example how this can be
done for users in the ntfsuser group to be able to mount any NTFS volume
if they have also the needed volume access rights.
  addgroup ntfsuser
  chown root:ntfsuser \$(which ntfs-3g)
  chmod 4750 \$(which ntfs-3g)
  usermod -aG ntfsuser allowed-user
The setuid-root ntfs-3g driver applies the principle of least privilege
during its lifetime as a safety measure.



