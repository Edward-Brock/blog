# Git 删除历史记录，清理仓库

当 commit 次数很多的时候，项目仓库会慢慢积攒很多无用的提交历史，这些历史记录会在项目 clone 的时候导致速度很慢。如果没有软件，一些 clone 过程体验会很不好，甚至频繁出错，所以删除提交历史可以作为一种清理仓库的手段。

## Git 代码

```shell
git checkout --orphan latest_branch
git add -A
git commit -am "init commit"
git branch -D master
git branch -m master
git push -f origin master
```

## 以下是各条语句的解析

```shell
git checkout --orphan latest_branch
# 创建新分支 "latest_branch" 并转到该分支
# git checkout 命令为在当前分支上创建新分支(并转到该分支)
# git checkout --orphan 命令为从空白处创建新分支(并转到该分支)

git add -A
# 添加所有文件(当前最新状态)到暂存区

git commit -am "commit message"
# 将暂存区提交到新纪录(latest_branch分支)
# "commit message"为新纪录的名称

git branch -D master
# 删除主分支

git branch -m master
# 将 latest_branch(当前) 分支重命名为 master(主分支)

git push -f origin master
# 同步本地状态到服务器

# 此时，完成的效果为:
#   将master分支所有记录删除，并新建一个 "commit message" 记录，记录中内容为当前项目最新状态
#   此时远程服务器内所有垃圾都被清空
#   如果使用了 Github Desktop 等软件，本地垃圾可能未被清空，可删除项目并重新 clone
```
