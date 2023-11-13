# Git 撤销



## 案例 1：撤销本地分支的上一个提交

本地分支上的提交，指的是提交尚未 git push 到远程仓库，在此情形下，撤销命令如下：

```shell
$ git reset --hard HEAD^
```

如果是希望撤销到上一个提交，但工作目录中的文件保留当前提交的内容状态，这样可以在此基础修改后重新提交：

``` shell
# 在 VSCode 中 git 插件的"撤销上次提交"使用就是该命令:
$ git reset HEAD^
```



## 案例 2：撤销远程分支的上一个提交

如果你想要撤销已经 push 的分支上的提交，你可以使用以下两个命令来回退提交并进行撤销：

1. 首先使用 `git log` 命令查看提交历史，找到你想要回退的提交的哈希值（commit hash）。
2. 使用 `git revert` 命令回退提交。回退提交**会创建一个新的提交**来撤销你想要回退的提交所引入的更改。命令的语法如下所示：

``` shell
git revert <commit_hash>
```

其中，`<commit_hash>` 是要回退的提交的哈希值。

注意，回退提交**会创建一个新的撤销提交**，并将其添加到分支的提交历史中。这意味着这个撤销提交会保持远程仓库中的历史记录一致，并且其他人可能会看到这个撤销提交。

另一个方法是使用 `git reset` 命令回退提交并删除回退的提交。但是需要注意，`git reset` 命令会修改分支历史，如果分支已经被其他人所使用，在这种情况下，最好不要使用 `git reset` 命令来修改已经共享的提交历史。

因此，推荐使用 `git revert` 命令来撤销已经 push 的分支上的提交，以保持提交历史的一致性。

> 关于 `git revert` 的更多使用方式，参考 [《git revert.md》](./git revert.md)



## 案例 3: 撤销本地分支上 git merge 合并提交

有时候 `git merge test_branch` 之后发现合并错了，想撤销：

``` shell
$ git reset --hard HEAD^
```



## 案例 4: 撤销本地分支上 git pull 自动生成的合并提交

实际上 `git pull` 等价于先执行 `git fetch` 再执行 `git merge` 的组合；因此，很多时候 `git pull` 会自动生成一个合并提交，入股不希望生成自动的合并提交，并且已经不小心执行了 `git pull`，那么可以先撤销再 `git rebase` 则不会生成额外的合并提交：

``` shell
# 撤销 git pull 自动创建的合并提交
$ git reset --hard HEAD^
# 抓取最新的远程仓库的提交
$ git fetch
# 使用 rebase 
$ git rebase origin/<分支名>
```