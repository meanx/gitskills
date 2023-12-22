# git stash drop 之后如何恢复



## 参考资料

[`git stash drop` 误用恢复](https://blog.csdn.net/u014213012/article/details/104640094)
[如何恢复丢弃的 `git stash` 暂存](https://zhuanlan.zhihu.com/p/28948567)
[找回 `git stash` 误删除的代码并恢复](https://zhuanlan.zhihu.com/p/328105834)



## 场景

代码正在开发中不想 `commit`，但是要切换其它分支去处理临时紧急任务，于是使用 `git stash` 备份当前的工作区的内容。
切换回来时不小心使用了 `git stash drop`，结果暂存的修改全部没有了。



## 方法一、知道删除 stash 的 SHA1 值

示例：

```shell
# 起初共有 2 个 stash
$ git stash list
stash@{0}: On master: modify file2.txt
stash@{1}: On master: modify file1.txt

# 误操作删除了 1 个
$ git stash drop
Dropped refs/stash@{0} (fad2c40bba8fd7662ced91f338fa589cd48e8d0b)

# 删除后剩下 1 个 stash
$ git stash list
stash@{0}: On master: modify file1.txt

# 通过被删除的 stash 的 sha1 值, 将对于的文件变更应用到 apply 当前工作副本
$ git stash apply fad2c40bba8fd7662c
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   file2.txt

no changes added to commit (use "git add" and/or "git commit -a")

# 重新将 stash 的文件变更暂存到 stash 中:
$ git stash save "modify file2.txt"
Saved working directory and index state On master: modify file2.txt

$ git stash list
stash@{0}: On master: modify file2.txt
stash@{1}: On master: modify file1.txt
```



## 方法二、不知道删除 stash 的 SHA1 值，但是大概记得自己修改了哪些内容

如果使用 `git stash drop` 误删除了最新的一个 stash，git 会显示被删除的 stash 的 SHA1 值；这时，使用前面的方法一即可找回该 stash；

如果使用 `git stash clear`误删除了所有的 stash，git 并不会显示被删除的 stash 的 SHA1 值；此时，需要使用到 `git fsck --lost-found` 命令查找被删除的 stash 的 SHA1 值：

示例：

```shell
# 起初共有 2 个 stash
$ git stash list
stash@{0}: On master: modify file2.txt
stash@{1}: On master: modify file1.txt

# 误操作删除了所有的 stash
$ git stash clear

# 删除后剩下 0 个 stash
$ git stash list

# 使用 git fsck -h 查看 fsck 命令的帮助信息
$ git fsck -h
usage: git fsck [<options>] [<object>...]

    -v, --verbose         be verbose
    --unreachable         show unreachable objects
    --dangling            show dangling objects
    --tags                report tags
    --root                report root nodes
    --cache               make index objects head nodes
    --reflogs             make reflogs head nodes (default)
    --full                also consider packs and alternate objects
    --connectivity-only   check only connectivity
    --strict              enable more strict checking
    --lost-found          write dangling objects in .git/lost-found
    --progress            show progress
    --name-objects        show verbose names for reachable objects


$ git fsck --lost-found
Checking object directories: 100% (256/256), done.
dangling commit fad2c40bba8fd7662ced91f338fa589cd48e8d0b
dangling commit 3e90186ea3fcf126a29c32bf2bae2cde2117f1b1

$ git fsck --dangling
Checking object directories: 100% (256/256), done.
dangling commit fad2c40bba8fd7662ced91f338fa589cd48e8d0b
dangling commit 3e90186ea3fcf126a29c32bf2bae2cde2117f1b1

# 注意 --unreachable 会显示 tree / blob 对象的 SHA1 值
$ git fsck --unreachable
Checking object directories: 100% (256/256), done.
unreachable tree 050be87ad90bd9536f2c0a968317602ab4072873
unreachable commit 4778e40bf66d268f6223a92b61c8c0e3eedb3840
unreachable blob cbf26c15901e4923b7aadc4a8bdc2a8f4d5d7d6e
unreachable blob d8263ee9860594d2806b0dfd1bfd17528b0ba2a4
unreachable commit fad2c40bba8fd7662ced91f338fa589cd48e8d0b
unreachable commit fb44a922453435feff411eaf7b92eb5fe6019ed6
unreachable tree 7da0efc1c34cf97ae15258dcb720ace26899540d
unreachable commit 3e90186ea3fcf126a29c32bf2bae2cde2117f1b1
```

使用 `git fsck --lost-found` 只是查找出所有 danging 的 commit 的 SHA1 值；接下来需要进一步从这些 SHA1 值找出哪些 SHA1 是属于被误删除的 stash；这里，需要使用到 `git show SHA1` 命令查看某个 SHA1 的具体详情信息：

示例：

```shell
$ git show fad2c40bba8fd7662c
commit fad2c40bba8fd7662ced91f338fa589cd48e8d0b
Merge: 0da86a1 fb44a92
Author: MeanXu <meanx@shineauto.com.cn>
Date:   Wed Dec 13 14:52:35 2023 +0800

    On master: modify file2.txt

diff --cc file2.txt
index e69de29,e69de29..d8263ee
--- a/file2.txt
+++ b/file2.txt
@@@ -1,0 -1,0 +1,1 @@@
++2

```

通过 `git show SHA1` 显示的详情信息中，可以判断出 `modify file2.txt` 是其中一个 stash 在创建时添加的 `comment` 内容，因此，可以判断该 SHA1 就是被删除的 stash 中的其中之一。以此类推，可以一步步找回所有被误删除的 stash。

### 技巧

不难发现，通过先 `git fsck --lost-found` 列出所有 danging 的 commit 的 SHA1 值，再手动 `git show SHA1` 一个个查询所需的 stash，效率比较慢。

其实，可以使用重定义 `>>` 符号，将结果保存到文本文件，然后使用 `emeditor` 或 `notepad++` 等工具的查询或标签功能，能够跟方便检索所删除的 stash。



**步骤 1：**

```shell
# 使用"重定向"
$ git fsck --lost-found >> 1.txt
# 或者，使用"追加重定向"
$ git fsck --lost-found >> 1.txt
```

执行上步命令后，将会在 git 本地仓库根目录下生成 1.txt 文件，下一步将会用到该文件。

示例：

```shell
$ git fsck --lost-found > 1.txt
Checking object directories: 100% (256/256), done.
```



**步骤 2：**

创建脚本自动执行 `git show SHA1` 快速生成 `git fsck --lost-found` 列出的所有 commit 的详情信息，并写入到文件中；

在 git 本地仓库根目录下，创建脚本 `show.sh` 文件，文件内容如下：
``` shell
#!/bin/sh
READFILENAME="1.txt"      # 上一步重定向生成的文件的名称
RETFILENAME="./ret.txt"   # 筛选结果文件名

while read -r line       
do    
    VAR=`echo $line | awk '/dangling commit/ {print $3}'`
    if [ $VAR ]; then
        git show $VAR >> $RETFILENAME    
        echo "-------------------------------------\n" >>$RETFILENAME # 每条记录用---分割开
    fi
done < $READFILENAME
```

然后，在 `git bash` 中执行该脚本：

``` shell
$ ./show.sh
```

打开生成的 `ret.txt` 文件：

``` 
commit fad2c40bba8fd7662ced91f338fa589cd48e8d0b
Merge: 0da86a1 fb44a92
Author: MeanXu <meanx@shineauto.com.cn>
Date:   Wed Dec 13 14:52:35 2023 +0800

    On master: modify file2.txt

diff --cc file2.txt
index e69de29,e69de29..d8263ee
--- a/file2.txt
+++ b/file2.txt
@@@ -1,0 -1,0 +1,1 @@@
++2
-------------------------------------\n
commit 3e90186ea3fcf126a29c32bf2bae2cde2117f1b1
Merge: 0da86a1 4778e40
Author: MeanXu <meanx@shineauto.com.cn>
Date:   Wed Dec 13 14:52:24 2023 +0800

    On master: modify file1.txt

diff --cc file1.txt
index 8b13789,8b13789..cbf26c1
--- a/file1.txt
+++ b/file1.txt
@@@ -1,1 -1,1 +1,2 @@@
  
++1
-------------------------------------\n
```

可见，该脚本自动将 `git fsck --lost-found` 中得各个 commit 得详情信息写入到生成得 `ret.txt` 文件中；该技巧省去了手动输入 `git show SHA1` 一个个 commit 肉眼检索目标 stash 的 SHA1 值得弊端。
