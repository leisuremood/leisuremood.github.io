---
layout: post
title:  "chrome的tampermonkey"
date:   2018-12-06
tags:
      - 随笔
---

# chrome的tampermonkey


<https://sspai.com/post/40485>

用 Chrome 的人都需要知道的「神器」扩展：「油猴」使用详解

2017年08月22日

[![](https://cdn.sspai.com/attachment/origin/2016/09/11/346714.png?imageMogr2/quality/95/thumbnail/!60x60r/gravity/Center/crop/60x60){style="max-width:500px;"}](https://sspai.com/user/724096) [Eric_hong](https://sspai.com/user/724096)

不装扩展（Extensions）的 Chrome 只能发挥它 40% 的能力。

各类实用的 Chrome 扩展是不少人选择 Chrome
浏览器的重要原因，经过多年发展，Chrome 的扩展种类已经非常丰富，除了那些「即装即用」的小工具之外，也有很多被誉为「神器」的强大扩展程序。

少数派此前介绍过的 [Stylish](https://sspai.com/post/34508) 就算一款「神器」，通过安装各类
CSS
模板，它几乎可以美化一切网页。而另一款不得不提的「神器」就是今天要介绍的
Tampermonkey，也被戏称为「油猴」。

和 Stylish
类似，「油猴」也可以通过安装各类脚本对网站进行定制。不过它能定制的不仅仅是网站的样式，还能实现更多更强大的功能，例如：

-   直接下载百度网盘文件

-   重新定制繁杂的微博页面

-   去掉视频播放广告

-   将网站默认的「二维码登录」改回「账号密码登录」

-   绕过搜索引擎的跳转提示

-   还原清新的小说阅读模式

-   豆瓣和 IMDb 互相显示评分

-   ......

你可能听说过「油猴」，但是因为看到「脚本」而不敢尝试，其实它的操作非常简单，只要经过简单设置，下载一些现成脚本，就可以实现上面提到的实用的功能。

如何使用 Tampermonkey

如果能正常访问 Chrome
应用商店，可以直接在商店内下载 [Tampermonkey](https://chrome.google.com/webstore/detail/tampermonkey/dhdgffkkebhmkfjojejmpbldmpobfkfo)。

如果不能，在这里推荐一个叫 [Crx4Chrome](https://www.crx4chrome.com/) 的网站，可以直接下载
Chrome Store 插件，在 Crx4Chrome 搜索并下载 [Tampermonkey
插件](https://www.crx4chrome.com/extensions/dhdgffkkebhmkfjojejmpbldmpobfkfo/) 到本地之后，再打开
Chrome 浏览器「扩展程序」页面，将下载的 crx 文件拖拽到页面即可完成安装。

 ![](https://cdn.sspai.com/2017/08/22/24e0cd98a99fefc5b126db8822d048c4.jpg?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1){style="max-width:500px;"}

另外，Tampermonkey 还支持 Microsoft Edge、Safari、Opera
Next、Firefox、Dolphin Browser、UC Browser
等浏览器平台，你可以在 [软件官网](https://tampermonkey.net/) 下载到对应版本。

Tampermonkey 设置选项 

安装好之后，会在浏览器地址栏右侧看到类似望远镜的图标，这个就是
Tampermonkey，点击右键选择选项，即可看到设置页面：

 ![](https://cdn.sspai.com/2017/08/22/5e4b004692953d98e9f1d38fc2ba9d9b.jpg?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1){style="max-width:500px;"}

Tampermonkey
提供了友好的中文化界面，懒得折腾的用户使用默认设置即可，无需更改任何选项。

如果需要更多高级设置选项的话，可自行打开「初学者」或者「高级」配置模式，设置将提供动作菜单、更细致的脚本更新、TESLA、加强版编辑器、安全、黑名单检查等高级选项。

脚本安装和管理 

油猴默认是没有安装任何脚本的，你可以通过设置页面的「已安装脚本 - 获取脚本...」来下载各种脚本。

比较常用的下载渠道有三个：

-   [Greasy
    Fork](https://greasyfork.org/zh-CN)：支持中文，按照今日安装、总安装数、得分、创建日期等的排序方式给出脚本列表，可按脚本生效的网站过滤，每一脚本都有中文介绍，并且详细列出了作者、安装数、更新日期日志、安装使用截图、兼容性、应用到、代码查看等信息描述。

-   [OpenUserJS](https://openuserjs.org/)、[Userscripts
    Mirror](https://userscripts-mirror.org/)：这两个网站都没有提供中文界面，Userscripts
    Mirror 已经停止了更新，用户可以在这个站点找到历史资源。

找到需要的脚本后，会在介绍页面看到安装（install）按钮，点击下载脚本后会自动跳转到安装界面，再点击安装就可以享用脚本了。

比如，在 OpenUserJS 的 Yet Another Weibo Filter 脚本页面，点击右侧
Install 之后会跳转到 Tampermonkey
的安装页面，这里列出了脚本的基本说明和源代码，再次点击页面中的「安装」按钮即可。

 ![](https://cdn.sspai.com/2017/08/22/d94fb25ea580d8d150520fe8807a295b.jpg?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1){style="max-width:500px;"}

此时打开微博网站，点击地址栏右侧的 Tampermonkey 图标，可以看到这个 Yet
Another Weibo Filter 已经成功启用，用户可以点击 ON
按钮临时关闭使用该脚本。

 ![](https://cdn.sspai.com/2017/08/22/7bbd95560daf3e93a7adcd3754e17464.jpg?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1){style="max-width:500px;"}

打开 Tampermonkey 设置页面的「已安装脚本」，我们可以看到刚安好的 Yet
Another Weibo Filter
脚本，用户在这里可以选择是否打开脚本，或是对脚本进行编辑、提交 Bug
以及删除脚本等多项操作。

 ![](https://cdn.sspai.com/2017/08/22/d8b234b3f39108a44a1708c509ed265e.jpg?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1){style="max-width:500px;"}

建议在安装好各个脚本之后，在实用程序的页面中，将脚本存储和 Tampermonkey
设置以文件或者压缩包的形式导出，方便以后数据备份。

优秀脚本推荐 

在上面提供的三个网站大家可以看到各种功能的脚本，你可以根据自己的需求进行下载，如果不知道该下载哪些，下面为大家推荐
15 个优秀实用的用户脚本，不妨先来看看。

看真正想看的微博： [Yet Another Weibo
Filter](https://greasyfork.org/zh-CN/scripts/3249-yet-another-weibo-filter)

Weibo
官方界面已经成为不少脚本应用必修的对象，ts 开发的这款脚本可以高度定制
Weibo
电脑端版面，去除各类广告、微博主自带的各种徽标、过滤热门话题等主要功能，用户需要在脚本的设置中启用相应功能。

如果希望单独安装浏览器插件的话，推荐 [眼不见心不烦](https://openuserjs.org/scripts/ts/%5Bhttps://code.google.com/p/weibo-content-filter/)，其支持 [Chrome](https://chrome.google.com/webstore/detail/aognaapdfnnldnjglanfbbklaakbpejm) 和 [Firefox
脚本](https://bitbucket.org/salviati/weibo-cleaner/downloads/weiboCleaner-latest.user.js)

 ![](https://cdn.sspai.com/2017/08/22/58468d8dfb70175b9d8723a74e86879e.jpg?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1){style="max-width:500px;"}还原真实下载地址： [百度网盘直接下载助手](https://greasyfork.org/zh-CN/scripts/23635-%E7%99%BE%E5%BA%A6%E7%BD%91%E7%9B%98%E7%9B%B4%E6%8E%A5%E4%B8%8B%E8%BD%BD%E5%8A%A9%E6%89%8B)

安装百度网盘直接下载助手之后，打开需要下载的资源页面，会出现下载助手的按钮，提供直接下载（支持多文件和目录下载）、显示链接以及外链下载的选项，可实现直接复制到下载工具使用。

 ![](https://cdn.sspai.com/2017/08/22/2270e20409022d4df201689d13e13e28.jpg?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1){style="max-width:500px;"}让所有视频网页全屏： [视频网页全屏](https://greasyfork.org/zh-CN/scripts/4870-maximize-video)

可以让网页中任何一个视频网页全屏播放的「神器」，目前支持有多个视频的任意网页、HTML5
格式的视频。

 ![](https://cdn.sspai.com/2017/08/22/b10c152dd613d92831699f231fad7ddf.jpg?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1){style="max-width:500px;"}豆瓣和
IMDb
互相显示评分： [MoreMovieRatings](https://greasyfork.org/zh-CN/scripts/7687-moremovieratings)

不少人看电影时喜欢参考 IMDb
和豆瓣电源的评分，这款脚本正好满足两者需求，可以在豆瓣和 IMDb
互相显示评分，电影党必备。

 ![](https://cdn.sspai.com/2017/08/22/66d6956443781f679a395d358e27a65a.jpg?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1){style="max-width:500px;"}查看完整的知乎回答而无需注册登录： [Zhihu
Visitor](https://openuserjs.org/scripts/ts/Zhihu_Visitor)

知乎问题页面里，比较长的答案添加展开按钮，点击可以显示全文。点击「更多回答」可以加载更多回答而非登录框。隐藏了必须登录才能使用的相关功能的按钮，如点赞或收藏等。

 ![](https://cdn.sspai.com/2017/08/22/c40144e1e594c520cd37b41c0c0d0a28.jpg?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1){style="max-width:500px;"}增强版的
YouTube 功能改造： [YouTube
+](https://openuserjs.org/scripts/ParticleCore/YouTube_+)

YouTube + 可以给 Youtube
增加更多的功能选项，包括但不限于小窗口播放视频、播放您最近订阅播放列表、视频截图保存、只允许你订阅频道的视频播放广告等等。不过目前
YouTube + 不支持 YouTube beta Material Layout 测试版。

 ![](https://cdn.sspai.com/2017/08/22/f6e9a705a48f01cc1bba7cbc2c40d397.jpg?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1){style="max-width:500px;"}自动翻页
Google 搜索结果： [Endless
Google](https://openuserjs.org/scripts/tumpio/Endless_Google)

实现无需手动点击 Google 搜索结果的页码，实现自动翻页显示搜索内容。

 ![](https://cdn.sspai.com/2017/08/22/fc18e3c76853552bad55c1443a1dcc35.jpg?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1){style="max-width:500px;"}轻松下载
Instagram
图片和视频： [IGHelper](https://greasyfork.org/en/scripts/22660-ighelper-download-instagram-pic-vids)

方便用户下载 Instagram
的图片和视频，将鼠标移动到图片或者视频上，即可看到下载按钮。

 ![](https://cdn.sspai.com/2017/08/22/509b4449467cdfb31394eaa7f6400706.jpg?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1){style="max-width:500px;"}悬停查看和下载图片： [Mouseover
Popup Image
Viewer](https://greasyfork.org/en/scripts/404-mouseover-popup-image-viewer)

只需将鼠标光标悬停在多媒体资源的链接上，即可直接显示完整的图像和视频剪辑，避免了用户二次点击，并且通过快捷键实现下载、缩小放大、顺序浏览图册等功能。脚本已经上百个图像和视频托管服务（如
Facebook、500px、Flick 和 YouTube）。

 ![](https://cdn.sspai.com/2017/08/22/ddae8e342263a472fb9e64f6cf7089cc.jpg?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1){style="max-width:500px;"}YouTube
影片下载为 MP3 格式文件： [Simple YouTube MP3
Button](https://greasyfork.org/en/scripts/20015-simple-youtube-mp3-button)

脚本提供了即时转换功能，可将 YouTube 影片以 MP3 音频文件格式下载到本地。

 ![](https://cdn.sspai.com/2017/08/22/92bcf02b96fdfa6dac346c8e1e78a724.jpg?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1){style="max-width:500px;"}GitHub
中文化界面： [GitHub
汉化插件](https://openuserjs.org/scripts/52cik/GitHub_%E6%B1%89%E5%8C%96%E6%8F%92%E4%BB%B6)

很多新手朋友不太会玩 GitHub，可能被全英文界面所困扰，这款脚本实现汉化了
GitHub 界面的部分菜单及内容，新手熟悉之后可选择停用脚本恢复英文模式。

 ![](https://cdn.sspai.com/2017/08/22/79e2f0811a35d190d8ed2a63ca2c547f.jpg?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1){style="max-width:500px;"}给你最好用的
YouTube 视频下载工具： [Youtube Best Video Downloader
2](https://greasyfork.org/en/scripts/19592-youtube-best-video-downloader-2)

脚本提供了 YouTube 视频下载的快捷功能，可保存为 Full-HD
MP4、FLV、3GP、MP3（码率为 128kbps 或者 192kbps）、M4A 以及 AAC
格式。经测试，这款脚本会和上面提及到的 YouTube
+ 脚本有冲突，需要暂时停用 YouTube +，才可看到下载按钮。

 ![](https://cdn.sspai.com/2017/08/22/533cc8aa2d06e71dc7dfe472fd3dacb0.jpg?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1){style="max-width:500px;"}Feedly
订阅工具增强版： [Feedly filtering and
sorting](https://greasyfork.org/en/scripts/20483-feedly-filtering-and-sorting)

此脚本主要是为 Feedly
订阅增强了部分功能，包括了高级关键字匹配、自动加载、高亮显示自定义标题、订阅内容高级排序规则等。

 ![](https://cdn.sspai.com/2017/08/22/6baac3aa25156643b8a4e8d50b94a201.jpg?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1){style="max-width:500px;"}还你清新的小说阅读模式： [My
Novel Reader](https://greasyfork.org/zh-CN/scripts/292-my-novel-reader)

小说阅读脚本实现了统一阅读样式，内容去广告、修正拼音字、段落整理，自动下一页的功能，相当适合重度的小说阅读用户。

 ![](https://cdn.sspai.com/2017/08/22/825961a121ecc06167fc8e788aec8ba3.jpg?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1){style="max-width:500px;"}绕过搜索结果的自我跳转，直接访问原始网页： [AC-baidu](https://greasyfork.org/zh-CN/scripts/14178-ac-baidu-%E4%BC%98%E5%8C%96%E7%99%BE%E5%BA%A6-%E6%90%9C%E7%8B%97-%E8%B0%B7%E6%AD%8C%E6%90%9C%E7%B4%A2%E7%BB%93%E6%9E%9C%E4%B9%8B%E9%87%8D%E5%AE%9A%E5%90%91%E5%8E%BB%E9%99%A4-%E5%8E%BB%E5%B9%BF%E5%91%8A-favicon)

脚本可实现绕过百度、搜狗搜索结果中的自己的跳转链接，直接访问原始网页（间接缩短访问目标网页的时间）；可去除百度搜索结果中多余广告 ；添加
Favicon 显示；添加计数。

 ![](https://cdn.sspai.com/2017/08/22/ae799135967d04f15b65a5ec0521fc72.jpg?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1){style="max-width:500px;"}结语 \| 动手玩 

上面给出的 15
个脚本只是很小的一部分，脚本工具可以专门改变特定网站的行为，比如过滤广告、添加下载按钮、网页界面定制等等，同时由于涉及到版权的问题，并没有向大家推荐类似绕过
VIP 视频限制之类的脚本。这些在 Greasy Fork 和 OpenUserJS
都提供了相当不错的选择，大家根据需求自行下载试玩。

如果觉得现有的脚本还不够完善，那就动手写一个吧。最后，欢迎大家在留言中批评、指点、分享、投币，也可以留言哪些脚本神器值得推荐。

参考链接：

-   [不喜欢某个网站的样子？用 Stylish
    给它一键「换肤」](https://sspai.com/post/34508)

-   [有哪些值得推荐的油猴脚本?](https://www.runningcheese.com/userscripts)

[用 Chrome 怎么能不装扩展？少数派以往推荐的好用的 Chrome
插件都在这里了 👉](https://sspai.com/topic/102)



1.  



