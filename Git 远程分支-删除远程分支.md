# Git 如何删除远程分支



要删除远程分支，你可以按照以下步骤进行操作：

1. 首先，使用以下命令查看远程分支的列表：

   ```shell
   $ git branch -r
   ```

   找到你想要删除的远程分支，并记下它的完整名称（例如 `origin/branch-to-delete`）。

2. 使用以下命令删除远程分支：

   ```shell
   $ git push origin --delete branch-to-delete
   # 或者(注意, 冒号:之后不要有空格)
   $ git push origin :branch-to-delete
   ```

   这将删除名为 `branch-to-delete` 的远程分支。

请注意，删除远程分支是一个不可逆的操作，所以在执行之前，请确保你真的想要删除该分支，并且与团队成员进行了充分的沟通。

