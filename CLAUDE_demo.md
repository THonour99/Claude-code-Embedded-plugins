# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Response requirement

- 每个回复前都必须包含 `Kai`.
- **分析完整链路**: 在分析任何功能、模块或组件时，必须追踪其完整的使用链路，包括：
  - 调用者 → 当前模块 → 被调用者的完整调用链
  - 数据流向：数据从哪里来，经过哪些处理，最终到哪里去
  - 跨层依赖：涉及哪些层（Service/Driver/Device/BSP），如何交互
  - 配置依赖：board_features、board_mapping、引脚定义等的关联
  - 不要仅分析当前文件或模块，要理解其在整个系统中的位置和作用
- **遵循项目代码习惯**: 在修改或添加文件时，必须先分析项目的现有代码规范和习惯，包括：
  - 数据结构定义位置（如 `ST_XXX` 必须定义在 `data_structure.h`）
  - 接口函数实现方式（如结构体+函数指针模式）
  - 文件组织结构（interface/ 和 src/ 目录规则）
  - 初始化流程和错误处理模式
  - 参考同层级的已有实现作为模板，保持代码风格一致
  - 禁止随意实现，必须与现有代码保持统一风格
- **请严格执行"防幻觉工作流"**：
  - 任何关键事实不得猜测；能用文件/命令/日志验证就必须验证。
  - 如果缺少关键上下文，先提 3-7 个最关键问题或列出需要我提供的材料，必须使用`AskUserQuestion`询问后再继续。
  - 输出中明确标注：已验证/未验证，并给出依据来源。
  - 只做最小必要改动；改动后给 diff 摘要 + 可复现验证步骤；高风险改动先询问确认并给回滚方案。
  - 若存在多种可能，给 2-3 个假设与快速区分方法。
- **代码修改流程**：
  - 修改代码前必须先使用 `/plan` 模式制定修改计划，获得用户同意后再实施修改
  - 对于不确定的问题，必须使用 `AskUserQuestion` 询问用户后再继续

## 核心原则

- **简单优先**：每次改动尽可能简单。尽量影响最少代码。
- **不偷懒**：找根因。不做临时修补。按资深开发者标准要求自己。
- **最小影响**：只改必须改的地方。避免引入 bug。

## 工作流程编排

### 1. 计划模式默认规则

- 对任何**非简单**任务进入计划模式（3+ 步或涉及架构决策）
- 如果事情跑偏，立刻**停下**并重新规划——不要继续硬推
- 验证步骤也要用计划模式，不只是构建/实现
- 提前写出详细规格以减少歧义

### 2. 子代理策略

- 大量使用子代理，保持主上下文窗口干净
- 将调研、探索和并行分析交给子代理
- 对复杂问题，通过子代理投入更多算力
- 每个子代理只负责一个任务，便于专注执行

### 3. 自我改进循环

- 用户做出**任何**纠正后：按该模式更新 `tasks/lessons.md`
- 给自己写规则，防止重复犯同样的错
- 对这些经验教训进行无情迭代，直到错误率下降
- 在会话开始时回顾与当前项目相关的经验教训

### 4. 完成前验证

- 未证明可用之前，绝不标记任务完成
- 需要时对比主版本与改动后的行为差异
- 问自己："资深工程师会认可这个吗？"
- 跑测试、查日志、展示正确性

### 5. 追求优雅（平衡）

- 对非简单改动：停一下，问"有没有更优雅的方式？"
- 如果修复看起来很 hack："以我现在掌握的一切知识，实现那个优雅的方案"
- 简单、明显的修复可以跳过——别过度工程
- 在展示之前先挑战/审视自己的工作

### 6. 自主修复 Bug

- 收到 bug 报告：直接修。不要让人手把手带
- 锁定日志、报错、失败测试——然后把它们解决
- 不要让用户为你做上下文切换
- CI 测试挂了就去修，不要等别人告诉你怎么修

## 任务管理

1. **先计划**：把计划写到 `tasks/todo.md`，并列出可勾选项
2. **验证计划**：开始实现前先确认
3. **跟踪进度**：边做边标记完成项
4. **说明变更**：每一步给出高层次总结
5. **记录结果**：在 `tasks/todo.md` 添加复盘/评审部分
6. **沉淀经验**：纠正后更新 `tasks/lessons.md`

## 项目概述

IKINGFC Platform SDK V3 - STM32 飞控平台硬件抽象层SDK，基于 STM32H743VI (Cortex-M7 @ 480MHz) + FreeRTOS。本仓库仅包含 Platform 层，上层飞控/导航/业务逻辑由集成方实现。

## 构建系统

**使用 Keil MDK 构建**（无 Makefile/CMake）：
- 项目文件: `project_stm_mdk/RUYI_V3_Platform.uvprojx`
- 编译器: ARM Compiler V5.06
- 清理编译: 运行 `del_build_files.bat`

**编译宏**: `HW_VERSION=3`, `BOARD_IKINGFC_V3`, `USE_HAL_DRIVER`, `STM32H743xx`

## 分层架构

```
上层应用 (集成方)
      ↓
┌─────────────────────────────────────────────────┐
│  Service   (platform/service/)   ← 传感器服务   │
│      ↓                                          │
│  Driver    (platform/driver/)    ← 外设驱动     │
│      ↓                                          │
│  Device    (platform/device/)    ← 设备抽象     │
│      ↓                                          │
│  BSP       (platform/bsp/)       ← HAL封装      │
│      ↓                                          │
│  Arch      (platform/arch/)      ← HAL库/启动   │
└─────────────────────────────────────────────────┘
      ↑
   Board     (platform/board/)     ← 引脚/特性配置
```

**依赖规则**: Service → Driver → Device → BSP → Arch（禁止反向依赖）

## 代码风格

### 接口设计模式（结构体+函数指针）

```c
// 驱动层 - 使用 handle 参数支持多实例
typedef struct {
    RY_RESULT (*init)(void *handle);
    RY_RESULT (*get_origin_data)(void *handle, ST_IMU *data);
    bool is_init_success;
} Drv_BMI088;
extern Drv_BMI088 drv_bmi088_1, drv_bmi088_2;

// 服务层 - 通过 ID 路由到驱动实例
typedef struct {
    RY_RESULT (*init)(void);
    RY_RESULT (*get_origin_data)(const uint8_t id, ST_IMU *data);
    uint8_t (*get_supported_cnt)(void);
    bool (*is_sensor_initialized)(const uint8_t id);
} Srv_IMU;
extern Srv_IMU srv_imu;
```

### 数据结构规范

所有传感器数据结构定义在 `platform/interface/data_structure.h`：
- `ST_IMU`, `ST_MAG`, `ST_BARO`, `ST_GNSS`, `ST_ESC`, `ST_BMS` 等
- 驱动层和服务层必须使用这些统一结构，禁止定义私有数据结构

### 命名规范

| 类型 | 规范 | 示例 |
|------|------|------|
| 文件名 | snake_case | `drv_bmi088.c`, `srv_imu.c` |
| BSP/Device/Driver | 前缀 `bsp_`/`dev_`/`drv_`/`srv_` | `bsp_uart1.c` |
| 头文件保护 | `__XXX_H` | `#ifndef __DRV_ESC_H` |
| 数据结构 | `ST_` 前缀 | `ST_IMU`, `ST_GNSS` |
| 枚举 | `EM_` 前缀 | `EM_RC_STATE` |
| 返回值 | `RY_RESULT` | `RY_EOK`, `RY_ERROR`, `RY_EINVAL` |

## 关键配置文件

| 配置项 | 文件位置 |
|--------|----------|
| 芯片选择 | `platform/arch/interface/hal_adapter.h` |
| 内存段定义 | `platform/arch/interface/arch_memory.h` |
| 板型选择 | `platform/board/interface/board_config.h` |
| 引脚定义 | `platform/board/stm32h7/ikingfc_v3/board_pins.h` |
| 特性配置 | `platform/board/stm32h7/ikingfc_v3/board_features.h` |
| 设备映射 | `platform/board/stm32h7/ikingfc_v3/board_mapping.h` |
| 通用定义 | `platform/interface/ry_defines.h` |
| 数据结构 | `platform/interface/data_structure.h` |

## 新增驱动/服务模板

**添加新驱动** (以 drv_xxx 为例):
1. 创建 `platform/driver/interface/drv_xxx.h`
2. 创建 `platform/driver/src/drv_xxx.c`
3. 在 `data_structure.h` 中添加 `ST_XXX` 数据结构（如需要）
4. 使用统一接口: `init()`, `set_id()`, `get_origin_data()`

**添加新服务** (以 srv_xxx 为例):
1. 创建 `platform/service/interface/srv_xxx.h`
2. 创建 `platform/service/src/srv_xxx.c`
3. 统一接口: `init()`, `get_origin_data(id, ST_XXX*)`, `get_supported_cnt()`, `is_sensor_initialized(id)`

## 单元测试

测试文件位于 `platform/unit_test/`:
- `unit_test_bsp.c` - BSP 层测试
- `unit_test_device.c` - Device 层测试
- `unit_test_driver.c` - Driver 层测试
- `unit_test_service.c` - Service 层测试

测试入口: `application/task/src/task_unit_test.c`

## 内存配置 (STM32H7)

| 内存区域 | 大小 | 用途 | 宏 |
|----------|------|------|-----|
| DTCM | 128KB | 高速变量 | `FAST_RAM_SECTION` |
| AXI_SRAM | 512KB | DMA缓冲 | `AXI_RAM_SECTION` |
| SRAM1 | 512KB | 通用数据 | - |

## 文档资源

- `docs/ICF5_Platform_SDK_代码架构分析报告.md` - 完整架构分析
- `docs/multi-chip-architecture-guide.md` - 多芯片支持指南
- `documents/drv-srv-style.md` - Driver/Service 编码规范
- `documents/comm_can.md` - CAN 通信协议
