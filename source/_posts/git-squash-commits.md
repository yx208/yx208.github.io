---
title: Git：整理分支 Commit 的那些事
date: 2026/03/17 18:40:00
categories: git
tags:
  - git
---

在日常开发中，我们往往会在功能分支上留下许多零碎的提交——修个 typo、调个样式、补个注释……这些 commit 在开发过程中无可厚非，但合并到主分支前，整理成干净的历史会让项目更易维护。

## 方法一：`git rebase -i`

交互式 rebase 是最灵活的方式，可以精细控制每一个 commit 的去留。

```bash
# 基于主分支进行 rebase
git rebase -i main

# 或者指定某个具体的起点 commit
git rebase -i <base-commit-hash>
```

执行后会打开一个编辑器，列出所有 commit。把第一个保留为 `pick`，其余全部改成 `squash`：

```
pick   abc1234 第一个 commit
squash def5678 第二个 commit
squash ghi9012 第三个 commit
```

保存退出后，Git 会再弹出一个编辑器，让你为合并后的 commit 编写新的提交信息。

## 方法二：`git reset` + 重新提交

思路更直接——先把所有 commit 撤回，再一次性重新提交。

```bash
git reset <base-commit-hash>
git add .
git commit -m "合并后的 commit message"
```

## 方法三：`git merge --squash`

在目标分支上操作，合并的同时压缩所有 commit。

```bash
git checkout main
git merge --squash your-branch
git commit -m "合并后的 commit message"
```

---

## `git rebase -i` 详解

## 唯一的硬性规则：第一行必须是 `pick`

`squash` 的含义是"合并到上一个 commit"，第一行没有上一个，所以**第一行必须是 `pick`**，否则会报错：

```
error: cannot 'squash' without a previous commit
```

正确写法：

```
pick   abc1234 第一个 commit   ← 作为基础，不能是 squash
squash def5678 第二个 commit
squash ghi9012 第三个 commit
```

## 编辑器中可以做的三件事

打开编辑器后，你可以：

1. **改指令**（第一列）— 把 `pick` 改成 `squash`、`reword`、`drop` 等
2. **调整行的顺序** — 上下移动整行，改变 commit 的应用顺序
3. **删掉整行** — 等同于 `drop`，那个 commit 会被丢弃

需要注意的是，**第二列的 hash 和第三列的 message 改了没有任何效果**，它们只是给你看的参考信息，Git 不会读取它们。

## 排列顺序：上旧下新

编辑器中的 commit 顺序是**从上到下，从旧到新**，与 `git log` 默认显示的顺序相反：

```
pick   abc1234 最早的 commit
pick   def5678 中间的 commit
pick   ghi9012 最新的 commit   ← 最下面
```

## 常用指令速查

| 指令 | 简写 | 作用 |
|---|---|---|
| `pick` | `p` | 保留这个 commit |
| `squash` | `s` | 合并到上一个 commit，保留提交信息 |
| `fixup` | `f` | 合并到上一个 commit，丢弃提交信息 |
| `drop` | `d` | 删除这个 commit |
| `reword` | `r` | 保留 commit，但修改提交信息 |

`squash` 和 `fixup` 的区别只有一点：`squash` 会弹出编辑器让你合并编辑提交信息，`fixup` 则直接丢弃那条 message，不打断流程。文件改动两者都会保留并合并到上一个 commit。

`reword` 则相反——文件改动完全不动，只是在 Git 执行到那个 commit 时暂停，弹出编辑器让你修改提交信息，写完后继续往下执行。

## 关于 `drop`：文件改动也会一并丢弃

使用 `drop` 删除一个 commit 时，该 commit 引入的所有文件修改都会被丢弃。

如果后续的 commit 依赖了被 drop 的改动，rebase 会产生**冲突**。原因是 Git 逐个应用 commit 时，每个 commit 本质上是一个 diff 补丁，后面的补丁期望前面的改动已经存在，drop 掉中间某个 commit，地基塌了，后面的补丁自然就对不上了。

```
pick   abc1234 新增 foo() 函数
drop   def5678 在 foo() 里第 10 行加了一行逻辑   ← 丢弃
pick   ghi9012 在 foo() 里第 11 行又加了一行      ← 期望第 10 行存在，会产生冲突
```

如果 drop 的是一段完全独立、没有任何后续 commit 依赖的代码，则不会有冲突。

## 冲突时让后面的 commit 覆盖前面

遇到冲突时，可以用 `-X theirs` 策略，让 rebase 自动选择当前正在应用的 commit（即后面的那个）：

```bash
git rebase -i -X theirs main
```

或者在冲突发生后手动处理：

```bash
git checkout --theirs <file>
git add <file>
git rebase --continue
```

注意 rebase 时 `ours` / `theirs` 的含义与直觉**相反**：

| | 含义 |
|---|---|
| `--ours` | rebase 的目标基底（如 main） |
| `--theirs` | 你正在重放的 commit（功能分支） |

所以想让功能分支的改动覆盖，用的是 `--theirs`。

---

## 遇到问题时的补救指令

如果 rebase 过程中出错，可以重新编辑 todo 列表：

```bash
git rebase --edit-todo   # 重新打开编辑器修改指令
git rebase --continue    # 修改完后继续执行
```

或者直接放弃，回到 rebase 之前的状态：

```bash
git rebase --abort
```

---

## ⚠️ 注意：强推远程分支

rebase 会改写历史，如果分支已经 push 到远程，需要强制推送：

```bash
git push --force-with-lease
```

建议使用 `--force-with-lease` 而非 `--force`——前者会在远程有新提交时主动报错，避免覆盖别人的工作，更加安全。
