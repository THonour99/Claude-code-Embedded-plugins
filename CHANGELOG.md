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

## [2.0.0] - 2025-02

### 新增
- 🔄 **project-init 架构重构**
  - 从"预设模板填充"模式改为"智能检测+动态生成"模式
  - 完全基于项目检测结果生成 CLAUDE.md，无硬编码预设

- 🎯 **零预设原则**
  - 移除硬编码命名前缀（`ST_`/`EM_`/`RY_`）
  - 移除预设目录结构（`platform/service/` 等）
  - 移除预设接口模式（`Drv_XXX`/`Srv_XXX`）
  - 移除预设内存区域（DTCM/AXI_SRAM）
  - 移除预设配置文件名（`board_pins.h` 等）

- 📊 **增量更新机制**
  - 支持 `AUTO-GENERATED` 和 `USER-DEFINED` 区域标记
  - 重新运行时保留用户自定义内容
  - 支持旧格式迁移到新格式

- ✅ **生成后验证**
  - 验证所有引用的文件路径是否存在
  - 验证 MCU 对应的 HAL 头文件是否存在
  - 验证 Skills 引用是否有效
  - 输出验证报告

- 🛡️ **防幻觉机制强化**
  - 新增嵌入式专用验证清单（寄存器/时钟/DMA/中断/GPIO）
  - 细化每类操作的验证步骤

- 🔍 **检测能力增强**
  - 新增 STM32CubeIDE (.cproject) 支持
  - 新增 ThreadX (Azure RTOS) 检测
  - 新增中间件检测（LWIP/FatFS/USB/mbedTLS/FreeModbus/CAN）
  - 新增链接脚本解析（内存区域、栈堆大小）
  - 新增代码风格检测（命名模式提取）
  - 增强 Keil MDK 解析（编译器版本 V5/V6 判断、多目标支持）
  - 增强 FreeRTOS 配置提取（堆大小、优先级数量）

- 🌐 **用户配置**
  - 支持用户选择响应语言（中文/English/跟随输入）
  - 移除硬编码的"中文响应"

- 📁 **.gitignore 更新**
  - 添加 CLAUDE.md 备份文件忽略规则

### 变更
- Skills 映射表改为动态生成，仅包含已安装的 skill
- 任务文件路径不再硬编码，由用户决定
- 移除对 `date` 命令的依赖，改善跨平台兼容性

### 移除
- 移除所有硬编码的预设值
- 移除固定的层职责定义表（改为从代码检测）
- 移除固定的接口设计模式代码示例（改为从代码提取）

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

[未发布]: https://github.com/THonour99/Claude-code-Embedded-plugins/compare/v2.0.0...HEAD
[2.0.0]: https://github.com/THonour99/Claude-code-Embedded-plugins/compare/v1.2.0...v2.0.0
[1.2.0]: https://github.com/THonour99/Claude-code-Embedded-plugins/compare/v1.0.0...v1.2.0
[1.0.0]: https://github.com/THonour99/Claude-code-Embedded-plugins/releases/tag/v1.0.0
