# Project-Init Plugin（嵌入式版）

快速初始化嵌入式项目 CLAUDE.md 规范文件的 Claude Code 插件。

## 功能介绍

智能分析嵌入式项目结构和配置文件，自动推断芯片型号、RTOS、外设配置等信息，生成定制化的开发规范文件。

### 核心特性

- 🔍 **智能分析**: 自动检测 MCU 型号、RTOS、外设驱动
- 💡 **按需询问**: 只对无法推断的信息询问用户
- 🎯 **数据优先**: 优先使用项目实际数据，避免猜测
- 🌐 **跨平台**: 完全兼容 Windows / macOS / Linux
- 🔧 **嵌入式专用**: 针对 STM32/FreeRTOS/裸机等嵌入式场景优化

### 支持的项目类型

| 构建工具 | 检测文件 | 提取信息 |
|----------|----------|----------|
| Keil MDK | `*.uvprojx`, `*.uvproj` | MCU 型号、编译宏、编译器版本 |
| IAR | `*.ewp`, `*.eww` | MCU 型号、编译器配置 |
| CMake + GCC | `CMakeLists.txt` | 工具链、MCU 型号 |
| STM32CubeMX | `*.ioc` | MCU 型号、外设配置、时钟配置 |

### 支持的 RTOS

- FreeRTOS
- RT-Thread
- 裸机系统

## 安装方法

### 方式一：直接使用（推荐）

```bash
# 复制到全局 plugins 目录
cp -r project-init ~/.claude/plugins/
```

### 方式二：项目级配置

```bash
# 复制到项目目录
cp -r project-init /path/to/your/project/.claude/plugins/
```

## 使用方法

### 快速开始

1. 在 Claude Code 中执行命令：

```bash
/project-init
```

2. 按照提示回答问题（只会询问无法自动推断的信息）
3. 自动生成 `CLAUDE.md` 文件

### 执行流程

```text
启动命令
  ↓
检查现有 CLAUDE.md（如有则询问是否覆盖）
  ↓
检测嵌入式项目类型
├─ 检测 Keil MDK 项目（*.uvprojx）
├─ 检测 IAR 项目（*.ewp）
├─ 检测 CMake 项目（CMakeLists.txt）
└─ 检测 STM32CubeMX 配置（*.ioc）
  ↓
检测 RTOS
├─ FreeRTOS（FreeRTOS*.h）
├─ RT-Thread（rtthread.h）
└─ 裸机（无 RTOS 检测到）
  ↓
检测分层架构
├─ Service 层（platform/service/）
├─ Driver 层（platform/driver/）
├─ Device 层（platform/device/）
├─ BSP 层（platform/bsp/）
└─ Board 配置（board_*.h）
  ↓
收集补充信息（只询问无法推断的）
  ↓
生成 CLAUDE.md
  ↓
显示成功信息
```

## 生成的 CLAUDE.md 包含

### 嵌入式专用规范

- **分层架构规则**: Service → Driver → Device → BSP → Arch
- **层职责边界**: 每层的职责定义和禁止行为
- **中断安全规范**: ISR 编写规则、临界区保护
- **内存管理规范**: DTCM/AXI_SRAM/DMA 缓冲区使用规则
- **代码风格规范**: 结构体+函数指针模式、命名规范（ST_/EM_/drv_/srv_）

### AI 工作流程规范

- **防幻觉工作流**: 关键事实必须验证，禁止猜测
- **完整链路分析**: 追踪数据从产生到消费的完整路径
- **质量门控制**: Gate 0-3 四阶段质量检查
- **层职责边界检查**: 确保修改在正确的层进行

### 嵌入式 Skills 映射

| 触发场景 | 必须使用的技能 |
|---------|--------------|
| HardFault/崩溃/栈溢出 | `embedded-debug-assistant` |
| DMA优化/驱动性能/CPU占用 | `embedded-perf-analyzer` |
| HAL库使用检查 | `hal-check` |
| FreeRTOS任务分析 | `rtos-analyze` |

## 示例

### 使用场景：STM32 飞控项目初始化

```bash
cd /path/to/flight-controller-project
/project-init
```

**自动检测结果**：

```text
🔍 项目分析完成！

📋 项目信息:
   - MCU: STM32H743VI (Cortex-M7 @ 480MHz)
   - RTOS: FreeRTOS
   - 构建工具: Keil MDK (ARM Compiler V5.06)
   - 架构模式: 分层架构（Service/Driver/Device/BSP）

📂 检测到的配置文件:
   - board_pins.h
   - board_features.h
   - board_mapping.h
   - data_structure.h

✅ CLAUDE.md 生成完成！
```

### 覆盖现有配置

如果项目中已存在 `CLAUDE.md`，插件会询问：

```text
⚠️ 检测到已存在 CLAUDE.md 文件

请选择操作：
1. 备份后覆盖（推荐） - 保存为 CLAUDE.md.backup-{timestamp}
2. 直接覆盖 - 原文件将被替换
3. 取消操作 - 退出命令
```

## 配置说明

### plugin.json

```json
{
  "name": "project-init",
  "description": "智能分析嵌入式项目并生成 CLAUDE.md 规范文件（STM32/FreeRTOS/裸机）",
  "version": "1.2.0",
  "author": {
    "name": "Tangshikai",
    "email": "TKai.study@gmail.com"
  }
}
```

### allowed-tools

命令执行时使用以下工具：

- `Read`: 读取项目配置文件
- `Write`: 生成 CLAUDE.md 文件
- `AskUserQuestion`: 交互式收集用户输入
- `Bash(date:*)`: 获取时间戳
- `Glob`: 搜索项目文件
- `Grep`: 搜索代码内容

## 目录结构

```text
project-init/
├── .claude-plugin/
│   └── plugin.json          # Plugin 元数据配置
├── commands/
│   └── project-init.md      # 命令实现逻辑（嵌入式模板）
└── README.md                # 本文档
```

## 依赖要求

- Claude Code CLI（最新版本）
- 无需其他依赖，模板已内置

## 常见问题

### Q1: 支持哪些 MCU？

**A:** 主要针对 STM32 系列优化，但也支持其他 ARM Cortex-M 芯片。

### Q2: 生成的 CLAUDE.md 可以手动修改吗？

**A:** 完全可以！生成的文件只是起点，您应该根据项目实际情况调整细节。

### Q3: 如何恢复备份的文件？

**A:** 如果选择了"备份后覆盖"，原文件会保存为：

```bash
CLAUDE.md.backup-{timestamp}

# 恢复方法
cp CLAUDE.md.backup-20251103143000 CLAUDE.md
```

### Q4: 支持哪些操作系统？

**A:** 完全跨平台支持：

- ✅ Windows (CMD / PowerShell)
- ✅ macOS
- ✅ Linux
- ✅ WSL

## 版本历史

### v1.2.0 (2025-02)

- 🔧 **嵌入式专用版本**
  - 针对 STM32/FreeRTOS/裸机场景优化
  - 新增分层架构检测（Service/Driver/Device/BSP）
  - 新增层职责边界规范
  - 新增中断安全、内存管理规范
  - 新增完整数据使用链追踪要求
  - 集成嵌入式调试/性能分析 Skills

### v1.1.0 (2025-11)

- 支持 Monorepo（已移除）

### v1.0.0 (2025-11)

- 首次发布

## 许可证

MIT License

## 联系方式

- 作者：Tangshikai
- Email: <TKai.study@gmail.com>
