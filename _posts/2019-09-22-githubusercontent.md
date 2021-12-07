---
layout: post
title:  "GitHub网页githubusercontent地址访问"
date:   2019-09-22
tags:
      - it
---

ref: https://zhuanlan.zhihu.com/p/107691233

hosts文件内容如下：
~~~
# 2020年12月30日更新

# GitHub Start
52.74.223.119     github.com
52.74.223.119   gist.github.com
54.169.195.247   api.github.com
185.199.111.153   assets-cdn

.github.com
199.232.96.133    raw.githubusercontent.com
199.232.96.133    gist.githubusercontent.com
199.232.96.133    cloud.githubusercontent.com
199.232.96.133   camo.githubusercontent.com
199.232.96.133   avatars0.githubusercontent.com
199.232.96.133    avatars1.githubusercontent.com
199.232.96.133   avatars2.githubusercontent.com
199.232.96.133    avatars3.githubusercontent.com
199.232.96.133    avatars4.githubusercontent.com
199.232.96.133    avatars5.githubusercontent.com
199.232.96.133    avatars6.githubusercontent.com
199.232.96.133    avatars7.githubusercontent.com
199.232.96.133    avatars8.githubusercontent.com
199.232.96.133  user-images.githubusercontent.com
185.199.109.154   github.githubassets.com
# GitHub End
~~~

然后
sudo systemctl restart NetworkManager.service
不用可能也可以。

ping raw.githubusercontent.com
