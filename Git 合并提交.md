# Git 合并提交



## 情形 1：合并最新提交中的两个或多个提交

### 1.1 合并最新的两个提交

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



### 1.2 合并最新的多个提交

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



## 情形 2: 合并历史提交中的两个或多个提交

**步骤 0**：查看提交历史（使用 `git log --pretty=oneline` 或者 `git reflog --pretty=oneline`)

``` shell
$ git log --pretty=oneline 
945ee4e0b13822f1abac55af93d43e853f7e3758 测试 5
debeb58cf334fb943225f5cb85df46439d2df066 测试 4
a8c1dbd5a1376a96c19395065fa72657ddff79a2 测试 3
a29f69fdbca7e9124ecaae086480612dfafa13fb 测试 2
00cd72f277d01d845bdc241291705569726a03e9 测试 1 （测试 0 和 测试 1 使用相同代码）
f3c3e661ef3d89924d92629699ab9b187707cbe6 测试 1 （测试 0 和 测试 1 使用相同代码）
```

在这个案例中一共包含 6 条提交记录，希望将第 1 条和第 2 条提交合并为一条提交，即希望将 `00cd72` 和  `f3c3e6` 合并为一条提交。

**步骤 1**:  使用 `git reset --hard 00cd72` 将当前分支指向到第 2 条提交：

``` shell
$ git reset --hard 00cd72
HEAD 现在位于 00cd72f 测试 1 （测试 0 和 测试 1 使用相同代码）
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
[master 838644b] 测试 1 （测试 0 和 测试 1 使用相同代码）
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
1d6dcf677089f115d382ff03f736b172522fb4d2 测试 1 （测试 0 和 测试 1 使用相同代码）
```

