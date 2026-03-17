---
title: Git Workflow 随笔
date: 2025/12/15 18:40:00
categories: git
tags:
  - git
---

## 撤销一个已提交的 commit

回退 commit

```bash
git reset --soft HEAD^
```

如果已经 push 则需要 force

```bash
git push -f
```

### 执行 reset 后想回到 reset 之前的状态

最简单

```bash
git reset --soft HEAD@{1}
```

#### 保险方法: 查看 reflog 历史

```bash
git reflog

# 输出
a1b2c3d HEAD@{0}: reset: moving to HEAD^
e4f5g6h HEAD@{1}: commit: 你的提交信息
```

找到 reset 之前的提交 hash（通常是 HEAD@{1} 那一行），然后：

```bash
git reset --soft e4f5g6h
# 或者
git reset --soft HEAD@{1}
```

tip：
- git reflog 记录了所有 HEAD 移动的历史，包括 reset、checkout、commit 等操作
- HEAD@{1} 是引用日志中的相对引用，表示 HEAD 在 1 步之前的位置
- 使用 --soft 选项保持暂存区和工作区不变

## 在提交 PR 之前合并代码到最新的分支解决冲突

```
# 1. 切换到 dev 分支并拉取最新代码
git checkout dev
git pull origin dev

# 2. 切换到 feat 分支
git checkout feat

# 3. 将 feat 分支 rebase 到最新的 dev 分支
git rebase dev

# 4. 如果有冲突,解决冲突后继续
# git add <冲突文件>
# git rebase --continue

# 5. 强制推送到远程(如果之前已经推送过 feat 分支)
git push origin feat --force-with-lease
```

注意事项:
* --force-with-lease 比 --force 更安全,它会检查远程分支是否有其他人的提交
* 如果 rebase 过程中出现冲突,解决冲突后使用 git rebase --continue
* 如果想取消 rebase,使用 git rebase --abort

## 删除远程分支

```base
git push origin --delete <分支名>
```

简写

```base
git push origin :<分支名>
```

## 推送更新后更改用户名

修改用户名

```bash
# 修改全局配置
git config --global user.name "正确的用户名"
git config --global user.email "正确的邮箱"

# 或者只修改当前项目的配置
git config user.name "正确的用户名"
git config user.email "正确的邮箱"
```

更新历史提交

```bash
# git commit --amend --author="yx208 <yx208@example.com>"
git commit --amend --author="正确的用户名 <正确的邮箱>"
git push --force
```

修改多个提交

```bash
# 修改最近 3 个提交（根据实际情况调整数字）
git rebase -i HEAD~3

# 在打开的编辑器中，将需要修改的提交前的 pick 改为 edit
# 然后对每个标记为 edit 的提交执行：
git commit --amend --author="正确的用户名 <正确的邮箱>" --no-edit
git rebase --continue

# 最后强制推送
git push --force
```

example

```bash
# 1. 先查看当前的提交历史，确认哪些提交需要修改
git log --oneline -5
# 输出示例：
# abc1234 (HEAD -> main, origin/main) 修复了登录bug
# def5678 添加了用户注册功能
# ghi9012 更新README
# ...

# 2. 修改本地 Git 配置（避免以后再出错）
git config user.name "Zhang San"
git config user.email "zhangsan@example.com"

# 3. 开始交互式 rebase，修改最近 2 个提交
git rebase -i HEAD~2

# 4. 编辑器会打开，显示类似这样的内容：
# pick def5678 添加了用户注册功能
# pick abc1234 修复了登录bug
#
# 将前面的 pick 改为 edit：
# edit def5678 添加了用户注册功能
# edit abc1234 修复了登录bug
# 保存并关闭编辑器

# 5. 对第一个提交修改作者
git commit --amend --author="Zhang San <zhangsan@example.com>" --no-edit
git rebase --continue

# 6. 对第二个提交修改作者
git commit --amend --author="Zhang San <zhangsan@example.com>" --no-edit
git rebase --continue

# 7. 验证修改结果
git log -2 --pretty=format:"%h %an <%ae> %s"
# 应该显示：
# abc1234 Zhang San <zhangsan@example.com> 修复了登录bug
# def5678 Zhang San <zhangsan@example.com> 添加了用户注册功能

# 8. 强制推送到远程仓库
git push --force origin main
```

## 如何将分支上的所有 Commit 合并成一个

在日常开发中，我们往往会在一个功能分支上留下许多零碎的提交记录——修个 typo、调个样式、补个注释……这些 commit 在开发过程中无可厚非，但合并到主分支前，整理成一个干净的 commit 会让历史记录清晰许多。

### 方法一：`git rebase -i`

交互式 rebase 是最灵活的方式，可以精细控制每一个 commit 的去留。

```bash
# 基于主分支进行 rebase
git rebase -i main

# 或者指定某个具体的起点 commit
git rebase -i <base-commit-hash>
```

执行后会打开一个编辑器，列出所有 commit。把第一个保留为 `pick`，其余全部改成 `squash`（或简写 `s`）：

```
pick   abc1234 第一个 commit
squash def5678 第二个 commit
squash ghi9012 第三个 commit
```

保存退出后，Git 会再弹出一个编辑器，让你为合并后的 commit 编写新的提交信息。

---

### 方法二：`git reset` + 重新提交

思路更直接——先把所有 commit 撤回到暂存区，再一次性重新提交。

```bash
# 回退到分支起点，但保留所有文件改动
git reset <base-commit-hash>

# 重新一次性提交
git add .
git commit -m "合并后的 commit message"
```

适合分支 commit 数量较多、不想逐一处理的情况。

---

### 方法三：`git merge --squash`

在目标分支上操作，合并的同时压缩所有 commit，适合在合并入主分支时顺手整理。

```bash
# 切到主分支
git checkout main

# squash 合并功能分支（此步骤不会自动产生 commit）
git merge --squash your-branch

# 手动提交
git commit -m "合并后的 commit message"
```

---

### 强推远程分支

如果分支已经 push 到远程，squash 操作会改写历史，需要强制推送：

```bash
git push --force-with-lease
```
