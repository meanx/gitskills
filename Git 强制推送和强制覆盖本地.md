# [Git 强制推送和强制覆盖本地](https://blog.csdn.net/qq_27437991/article/details/82992323)



## 1、`git push -f` 强制推送

```shell
$ git push -f origin master 
```

例如：当在 github 新建仓库时，本地有代码需要直接推送，由于 github 新建的仓库含有 `readme` 文件，会导致推送失败，此时就需要强制提交。



## 2、git 强制覆盖本地

由于远程仓库被他人强制推送了提交，所以如果直接 git pull 将会遇到两种情形：



### 情形一、直接 `git pull` 后自动创建了 merge 提交

这种情形下，如果目的是不需要保留本地仓库分支的修改，希望直接使用远程参数的修改，可以先撤销本次 `git pull` 自动创建的合并提交，再通过 `git reset --hard` 强制使用远程仓库的分支覆盖本地分支：

```shell
# 撤销本次 `git pull` 自动创建的合并提交
git reset --hard HEAD^
# 使用远程仓库分支 origin/master 覆盖本地分支:
git reset --hard origin/master
```



### 情形二、直接 `git pull` 后引起 merge 冲突

```shell
/test/push-f/client-2 (master)$ git pull
Auto-merging 2.txt
CONFLICT (content): Merge conflict in 2.txt
Auto-merging 3.txt
CONFLICT (content): Merge conflict in 3.txt
Automatic merge failed; fix conflicts and then commit the result.

/test/push-f/client-2 (master|MERGING)$
```
此时可以通过 `git merge --abort` 将本地分支取消合并回复到原先本地分支的状态：
```shell
/test/push-f/client-2 (master|MERGING)$ git merge --abort

/test/push-f/client-2 (master)$

```
然后，先 `git fetch` 获取最新的远程仓库的更新，再通过 `git reset --hard` 强制使用远程仓库的分支覆盖本地分支：
```shell
# 从远程仓库下载最新版本
git fetch --all 
# 将本地设为刚获取的最新的内容
git reset --hard origin/master
```
当不需要一个本地的更改，但又不想一个一个去修改，可以直接用远程仓库覆盖本地的内容。



————————————————
版权声明：本文为 CSDN 博主「无名万物」的原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/qq_27437991/article/details/82992323