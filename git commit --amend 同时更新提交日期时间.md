# git commit --amend 同时更新提交日期时间

参考 [git修改commit时间-CSDN博客](https://blog.csdn.net/guoyajie1990/article/details/73824732)

有时需要修改上次 `git commit` 的时间，比如之前将代码提交到本地库中，现在想将这次提交推送到远程仓库，但是这次提交的时间显示还是昨天的时间，下面提供一个办法用于修改上次提交的时间：
使用：

```shell
$ git commit --amend  --date="commit_time"
```

"commit_time" 的格式比较难记，不过有个小技巧，我们可以先在命令行输入：

```shell
$ date -R
Fri, 20 Oct 2023 17:37:05 +0800
```

这个命令的输出格式与 `git commit --amend --date` 命令要填写的日期格式相同，自己再稍加修改一下即可。
如果我们只是想将上次 `git commit` 的时间改为当前时间，可以使用以下两个命令：

```
$ git commit --amend --date="$(date -R)"
# 或
$ git commit --amend --date=`date -R` # 测试报错
```

对于如何在 `git commit --amend` 时将提交时间修改为任意的时间，也简单，按照 `date -R` 命令的输出格式自己构造 commit_time 即可。

```shell
$ git commit --amend --date="Fri, 20 Oct 2023 17:37:05 +0800"
```

其实，以上语句中指定的`日期时间字符串值`中可以省略 `星期,  `，例如：

``` shell
$ git commit --amend --date="20 Oct 2023 17:37:05 +0800"
```

