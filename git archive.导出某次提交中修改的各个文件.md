# git 如何导出某个提交中的修改的各个文件



##### 问题：git 如何将某个提交中的修改的各个文件导出到某个目录中？

##### 回答:

要将某个提交中的修改的各个文件导出到某个目录中，你可以使用 `git archive` 命令结合 `git diff` 命令来实现。以下是一个基本的步骤：

1. **确定提交ID：**首先，确定你要导出的提交的提交ID（commit hash）。

2. **使用 `git diff` 查看修改：**运行 `git diff <commit_id>^ <commit_id> --name-only` 命令来查看在该提交中修改的文件列表。`<commit_id>` 是你要导出的提交的提交ID。这条命令将列出提交的父提交（`<commit_id>^`）和当前提交（`<commit_id>`）之间的文件变化。

3. **使用 `git archive` 导出文件：**运行 `git archive` 命令来将这些修改的文件导出到目标目录中。例如，运行以下命令：

   ```bash
   git archive <commit_id> --format=zip --output=export.zip $(git diff <commit_id>^ <commit_id> --name-only)
   ```

   这将创建一个名为 `export.zip` 的 zip 文件，其中包含在提交中发生的所有文件修改。

替换 `<commit_id>` 为你要导出的提交的提交ID。这个命令首先使用 `git diff` 列出在该提交中修改的文件列表，然后将这些文件使用 `git archive` 导出到一个 zip 文件中。

请注意，这个方法只导出了在某个提交中发生的修改，而没有包括该提交之前的文件状态。



##### 问题: 如何指定导出到某个目录中？而不是导出到压缩文件？

##### 回答：

如果你想直接将修改的文件导出到一个目录中而不是打包成压缩文件，你可以使用 `git archive` 命令的 `--output` 选项指定一个目录作为输出路径。

下面是一个示例命令：

```bash
git archive <commit_id> --output=/path/to/destination/directory/ $(git diff <commit_id>^ <commit_id> --name-only)
```

在这个命令中：

- `<commit_id>` 是你要导出的提交的提交ID。
- `--output=/path/to/destination/directory/` 选项指定了导出的目录。
- `$(git diff <commit_id>^ <commit_id> --name-only)` 用于获取在提交中修改的文件列表。

这个命令将会把修改的文件导出到指定的目录中。

##### 测试：

``` shell
$ git archive 7fe7722 --output=../itu-7fe772/ $(git diff 7fe7722~ 7fe7722 --name-only)
fatal: 不能创建归档文件 '../itu-7fe772/': 是一个目录
```

我使用你的方式，报错了?

##### 回答：

抱歉，看起来我有点错了。`git archive` 命令无法直接将文件导出到一个目录中，它要求输出必须是一个归档文件（例如 tar 文件或 zip 文件）。

下面是一个示例命令：

```
bashCopy Codegit archive <commit_id> --output=/path/to/destination/directory/export.tar $(git diff <commit_id>^ <commit_id> --name-only)
```

在这个命令中：

- `<commit_id>` 是你要导出的提交的提交ID。
- `--output=/path/to/destination/directory/export.tar` 选项指定了导出的目录和文件名，其中 `/path/to/destination/directory/` 是你要导出到的目录路径，`export.tar` 是输出的 tar 文件名。
- `$(git diff <commit_id>^ <commit_id> --name-only)` 用于获取在提交中修改的文件列表。

这个命令将会把修改的文件导出到指定的目录中的一个 tar 文件中。
