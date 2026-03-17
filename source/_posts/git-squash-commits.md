# Git：整理分支 Commit 的那些事

在日常开发中，我们往往会在功能分支上留下许多零碎的提交——修个 typo、调个样式、补个注释……这些 commit 在开发过程中无可厚非，但合并到主分支前，整理成干净的历史会让项目更易维护。

---

## 三种合并 Commit 的方法

### 方法一：`git rebase -i`（推荐）

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

### 方法二：`git reset` + 重新提交

思路更直接——先把所有 commit 撤回，再一次性重新提交。

```bash
git reset <base-commit-hash>
git add .
git commit -m "合并后的 commit message"
```

### 方法三：`git merge --squash`

在目标分支上操作，合并的同时压缩所有 commit。

```bash
git checkout main
git merge --squash your-branch
git commit -m "合并后的 commit message"
```

---

## `git rebase -i` 详解

### 唯一的硬性规则：第一行必须是 `pick`

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

### 每一行都可以自由移动

编辑器中的每一行都可以随意调整位置，Git 会**从上到下**按顺序重新应用这些 commit。不只是移动，你还可以删掉某行，或者把指令从 `pick` 改成其他任意指令。

### 常用指令速查

| 指令 | 简写 | 作用 |
|---|---|---|
| `pick` | `p` | 保留这个 commit |
| `squash` | `s` | 合并到上一个 commit，保留提交信息 |
| `fixup` | `f` | 合并到上一个 commit，丢弃提交信息 |
| `drop` | `d` | 删除这个 commit |
| `reword` | `r` | 保留 commit，但修改提交信息 |

### 关于 `drop`：文件改动也会一并丢弃

使用 `drop` 删除一个 commit 时，该 commit 引入的所有文件修改都会被丢弃。

需要注意的是，如果后续的 commit 依赖了被 drop 的改动，rebase 会产生**冲突**，需要手动解决。例如：

```
pick   abc1234 新增 foo() 函数
drop   def5678 在 foo() 里加了一行逻辑   ← 丢弃
pick   ghi9012 调用 foo()                 ← 依赖上面的改动，会产生冲突
```

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
