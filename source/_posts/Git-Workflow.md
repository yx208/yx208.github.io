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
