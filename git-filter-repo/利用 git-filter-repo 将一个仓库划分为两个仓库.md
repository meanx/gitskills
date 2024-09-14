# 利用 git-filter-repo 将一个仓库划分为两个仓库



## 背景

在前面的 《[利用 git-filter-repo 将两个仓库合并为一个仓库.md](./利用 git-filter-repo 将两个仓库合并为一个仓库.md)》一文中描述了如何将两个仓库合并为一个仓库；然而有些场景下，正好相反：希望将一个仓库分解为多个仓库。



## 方法

当前现有的 Git 仓库 `ITU-UNMAN` 包含两个目录 `ITU-MPU` 和 `ITU-MCU`；希望将该仓库以目录 `ITU-MPU` 和 `ITU-MCU` 拆解为两个独立的新仓库 `ITU-MPU` 和 `ITU-MCU`；并能够将原本仓库中对于子目录`ITU-MPU` 中的提交历史迁入到新的独立仓库 `ITU-MPU` 中，对于子目录`ITU-MCU` 中的提交历史迁入到新的独立仓库 `ITU-MCU` 中，你可以按照以下步骤操作：



### 步骤 1: 生成 `ITU-MPU` 子目录对应的仓库

1. 拷贝仓库 `ITU-ALL` 的副本

```bash
git clone /d/Develop/Git/git-test/Test/ITU-ALL/.git ITU-MPU-sub
cd ITU-MPU-sub
```

2. 使用 `git filter-repo` 调整仓库副本

   仅保留 `ITU-MPU`目录下的内容，并让其成为新的根目录，执行如下命令：

```bash
git filter-repo --subdirectory-filter ITU-MPU/
# 或者
git filter-repo --force --subdirectory-filter ITU-MPU/
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

至此我们的git项目迁移就完成了，不仅将代码迁移到新的仓库，也同时将提交历史带去了新的仓库。这样一来，对于开发同学来说迁移完全是无痛的，只是切换了新的git地址，而开发过程完全不会中断。



### 步骤 2: 生成 `ITU-MCU` 子目录对应的仓库

1. 拷贝仓库 `ITU-ALL` 的副本

```bash
git clone /d/Develop/Git/git-test/Test/ITU-ALL/.git ITU-MPU-sub
cd ITU-MPU-sub
```

2. 使用 `git filter-repo` 调整仓库副本

   仅保留 `ITU-MPU`目录下的内容，并让其成为新的根目录，执行如下命令：

```bash
git filter-repo --subdirectory-filter ITU-MPU/
# 或者
git filter-repo --force --subdirectory-filter ITU-MPU/
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

至此我们的git项目迁移就完成了，不仅将代码迁移到新的仓库，也同时将提交历史带去了新的仓库。这样一来，对于开发同学来说迁移完全是无痛的，只是切换了新的git地址，而开发过程完全不会中断。



## 参考资料

[利用 git-filter-repo 无缝迁移 git 项目 - SegmentFault 思否](https://segmentfault.com/a/1190000041375416)

[git 仓库拆分工具 git-filter-repo-CSDN博客](https://blog.csdn.net/zhulianseu/article/details/129401551)