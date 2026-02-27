---
description: "🚀 智能创建MR - 自动分析变更并生成规范的分支和提交"
---

# 🚀 智能创建 Merge Request

分析代码变更，自动生成分支名和提交信息，创建MR。

## 📊 变更分析

### Git状态和分支信息
!`git status --porcelain && echo "--- Current Branch ---" && git branch --show-current && echo "--- Remote ---" && git remote -v | head -1`

### 变更概览
!`git diff --cached --stat 2>/dev/null || git diff --stat`

### 提交历史参考
!`git log --oneline -3 --format="%s"`

### 变更详情
!`git diff --cached --name-status 2>/dev/null || git diff --name-status | head -20`

### 关键变更内容
!`git diff --cached -U2 2>/dev/null || git diff -U2 | head -100`

### 未跟踪文件
!`git ls-files --others --exclude-standard | grep -E '\.(ts|js|json|md|yml|yaml)$' | head -5`

## 🎯 执行策略

### 分支命名规范
- `feature/tapd单/描述` - 新功能
- `fix/tapd单/描述` - 修复问题
- `refactor/tapd单/描述` - 重构
- `docs/tapd单/描述` - 文档
- `chore/tapd单/描述` - 构建/工具

### 提交信息规范
遵循 [Conventional Commits](https://www.conventionalcommits.org/)：
```
type(scope): description --story=tapd单

feat(agent-cli): 添加新命令 --story=tapd单
fix(agent-cli): 修复内存泄漏 --story=tapd单
docs(agent-cli): 更新安装说明 --story=tapd单
```

## 注意事项
1. **不要使用 gh CLI**
2. **首次代码推送后，命令日志里面会有一个创建 MR 的链接**
3. **依赖库信息、代码文件名、接口名、类名等敏感信息不要提醒在更新CHANGELOG.md中**

## 🚀 自动化流程
1. **执行构建验证**（确保代码质量）
2. **分析变更类型和范围**
3. **创建合适分支**（如在主分支）
4. **检查并更新 docs 目录的相关文档** !!重要
5. **更新CHANGELOG.md**（添加到未发布版本，避免透露代码细节）!!重要
6. **生成规范提交信息**
7. **暂存并所有提交变更（git add .）**
8. **推送到远程**
9. **open 命令打开MR页面，等待合并**

---
**开始智能MR创建...**
