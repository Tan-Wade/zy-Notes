# 🔹本地已有项目（未git init） → GitHub 是空仓库（empty repo）

```bash
# 1️⃣ 初始化 git 仓库
git init

# 2️⃣ 添加所有文件到暂存区
git add .

# 3️⃣ 提交第一次 commit
git commit -m "Initial commit"

# 4️⃣ 关联远程 GitHub 仓库（用你的 repo 地址）
git remote add origin https://github.com/你的用户名/你的仓库名.git

# 5️⃣ 推送到 GitHub（现在 GitHub 默认是 main 分支）
git branch -M main
git push -u origin main
```

注意：

- repo 一定要是空的

  - GitHub 上 **不要勾选** README / .gitignore / license，不然第一次 push 会报 rejected（历史不一致）
- 使用`HTTPS`方式上传代码
- 确保用户有当前repo的读写权限（网页上设置）
- `git init`: 把当前目录变成一个 Git 仓库
- `git remote add origin ...`: 绑定 GitHub 上的空 repo
- `git branch -M main`: 把本地分支名统一为 main（避免 master/main 问题）
- `git push -u origin main`: 推送到 GitHub，并设置默认 upstream

# 🔹 推送本地修改到远程分支

步骤：先提交，再推送。

```bash
# 查看当前分支
git branch

# 查看当前分支与当前分支状态
git status

# 添加修改到本地，.代表所有修改，也可以只添加指定的文件
git add .

# 提交修改到本地，如果不带-m会进入文本编辑器
git commit -m "your commit message here"

# 推送到远程对应分支
git push origin

# 推送到远程指定分支
git push origin Zeyuan_Fusion_Model_Delpoyment
```

- **git add**：把文件的更改放到 Git 的“暂存区”。
- . 表示当前目录下的所有文件。
- **git commit**：真正把文件保存到本地 Git 仓库（不是 GitHub）。
- **-m “…”**：给这次提交一个备注，比如“第一次提交”。

# 🔹 合并分支

**把 Zeyuan_Fusion_Model_Delpoyment分支合并进 main**

```bash
# 先切到 main
git checkout main

# 拉取远程 main，保持最新
git pull origin main

# 合并 Zeyuan_Fusion_Model_Delpoyment 到 main
git merge zeyuan-backend-clean

# 推送更新后的 main
git push origin main

# 快速指令
git checkout main
git pull origin main
git merge zeyuan-backend-clean
git push origin main
```

**把  main的最新修改合并到你的分支**

你想让 你的分支保持和 main 一致（同步 main 的改动）：

```bash
# 确认在本地某个分支
git checkout zeyuan-branch
# 拉取远程 main 的更新并合并到当前分支
git pull origin main
```

`git pull origin main` 等价于 `git fetch origin main` + `git merge origin/main`

如果你想避免额外的 merge commit，可以用 **rebase**：

```bash
git checkout zeyuan-branch
git fetch origin
git rebase origin/main
# 如果这个分支之前已经 push 过，rebase 改写了历史，需要安全强推：
git push --force-with-lease origin zeyuan-branch
```

# 🔹 新建本地和远端分支new-branch

```bash
git fetch origin

# 在本地基于main分支创建新分支
git checkout -b version-0.1 origin/main

# 也可从远端 version-0.1.0 创建并切换到新分支
# git checkout -b zeyuan-orderPage origin/version-0.1.0

# 推送到远端并建立追踪关系，如果远端已有，则不需要下面的命令
git push -u origin version-0.1
```

- **git checkout -b <新分支名字>**：创建并立即切换到新分支。

# 🔹 删除本地和远端分支 BRANCH

确保当前不在要删除的分支上，例如先切到 main：

```bash
git checkout main
```

```bash
# 删除本地分支
git branch -d BRANCH

# 如果本地没合并且报错，可以强删
# git branch -D zeyuan-order-page

# 删除远端分支
git push origin --delete BRANCH
```

#  🔹丢弃所有未暂存的修改（git add .之前，或回到上次 commit 状态）

```bash
# 1️⃣ 丢弃所有未暂存的修改（回到上次 commit 状态）
git restore .

# 2️⃣ 如果有新增的未追踪文件（例如新建但没 add），也一起删除
git clean -fd
```

执行后：

- 所有文件恢复为上次提交的版本
- 未追踪的新文件（未 add）会被删除
- 不影响分支，不影响远端

# 🔹 Commit message

- **`feat`** (Feature): 新增功能。
- **`fix`** (Bug Fix): 修复 Bug。
- **`docs`** (Documentation): 文档变动。
- **`style`** (Style): 代码格式、空格、标点等不影响逻辑的变动。
- **`refactor`** (Refactor): 重构代码，既不是新功能也不是修复 Bug。
- **`perf`** (Performance): 性能优化。
- **`test`** (Test): 添加或修改测试。
- **`build`** (Build): 构建系统或外部依赖项的变更。
- **`ci`** (Continuous Integration): CI/CD 配置文件或脚本的变动。
- **`chore`** (Chore): 构建过程或辅助工具的变动，如更新依赖。
- **`revert`** (Revert): 回滚先前的提交。 

# 🔹 在错误的分支上改动，需要新建一个分支并将改动转移到新分支上

## 还没有 git add .

```bash
git switch -c 新分支名
git add .
git commit -m ""
git push -u origin 新分支名
```

## 已经git add . 但还没有git commit

```bash
git switch -c 新分支名
git commit -m ""
git push -u origin 新分支名
```







# 🔹 检查本地git身份

```bash
git config --global --list

git config --global user.name
git config --global user.email
```


