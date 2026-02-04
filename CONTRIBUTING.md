# 贡献指南

感谢您对 Claude Code 插件市场的关注！我们欢迎所有形式的贡献。

## 🤝 如何贡献

### 贡献方式

- 🐛 **报告 Bug**：发现问题请提交 Issue
- 💡 **功能建议**：提出新插件想法或改进建议
- 📝 **文档改进**：完善文档和示例
- 🔧 **代码贡献**：开发新插件或改进现有插件
- ⭐ **Star 和分享**：帮助更多人发现这个项目

## 📋 贡献流程

### 1. Fork 项目

点击仓库右上角的 "Fork" 按钮，将项目 Fork 到你的账户。

### 2. 克隆仓库

```bash
git clone https://github.com/ChamHerry/claude-code-third-party-plugins.git
cd claude-code-third-party-plugins
```

### 3. 创建分支

```bash
# 新插件
git checkout -b plugin/your-plugin-name

# Bug 修复
git checkout -b fix/issue-description

# 功能改进
git checkout -b feature/feature-description
```

### 4. 开发和测试

#### 开发新插件

```bash
# 创建插件目录
mkdir -p plugins/your-plugin-name/.claude-plugin
mkdir -p plugins/your-plugin-name/commands

# 编写插件文件
# - .claude-plugin/plugin.json
# - commands/*.md
# - README.md
```

#### 测试插件

```bash
# 复制到 Claude Code 插件目录
cp -r plugins/your-plugin-name ~/.claude/plugins/

# 启动 Claude Code 测试
claude

# 测试插件命令
/your-command
```

### 5. 提交变更

```bash
git add .
git commit -m "feat: add your-plugin-name plugin"

# 提交信息格式：
# feat: 新功能
# fix: Bug 修复
# docs: 文档更新
# style: 代码格式调整
# refactor: 代码重构
# test: 测试相关
# chore: 构建/工具变更
```

### 6. 推送到 GitHub

```bash
git push origin plugin/your-plugin-name
```

### 7. 创建 Pull Request

1. 访问你的 Fork 仓库
2. 点击 "New Pull Request"
3. 填写 PR 描述（见下方模板）
4. 等待审核

## 📝 Pull Request 模板

```markdown
## 变更类型
- [ ] 新插件
- [ ] Bug 修复
- [ ] 功能改进
- [ ] 文档更新
- [ ] 其他

## 变更描述
简要描述这个 PR 做了什么...

## 插件信息（如果是新插件）
- **插件名称**: your-plugin-name
- **功能描述**: 简要说明插件功能
- **命令列表**: /command1, /command2

## 测试情况
- [ ] 已在本地测试
- [ ] 所有命令正常工作
- [ ] 文档已更新
- [ ] 通过代码审查

## 截图/示例
如有必要，添加截图或使用示例...

## 相关 Issue
Closes #issue_number
```

## 🔧 开发新插件

### 插件标准结构

```
your-plugin-name/
├── .claude-plugin/
│   └── plugin.json          # 必需：插件元数据
├── commands/                 # 可选：斜杠命令
│   ├── command1.md
│   └── command2.md
├── agents/                   # 可选：专用代理
├── hooks/                    # 可选：钩子脚本
├── docs/                     # 可选：详细文档
└── README.md                # 必需：插件文档
```

### plugin.json 示例

```json
{
  "name": "your-plugin-name",
  "description": "简要描述插件功能",
  "version": "1.0.0",
  "author": {
    "name": "Your Name",
    "email": "your.email@example.com"
  },
  "keywords": ["keyword1", "keyword2"],
  "license": "MIT"
}
```

### 命令文件格式

```markdown
---
allowed-tools: Read, Write, AskUserQuestion, Bash
description: 命令的简要描述
---

命令的详细说明和执行逻辑...

## 步骤 1: ...
## 步骤 2: ...
```

### README.md 必需内容

1. **功能介绍**：清晰说明插件做什么
2. **安装方法**：如何安装此插件
3. **使用方法**：命令列表和使用示例
4. **配置说明**：可选配置项
5. **常见问题**：FAQ 和故障排除
6. **示例**：实际使用场景

## ✅ 代码规范

### 通用规范

- 遵循 **KISS 原则**（Keep It Simple, Stupid）
- 代码简洁易读
- 提供充分的注释
- 异常处理完善
- 用户体验友好

### 命令规范

- ✅ 清晰的执行步骤
- ✅ 详细的错误提示
- ✅ 合理的默认值
- ✅ 交互式确认（重要操作）
- ✅ 执行结果反馈

### 文档规范

- ✅ 使用中文或英文（保持一致）
- ✅ 提供使用示例
- ✅ 包含截图（如有必要）
- ✅ 说明适用场景
- ✅ 列出已知限制

## 🧪 测试要求

### 功能测试

提交前请确保：

- [ ] 所有命令都能正常执行
- [ ] 边界情况处理正确
- [ ] 错误提示清晰友好
- [ ] 文件操作安全（备份、确认）
- [ ] 在不同项目类型中测试

### 文档测试

- [ ] README.md 示例可以运行
- [ ] 所有链接有效
- [ ] 截图清晰准确
- [ ] 安装步骤完整

## 📚 开发资源

### 官方文档

- [Claude Code 文档](https://docs.claude.com/en/docs/claude-code/overview)
- [插件系统文档](https://docs.claude.com/en/docs/claude-code/plugins)
- [官方插件示例](https://github.com/anthropics/claude-code/tree/main/plugins)

### 本项目参考

- [project-init 插件](./plugins/project-init/) - 完整的插件实现示例
- [插件开发方案文档](./docs/todo/) - 架构设计参考

### 工具推荐

- **测试**: 在独立项目中测试插件
- **调试**: 使用 Claude Code 的日志功能
- **验证**: 检查 plugin.json 格式

## 🎯 插件想法

### 急需插件

- **测试生成器**：自动生成单元测试
- **API 文档生成**：从代码生成 API 文档
- **依赖更新器**：自动检查和更新依赖
- **数据库迁移**：数据库版本管理
- **性能分析**：代码性能建议

### 提交想法

有新插件想法？[创建 Issue →](https://github.com/ChamHerry/claude-code-third-party-plugins/issues/new)

## ❓ 常见问题

### Q: 我不懂编程能贡献吗？

A: 当然！你可以：
- 报告 Bug
- 改进文档
- 提出功能建议
- 分享使用经验

### Q: 插件需要多复杂？

A: 从简单开始！一个解决实际问题的小插件比复杂但无用的大插件更有价值。

### Q: 如何获得帮助？

A:
- 查看现有插件代码
- 阅读官方文档
- 在 Discussions 提问
- 提交 Issue

### Q: PR 审核需要多久？

A: 通常 3-7 天。我们会仔细审查以确保质量。

## 📄 许可证

贡献的代码将采用 MIT 许可证。详见 [LICENSE](./LICENSE)。

## 📞 联系我们

- **Issues**: [提交问题](https://github.com/ChamHerry/claude-code-third-party-plugins/issues)
- **Discussions**: [参与讨论](https://github.com/ChamHerry/claude-code-third-party-plugins/discussions)
- **Email**: wangxuecheng@example.com

---

感谢你的贡献！每一个贡献都让这个项目变得更好。🎉
