---
title: git笔记2
category:
  - 技术笔记
tag:
  - Git
  - 踩坑
abbrlink: 2664
date: 2020-05-29 16:18:17
---

# 问题
在之前使用git管理代码的时候，会经常出现commit后，才发现有的地方有注释没加或者注释错别字等等问题，导致需要重新修改在add在commit，然后就会出现log中很多很没意义的commit，记录一下当出现这种情况，怎么消除之前的commit，让log看起来更清晰明了
<!-- more -->
# 正文
对这种一般有两种方法，一种是操作HEAD回到指定的commit，然后修改内容在commit即可；另外就是合并多个commit
### 相关概念
git管理的目录中，有这么几个概念：
- 工作目录：workspace---为我们平时编辑文件的区域
- 缓存区：index---git add到的区域
- 本地仓库：local repository---git commit的区域
- 远程仓库：remote repository---git push的区域
- HEAD：指针，默认始终指向我们最新的commit

### 回滚commit
>相关概念
- 用到git reset命令
- 这个命令有三个参数:- - <soft|hard|mixed>
- 用法 git reset -- <参数> [指定commit编号]

>参数的含义
- soft：不改变工作区的内容，之前commit的内容会回退在缓存区
- mixed：不加参数时的默认为该参数，之前的commit内容不回退到缓存，需要再次add
- hard：会影响工作区，让回退前的commit的代码全部删除

>soft演示
- 现在有个目录的分支是这样 ![图1](/img/git/git.png)
- 情况是这样，在addcon这个commit后，发现没有添加注释，于是添加了注释，再一次进行了commit并注释为add注释；这一次commit就没有什么实质作用，现在干掉这次commit
- 步骤：使用reset，通过指定commit的id来改变HEAD指向到init：git reset --soft 234ee9da 
- 现在分支情况是 ![图2](/img/git/git2.png)
- 暂存区：![图3](/img/git/git3.png) 显示尚未commit
- 最后在commit一次，在查看分支情况：![图4](/img/git/git4.png) 这样就将前两次应该一次完成提交的commit改成了一次
- 总结：如果仅仅想回滚commit，重新commit，可以使用soft

>mixed
- 情况和上述一样,现在分支情况:![图5](/img/git/git5.png) 
- 现在回退到完成注释和功能的commit，然后干掉addcon2和添加注释两次commit   git reset --soft d62192e2  
- 查看现在分支状况和缓存区 ![图6](/img/git/git6.png)  ![图7](/img/git/git7.png) 
- 和soft的区别仅仅是mixed的时候缓存区中没有内容，需要手动add修改内容后在进行commit，但是相同的是都没有修改工作区的内容
- 总结：回滚版本并需要修改一下代码，重新commit，可以使用mixed

>hard
- 现在的分支情况： ![图8](/img/git/git8.png) 
- 背景：现在init之后的commit的内容都作废，需要重新开始，并且要清楚commit记录，可以使用hard
- ![图9](/img/git/git9.png) ![图10](/img/git/git10.png) 
- 总结：hard适用于回滚commit时，且回滚到的指定commit之后的commit都不需要，会清空缓存区和工作区中不需要的commit的内容

### 合并commit
使用git rebase来合并需要合并的多个commit
>演示
- 现在分支情况 ![图11](/img/git/git11.png) 
- 现在要把***完成功能1*** ***完成功能2*** ***完成所有功能***合并成一次commit
- 先把HEAD指向调到需要合并的多个commit的第一个commit:git rebase -i HEAD~3
- 说明：HEAD的数字1为最新的commit
- 这时会出现一个vim编辑器打开的文件 ![图12](/img/git/git12.png) 
- 头三行为要合并的分支，以第一行为例：**pick**是一个命令，**1e1f6cb**为该commit的编号，**完成功能1**为该commit的注释
- 合并commit就是通过改变不同的命令来实现，看到下面的注释有提示命令有pick、reword、edit、squash、fixup、exec、break、drop、label、reset
- 修改第二三行的pick为squash，该命令为使用commit，与之前的commit合并
- 修改保存退出后，会继续显示一个vim编辑器打开的文件，这里可以更改commit的注释 ![图13](/img/git/git13.png)
- 上面的为合并的commit的注释，自行选择是否可以修改；下面为被合并的commit的注释信息 
- 操作完后，现在分支情况 ![图14](/img/git/git14.png)



# 合并
之前对于分支合并，一直使用的merge，无意间一次看到rebase也可以用来合并分支，于是试验了一下两者的区别；记录一下

>merge
- 指定一个分支，合并到当前所在分支；下例合并dev到master
- git merge dev
- 现有的分支情况如下：![图15](/img/git/git15.png)
- 使用merge后 ![图16](/img/git/git16.png)
- 对比发现，两者合并后在master分支多了一个commit

>rebase
- 合并当前分支到执行分支；下例rebase 分支dev到master
- git rebase <指定branch>
- 现有的分支情况如下：![图15](/img/git/git15.png)
- 使用rebase后 ![图17](/img/git/git17.png)
- 没有新的commit出现，而是把dev最新的commit和master最新的commit进行了合并

>总结
- 对于需要严格保留每次commit记录的时候使用merge
- 对于只是在另一个分支进行了一些额外的开发测试，最后确认无误，且该功能本来就是要在某分支进行的，就可以直接使用rebase；比如自己在一个分支开发某一个模块，然后对于其中的一些地方不想影响其他代码，就可以单独开一个新分支进行开发测试，开发完成没问题后，不需要保留记录，直接rebase即可


# 其他操作
记录一下这个学习过程中了解到的一些其他的操作
>修改commit注释
```bash
    git commit - -amend
```

>撤销缓存区的文件
```bash
# 方式1
git reset
# 方式2 
git rm --cached <filename>
```

>从缓存和工作目录中同时清除
```bash
# 方式1
git reset --hard
# 方式2
git reset --f <filename>
```

>查看缓存区文件
```bash
git status
```
# 参考链接
- [gitbook中文](http://gitbook.liuhui998.com/4_2.html)
- 分支可视化使用的vscode的插件---GitGraph