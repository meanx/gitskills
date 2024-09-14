# [利用 git-filter-repo 无缝迁移 git 项目 - SegmentFault 思否](https://segmentfault.com/a/1190000041375416)

**swearer23** [2022-02-08](https://segmentfault.com/a/1190000041375416/revision) 阅读 4 分钟

### 背景

正所谓天下大事合久必分，分久必合。实际工作中的项目也类似，有的项目越来越大，或者有时候需要把没有前后端分离的项目代码拆分到两个仓库里，就涉及到对已有git项目的迁移操作。

如果只是简单的把代码拆开并不难，可以选择直接下载git项目源代码压缩包，拆分后push到新建的git仓库里就可以了。

但是现实世界往往没有那么简单，比如正在进行的开发分支还没有合并，或者我们想保留提交记录和分支关系。如此种种原因导致无法简单粗暴地把源代码扔到一个新建的git仓库里，还涉及对git记录进行必要的裁剪。

本文就介绍一个好用的工具来进行无损的git仓库迁移。

### 真实案例

在实际工作中，我们有一个项目，其项目目录结构很简单，是一个既含有前端代码，也含有服务端代码的仓库，如下所示：

```stylus
server/
   foo.c
   bar.c
webapp/
   app.tsx
   components/
           index.tsx
zebra.jpg
```

我们希望将前端和服务端代码拆分成两个独立的git仓库，但是因为开发同学还有正在开发的功能分支没有合并，因此我们希望能够同时迁移代码和git提交历史以及分支。最终希望得到的结果是：

```
git repo A: ./server/
git repo B: ./webapp/
```

### git-filter-branch 命令

通过查看 git 文档，首先考虑使用 `git filter-branch `命令来进行迁移。简单来说该命令可以用来操作目录树，同时修改历史提交记录。

在我还没来得及完全理解这个命令之前，就看到文档中有这样一段 `warning`

> *git filter-branch* has a plethora of pitfalls that can produce non-obvious manglings of the intended history rewrite (and can leave you with little time to investigate such problems since it has such abysmal performance). These safety and performance issues cannot be backward compatibly fixed and as such, its use is not recommended. Please use an alternative history filtering tool such as [git filter-repo](https://link.segmentfault.com/?enc=REtcYvxC%2B6ried5HFezzqA%3D%3D.Ra9zug3PkybMRTmO6hT%2BlWWB2Jz0vKyooZHVun5SCtxI5yFAnubp2t2Mt%2BsZsymI). If you still need to use *git filter-branch*, please carefully read [SAFETY](https://link.segmentfault.com/?enc=X8Eq%2Bcp25B3iPVkZ%2FViJrQ%3D%3D.6pExJYOhCeXFqQW7nABXFCsP%2FjAJTgoovXXTDcJr5C5weg7FWO1TTn92THzcpj%2BMKriyMKOmloihP4Bynr5pmg%3D%3D) (and [PERFORMANCE](https://link.segmentfault.com/?enc=0dAJN%2B%2FISB8vucV5ZhAS7w%3D%3D.fL2Y51Vhh3wW5Jck4boW2sDXTtxsQziOl%2BezOYCd0qVna7txzuGWcguXfRouCOHMKQLHe%2B%2Fh9M6h9kUEe4tk4g%3D%3D)) to learn about the land mines of filter-branch, and then vigilantly avoid as many of the hazards listed there as reasonably possible.

这里提到了 `filter-branch` 命令由于有可能产生杂乱的提交历史，以及惨不忍睹的执行效率，所以最终推荐了一个第三方工具 [git filter-repo](https://github.com/newren/git-filter-repo/)。接下来，就该今天的主角登场了。

### git-filter-repo

在 `github` 主页上，关于 `git-filter-repo` 有这样的描述

> `git-filter-repo` 可以胜任很多需要修改提交历史的场景，虽然它与 `git-filter-branch` 命令功能有些许重合，但摒弃了 `git-filter-branch` 那令人抓狂的执行效率。
>
> 在功能方面，`git-filter-repo` 的人机交互设计让其面对简单的修改更加游刃有余，同时仍然可以像复杂的`git-filter-branch` 命令一样完成庞杂的任务。

接下来我们考虑如何利用这个强大的工具来进行 git 项目的迁移。

首先，需要定义成功迁移的标准：

- 删减 server 目录，仅保留 webapp 目录，并让 webapp 成为新的根目录
- 推向新的 git 仓库
- 确认在新的 git 仓库中仍然保留所有与 webapp 相关的分支
- 确认迁移之后保留的分支历史记录应该与老仓库的历史记录一致

### 相关命令简介

> 注意，使用 `git-filter-repo` 需要 `git` 版本高于 `2.22.0`
>
> ``` shell
> $ git filter-repo --path-rename :ITU-MPU/
> Error: need git >= 2.22.0
> ```

查看 `git-filter-repo` 的文档可以看到有不少简单的示例，很幸运有一些例子正好可以解决我们的问题。

`git-filter-repo` 的命令选项 (flag) 主要用来操作目录树，根据操作的目录树自动判断需要修改的 git 提交历史信息。

比如我们需要保留 webapp 目录，删除 server 目录，那么仅需执行：

```bash
git filter-repo --path 'webapp/'
```

这样仓库中的目录结构就会变为：

```axapta
webapp/
   app.tsx
   components/
           index.tsx
```

可以看到，已经没有server文件夹了。

再比如如果我们希望删除 .DS_Store 文件以及其提交历史：

```bash
git filter-repo --invert-paths --path '.DS_Store'
```

当然通常我们需要删除根目录下所有 .DS_Store 文件及其历史，那么可以加上`--use-base-name`选项，表示匹配文件名，而不是匹配完整路径：

```bash
git filter-repo --invert-paths --path '.DS_Store' --use-base-name
```

其中 `--path` 选项后跟需要操作的路径或者文件名，`--invert-paths` 选项字面意思是反向，因此该标记表示的是删除操作。

以上都是删除某些文件或者保留某些文件的操作，其目录结构仍然会保留原始仓库的结构，但我们需要的是仅保留webapp 目录下的所有文件，并将其中的内容移动到根目录下。针对这种场景 `git-filter-repo` 提供了一个叫做 `--subdirectory-filter` 的选项，接下来就进行实际操作。

### 实际操作

> **`git-filter-repo` 需要在一个干净的，刚刚 clone 下来的仓库中进行操作，否则会提示操作失败并停止操作**

接下来再看一下老的仓库目录结构

```stylus
server/
   foo.c
   bar.c
webapp/
   app.tsx
   components/
           index.tsx
zebra.jpg
```

仅保留 webapp 目录下的内容，并让其成为新的根目录，执行如下命令：

```bash
git filter-repo --subdirectory-filter webapp/
```

执行结果的目录结构如下所示：

```axapta
app.tsx
components/
    index.tsx
```

至此，目录结构已经如愿完成，为了确认迁移的历史记录也是完整的，执行

```bash
git branch # 观察是否所有需要保留的分支仍然存在
git log #观察分支的提交记录是否正确
```

如果确认本地仓库的迁移结果正确，再执行命令将当前本地仓库推向迁移的目的仓库即可：

```bash
git remote add <new-origin> git://somehost/some-project.git # 添加新的远程仓库
git push <new-origin> --all # 向新的仓库推送本地所有分支
```

至此我们的 git 项目迁移就完成了，不仅将代码迁移到新的仓库，也同时将提交历史带去了新的仓库。这样一来，对于开发同学来说迁移完全是无痛的，只是切换了新的 git 地址，而开发过程完全不会中断。

### 结语

在 git 项目需要迁移的场景中，日常工作中也许第一反应就是让开发同学们放下手中的工作，全部推向一个指定用于迁移的分支，然后以这个分支为准，下载源代码，再将其推送到新的仓库中。

这种操作方式虽然简单，但是对于开发同学来说，却会造成很多麻烦。

比如新仓库的代码不仅含有自己未开发完成的代码，也含有其他人未完成的代码，很有可能在迁移完成之后项目都跑不起来。

而除此以外，如果新仓库的代码有一个需要很快就上线的功能，或者紧急修复，面对一个百废待兴的新仓库，如何整理出一个可以上线的代码版本，又是一件非常头大的事情。

`git-filter-branch` 可以帮助我们修改目录树和提交历史，但是执行效率和混乱的提交历史显得太过于繁琐。

`git-filter-repo` 工具提供了强大的工具集，良好的用户使用界面，以及高效率的处理机制，在目前确实是处理仓库迁移的最优选择。

本文仅仅是讲解了 `git-filter-repo` 的一种应用场景，其官方文档上有更多的使用场景，可以根据更多的条件来过滤和操作文件和提交历史。

更多的精彩还请移步官方文档：

[git filter-repo github](https://link.segmentfault.com/?enc=QOmJs97fENeJpxfe7VppUw%3D%3D.nek7XbuP%2FJ%2BQHAMG6mLMjwbAd%2BRqrZQQtRwueVsLrn48b2Mfqi3ISf0hUiDXRtKJ)

[git filter-repo 文档](https://link.segmentfault.com/?enc=jUxFmk9DWjtI5zJOyKkcHQ%3D%3D.RqmN%2BeBUBOmMSyNj9zLNxIonFmkCzvl84UR1wgQqK29RUO3urI2p8OpNXAq%2F2q3csqCnFD%2FCKzkTfuoh72XDIWnrCrBFrd79IC1QeCgRl8woVyopIQor4jxR%2Fe39tM5Jvskauw3OwdS%2FXFHVrasxNg%3D%3D)