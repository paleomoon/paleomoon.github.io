---
title: github pages+hexo搭建个人博客
date: 2017-02-26 18:51:16
tags: [github pages,博客]
categories: 技术
---
## 前言
一直想找个简洁美观又方便（支持markdown）的地方写点东西，试过博客园，CSDN，还有各种笔记，都感觉不怎么舒服。后来发现很多人推荐用github pages+hexo搭建个人博客，果然一开始见到给我的感觉就是：惊艳。于是折腾起来，其间也遇到的各种问题，最后花了一天时间，终于完成，包括各种自定义效果和第三方服务，都尝试了一遍。最后完成打开网站的那一刻，成就感爆棚有木有！<!-- more --> 其实也没有，因为终究还是简单的使用，要是自己能完完全全做出个博客网站那才真的有成就感。好了，搭建博客的具体步骤就不再总结，网上很多，文末给出两篇觉得比较好的，这里总结一下一些需要注意的地方。
## 写博客基本流程
`hexo new [layout] <title>`,layout有post、page 和 draft。也可以自己在scaffolds下新建有一个layout的md文件，使用自定义layout. 
在source/_posts下打开对应文件。  
文章头部添加信息，写文章。** 注意引号后面有空格 **  可以建立自己的文章模板，避免每次都要输入新的属性，修改scaffolds下的post.md即可.  
`hexo generate`  
`hexo server` http://localhost:4000 预览  
`hexo deploy` 部署
## 文章修改
修改完直接刷新localhost就能看到效果。  
有时候修改了没生效，尝试`hexo  clean`或删除public,db.json。
## 主题的分类与标签功能出现“Cannot GET/xxxx”问题
没有初始化分类的子目录。当添加一个分类菜单，主题默认中并没有对应页面，需要自己new.  
`hexo new page "tags"`  
sources/tags/index.md,修改type: "tags",comments: false
## 设置阅读全文
在文章中使用`<!-- more --> `手动进行截断，此上内容为摘要，并显示阅读全文按钮。
## 搜索菜单  
菜单下不需添加search,也不要new page(因为无界面)，启用后会自动添加到最后。
## 评论、分享系统
多说，可自定义css样式，这个可以玩玩
## 多说热评
在标签等页面也显示，不够美观，关掉。以后再研究。
## 图片  
对于本地图片，需要在source目录下面新建一个目录images，然后把图片放到目录中.  
`![这是一张图片](/images/fens.me.png)`  
由于github有300M存储空间限制，所以最好使用七牛云图床生成图片外链。
## 站点统计
使用不蒜子。
## RSS订阅
## 远程无法更新页面
由于之前胡乱操作导致本地目录混乱，于是整理，不小心删除.deploy_git下的.git,导致github远程无法更新静态页面。还原即可。
## 优化部署与管理  
hexo deploy只是将.deploy_git(或public?)中的文件即生成的静态网页文件上传到github部署,那我们写的博客源文件及主题如何备份呢？  
有人提出一种比较好的方案：在远程仓库创建两个分支，一个用来存放Hexo网站的文件，一个用来发布网站。
>* 博客搭建流程
创建仓库，username.github.io；  
创建两个分支：master 与 hexo；  
设置hexo为默认分支（因为我们只需要手动管理这个分支上的Hexo网站文件）；  
使用git clone git@github.com:username/username.github.io.git拷贝仓库；  
username.github.io文件夹下通过Git bash依次执行npm install hexo、hexo init、npm install 和 npm install hexo-deployer-git（此时当前分支应显示为hexo）;  
修改_config.yml中的deploy参数，分支应为master；  
依次执行git add .、git commit -m “…”、git push origin hexo提交网站相关的文件；  
执行hexo generate -d生成网站并部署到GitHub上。  
这样一来，username.github.io仓库就有两个分支，一个hexo分支用来存放网站的原始文件，一个master分支用来存放生成的静态网页。
>* 日常修改
在本地对博客进行修改（添加新博文、修改样式等等）后，通过下面的流程进行管理：  
依次执行git add .、git commit -m “…”、git push origin hexo指令将改动推送到GitHub（此时当前分支应为hexo）；  
然后才执行hexo generate -d发布网站到master分支上。
>* 本地资料丢失
当重装电脑之后，或者想在其他电脑上修改博客，可以使用下列步骤：  
使用git clone git@github.com:username/username.github.io.git拷贝仓库（默认分支为hexo）；  
在本地新拷贝的username.github.io文件夹下通过Git bash依次执行下列指令：npm install hexo、npm install、npm install hexo-deployer-git（记得，不需要hexo init这条指令）。  

这里会有几个问题  
首先直接将整个hexo目录push到远程。由于一开始是直接clone主题next到hexo目录下的themes下，hexo下就有两个.git，肯定报错。删除及next下的.git，结果文件结构产生混乱。最后是手动删除重复的文件，再add,commit,push。    
另外还是由于直接clone主题，这时在远程next文件夹是灰的，意味着修改的主题并没有上传。自己琢磨，前面由于next下有.git，可能导致next目录以下有冲突而忽略了此文件夹下的文件，这时只要next没动，怎么改里面的东西都还是不会上传，包括添加.gitkeep都不会有效果。于是 git ls-files，果然没有这些文件。将整个next文件夹剪切到其他位置，add,commit,然后再将next放回，add,commit,push,问题解决！

## 总结
整个过程很有激情，也学到了很多，包括git、markdown。    
遇到问题首先看官方文档！首先看官方文档！首先看官方文档！重要的事情说三遍！  

## 后记
博客是初步搭建好了，接下来还需要逐步完善，包括功能完善、自己写一些样式进行界面优化、购买域名、SEO优化等等。  
最重要的是，希望能够注重内容的建设。可能自己就是个没什么内涵的人，早年间写过一些文字，大多华而不实，后来意识到这点，以至于有些生厌，所以很久没有写作了。希望能够坚持写些东西，不管是技术上的，还是一些生活随笔，贵在坚持。  
我也希望不久的将来，能够自己创建出一个完整的网站。虽然这条路并没有想象中的平坦，但仍然在朝着这个方向努力，每天有收获的感觉很充实。  

## 参考
[手把手教你建github技术博客](http://www.jianshu.com/p/701b1095da11#)  
[Hexo博客搭建全攻略(一):基于Hexo+Github环境搭建 系列文章](http://www.jianshu.com/p/5fc4672b7d2e)  
[使用hexo，如果换了电脑怎么更新博客？](https://www.zhihu.com/question/21193762)  
[Hexo 中文文档](https://hexo.io/zh-cn/docs/)  
[Next 使用文档](http://theme-next.iissnan.com/)  
[多说](http://jimhoo.duoshuo.com/)  
[不蒜子](http://busuanzi.ibruce.info/)  
[七牛云](https://portal.qiniu.com/signin)


