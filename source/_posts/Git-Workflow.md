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
