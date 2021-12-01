---
layout: post
title:  Git代理设置与访问Github
date:   2015-01-29
tags:
      - 随笔
---
::: txtcont
https://blog.csdn.net/bluishglc/article/details/37807501

\

由于目前工作的网络环境有着很严格的限制，可以说最近在通过代理访问Github上颇费了一些周折，也积累了一些相关的经验，觉得有必要备忘一下。本着"不重新发明轮子"的宗旨，本文不会对Github代理设置的每一个细节进行赘述，文中会给出现成的参考文章，这里要做的是针对一些重要的问题进行解释和补充，相信一定能给遇到同样问题朋友以帮助。本文原文出处:<https://blog.csdn.net/bluishglc/article/details/37807501> 严禁任何形式的转载，否则将委托CSDN官方维护权益！\
\
**1. 别折腾了，请先屏蔽NTLM验证！**\
\
首先，如果你的网络代理使用的是NTLM验证，不要作任何无谓的"挣扎"，请先安装Cntlm，建立一个无须身份验证的普通HTTP代理，因为目前版本的GIT不支持NTLM验证的代理，不先借助Cntlm屏蔽掉NTLM验证，后续的任何工作都是无意义的。关于Cntlm的安装和配置请参考：<https://blog.csdn.net/bluishglc/article/details/37600773>\
\
**2. Git代理配置走起 **\
\
关于Git代理的配置请参考：<https://jixiuf.github.io/git/git-proxy-on-windows.html> 这里对文中提到的ssh的config文件简单地解释一下，这个文件主要是保存一些经常访问的目标主机的连接配置，很像Putty里保存的session。config文件的结构是是以Host开头的配置项是作为一组，Host项之后到下一个Host之前的所以配置项都是针对这个Host配置的！另外，文中config文件提到的connect是一种代理转发工具，对一个简单的命令行工具不太可能设计如何配置代理，或者像SSH这样为了保证程序本身功能的单一，把代理配置委托第三方工具实现，这就是connect的作用。\
\
**3. https://？可以！git://？没门！**\
\
安装配置好Cntlm，参看前面的文章进行配置，我们就可以在Github上检入检出https://描述的repository了，如果你尝试使用git://或git\@github.com格式的地址访问，则在某些代理环境下可能会报出如下错误：\
\
HTTP/1.1 502 Proxy Error ( The specified Secure Sockets Layer (SSL) port
is not allowed. Forefront TMG is not configured to allow SSL requests
from this port. Most Web browsers use port 443 for SSL requests.  )\
\
引起这一问题的原因是git://使用的9418端口以及基于SSH的git\@github.com格式的地址使用的22端口在你的网络环境里(特别是代理服务端)被防火墙屏蔽了。这是极为常见的，大多数的代理环境基于安全考虑，会屏蔽除80(http)和443(https)之外的端口。那这是否意味着我们就不能在这种网络环境里使用git://或git\@github.com格式的地址呢？请继续看下文。\
\
**\
4. 幸好有你：SSH over the HTTPS port**\
\
虽然可以在Github上使用HTTPS协议push代码（使用的是用户名在Github上的用户名和密码），但通常来说，使用SSH检出和提交代码是更为安全，更适合团队开发，那么怎么解决前文提到的问题呢？对此，Github通过将ssh的连接端口指定为https使用的443，使用了另外一个主机名：ssh.github.com来解决这一问题，这样我们可以通过大多数代理服务器允许的443端口，使用SSH访问Github了，这一点在前面参考文章中提到的config文件里也配置过了，就是Host为ssh.github.com的部分，关于这部分的说明和配置，Github的官方帮助文档也有说明：<https://help.github.com/articles/using-ssh-over-the-https-port>。这里我只想说一下如何验证配置已经成功，显然，只要我们能通过SSH登入目标主机就表明配置正确了：\
\
方法一：像我们前面提到的参考文章，它把ssh.github.com的配置参数写进了ssh的config文件，这样可以在命令行工具中使用\
\
**ssh -T -p 443 git\@ssh.github.com**\
\

就能验证config文件配置是否正确了，成功登入后提示：

![](https://simg.sinajs.cn/blog7style/images/common/sg_trans.gif "Git代理设置与访问Github"){real_src="https://img.blog.csdn.net/20140714203047421?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYmx1aXNoZ2xj/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center"
style="max-width:500px;"}

方法二：如果在配置这些参数之前我们不能确定它们的正确性，我们可以先通过命令行的方式来指定这些参数，待验证通过后把这些参数写入到config文件中。此时的命令要这样写：\
\
**ssh -o ProxyCommand=\"/path/to/connect -H 代理主机:端口 %h %p\" -i
\"/path/to/your/github_private_key\" -T -p 443 git\@ssh.github.com**\
\
成功登入后提示：\
![](https://simg.sinajs.cn/blog7style/images/common/sg_trans.gif "Git代理设置与访问Github"){real_src="https://img.blog.csdn.net/20140714203424859?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYmx1aXNoZ2xj/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center"
style="max-width:500px;"}

\

方法三：我们还可以通过putty以另外一咱完全独立和便捷的方式来验证，即在putty中建立一个session,目标主机是ssh.github.com，端口443，登入用户名443，指定好你的Github私钥（注意：putty使用的私钥与openssh的私钥格式是不一样的，如果你没有保存putty格式的私钥就需要通过putty的keygen工具生成或转换），设定好代理主机和端口然后连接，如果出现如下图的提示，也表示配置正确！\
\
![](https://simg.sinajs.cn/blog7style/images/common/sg_trans.gif "Git代理设置与访问Github"){real_src="https://img.blog.csdn.net/20140714203518804?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYmx1aXNoZ2xj/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center"
style="max-width:500px;"}

\

 

最后特别提醒的是，在上述配置验证成功后，我们从github上检出项目时务必记得修改主机名，把git\@github.com改为git@**ssh**.github.com

以检出opentsdb项目为例，命令要从\
**\
**

**git clone git\@github.com:OpenTSDB/opentsdb.git**\
\

变为：\
\

**git clone git\@ssh.github.com:OpenTSDB/opentsdb.git**

**\
**
:::
