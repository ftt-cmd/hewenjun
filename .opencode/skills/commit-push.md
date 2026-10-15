# Commit & Push

将当前项目提交并推送到 Gitee 远程仓库 https://gitee.com/hwjcqq/hewenjun114.git

## 步骤

1. 检查当前 git 状态
2. 暂存所有变更
3. 提交变更（commit message 使用中文描述）
4. 推送到远程仓库

## 执行命令

```powershell
# 1. 查看当前状态
git status

# 2. 暂存所有变更
git add .

# 3. 提交
git commit -m "更新项目文件"

# 4. 推送到远程
git push origin HEAD
```

## 注意事项

- 如果用户指定了 commit message，使用用户提供的
- 如果推送失败，检查远程仓库认证状态
- 推送前确认当前分支正确
