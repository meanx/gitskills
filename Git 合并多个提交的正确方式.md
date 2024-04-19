# [Git 合并多个提交的正确方式]([Git 合并多个提交的正确方式 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/139321091))

[小揪](https://www.zhihu.com/people/wo-yao-chao-neng-li)

Slow is fast

47 人赞同了该文章



## **1. 应用场景**

对于一个项目，你可能会多次提交代码，每次提交都对应一个`commit sha`

![img](image\v2-a72c44bd0be37e42031fd18260065674_720w.png)

当完成项目，要进行分支合并的时候，只想保留一个或某几个 commit，这时候就需要合并 commit 了。

## **2. 如何合并**

这里介绍两种方式，第一种是`git rebase`，第二种是`git rebase --autosquash`，后者在`git commit`时是有条件的。

### **1) git rebase**

第一步，开启交互模式

```text
git rebase -i <commit_sha>
```

注意，这里的`<commit_sha>`是你针对此项目第一个提交的前一个提交的 commit。

举个例子：

下面是工作中的提交记录：

![img](image\v2-36490a04fd83a89cee7f90b5c206b504_720w.png)

这时我们想要把前七个 commit 合并成一个，即：

![img](image\v2-85000441614822394b9fd956f409ee5c_720w.png)

其中`commit_sha`是第一个提交的前一个提交的哈希。 因此，在我的示例中，命令为：

```text
git rebase -i 6394dc
```

第二步，摘取合并

这时候会弹出一个框，列出了你想要合并的所有 commit。注意列出的顺序是从老到新的。

![img](image\v2-2bbca65c61436232fd4914eae8fb31fe_720w.png)

> 链接：https://blog.csdn.net/w57685321/article/details/86597808
>
> 执行 `git rebase -i <sha>` 命令后会跳到一个 vi 编辑器
> 里面的提示有：
> pick：保留该commit（缩写:p）
> reword：保留该commit，但我需要修改该commit的注释（缩写:r）
> edit：保留该commit, 但我要停下来修改该提交(不仅仅修改注释)（缩写:e）
> squash：将该commit和前一个commit合并（缩写:s）
> fixup：将该commit和前一个commit合并，但我不要保留该提交的注释信息（缩写:f）
> exec：执行shell命令（缩写:x）
> drop：我要丢弃该commit（缩写:d）

更改每行 commit_sha 最前面的单词，我们打算把这七个合并成一个 commit，那么更改如下：

![img](image\v2-db5e3d23fe4caa0b2b86def07cdd47ef_720w.png)

保存退出后，又弹出一个新的框，让我们更改commit信息，编辑完后退出就好了。最后完成的效果如下：

![img](image\v2-88260d5decf294c0b0596d244a2d7d7c_720w.png)

### 2) git rebase --autosquash

顾名思义，就是会自动帮你压缩 commit。但是你在`git commit`的时候需要使用特殊命令：

```text
git commit --fixup=<commit_sha>
```

这里的`commit_sha`是指你对哪个commit进行了更改，所以必须先至少存在一个对该项目的提交。

举个例子：

下面是工作中的提交记录：

![img](image\v2-a2810d935edf11bedad14cf58ac5d10c_720w.png)

我现在有有了一个新的改动，那么在提交时，就需要用如下命令表明我是对上一个commit进行了更改：

```text
git commit --fixup=d94e78
```

产生的效果是这样的：

![img](image\v2-7f09787c26a16a6943fc7fb13635a1d3_720w.png)

这时候又有了一个新的改动，那么提交时的命令不变：

```text
git commit --fixup=d94e78
```

产生的效果：

![img](image\v2-eed9303b931ad76e2e80d86677111b0d_720w.png)

接下来我们进行合并操作：

```text
git rebase --autosquash -i <commit_sha>
```

注意，这里的`commit_sha`是 older commit `6394dc`。

Git这时会很机智的补全单词，不需要手动输入了。

![img](image\v2-0c6076cc9e3515e77aeabd811bc1deb6_720w.png)

直接保存退出，重新编辑提交信息即可。

以上两种方式，一种是提交时轻松，合并时麻烦，另外一种相反。可以根据喜好来选择使用。

编辑于 2020-05-11 10:32