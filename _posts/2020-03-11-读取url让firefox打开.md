---
layout: post
title:  "读取url让firefox打开"
date:   2020-03-11
tags:
      - 随笔
---

# 读取url让firefox打开


-   读这个文件，把文件每一行存在一个列表里并返回

(defun read-file-as-lines
(file-path)  (with-temp-buffer    (insert-file-contents
file-path)    (split-string (buffer-string) \"\\n\" t)))

(read-file-as-lines \"./1.txt\") > #\'urls  ;; 把结果给变量urls。

-   列表一个个打印出来

 (while urls

(message \"%s\" (pop urls)       

(sleep-for 1)))        ;; pop用于减少list

-   调用firefox

\"C:\\Program Files\\Mozilla Firefox\\firefox.exe\" -new-tab \$urls



