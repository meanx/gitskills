# git push 如何推送多个分支



## `git push` 命令不支持通配符

### 问题

`git push` 分支时，对于多个分支，比如： `release_v1.1`, `release_v1.2`, `release_v1.3` 能否通过通配符一次性 push 多个分支？

### GPT 3.5

在 Git 中，`git push` 命令不支持直接使用通配符来一次性推送多个分支。但是，你可以使用一个简单的循环或脚本来遍历多个分支并逐个进行推送。

以下是一个示例的 Bash 脚本，用于遍历多个分支并逐个推送：

``` shell
#!/bin/bash

branches=("release_v1.1" "release_v1.2" "release_v1.3")

for branch in "${branches[@]}"
do
    git push origin "$branch"
done
```

在这个示例中，我们定义了一个包含多个分支名称的数组 `branches`。然后，使用 `for` 循环遍历数组中的每个分支，并使用 `git push` 命令推送到远程仓库的对应分支。

你可以根据实际需要修改 `branches` 数组中的分支名称，或者将脚本集成到你的工作流程中。

希望这个示例对你有帮助！如果你还有其他问题，请随时提问。



### 案列

使用 `git branch -l | grep release_v1.5` 获取需要 push 的本地分支：
``` shell
$ git branch -l | grep release_v1.5
  release_v1.5.0
  release_v1.5.1
  release_v1.5.2
  release_v1.5.3
  release_v1.5.4
  release_v1.5.5
  release_v1.5.6
  release_v1.5.7
```
然后，在项目本地仓库的根目录中创建 `git-push-multi.sh` 脚本文件，并编辑为：

``` shell
#!/bin/bash

branches=("release_v1.5.0" "release_v1.5.1" "release_v1.5.2" "release_v1.5.3" "release_v1.5.4" "release_v1.5.5" "release_v1.5.6" "release_v1.5.7")

for branch in "${branches[@]}"
do
    git push gee "$branch"
done
```

接着，修改 `git-push-multi.sh` 脚本文件的可执行权限：

``` shell
# 先查看：git-push-multi.sh 没有可执行权限
~/work/itu-mpu(develop)$ ls -l
总用量 56
-rw-rw-r--  1 xcyyj xcyyj   227 1月  31 16:32 git-push-multi.sh
-rwxrwxr-x  1 xcyyj xcyyj  1239 1月  31 15:58 lazy.sh
drwxrwxr-x  4 xcyyj xcyyj  4096 10月  9 16:21 lib
drwxrwxr-x  3 xcyyj xcyyj  4096 1月   8 15:50 out
-rwxrwxr-x  1 xcyyj xcyyj  3841 1月  31 15:58 README.md
drwxrwxr-x  2 xcyyj xcyyj  4096 9月  15 17:07 script
drwxrwxr-x  4 xcyyj xcyyj  4096 1月   8 15:29 sdk
drwxrwxr-x 34 xcyyj xcyyj  4096 1月  15 11:45 soc
# 修改权限
~/work/itu-mpu(develop)$ chmod 774 ./git-push-multi.sh 
# 再查看: git-push-multi.sh 具有可执行权限
~/work/itu-mpu(develop)$ ls -l
总用量 56
-rwxrwxr--  1 xcyyj xcyyj   227 1月  31 16:32 git-push-multi.sh
-rwxrwxr-x  1 xcyyj xcyyj  1239 1月  31 15:58 lazy.sh
drwxrwxr-x  4 xcyyj xcyyj  4096 10月  9 16:21 lib
drwxrwxr-x  3 xcyyj xcyyj  4096 1月   8 15:50 out
-rwxrwxr-x  1 xcyyj xcyyj  3841 1月  31 15:58 README.md
drwxrwxr-x  2 xcyyj xcyyj  4096 9月  15 17:07 script
drwxrwxr-x  4 xcyyj xcyyj  4096 1月   8 15:29 sdk
drwxrwxr-x 34 xcyyj xcyyj  4096 1月  15 11:45 soc
```

最后，在终端运行 `git-push-multi.sh`

``` shell
~/work/itu-mpu(develop)$ ./git-push-multi.sh
```

