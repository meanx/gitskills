# 利用 git-filter-repo 将两个仓库合并为一个仓库



## 背景

我有个两个代码仓库 `ITU-MPU` 和 `ITU-MCU`，各自包含 MPU 和 MCU 的代码提交； 现在，我想创建一个新的仓库 `ITU`，然后在仓库 `ITU` 中分别创建子目录 `ITU-MPU` 和 `ITU-MCU`； 希望将原本的两个仓库 `ITU-MPU` 和 `ITU-MCU` 中的代码提交迁移到新仓库 `ITU` 中的子目录 `ITU-MPU` 和 `ITU-MCU` 中， 并且保留原本仓库中的提交记录，我应该如何迁移了？



## 方式一: 测试不可行(目录结构非预期)

为了将两个现有的 Git 仓库 (`ITU-MPU` 和 `ITU-MCU`) 的提交历史迁移到一个新的 Git 仓库 (`ITU`)，并将它们分别放在新的仓库的子目录中 (`ITU-MPU` 和 `ITU-MCU`)，你可以按照以下步骤操作：

### 步骤 1: 创建新的 Git 仓库

首先，你需要创建一个新的 Git 仓库 `ITU`，并在本地克隆它：

```bash
mkdir ITU
cd ITU
git init
```

### 步骤 2: 迁移 `ITU-MPU` 仓库到子目录

1. **添加远程仓库**：
   在 `ITU` 仓库中，添加 `ITU-MPU` 仓库作为远程仓库：

   ```bash
   git remote add itumpu D:/Develop/Git/git-test/Test/ITU-MPU/.git
   ```

2. **获取 `ITU-MPU` 仓库的提交记录**：

   ```bash
   git fetch itumpu
   ```

3. **创建并切换到一个新的分支**：

   ```bash
   git checkout -b itumpu-import itumpu/develop
   ```

4. **使用 `git filter-repo` 迁移到子目录**：

   安装 `git filter-repo`（如果还未安装）：
   
   ```bash
   pip install git-filter-repo
   ```
   
   然后运行：

   ```bash
   git filter-repo --path-rename :ITU-MPU/
   ```
   
5. **将更改提交到 `ITU` 仓库**：

   ```bash
   git checkout master
   git merge itumpu-import --allow-unrelated-histories
   ```

6. **删除临时分支和远程**：

   ```bash
   git branch -d itumpu-import
   git remote remove itumpu
   ```

### 步骤 3: 迁移 `ITU-MCU` 仓库到子目录

1. **添加 `ITU-MCU` 仓库作为远程仓库**：

   ```bash
   git remote add itumcu /path/to/ITU-MCU
   ```

2. **获取 `ITU-MCU` 仓库的提交记录**：

   ```bash
   git fetch itumcu
   ```

3. **创建并切换到一个新的分支**：

   ```bash
   git checkout -b itumcu-import itumcu/develop
   ```

4. **使用 `git filter-repo` 迁移到子目录**：

   **使用 `git filter-repo`**：

   ```bash
   git filter-repo --path-rename :/ITU-MCU/
   ```

5. **将更改提交到 `ITU` 仓库**：

   ```bash
   git checkout master
   git merge itumcu-import --allow-unrelated-histories
   ```

6. **删除临时分支和远程**：

   ```bash
   git branch -d itumcu-import
   git remote remove itumcu
   ```

### 步骤 4: 清理和推送

1. **清理工作目录**（如果需要的话，删除旧的文件和分支）：

   ```bash
   git gc
   ```

2. **推送到远程 `ITU` 仓库**：

   ```bash
   git remote add origin /path/to/ITU
   git push -u origin main
   ```

这样，你就完成了将 `ITU-MPU` 和 `ITU-MCU` 的提交记录迁移到新的 `ITU` 仓库，并且将它们放在了子目录中的操作。



## 方式二: 测试通过

为了将两个现有的 Git 仓库 (`ITU-MPU` 和 `ITU-MCU`) 的提交历史迁移到一个新的 Git 仓库 (`ITU`)，并将它们分别放在新的仓库的子目录中 (`ITU-MPU` 和 `ITU-MCU`)，你可以按照以下步骤操作：

### 步骤 1: 创建新的 Git 仓库

首先，你需要创建一个新的 Git 仓库 `ITU`，并在本地克隆它：

```bash
mkdir ITU
cd ITU
git init
```

### 步骤 2: 迁移 `ITU-MPU` 仓库到子目录

0. **准备要迁入的仓库**
    将仓库 `ITU-MPU` 的本地仓库复制一份（或者重新 clone 一份）：

    ```bash
    git clone D:/Develop/Git/git-test/Test/ITU-MPU/.git ITU-MPU-TODO
    cd ./ITU-MPU-TODO
    ```

    **使用 `git filter-repo` 迁移到子目录**：

    安装 `git filter-repo`（如果还未安装）：

    ```bash
    pip install git-filter-repo
    ```

    然后在 `ITU-MPU-TODO` 仓库中运行：

    ```bash
    git filter-repo --path-rename :ITU-MPU/
    # 或者
    git filter-repo --force --path-rename :ITU-MPU/
    ```

    执行之后，可以发现在 `ITU-MPU-TOODO` 仓库的根目录中创建了子目录 `ITU-MPU` ，并且将仓库中的所有文件移入该目录中，并且保留了所有的历史记录；
    
    这样，就完成了要迁入的 `ITU-MPU` 仓库的准备工作（即使用 `ITU-MPU-TODO` 仓库）;
    
1. **添加远程仓库**：
   在 `ITU` 仓库中，添加 `ITU-MPU-TODO` 仓库作为远程仓库：

   ```bash
   git remote add itumpu D:/Develop/Git/git-test/Test/ITU-MPU-TODO/.git
   ```

2. **获取 `ITU-MPU-TODO` 仓库的提交记录**：

   ```bash
   git fetch itumpu
   ```

3. **将更改合并到 `ITU` 仓库**：

   ```bash
   git merge itumpu/develop --allow-unrelated-histories
   ```

4. **移除临时的远程远程**：

   可根据需要决定是否移除远程仓库;

   ```bash
   git remote remove itumpu
   ```

### 步骤 3: 迁移 `ITU-MCU` 仓库到子目录

0. **准备要迁入的仓库**
    将仓库 `ITU-MCU` 的本地仓库复制一份（或者重新 clone 一份）：

    ```bash
    git clone D:/Develop/Git/git-test/Test/ITU-MCU/.git ITU-MCU-TODO
    cd ./ITU-MCU-TODO
    ```

    **使用 `git filter-repo` 迁移到子目录**：

    安装 `git filter-repo`（如果还未安装）：

    ```bash
    pip install git-filter-repo
    ```

    然后在 `ITU-MCU-TODO` 仓库中运行：

    ```bash
    git filter-repo --path-rename :ITU-MCU/
    # 或者
    git filter-repo --force --path-rename :ITU-MCU/
    ```

    执行之后，可以发现在 `ITU-MCU-TOODO` 仓库的根目录中创建了子目录 `ITU-MCU` ，并且将仓库中的所有文件移入该目录中，并且保留了所有的历史记录；
    
    这样，就完成了要迁入的 `ITU-MCU` 仓库的准备工作（即使用 `ITU-MCU-TODO` 仓库）;
    
1. **添加 `ITU-MCU-TODO` 仓库作为远程仓库**：

   ```bash
   git remote add itumcu D:/Develop/Git/git-test/Test/ITU-MCU-TODO/.git
   ```

2. **获取 `ITU-MCU-TODO` 仓库的提交记录**：

   ```bash
   git fetch itumcu
   ```

3. **将更改合并到 `ITU` 仓库**：

   ```bash
   git merge itumcu/develop --allow-unrelated-histories
   ```

4. **移除临时的远程远程**：

   可根据需要决定是否移除远程仓库;

   ```bash
   git remote remove itumcu
   ```

### 步骤 4: 清理和推送

1. **清理工作目录**（如果需要的话，删除旧的文件和分支）：

   ```bash
   git gc
   ```

2. **推送到远程 `ITU` 仓库**：

   ```bash
   git remote add origin /path/to/ITU
   git push -u origin main
   ```

这样，你就完成了将 `ITU-MPU` 和 `ITU-MCU` 的提交记录迁移到新的 `ITU` 仓库，并且将它们放在了子目录中的操作。