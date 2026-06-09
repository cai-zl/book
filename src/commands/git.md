# Git 常用命令

## 基础操作

```bash
# 克隆仓库
git clone <url>

# 查看状态
git status

# 添加文件到暂存区
git add <file>
git add .                    # 添加所有修改

# 提交
git commit -m "message"

# 拉取 / 推送
git pull
git push

# 查看提交历史
git log --oneline --graph -20
```

## 分支管理

```bash
# 创建并切换分支
git checkout -b <branch>

# 切换分支
git switch <branch>

# 查看所有分支
git branch -a

# 合并分支
git merge <branch>

# 删除本地分支
git branch -d <branch>

# 删除远程分支
git push origin --delete <branch>
```

## 撤销与回退

```bash
# 撤销工作区修改（未 add）
git checkout -- <file>
git restore <file>

# 撤销暂存（已 add，未 commit）
git reset HEAD <file>
git restore --staged <file>

# 回退到上一次提交（保留工作区修改）
git reset --soft HEAD~1

# 回退到上一次提交（丢弃所有修改）
git reset --hard HEAD~1

# 修改上次提交信息
git commit --amend -m "new message"
```

## 文件回滚

```bash
# 回滚单个文件到指定提交版本
git checkout <commit> -- <file>
git restore --source=<commit> <file>

# 示例：回滚 config.yaml 到上一次提交
git restore --source=HEAD~1 config.yaml

# 回滚文件到某个历史版本（不自动提交）
git show <commit>:<file> > <file>
```

## 暂存工作区

```bash
# 暂存当前修改
git stash

# 查看暂存列表
git stash list

# 恢复最近暂存
git stash pop

# 恢复指定暂存
git stash apply stash@{0}
```

## 标签

```bash
# 创建标签
git tag v1.0.0
git tag -a v1.0.0 -m "release 1.0"

# 推送标签
git push origin v1.0.0
git push origin --tags

# 删除标签
git tag -d v1.0.0
git push origin --delete v1.0.0
```

## 远程仓库

```bash
# 查看远程仓库
git remote -v

# 添加远程仓库
git remote add origin <url>

# 设置上游分支
git push -u origin <branch>
```

## 冲突处理

```bash
# 查看冲突文件
git diff --name-only --diff-filter=U

# 使用某一方版本
git checkout --theirs <file>    # 使用对方版本
git checkout --ours <file>      # 使用自己版本
```
