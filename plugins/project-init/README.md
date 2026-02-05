# Project-Init Plugin（嵌入式版 v2.0）

智能检测嵌入式项目并**动态生成** CLAUDE.md 规范文件的 Claude Code 插件。

## 核心理念：零预设，全检测

v2.0 版本彻底重构了生成逻辑：

- ❌ **不再使用**硬编码的命名前缀（如 `ST_`/`EM_`/`RY_`）
- ❌ **不再预设**固定的目录结构（如 `platform/service/`）
- ❌ **不再假设**特定的错误码或接口模式
- ✅ **完全基于**项目实际检测结果动态生成

## 功能介绍

### 核心特性

- 🔍 **智能检测**: 深度解析 IDE 项目文件、RTOS、中间件、代码风格
- 🎯 **动态生成**: 所有内容从检测结果生成，无硬编码预设
- 🛡️ **防幻觉**: 内置嵌入式专用验证清单，减少 AI 幻觉
- 📊 **增量更新**: 支持保留用户自定义区域，只更新自动生成部分
- ✅ **生成后验证**: 自动验证所有引用路径和 Skills 是否有效

### 支持的 IDE/构建系统

| 构建工具 | 检测文件 | 提取信息 |
|----------|----------|----------|
| **Keil MDK** | `*.uvprojx`, `*.uvproj` | MCU 型号、编译宏、编译器版本（V5/V6）、链接脚本、Include 路径、多目标支持 |
| **IAR** | `*.ewp` | MCU 型号、编译器配置、链接脚本 |
| **STM32CubeMX** | `*.ioc` | MCU 型号、外设配置、DMA 配置、中断配置、时钟配置 |
| **STM32CubeIDE** | `.cproject` | MCU 型号、编译宏、链接脚本 |
| **CMake + GCC** | `CMakeLists.txt` | 项目名、工具链、MCU 型号、链接脚本 |

### 支持的 RTOS

- FreeRTOS（含配置提取：堆大小、优先级数量）
- RT-Thread
- ThreadX (Azure RTOS)
- 裸机系统

### 支持的中间件检测

- LWIP（TCP/IP 协议栈）
- FatFS（文件系统）
- USB（Host/Device）
- mbedTLS（安全库）
- FreeModbus
- CAN 协议栈

## 安装方法

### 方式一：全局安装（推荐）

```bash
# 复制到全局 plugins 目录
cp -r project-init ~/.claude/plugins/
```

### 方式二：项目级安装

```bash
# 复制到项目目录
cp -r project-init /path/to/your/project/.claude/plugins/
```

## 使用方法

### 快速开始

```bash
# 在嵌入式项目目录中执行
/project-init
```

### 执行流程

```text
启动命令
  ↓
前置检查
├─ 检查 CLAUDE.md 是否存在
├─ 检查是否有增量更新标记
└─ 询问操作方式（更新/覆盖/取消）
  ↓
项目智能分析
├─ 2.1 检测 IDE/构建系统（Keil/IAR/CubeMX/CMake）
├─ 2.2 检测 RTOS（FreeRTOS/RT-Thread/ThreadX/裸机）
├─ 2.3 检测中间件（LWIP/FatFS/USB/mbedTLS 等）
├─ 2.4 解析链接脚本（内存区域、栈堆大小）
├─ 2.5 扫描代码风格（命名模式、错误码类型）
├─ 2.6 检测项目架构（从实际目录结构推断）
└─ 2.7 检测已安装 Skills
  ↓
收集补充信息
├─ 询问用户称呼
├─ 询问语言偏好
└─ 询问未检测到的关键信息
  ↓
生成 CLAUDE.md（带增量更新标记）
  ↓
生成后验证
├─ 验证文件路径
├─ 验证 MCU 配置
└─ 验证 Skills 引用
  ↓
输出验证报告
```

## 生成的 CLAUDE.md 结构

### 增量更新标记

生成的文件包含两个区域：

```markdown
<!-- AUTO-GENERATED:START -->
... 自动生成内容，重新运行会更新 ...
<!-- AUTO-GENERATED:END -->

<!-- USER-DEFINED:START -->
... 用户自定义内容，重新运行会保留 ...
<!-- USER-DEFINED:END -->
```

### 通用层（固定内容）

适用于所有嵌入式项目：

- **Response requirement**: 防幻觉工作流、层职责边界
- **核心原则**: KISS/DRY/SoC/最小影响
- **工作流程编排**: 计划模式、子代理策略
- **质量门**: Gate 0-3 四阶段检查
- **嵌入式防幻觉检查清单**: 寄存器/时钟/DMA/中断/GPIO 验证
- **禁止行为清单**: ISR 规范、跨层调用限制

### 动态层（检测生成）

完全从项目检测结果填充：

- **项目概述**: MCU、RTOS、编译器、构建工具、中间件
- **构建系统**: 项目文件路径、编译宏、链接脚本
- **项目架构**: 从实际目录结构生成架构图
- **命名规范**: 从代码中提取的命名模式和示例
- **内存配置**: 从链接脚本解析的内存区域
- **关键配置文件**: 扫描实际存在的配置文件
- **Skills 映射**: 仅包含已安装的 Skills

## 与 v1.x 的区别

| 特性 | v1.x | v2.0 |
|------|------|------|
| 命名规范 | 硬编码 `ST_`/`EM_`/`RY_` | 从代码检测 |
| 目录结构 | 预设 `platform/service/` | 从实际目录检测 |
| 接口模式 | 预设 `Drv_XXX`/`Srv_XXX` | 从代码检测 |
| 内存配置 | 预设 DTCM/AXI_SRAM | 从链接脚本解析 |
| 增量更新 | 不支持 | ✅ 支持 |
| 生成后验证 | 不支持 | ✅ 支持 |
| 防幻觉清单 | 基础版 | 嵌入式专用增强版 |
| 语言配置 | 硬编码中文 | 用户选择 |

## 示例

### 检测结果示例

```text
✅ CLAUDE.md 生成完成！

📋 检测结果:
   - MCU: STM32H743VI (Cortex-M7 @ 480MHz)
   - RTOS: FreeRTOS
   - 构建工具: Keil MDK (ARM Compiler V6)
   - 中间件: LWIP, FatFS, USB Device
   - 代码风格: snake_case 函数, PascalCase 结构体

📂 文件已创建: ./CLAUDE.md

📝 增量更新标记已添加:
   - AUTO-GENERATED 区域: 重新运行会更新
   - USER-DEFINED 区域: 重新运行会保留

📋 验证报告:
   ✅ 项目文件: Project.uvprojx - 存在
   ✅ 链接脚本: STM32H743VI_FLASH.sct - 存在
   ✅ Skills: 2 个已验证
```

### 增量更新示例

已存在 CLAUDE.md 时：

```text
⚠️ 检测到已存在 CLAUDE.md 文件

检测到增量更新标记，请选择操作：
1. 更新自动生成区域（推荐） - 保留用户自定义内容
2. 全量覆盖 - 重新生成完整文件
3. 取消操作
```

## 配置说明

### plugin.json

```json
{
  "name": "project-init",
  "description": "智能检测嵌入式项目并动态生成 CLAUDE.md 规范文件（零预设模板）",
  "version": "2.0.0",
  "author": {
    "name": "Tangshikai",
    "email": "TKai.study@gmail.com"
  }
}
```

### allowed-tools

```yaml
- Read: 读取项目配置文件
- Write: 生成 CLAUDE.md 文件
- AskUserQuestion: 交互式收集用户输入
- Glob: 搜索项目文件
- Grep: 搜索代码内容、解析配置
- LS: 列出目录结构
- Bash(mkdir:*): 创建备份目录
- Bash(cp:*): 备份文件
```

## 目录结构

```text
project-init/
├── .claude-plugin/
│   └── plugin.json          # Plugin 元数据配置
├── commands/
│   └── project-init.md      # 命令实现逻辑
└── README.md                # 本文档
```

## 常见问题

### Q1: 检测结果不准确怎么办？

**A:**
1. 生成的 CLAUDE.md 可以手动修正
2. 在 USER-DEFINED 区域添加补充说明
3. 重新运行时选择"更新自动生成区域"会保留您的修改

### Q2: 如何保留我的自定义内容？

**A:**
所有内容都应放在 `<!-- USER-DEFINED:START -->` 和 `<!-- USER-DEFINED:END -->` 之间。
重新运行 `/project-init` 时选择"更新自动生成区域"即可保留这些内容。

### Q3: 支持哪些 MCU？

**A:**
支持所有能被 IDE 项目文件识别的 MCU。主要针对 ARM Cortex-M 系列优化，
包括 STM32、NXP LPC/i.MX RT、Nordic nRF、TI MSP432 等。

### Q4: 为什么要移除硬编码的命名规范？

**A:**
不同项目有不同的命名习惯。硬编码的规范可能与项目实际情况不符，
导致 AI 生成的代码风格不一致。v2.0 从代码中检测实际使用的命名模式，
确保生成的规范与项目一致。

## 版本历史

### v2.0.0 (2025-02)

- 🔄 **架构重构**: 从"预设模板填充"改为"智能检测+动态生成"
- 🎯 **零预设原则**: 移除所有硬编码的前缀、路径、模式
- 📊 **增量更新**: 支持 AUTO-GENERATED/USER-DEFINED 区域分离
- ✅ **生成后验证**: 自动验证文件路径和 Skills 引用
- 🛡️ **防幻觉增强**: 新增嵌入式专用验证清单
- 🌐 **语言配置**: 支持用户选择响应语言
- 🔍 **检测增强**:
  - 新增 STM32CubeIDE (.cproject) 支持
  - 新增 ThreadX (Azure RTOS) 检测
  - 新增中间件检测（LWIP/FatFS/USB/mbedTLS 等）
  - 新增链接脚本解析（内存区域、栈堆大小）
  - 新增代码风格检测（命名模式提取）

### v1.2.0 (2025-02)

- 嵌入式专用版本首发

### v1.0.0 (2025-11)

- 首次发布

## 许可证

MIT License

## 联系方式

- 作者：Tangshikai
- Email: <TKai.study@gmail.com>
