# git config（system、global、local）配置文件位置

[git config的配置（system、global、local） | 老潘的博客 (panyanbin.com)](https://www.panyanbin.com/article/ff4e1f85.html)



现在大部分的项目代码的都使用 git 做版本控制，因此我们有必要了解git的配置文件。

如果对git的配置文件不了解会导致有些时候明明配置了却发现不生效，比如在某个非git仓库目录下执行配置用户名命令：`git config user.name xxx`，却发现某个项目仓库并没有生效。

首先，我们在命令行中输入`git config`可以看到如下图提示：

``` shell
# Windows 平台
C:\Users\60173> git config
usage: git config [<options>]

Config file location
    --global              use global config file
    --system              use system config file
    --local               use repository config file
    --worktree            use per-worktree config file
    -f, --file <file>     use given config file
    --blob <blob-id>      read config from given blob object
# Ubuntu 平台：
$ git config
用法：git config [<选项>]

配置文件位置
    --global              使用全局配置文件
    --system              使用系统级配置文件
    --local               使用仓库级配置文件
    -f, --file <文件>     使用指定的配置文件
    --blob <数据对象 ID>   从给定的数据对象读取配置
```

Git有3个级别的配置文件（`local`、`global`、`system`），worktree基本不使用此处不作考虑。



## system 系统级

系统级配置文件含有系统里每位用户及他们所拥有的仓库的配置值。

如果是 Linux 系统，其位置为 `/etc/gitconfig`；

如果是 Windows 系统，其位置为git的安装目录下的`/etc/gitconfig`，即如果git的安装目录为`D:\Git`，则配置文件地址为`D:\Git\etc\gitconfig`。

**优先度最低，其配置值可被全局级配置和本地级配置的值覆盖**。一般我们很少会使用系统级的配置。

示例：

``` shell
C:\Users\60173>git config --system --list --show-origin
file:D:/Program Files/Git/etc/gitconfig diff.astextplain.textconv=astextplain
file:D:/Program Files/Git/etc/gitconfig filter.lfs.clean=git-lfs clean -- %f
file:D:/Program Files/Git/etc/gitconfig filter.lfs.smudge=git-lfs smudge -- %f
file:D:/Program Files/Git/etc/gitconfig filter.lfs.process=git-lfs filter-process
file:D:/Program Files/Git/etc/gitconfig filter.lfs.required=true
file:D:/Program Files/Git/etc/gitconfig http.sslbackend=openssl
file:D:/Program Files/Git/etc/gitconfig http.sslcainfo=D:/Program Files/Git/mingw64/ssl/certs/ca-bundle.crt
file:D:/Program Files/Git/etc/gitconfig core.autocrlf=true
file:D:/Program Files/Git/etc/gitconfig core.fscache=true
file:D:/Program Files/Git/etc/gitconfig core.symlinks=false
file:D:/Program Files/Git/etc/gitconfig pull.rebase=false
file:D:/Program Files/Git/etc/gitconfig credential.https://dev.azure.com.usehttppath=true
file:D:/Program Files/Git/etc/gitconfig init.defaultbranch=master
```



## global 全局级

全局级配置文件包含当前系统用户的拥有的仓库配置值，每个系统用户的全局级配置相互隔离。全局级别的配置默认保存在当前系统用户的主目录下的 .gitconfig 文件内。Windows通常保存在`C:\Users\xxxx\.gitconfig`，Linux为`/home/xxx/.gitconfig`。

示例:

``` shell
C:\Users\60173>git config --global --list --show-origin
file:C:/Users/60173/.gitconfig  core.autocrlf=false
file:C:/Users/60173/.gitconfig  user.email=meanx@shineauto.com.cn
file:C:/Users/60173/.gitconfig  user.name=MeanXu
file:C:/Users/60173/.gitconfig  credential.http://192.168.3.25:8088.provider=generic
file:C:/Users/60173/.gitconfig  credential.helper=store
```

**优先度比系统级高，可覆盖系统级的配置值**。全局级的配置平时使用得比较多，比如设置账号和邮箱：

```
git config --global user.name panyanbin
git config --global user.email me@panyanbin.com
```



## local 本地级

本地级别的配置保存在当前仓库下面的 `.git\config` 文件内，通常 .git 文件夹是隐藏的，Window要在文件管理器的文件夹选项中打开显示隐藏文件夹才可以看到。这里的配置仅对当前仓库有效，不影响其他的仓库。

示例：

``` shell
C:\Users\60173>git config --local --list --show-origin
fatal: --local can only be used inside a git repository

C:\Users\60173>cd /d E:\Work\ITU\D2211\itu-mpu

E:\Work\ITU\D2211\itu-mpu>git config --local --list --show-origin
file:.git/config        core.repositoryformatversion=0
file:.git/config        core.filemode=false
file:.git/config        core.bare=false
file:.git/config        core.logallrefupdates=true
file:.git/config        core.symlinks=false
file:.git/config        core.ignorecase=true
file:.git/config        remote.origin.url=http://192.168.3.25:8088/youshougeng/itu-mpu/
file:.git/config        remote.origin.fetch=+refs/heads/*:refs/remotes/origin/*
file:.git/config        branch.main.remote=origin
file:.git/config        branch.main.merge=refs/heads/main
```

**优先级别最高，如果全局级别或系统级别的配置里出现了同一配置项，则以本地级别配置内容为准**



## 配置使用

- 获取配置项的值

获取某一项配置时若不指定级别，则会从本地级开始一级一级往上查找直到配置不存在。即：先从本地级配置中开始查找配置项，若本地级不存在则往上从全局级别配置查找，若全局级别还是不存在该配置项就从系统级配置查找，若还是不存在则返回空。

```
git config --global --get user.name
```

- 设置配置项的值

设置某一项配置时若不指定级别，则会配置项默认会设置到本地级的配置文件中，即设置配置项时默认使用`--local`级别。若指定级别配置项就设置到该级别的配置文件中。

```
git config --global user.name panyanbin
```

- 删除配置项

与设置配置项一样，删除配置项若不指定级别，则默认使用`--local`级别。指定级别则删除该级别的配置文件中的配置项

```
git config --global --unset user.name
```

- 获取所有配置项

若不指定配置级别，则会返回3个级别的配置，从上往下按系统级别、全局级别、本地级别的顺序进行输出显示。

```
git config --global --list
git config --global -l
```

了解更多内容可以看 [git官网](https://git-scm.com/book/zh/v2/自定义-Git-配置-Git) 的配置说明。

