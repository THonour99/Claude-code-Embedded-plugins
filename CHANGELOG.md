# 更新日志

本文档记录项目的所有重要变更。

格式遵循 [Keep a Changelog](https://keepachangelog.com/zh-CN/1.0.0/)，
版本号遵循 [语义化版本](https://semver.org/lang/zh-CN/)。

## [未发布]

### 计划中
- embedded-debug-assistant：HardFault/崩溃/栈溢出调试助手
- embedded-perf-analyzer：DMA优化/驱动性能/CPU占用分析
- hal-check：HAL库使用检查工具
- rtos-analyze：FreeRTOS/RT-Thread 任务分析
- memory-map：内存布局可视化分析

## [1.2.0] - 2025-02

### 新增
- 🔧 **project-init 嵌入式专用版本**
  - 针对 STM32/FreeRTOS/裸机场景优化
  - 支持 Keil MDK（*.uvprojx）项目检测，提取 MCU 型号、编译宏
  - 支持 IAR（*.ewp）项目检测
  - 支持 CMake + GCC 项目检测
  - 支持 STM32CubeMX（*.ioc）配置检测
  - 新增 RTOS 自动检测（FreeRTOS/RT-Thread/裸机）
  - 新增分层架构检测（Service/Driver/Device/BSP）
  - 新增层职责边界规范
  - 新增中断安全、内存管理规范
  - 新增完整数据使用链追踪要求
  - 集成嵌入式调试/性能分析 Skills 映射

### 变更
- 项目定位从通用插件市场调整为嵌入式开发专用插件集
- marketplace 名称更新为 embedded-dev-plugins

### 移除
- 移除 Monorepo 支持（v1.1.0 功能）
- 移除通用技术栈模板（Go/Python/TypeScript/Java）

## [1.0.0] - 2025-11-03

### 新增
- 🎉 项目初始发布
- ✨ **project-init** 插件
  - 交互式项目规范初始化
  - 基于 CLAUDE_TEMPLATE.md 模板
  - 自动备份机制
- 📦 插件市场基础架构
  - 标准化插件目录结构
  - 插件安装文档
  - 开发指南
- 📚 完整的项目文档

### 文档
- 详细的安装和使用指南
- 插件开发最佳实践
- 贡献指南

---

## 版本说明

### 版本号格式：主版本号.次版本号.修订号

- **主版本号**：不兼容的 API 修改
- **次版本号**：向后兼容的功能性新增
- **修订号**：向后兼容的问题修正

### 变更类型

- **新增**：新功能
- **变更**：对现有功能的变更
- **弃用**：即将删除的功能
- **移除**：已删除的功能
- **修复**：任何 bug 修复
- **安全**：修复安全问题

---

[未发布]: https://github.com/THonour99/Claude-code-Embedded-plugins/compare/v1.2.0...HEAD
[1.2.0]: https://github.com/THonour99/Claude-code-Embedded-plugins/compare/v1.0.0...v1.2.0
[1.0.0]: https://github.com/THonour99/Claude-code-Embedded-plugins/releases/tag/v1.0.0
