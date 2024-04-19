# Git 合并提交



## 参考资料

[git rebase -i 合并多次提交_rebase 最近两次提交到另外一个分支](https://blog.csdn.net/w57685321/article/details/86597808)

[Git 合并多个提交的正确方式](https://zhuanlan.zhihu.com/p/139321091)



## 方式一: 使用 `git rebase -i` 方式

格式：`git rebase -i [startpoint] [endpoint]`

其中 `-i` 的意思是 `–interactive`，即弹出交互式的界面让用户编辑完成合并操作；`[startpoint] [endpoint]`则指定了一个编辑区间（该区间指定的是一个`(前开,后闭]`的区间），如果不指定 `[endpoint]`，则该区间的终点默认是当前分支 HEAD 所指向的 commit。

### 情形 1：合并最新提交中的两个或多个提交

#### 1.1 合并最新的两个提交

**步骤 0**：查看提交历史（使用 `git log --pretty=oneline` 或者 `git reflog --pretty=oneline`)

``` shell
$ git log --pretty=oneline 
c1942a45f59aa5027a457a139649421d0ce1cfe6 (HEAD -> master) 提交 5
f6a457b240218c897139467577a1ad7829e0428b 提交 4
c9abc979dea8bf24908ce18ff46d6bbd4327f9da 提交 3
450ee08270ae6533be233c27fb7a42fbbba11e6f 提交 2
29022bda7fe0d759f6b48a87638c73268664d542 提交 1
```

目标是要将最后的两次提交合并为一个提交！

**步骤 1**：使用 `git rebase -i HEAD~2` 或者 `git rebase -i c9abc9 `

执行这个命令后会跳到一个 vi 编辑器，里面提示的有：

> pick：保留该commit（缩写:p）
> reword：保留该commit，但我需要修改该commit的注释（缩写:r）
> edit：保留该commit, 但我要停下来修改该提交(不仅仅修改注释)（缩写:e）
> squash：将该commit和前一个commit合并（缩写:s）
> fixup：将该commit和前一个commit合并，但我不要保留该提交的注释信息（缩写:f）
> exec：执行shell命令（缩写:x）
> drop：我要丢弃该commit（缩写:d）


``` shell
pick f6a457b 提交 4
pick c1942a4 提交 5

# Rebase c9abc97..c1942a4 onto c9abc97 (2 commands)
#
# Commands:
# p, pick <commit> = use commit
# r, reword <commit> = use commit, but edit the commit message
# e, edit <commit> = use commit, but stop for amending
# s, squash <commit> = use commit, but meld into previous commit
# f, fixup [-C | -c] <commit> = like "squash" but keep only the previous
#                    commit's log message, unless -C is used, in which case
#                    keep only this commit's message; -c is same as -C but
#                    opens the editor
# x, exec <command> = run command (the rest of the line) using shell
# b, break = stop here (continue rebase later with 'git rebase --continue')
# d, drop <commit> = remove commit
# l, label <label> = label current HEAD with a name
# t, reset <label> = reset HEAD to a label
# m, merge [-C <commit> | -c <commit>] <label> [# <oneline>]
# .       create a merge commit using the original merge commit's
# .       message (or the oneline, if no original merge commit was
# .       specified); use -c <commit> to reword the commit message
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
#
```

我们希望将 `提交 5` 的 commit 合并到 `提交 4` 一起形成 1 个 commit；所以使用:  `pick <提交4>` 和 `squash <提交 5>` 的方式，具体步骤：

按键盘 `i` 键进入 vi 编辑器的插入模式，将行：

``` shell
pick f6a457b 提交 4
pick c1942a4 提交 5
```

修改为：

``` shell
pick f6a457b 提交 4
s c1942a4 提交 5
```

然后，按 `Esc` 键，输入 `:wq` 保持修改并退出 vi 编辑器。

再次，跳到一个 vi 编辑器，里面提示的有：

``` shell
# This is a combination of 2 commits.
# This is the 1st commit message:

提交 4

# This is the commit message #2:

提交 5

# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
#
# Date:      Fri Aug 18 14:45:13 2023 +0800
#
# interactive rebase in progress; onto c9abc97
# Last commands done (2 commands done):
#    pick f6a457b 提交 4
#    squash c1942a4 提交 5
# No commands remaining.
# You are currently rebasing branch 'master' on 'c9abc97'.
#
# Changes to be committed:
#       modified:   1.txt
```

这一步的提示表示编辑`合并后的 commit message` 内容，并且默认将被合并的原本的多个提交的 `commit message` 引入进来，方便基于这些原本的 `commit message` 进行修改。

修改后，按 `Esc` 键，输入 `:wq` 保持修改并退出 vi 编辑器。

此时，合并任务就已经完成了：

``` shell
$ git log --pretty=oneline 
12530414124c61826e5c2a3252a5b45e1472f7ff (HEAD -> master) 提交 4
c9abc979dea8bf24908ce18ff46d6bbd4327f9da 提交 3
450ee08270ae6533be233c27fb7a42fbbba11e6f 提交 2
29022bda7fe0d759f6b48a87638c73268664d542 提交 1
```

#### 1.2 合并最新的多个提交

为了恢复原本的 5 个提交的仓库状态，可以使用 `git reset` 进行恢复：

``` shell
$ git reset --hard c1942a4
HEAD is now at c1942a4 提交 5

$ git log --pretty=oneline 
c1942a45f59aa5027a457a139649421d0ce1cfe6 (HEAD -> master) 提交 5
f6a457b240218c897139467577a1ad7829e0428b 提交 4
c9abc979dea8bf24908ce18ff46d6bbd4327f9da 提交 3
450ee08270ae6533be233c27fb7a42fbbba11e6f 提交 2
29022bda7fe0d759f6b48a87638c73268664d542 提交 1
```

**步骤 0**：查看提交历史（使用 `git log --pretty=oneline` 或者 `git reflog --pretty=oneline`)

``` shell
$ git log --pretty=oneline 
c1942a45f59aa5027a457a139649421d0ce1cfe6 (HEAD -> master) 提交 5
f6a457b240218c897139467577a1ad7829e0428b 提交 4
c9abc979dea8bf24908ce18ff46d6bbd4327f9da 提交 3
450ee08270ae6533be233c27fb7a42fbbba11e6f 提交 2
29022bda7fe0d759f6b48a87638c73268664d542 提交 1
```

目标是要将最后的三次提交合并为一个提交！

**步骤 1**：使用 `git rebase -i HEAD~3` 或者 `git rebase -i 450ee0 `

执行这个命令后会跳到一个 vi 编辑器：

``` shell
pick c9abc97 提交 3
pick f6a457b 提交 4
pick c1942a4 提交 5

# Rebase 450ee08..c1942a4 onto 450ee08 (3 commands)
#
# Commands:
# p, pick <commit> = use commit
# r, reword <commit> = use commit, but edit the commit message
# e, edit <commit> = use commit, but stop for amending
# s, squash <commit> = use commit, but meld into previous commit
# f, fixup [-C | -c] <commit> = like "squash" but keep only the previous
#                    commit's log message, unless -C is used, in which case
#                    keep only this commit's message; -c is same as -C but
#                    opens the editor
# x, exec <command> = run command (the rest of the line) using shell
```

我们希望将 `提交 4、提交5` 的 commit 合并到 `提交 3` 一起形成 1 个 commit；所以使用:  `pick <提交3>` 和 `squash <提交 4>` 及`squash <提交 5>` 的方式，具体步骤：

按键盘 `i` 键进入 vi 编辑器的插入模式，将行：

``` shell
pick c9abc97 提交 3
pick f6a457b 提交 4
pick c1942a4 提交 5
```

修改为：

``` shell
pick c9abc97 提交 3
s f6a457b 提交 4
s c1942a4 提交 5
```

然后，按 `Esc` 键，输入 `:wq` 保持修改并退出 vi 编辑器。

再次，跳到一个 vi 编辑器，里面提示的有：

``` shell
# This is a combination of 3 commits.
# This is the 1st commit message:

提交 3

# This is the commit message #2:

提交 4

# This is the commit message #3:

提交 5

# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
#
```

这一步的提示表示编辑`合并后的 commit message` 内容，并且默认将被合并的原本的多个提交的 `commit message` 引入进来，方便基于这些原本的 `commit message` 进行修改。

修改为：

``` shell
# This is a combination of 3 commits.
# This is the 1st commit message:

提交 3 + 提交 4 + 提交 5
```

按 `Esc` 键，输入 `:wq` 保持修改并退出 vi 编辑器。

此时，合并任务就已经完成了：

``` shell
$ git log --pretty=oneline 
e94c29f0f5e3344d80e5fc4783e1b4a7969baf19 (HEAD -> master) 提交 3 + 提交 4 + 提交 5
450ee08270ae6533be233c27fb7a42fbbba11e6f 提交 2
29022bda7fe0d759f6b48a87638c73268664d542 提交 1
```

### 情形 2: 合并历史提交中的两个或多个提交

**步骤 0**：查看提交历史（使用 `git log --pretty=oneline` 或者 `git reflog --pretty=oneline`)

``` shell
$ git log --pretty=oneline 
c1942a45f59aa5027a457a139649421d0ce1cfe6 (HEAD -> master) 提交 5
f6a457b240218c897139467577a1ad7829e0428b 提交 4
c9abc979dea8bf24908ce18ff46d6bbd4327f9da 提交 3
450ee08270ae6533be233c27fb7a42fbbba11e6f 提交 2
29022bda7fe0d759f6b48a87638c73268664d542 提交 1
```

在这个案例中一共包含 5 条提交记录，希望将 `提交 2` 和 `提交 3` 和 `提交 4` 合并为一条提交。

**步骤 1**：使用 `git rebase -i 29022b`

执行这个命令后会跳到一个 vi 编辑器：

``` shell
pick 450ee08 提交 2
pick c9abc97 提交 3
pick f6a457b 提交 4
pick c1942a4 提交 5

# Rebase 29022bd..c1942a4 onto 29022bd (4 commands)
#
# Commands:
# p, pick <commit> = use commit
# r, reword <commit> = use commit, but edit the commit message
# e, edit <commit> = use commit, but stop for amending
# s, squash <commit> = use commit, but meld into previous commit
# f, fixup [-C | -c] <commit> = like "squash" but keep only the previous
#                    commit's log message, unless -C is used, in which case
#                    keep only this commit's message; -c is same as -C but
#                    opens the editor
```

修改为：

``` shell
pick 450ee08 提交 2
s c9abc97 提交 3
s f6a457b 提交 4
pick c1942a4 提交 5

```

然后，按 `Esc` 键，输入 `:wq` 保持修改并退出 vi 编辑器。

再次，跳到一个 vi 编辑器，里面提示的有：

``` shell
# This is a combination of 3 commits.
# This is the 1st commit message:

提交 2

# This is the commit message #2:

提交 3

# This is the commit message #3:

提交 4

# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
#
```

修改为：

``` shell
# This is a combination of 3 commits.
# This is the 1st commit message:

提交 2 + 提交 3 + 提交 4
```

按 `Esc` 键，输入 `:wq` 保持修改并退出 vi 编辑器。

此时，合并任务就已经完成了：

``` shell
$ git log --pretty=oneline 
2866bded80f459cc9bfa84c8b9b27b8f63c33445 (HEAD -> master) 提交 5
f59131a45ece719061734d5b3e303aab1a109f44 提交 2 + 提交 3 + 提交 4
29022bda7fe0d759f6b48a87638c73268664d542 提交 1
```




## 方式二: 使用 `git reset` 和 `git commit --amend` 方式

### 情形 1：合并最新提交中的两个或多个提交

#### 1.1 合并最新的两个提交

**步骤 0**：查看提交历史（使用 `git log --pretty=oneline` 或者 `git reflog --pretty=oneline`)

``` shell
$ git log --pretty=oneline
0fe7f86f6c5158829996c819e05ff60e79dbd4fd (HEAD -> master) 提交 5
ffc67af33ffb366d09e34ebf7bbe5b2701f2748b 提交 4
ce2581490a39d0e27ece289165187f66d400f350 提交 3
1393b0fadf4a0bc97d94c4221148dad1a96c37e6 提交 2
1bdacfdf972ab6ea3b8cb67fcc334e09c756a630 提交 1
```

目标是要将最后的两次提交合并为一个提交！

**步骤 1**：使用 `git reset HEAD~` 将当前分支指向到前一条提交（即：提交 4），但是工作区保留当前最新提交（提交 5）的内容：

``` shell
$ git reset HEAD~
Unstaged changes after reset:
M       1.txt
```

**步骤 2**：使用 `git commit --amend` 将当前工作区的变更合并到前一条提交（即：提交 4）：

``` shell
# 确保在 commit --amend 之前，将变更添加到暂存区 index 中:
$ git add .
# 然后, 以 --amend 方式提交
$ git commit --amend
[master 848d965] 提交 4
 Date: Tue Jan 9 15:17:29 2024 +0800
 1 file changed, 3 insertions(+), 1 deletion(-)

```

到此，最后两条提交已经合并为一条提交！

``` shell
$ git log --pretty=oneline
848d9658492442fd058bf29c116812daf2849692 (HEAD -> master) 提交 4
ce2581490a39d0e27ece289165187f66d400f350 提交 3
1393b0fadf4a0bc97d94c4221148dad1a96c37e6 提交 2
1bdacfdf972ab6ea3b8cb67fcc334e09c756a630 提交 1
```



#### 1.2 合并最新的多个提交

**步骤 0**：查看提交历史（使用 `git log --pretty=oneline` 或者 `git reflog --pretty=oneline`)

``` shell
$ git log --pretty=oneline
0fe7f86f6c5158829996c819e05ff60e79dbd4fd (HEAD -> master) 提交 5
ffc67af33ffb366d09e34ebf7bbe5b2701f2748b 提交 4
ce2581490a39d0e27ece289165187f66d400f350 提交 3
1393b0fadf4a0bc97d94c4221148dad1a96c37e6 提交 2
1bdacfdf972ab6ea3b8cb67fcc334e09c756a630 提交 1
```

目标是要将最后的三次提交合并为一个提交！

**步骤 1**：使用 `git reset HEAD~2` 将当前分支指向到前一条提交的前一条提交（即：提交 3），但是工作区保留当前最新提交（提交 5）的内容：

``` shell
$ git reset HEAD~2
Unstaged changes after reset:
M       1.txt
```

**步骤 2**：使用 `git commit --amend` 将当前工作区的变更合并到前一条提交的前一条提交（即：提交 3）：

``` shell
# 确保在 commit --amend 之前，将变更添加到暂存区 index 中:
$ git add .
# 然后, 以 --amend 方式提交
$ git commit --amend
[master 95810a7] 提交 3
 Date: Tue Jan 9 15:17:19 2024 +0800
 1 file changed, 4 insertions(+), 1 deletion(-)
```

到此，最后两条提交已经合并为一条提交！

``` shell
$ git log --pretty=oneline
95810a74a281630e26112c9a2fb4657dc8897e05 (HEAD -> master) 提交 3
1393b0fadf4a0bc97d94c4221148dad1a96c37e6 提交 2
1bdacfdf972ab6ea3b8cb67fcc334e09c756a630 提交 1
```



### 情形 2: 合并历史提交中的两个或多个提交

**步骤 0**：查看提交历史（使用 `git log --pretty=oneline` 或者 `git reflog --pretty=oneline`)

``` shell
$ git log --pretty=oneline 
945ee4e0b13822f1abac55af93d43e853f7e3758 测试 5
debeb58cf334fb943225f5cb85df46439d2df066 测试 4
a8c1dbd5a1376a96c19395065fa72657ddff79a2 测试 3
a29f69fdbca7e9124ecaae086480612dfafa13fb 测试 2
00cd72f277d01d845bdc241291705569726a03e9 测试 1
f3c3e661ef3d89924d92629699ab9b187707cbe6 测试 0
```

在这个案例中一共包含 6 条提交记录，希望将第 1 条（`测试 0`）和第 2 条（`测试 1`）提交合并为一条提交，即希望将 `f3c3e6` 和 `00cd72` 合并为一条提交。

**步骤 1**:  使用 `git reset --hard 00cd72` 将当前分支指向到第 2 条提交：

``` shell
$ git reset --hard 00cd72
HEAD 现在位于 00cd72f 测试 1
```

**步骤 2**：使用 `git reset f3c3e6` 将当前分支指向到第 1 条提交，但是工作区保留第 2 条提交的内容：

``` shell
$ git reset f3c3e6
重置后取消暂存的变更：
M       main.cpp
```

**步骤 3**：使用 `git commit --amend` 将第 2 条提交的变更合并到第 1 条提交：

``` shell
# 确保在 commit --amend 之前，将变更添加到暂存区 index 中:
$ git add .
# 然后, 以 --amend 方式提交
$ git commit --amend
[master 838644b] 测试 1
 Date: Tue Jan 9 10:33:15 2024 +0800
 3 files changed, 676 insertions(+)
 create mode 100644 main.cpp
 create mode 100644 uv_log.cpp
 create mode 100644 uv_log.h
```

**步骤 4**：使用 `git cherry-pick` 将其余不需要合并的提交 pick 出来：

``` shell
$ git cherry-pick a29f69^..945ee4
[master 7ffbcf4] 测试 2
 Date: Tue Jan 9 13:36:19 2024 +0800
 1 file changed, 1 insertion(+), 1 deletion(-)
[master 3fc5fef] 测试 3
 Date: Tue Jan 9 13:39:57 2024 +0800
 1 file changed, 1 insertion(+), 1 deletion(-)
[master eccc599] 测试 4
 Date: Tue Jan 9 13:41:27 2024 +0800
 1 file changed, 1 insertion(+), 1 deletion(-)
[master 153c35e] 测试 5
 Date: Tue Jan 9 11:29:39 2024 +0800
 1 file changed, 3 insertions(+), 3 deletions(-)
```

到此，第 1 条和第 2 条提交合并为一条提交！

``` shell
$ git log --pretty=oneline 
372c2c4b60d05287feccd7931e265c764e98e947 测试 5
58be2d18b0e7cc508c0fe7a690dd78febfb8d427 测试 4
696078e0368fa2251b49ab68381dcccdf666a08e 测试 3
39831e1664a89e114fa697ea832f585ef427d4b8 测试 2
1d6dcf677089f115d382ff03f736b172522fb4d2 测试 1
```

