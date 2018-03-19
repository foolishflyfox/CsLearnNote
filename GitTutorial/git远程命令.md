---
export_on_save:
 html: true
---
# git的远程命令

> 推荐一个实验git操作的好网站：[Learning Git Branch](https://learngitbranching.js.org/?NODEMO)

> 参考：阮一峰先生的 [Git远程操作详解](http://www.ruanyifeng.com/blog/2014/06/git_remote.html)

## 通俗理解

暑假时候，老师布置了一份数学作业，要求同学们两人一组一起完成。一位叫小明的同学成绩一向不好，心想着得拉一位学霸组队。他有两个学霸的朋友小强和小红，考虑了一下，小明决定和小强组队完成作业。于是两人愉快地组队成功，任务分配是小强负责想解题思路、证明，小明复杂根据思路进行计算和整理。

作业第一阶段的任务主要交给小强，一段时间后，小强完成了部分的工作，小明可以按照小强提供的思路开始计算工作了。

小明先将小强已经完成的数学作业用打印机打印了一份：`git clone XX村/小强家/数学作业.git`（相当于对项目仓库的拷贝），之后，小明开始看小强的工作。

一段时间之后，小明突然忘了自己到底是和谁组队来着，他想知道自己手上的这份作业前期工作是小强还是小红完成的，没关系，通过 `git remote -v` 显示 : `分析证明 XX村/小强家/数学作业.git` 他知道自己原来是和小强组队的。

两人就这样并行地工作着。一段时间之后，小明像看看小强的进展，于是从小强那里取得了最新版本的作业：`git fetch origin 分析推理部分`，然后跟自己手上的版本比较了一下有什么不同：`git diff 分析推理部分 origin/分析推理部分`，发现了小强的确修改了一些东西，只怪自己太渣，完全看不懂，所以直接通过两步进行了合并：`git checkout 分析推理部分` ，`git merge origin/分析推理部分`。

后来，小强那边又有了更新，小明想，反正我也看不出什么东西，直接把小强更新的文件拿来合并好了：`git pull origin 分析推理部分:分析推理部分`，将自己这边小强的工作部分直接进行了更新。

再后来，小明也完成了部分的计算任务，想要把自己的工作传一份给小强看看：`git push origin 计算总结部分:计算总结部分`，这样就将自己的工作内容传送给了小强。

使用上述的工作方式，两人就可以顺利地完成这一需要相互协作的任务。

这个故事讲的是成员间相互协作的社会化编程方式。主要涉及到下面将要讲的5个命令：`git clone`、`git remote`、`git fetch`、`git pull`、`git push`。

## git clone

从远程服务器获得一个仓库的命令为：
```git
$ git clone 版本库的地址 [本地目录名]
```
该命令分为两步操作，首先根据 *本地目录名* 创建一个指定的 **本地文件夹**，然后将远程仓库中的内容拷贝到该文件夹中。如果 *本地目录名* 没有指定，则在当前目录中创建一个与远程仓库同名的文件夹，再进行仓库拷贝。

拷贝 github 中仓库通常使用的clone命令为：
```git
$ git clone https://github.com/username/repo.git
$ git clone git@github.com:username/repo.git
```
通常来说，使用 git 协议下载的速度最快，https 的速度较慢。

但在用 git 协议进行 clone ，要求用户必须已经注册了一个 github 账号，并且已经 [利用 ssh 完成本地 Git 与 Github 的绑定](http://blog.csdn.net/qq_35246620/article/details/69061355)，否则将会出现 `Permission denied (publickey)` 的错误而导致不能 clone。而使用 https 协议不需要有 github 账号也可以完成复制。
> 注：ubuntu 下，在用 `ssh-keygen` 命令完成密钥与私钥的创建后，需要使用命令 `ssh-add` 将新创建的密钥添加到系统的认证代理中，否则还是会出现 `Permission denied`。

## git remote

`git remote` 用于操作当前仓库与远程仓库的关系。如：

- 查看当前仓库与远程仓库的关系：
```git
$ git remote -v
origin git@github.com:username/repo.git(fetch)
origin git@github.com:username/repo.git(push)
```

- 删除当前仓库与远程仓库之间的关系：
```git
$ git remote rm origin
```

- 建立当前仓库与一个远程仓库之间的关系：
```git
$ git remote add origin git@github.com:username/repo.git
```

- 修改远程仓库对应名称：
```git
$ git remote rename origin new_name
```

## git fetch

在远程仓库有了更新之后，如果希望将这些更新取回到本地，可以使用 `git fetch` 命令，如：
```git
$ git fetch origin
```
该命令用于取回远程主机上所有分支的更新，但并不直接添加到本地的分支中。

如果想要取回指定分支 `feature-A` 上的更新，可以使用命令：
```git
$ git fetch origin feature-A
```

如果你想要看看 fetch 得到的 master 分支是怎么样的，可以使用：
```git
$ git checkout origin/master
```
然后查看仓库即可。

如果你想要看看 fetch 得到的 master 分支与本地的 master 分支存在什么区别，可以使用：
```git
$ git diff master origin/master
```

如果你想将远程主机上的修改合并到当前分支上，可以使用：
```git
$ git checkout master
$ git merge origin/master
```
如果两者存在冲突，修改冲突文件并提交即可。

## git pull

`git pull` 的作用是取回远程主机中的某个分支的更新，并与本地的指定分支进行合并。其格式如下：
```git
用法: git pull 远程主机名 远程分支名:本地分支名
$ git pull origin featureA:master
```
可以翻译成 *pull origin's featureA branch to local master* ，表示将远程主机 origin 的 featureA 分支取回，并与本地的 master 分支进行合并。如果指定的本地分支不存在，git 将会自动进行创建。

上面的命令等价于以下三条命令：
```git
$ git fetch origin featureA
$ git checkout master
$ git merge origin/featureA
```

如果当前分支就是 master 分支，第二句命令可以省略。

默认情况向本地 master 追踪的是远程的 master，对应的分支追踪相应的远程分支。所以，如果希望本地分支更新其所追踪的远程分支，只需要执行：
```git
$ git pull
```

## git push

`git push` 用于将本地分支的更新推送到远程分支上。格式上和 `git pull` 非常类似：
```git
用法: git push 远程主机名 本地分支名:远程分支名
$ git push origin master:featureA
```
可以翻译成 *push local master branch to origin's featureA*，表示将本地的 master 分支推送到远程主机 origin 的 featureA 分支上。 如果在远程主机上指定远程分支不存在，那么会自动在远程主机上进行创建。

如果想要删除掉远程的 featureA 分支，可以执行：
```git
$ git push origin :master
```
其中指定本地分支的位置为空即可。

如果在远程主机名后只跟一个分支名，表示本地分支名和远程分支名相同，如：
```git
$ git push origin featureA
等价于：
$ git push origin featureA:featureA
```

再简洁一点，将分支名也给省略，如：
```git
$ git push origin
```
表示将当前分支推送到远程主机的同名分支中。

如果当前分支只有一个追踪分支，那么远程主机名也可以省略：
```git
$ git push
```

如果当前分支与多个主机存在追踪关系，那么可以在 push 时使用 -u 选项指定一个默认主机：
```git
$ git push -u origin master
```
之后，在该分支上的推送直接使用命令 `git push` 即可。

如果在修改本地 featureA 分支的同时，远程主机的 featureA 也被修改，那么 push 的时候就会产生冲突。我们需要先 pull 取回远程的 featureA 分支，合并后再 push。

