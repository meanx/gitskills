## 参考资料

Git 清除悬空提交(dangling commit)
https://www.codenong.com/18514659

如何从git存储库中删除所有悬空的提交
http://www.tekkie.ro/news/howto-remove-all-dangling-commits-from-your-git-repository/

git reflog expire --expire=now --all
git gc --prune=now



## 方法说明

如果你希望完全清除已经删除的提交及其相关数据，使它们不再占用存储空间，你可以使用 `git gc` 和 `git reflog expire` 等命令来进行垃圾收集和清理。这些步骤将帮助你清理那些不再需要的对象。以下是详细的步骤：

1. **确保没有引用到这些提交**： 确保你没有任何分支或标签指向那些你想要删除的提交。

2. **清除 reflog**： Git 会在 reflog 中保留所有的引用历史，因此你需要清理 reflog。

   ```shell
   git reflog expire --expire=now --all
   ```

3. **运行垃圾收集**： 使用 `git gc` 命令来执行垃圾收集，清除未引用的对象。

   ```shell
   git gc --prune=now --aggressive
   ```

   - `--prune=now`：表示立即删除所有不可达的对象。
   - `--aggressive`：会花费更多的时间和计算资源来优化仓库。

4. **检查结果**： 你可以使用以下命令查看当前仓库中的对象，以确认是否成功清理了不需要的数据：

   ```shell
   git count-objects -v
   ```

以下是完整的命令序列：

```shell
git reflog expire --expire=now --all
git gc --prune=now --aggressive
git count-objects -v
```

通过这些步骤，Git 应该会删除那些不再需要的对象，从而释放存储空间。请注意，这些操作会永久删除相关数据，因此一定要确认这些数据确实不需要，并且没有其他引用指向这些提交。