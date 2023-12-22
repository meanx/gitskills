# Git 如何修改远程分支名称



要修改远程分支的名称，你需要执行以下步骤：

1. 首先，使用以下命令查看远程分支的列表：

   ```shell
   $ git branch -r
   ```

   找到你想要修改名称的远程分支，并记下它的完整名称（例如 `origin/old-branch`）。

2. 使用以下命令将本地分支重命名为新的名称：

   ```shell
   $ git branch -m old-branch new-branch
   ```

   这将把本地分支 `old-branch` 重命名为 `new-branch`。

3. 接下来，使用以下命令将重命名后的本地分支推送到远程仓库：

   ```shell
   $ git push origin :old-branch new-branch
   ```

   这将删除远程的 `old-branch` 分支，并将本地的 `new-branch` 分支推送到远程仓库。

4. 最后，使用以下命令将远程仓库中的分支跟踪关联到新的本地分支：

   ```shell
   $ git branch -u origin/new-branch new-branch
   ```

   这将将远程仓库的 `new-branch` 分支与本地的 `new-branch` 分支进行关联。

请确保在执行这些操作之前，你已经备份了重要的代码，并且与团队成员进行了充分的沟通。

