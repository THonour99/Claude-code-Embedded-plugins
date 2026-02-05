# Claude Code 嵌入式开发插件集

<div align="center">

**专为 STM32/FreeRTOS/裸机 嵌入式开发打造的 Claude Code 插件**

[![Plugins](https://img.shields.io/badge/plugins-1-blue.svg)](./plugins)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](./LICENSE)
[![Claude Code](https://img.shields.io/badge/Claude_Code-compatible-purple.svg)](https://claude.ai/code)
[![Embedded](https://img.shields.io/badge/Embedded-STM32%20%7C%20FreeRTOS-orange.svg)](./plugins/project-init)

[插件列表](#-插件列表) • [安装方法](#-安装方法) • [开发指南](#️-开发插件) • [贡献](#-贡献)

</div>

---

## 📦 项目简介

嵌入式开发专用 Claude Code 插件集合，通过智能分析项目结构和配置文件，自动推断芯片型号、RTOS、外设配置等信息，帮助嵌入式开发者快速建立规范化的开发流程。

### 核心特性

- ✅ **智能分析**：自动检测 MCU 型号、RTOS、IDE 配置
- ✅ **多平台支持**：Keil MDK / IAR / CMake + GCC / STM32CubeMX
- ✅ **分层架构**：Service → Driver → Device → BSP → Arch
- ✅ **嵌入式规范**：中断安全、内存管理、代码风格规范
- ✅ **跨平台**：Windows / macOS / Linux 完全兼容

## 🎯 插件列表

### [project-init](./plugins/project-init/)（嵌入式版）

**嵌入式项目规范初始化插件**

智能分析嵌入式项目结构和配置文件，自动推断芯片型号、RTOS、外设配置等信息，生成定制化的 CLAUDE.md 开发规范文件。

- **命令**: `/project-init` - 智能分析并初始化项目 CLAUDE.md 规范
- **支持的 IDE**:
  - Keil MDK（`*.uvprojx`, `*.uvproj`）
  - IAR（`*.ewp`, `*.eww`）
  - CMake + GCC（`CMakeLists.txt`）
  - STM32CubeMX（`*.ioc`）
- **支持的 RTOS**: FreeRTOS、RT-Thread、裸机系统
- **特性**:
  - 自动检测 MCU 型号、编译器版本、外设配置
  - 检测分层架构（Service/Driver/Device/BSP）
  - 只对无法推断的信息询问用户
  - 自动备份现有配置
- **适用场景**: STM32 项目初始化、嵌入式团队规范标准化、飞控/工控等复杂项目

## 📥 安装方法

### 方式一：交互式安装（最简单）

在 Claude Code 会话中执行：

```bash
# 1. 添加插件市场
/plugin marketplace add https://github.com/THonour99/Claude-code-Embedded-plugins

# 2. 打开插件管理界面
/plugin

# 3. 选择 "Browse Plugins"，然后找到并安装 project-init
```

### 方式二：命令行安装

在 Claude Code 会话中执行：

```bash
# 1. 添加插件市场
/plugin marketplace add https://github.com/THonour99/Claude-code-Embedded-plugins

# 2. 直接安装插件（需要指定市场名称）
/plugin install project-init@embedded-dev-plugins
```

> **注意**：使用命令行安装时，必须指定 `@marketplace-name` 来明确插件来源。

## 🚀 使用插件

安装后，在 Claude Code 中即可使用插件命令：

```bash
# 启动 Claude Code
claude

# 使用插件命令（以 project-init 为例）
/project-init
```

### 📦 插件管理命令

在 Claude Code 会话中使用以下命令：

```bash
# 打开插件管理界面（可视化管理）
/plugin

# 浏览可用插件
/plugin
# 然后选择 "Browse Plugins"

# 管理已安装的插件
/plugin
# 然后选择 "Manage Plugins"

# 卸载插件
/plugin uninstall project-init@embedded-dev-plugins

# 禁用插件（不删除）
/plugin disable project-init@embedded-dev-plugins

# 启用已禁用的插件
/plugin enable project-init@embedded-dev-plugins

# 查看所有可用命令（包括插件命令）
/help
```

## 📖 插件文档

每个插件都包含详细的 README.md 文档，包括：

- 功能介绍和特性说明
- 详细的使用教程
- 配置选项说明
- 常见问题解答
- 使用场景示例

查看具体插件文档：[plugins/](./plugins/)

## 🛠️ 开发插件

### 插件标准结构

```
plugin-name/
├── .claude-plugin/
│   └── plugin.json          # 插件元数据（必需）
├── commands/                 # 斜杠命令（可选）
│   └── command-name.md
├── agents/                   # 专用代理（可选）
├── hooks/                    # 钩子脚本（可选）
└── README.md                # 插件文档（必需）
```

### plugin.json 配置示例

```json
{
  "name": "my-plugin",
  "description": "插件功能描述",
  "version": "1.0.0",
  "author": {
    "name": "Your Name",
    "email": "your.email@example.com"
  }
}
```

### 命令文件格式

```markdown
---
allowed-tools: Read, Write, Bash
description: 命令描述
---

命令执行逻辑...
```

### 开发步骤

1. **创建插件目录结构**
   ```bash
   mkdir -p my-plugin/.claude-plugin
   mkdir -p my-plugin/commands
   ```

2. **编写 plugin.json**
   - 定义插件元数据
   - 指定版本和作者信息

3. **实现命令逻辑**
   - 在 `commands/` 目录创建 `.md` 文件
   - 定义 `allowed-tools` 和执行步骤

4. **编写文档**
   - 创建 README.md
   - 包含使用示例和配置说明

5. **测试插件**
   - 复制到 `~/.claude/plugins/`
   - 在 Claude Code 中测试命令

## 📚 参考资源

### 官方文档

- [Claude Code 官方文档](https://docs.claude.com/en/docs/claude-code/overview)
- [插件系统文档](https://docs.claude.com/en/docs/claude-code/plugins)
- [官方插件示例](https://github.com/anthropics/claude-code/tree/main/plugins)

### 最佳实践

- **遵循分层架构**：Service → Driver → Device → BSP
- **中断安全优先**：ISR 编写规则、临界区保护
- **内存管理规范**：DTCM/AXI_SRAM/DMA 缓冲区使用规则
- **数据优先原则**：优先使用项目实际数据，避免猜测
- **版本语义化**：使用 semver 管理版本

## 🤝 贡献

我们欢迎社区贡献新插件或改进现有插件！

### 贡献流程

1. **Fork 本仓库**

2. **创建插件分支**
   ```bash
   git checkout -b plugin/your-plugin-name
   ```

3. **开发插件**
   - 在 `plugins/` 目录下创建新插件
   - 遵循标准插件结构
   - 编写完整的 README.md

4. **测试验证**
   - 在本地测试插件功能
   - 确保所有场景正常工作

5. **提交 Pull Request**
   - 清晰描述插件功能
   - 提供使用示例截图
   - 说明测试情况

### 贡献指南

- ✅ 插件应解决嵌入式开发的实际问题
- ✅ 支持常见的嵌入式开发工具链
- ✅ 遵循现有插件的风格和分层架构
- ✅ 提供充分的测试和使用示例
- ❌ 避免重复造轮子
- ❌ 不引入不必要的外部依赖

## 📋 插件清单

| 插件名称 | 版本 | 描述 | 作者 |
|---------|------|------|------|
| [project-init](./plugins/project-init/) | v1.2.0 | 嵌入式项目规范初始化（STM32/FreeRTOS/裸机） | Tangshikai |

_更多嵌入式开发插件持续开发中..._

## 🎨 插件分类

### 🚀 项目初始化
- [project-init](./plugins/project-init/) - 嵌入式项目规范初始化

### 🔧 嵌入式工具
_开发中..._

### 🐛 调试辅助
_开发中..._

### ⚡ 性能分析
_开发中..._

### 📝 代码生成
_开发中..._

## 💡 插件规划

以下是计划开发的嵌入式专用插件：

- **embedded-debug-assistant**：HardFault/崩溃/栈溢出调试助手
- **embedded-perf-analyzer**：DMA优化/驱动性能/CPU占用分析
- **hal-check**：HAL库使用检查工具
- **rtos-analyze**：FreeRTOS/RT-Thread 任务分析
- **memory-map**：内存布局可视化分析

[提交想法 Issue →](https://github.com/THonour99/Claude-code-Embedded-plugins/issues/new)

## 📜 许可证

本项目采用 MIT 许可证。详见 [LICENSE](./LICENSE) 文件。

## 🔗 相关链接

- [Claude Code 官网](https://claude.ai/code)
- [Claude Code GitHub](https://github.com/anthropics/claude-code)
- [Claude API 文档](https://docs.anthropic.com/)
- [社区论坛](https://community.anthropic.com/)

## 📞 联系我们

- **Issues**: [提交问题](https://github.com/THonour99/Claude-code-Embedded-plugins/issues)
- **Discussions**: [参与讨论](https://github.com/THonour99/Claude-code-Embedded-plugins/discussions)
- **Email**: TKai.study@gmail.com

---

<div align="center">

**如果这个项目对你的嵌入式开发有帮助，请给我们一个 Star！**

Made with dedication by Tangshikai

</div>
