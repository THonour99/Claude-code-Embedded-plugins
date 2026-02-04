# Claude Code 嵌入式开发插件

本目录包含针对嵌入式开发（STM32/FreeRTOS/裸机）场景优化的 Claude Code 插件集合。

## 什么是 Claude Code 插件？

Claude Code 插件是通过自定义斜杠命令、专用代理、钩子和 MCP 服务器来增强 Claude Code 的扩展模块。插件可以在项目和团队之间共享，提供一致的工具和工作流程。

了解更多：[官方插件文档](https://docs.claude.com/en/docs/claude-code/plugins)

## 本目录中的插件

### [project-init](./project-init/)

嵌入式项目规范初始化插件

智能分析嵌入式项目结构和配置文件，自动推断芯片型号、RTOS、外设配置等信息，生成定制化的 CLAUDE.md 开发规范文件。

- **命令**: `/project-init` - 智能分析嵌入式项目并生成 CLAUDE.md
- **特性**:
  - 自动检测 MCU 型号、RTOS、构建工具（Keil MDK/IAR/CMake）
  - 自动推断分层架构（Service/Driver/Device/BSP/Arch）
  - 检测 STM32CubeMX 配置（.ioc 文件）
  - 内置嵌入式专用模板（中断安全、内存管理、DMA 规范）
  - 按需询问，优先使用项目实际数据
  - 完全跨平台支持（Windows/macOS/Linux）
- **适用场景**: STM32 飞控开发、传感器驱动开发、RTOS 应用开发

## 安装

这些插件已包含在本仓库中。要在您的项目中使用它们：

### 方式一：全局安装

```bash
/plugin marketplace add https://github.com/THonour99/Claude-code-Embedded-plugins

```


## 使用插件

安装后，在 Claude Code 中使用插件命令：

```bash
# 启动 Claude Code
claude

# 初始化嵌入式项目规范
/project-init
```

详细使用方法请查看各插件的 README.md 文档。

## 插件结构

每个插件遵循标准的 Claude Code 插件结构：

```text
plugin-name/
├── .claude-plugin/
│   └── plugin.json          # 插件元数据
├── commands/                 # 斜杠命令
│   └── command-name.md
├── agents/                   # 专用代理（可选）
├── hooks/                    # 钩子脚本（可选）
└── README.md                # 插件文档
```

## 贡献

欢迎为本目录添加新的嵌入式开发相关插件！

## 了解更多

- [Claude Code 文档](https://docs.claude.com/en/docs/claude-code/overview)
- [插件系统文档](https://docs.claude.com/en/docs/claude-code/plugins)

---

作者: Tangshikai | <TKai.study@gmail.com>


