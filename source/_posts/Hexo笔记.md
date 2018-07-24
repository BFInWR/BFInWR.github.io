---
title: Hexo笔记
date: 2018-07-16 16:48:45
categories: "Hexo教程"
tags:
	- Hexo
	- 教程
description: "一个Hexo笔记"
---

hexo clean  //清除缓存
hexo g  //重新生成代码
hexo s  //部署到本地

//然后打开浏览器访问 localhost:4000 查看效果

依次执行以下命令：清理 – 生成 – 部署github
	
``` bash
$ hexo clean
$ hexo generate
$ hexo deploy
```

##git push & git pull 推送/拉取分支
git push 使用本地的对应分支来更新对应的远程分支。

如果在github的remote上已经有了文件，会出现错误。此时应当先pull一下，即：
git pull 获取并合并其他的仓库，或者本地的其他分支。
```
$ git pull origin (master/hexo)
$ git push origin (master/hexo)
```
***
## 博客管理流程
    1.创建两个分支：master 与 hexo；
    2.设置hexo为默认分支（因为我们只需要手动管理这个分支上的Hexo网站文件）；
    3.使用git clone git@github.com:GitName/GitName.github.io.git拷贝仓库；
    4.在本地GitName.github.io文件夹下通过Git bash依次执行npm install hexo、hexo init 、npm install和 npm install hexo-deployer-git（此时当前分支应显示为hexo）;
    5.修改_config.yml中的deploy参数，分支应为master；
    6.依次执行git add .、git commit -m “…”、git push origin hexo提交网站相关的文件；
    7.执行hexo generate -d生成网站并部署到GitHub上。

这样一来，在GitHub上的GitName.github.io仓库就有两个分支，一个hexo分支用来存放网站的原始文件，一个master分支用来存放生成的静态网页。

## 日常修改

在本地对博客进行修改（添加新博文、修改样式等等）后，通过下面的流程进行管理：

    1.依次执行git add .、git commit -m “…”、git push origin hexo指令将改动推送到GitHub（此时当前分支应为hexo）；
    2.然后才执行hexo generate -d发布网站到master分支上。

虽然两个过程顺序调转一般不会有问题，不过逻辑上这样的顺序是绝对没问题的（例如突然死机要重装了，悲催….的情况，调转顺序就有问题了）。

## 本地资料丢失

当重装电脑之后，或者想在其他电脑上修改博客，可以使用下列步骤：

    1.使用git clone git@github.com:GitName/GitName.github.io.git拷贝仓库（默认分支为hexo）；
    2.在本地新拷贝的GitName.github.io文件夹下通过Git bash依次执行下列指令：npm install hexo、npm install、npm install hexo-deployer-git（记得，不需要hexo init这条指令）。
