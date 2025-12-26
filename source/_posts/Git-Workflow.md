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
