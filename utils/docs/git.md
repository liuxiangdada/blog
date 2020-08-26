# Git

## 定义

Git是一款软件配置管理工具，或者说是一个版本控制系统，目的是管理代码的版本
- 其他的版本控制系统诸如CVS、Subversion、Perforce、Bazaar 等等都是保存文件的变更，即它们保存基本文件和每个文件随着时间逐步积累的差异
- Git是创建每次提交后全部文件的快照并保存这个快照的索引

## 特点

- 直接记录快照，而非差异比较
- 操作几乎都在本地执行
- git保证完整性：每次存储前都会计算所有数据的校验和，生成一个40位的字符串并根据校验和确保数据不被无意修改
- git一般只添加数据：几乎只对文件进行增加或者修改操作，而不是执行删除这种不可逆操作

## 工作流程

1. 在本地工作区修改文件
2. 将修改保存到暂存区
3. 提交修改，将暂存区的文件以快照的形式永久的存储到本地仓库目录
4. 将提交推送到远端仓库

![git工作流](../img/git工作流.png)

## 常用命令

### git clone

将远程仓库克隆到本地，完整的命令如下

```
git clone <远程仓库网址> <本地目录>

-o 修改远程主机名，默认为origin
```

### git remote

管理远程主机名

```
git remote // 列出所有远程主机名

-v // 查看远程主机网址
show <主机名> // 查看主机详细信息
add <主机名> <主机网址> // 添加新的远程主机
rm <主机名> // 删除现有的某个远程主机
rename <原主机名> <新主机名> // 改名
```

### git fetch

将远端仓库的更新取回到本地仓库

```
git fetch <主机名> <分支名> // 取回指定分支的更新

-p/--prune // 拉取前删除不存在远端仓库的追踪分支
```

### git pull

将远端仓库某个分支的更新取回到本地仓库再合并到指定分支

```
git pull <主机名> <远程分支名>:<本地分支名>
```

实质上等价于`git fetch`和`git merge`

如果当前分支和需要更新的远程分支的同名，则可以省略本地分支名

```
git pull <主机名> <远程分支名>
```

如果本地的分支和远程分支存在追踪关系，则可以省略远程分支名

```
git pull <主机名>
```

如果当前分支只存在一个追踪分支，即只与一个远程主机中的分支建立追踪关系，则主机名也可以省略

```
git pull
```

默认`git pull`采用`merge`模式合并分支，如果我们要使用`rebase`模式，可指定子参数

```
git pull --rebase <主机名> <远程分支名>:<本地分支名>
```

### git push

将本地的修改推送到远程分支，命令类似`git pull`

```
git push <主机名> <本地分支名>:<远程分支名>
```

如果本地的该分支已经与远程分支建立了追踪关系，则可以省略远程分支名，如果远程不存在该分支，则会新建

```
git push <主机名> <本地分支名>
```

如果省略本地分支名，表示删除指定的远程分支，因为这相当于将一个空的分支推送到远程

```
git push <主机名> :<远程分支名>
```

其实如果存在追踪关系，且当前分支就是要推送的分支，则本地分支名也可以省略

```
git push <主机名>
```

如果推送的本地分支只存在一个追踪分支，则可以省略主机名

```
git push
```

如果推送的本地分支和远端多个主机存在追踪关系，则通过`-u`子参数指定默认主机，这样后面无需再指定主机名了

```
git push -u <主机名> <本地分支名>:<远程分支名>
```

其他命令

```
--all 推送本地所有分支到远端
--force 强制推送本地分支的更新到远端，该命令会忽视本地和远程的差异，强制推送本地更新
--tags 默认推送不会推送tag，使用该命令推送tag
```

### git rebase

合并分支的一种方法，我们来看看它和`merge`的差异

`merge`在比较本地分支和远程分支时，如果本地分支签出时的提交已经落后于远端，举个例子，我们在远程分支的c2提交签出开发分支experiment，
本地开发提交了c3，合并时拉取最新的远程分支，发现协作者已经提交了c4，这时不能直接的使用快进模式，而是要进行三方合并，生成一个新的合并提交c5，如果有冲突还要处理冲突，如图：

![merge合并](../img/basic-rebase-2.png)

`rebase`在比较本地分支和远程分支时，如果本地分支签出时的提交已经落后于远端，举个例子，我们在远程分支的c1提交签出开发分支experiment，
本地开发提交了c2，合并时拉取最新的远程分支，发现协作者已经提交了c3，这时它会找到变基的两个分支的共同父提交c2，然后对比当前分支相对于c2的历次提交，提取相应的修改作为临时文件，
接着将当前分支指向远程分支的基底c3,最后把此前存的临时文件依次应用，如图：

![rebase合并](../img/basic-rebase-3.png)

## 基于gitFlow的工作流