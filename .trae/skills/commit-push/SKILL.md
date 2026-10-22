---
name: "commit-push"
description: "将当前项目提交并推送到 Gitee 远程仓库。当用户要求提交代码、推送项目或同步到远程仓库时调用。"
---

# Commit & Push

将当前项目提交并推送到 Gitee 远程仓库 https://gitee.com/hwjcqq/hewenjun114.git

## 何时使用

当用户提出以下需求时调用此 skill：
- 提交项目代码
- 推送项目到远程仓库
- 同步代码到 Gitee
- git commit 并 push

## 执行步骤

1. 检查当前 git 状态
2. 暂存所有变更
3. 提交变更（commit message 使用中文描述）
4. 推送到远程仓库（使用内嵌认证）

## 执行命令

```powershell
# 1. 查看当前状态
git status

# 2. 暂存所有变更
git add .

# 3. 提交
git commit -m "更新项目文件"

# 4. 推送到远程（认证信息内嵌URL）
git push https://18982681971:<token>@gitee.com/hwjcqq/hewenjun114.git master
```

## 注意事项

- Gitee 单文件不能超过 100MB，推送前需检查是否有超大文件
- 如果历史中曾提交过大文件，需使用 git filter-branch 或 BFG 清除
- 推送前确认当前分支正确
- 如果用户指定了 commit message，使用用户提供的
