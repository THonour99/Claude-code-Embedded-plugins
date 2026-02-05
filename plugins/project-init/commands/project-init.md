---
allowed-tools: Read, Write, AskUserQuestion, Glob, Grep, LS, Bash(mkdir:*), Bash(cp:*)
description: 智能检测嵌入式项目并动态生成 CLAUDE.md 规范文件（零预设模板）
---

智能检测嵌入式项目结构、IDE 配置、RTOS、中间件和代码风格，**完全基于检测结果动态生成** CLAUDE.md，不使用任何硬编码的预设值。

**核心特性**: 🔍 智能检测 | 🎯 动态生成 | 🛡️ 防幻觉 | 📊 增量更新 | ✅ 生成后验证

---

## 执行步骤

### 1. 前置检查

检查当前目录是否已存在 CLAUDE.md 文件：

1. 使用 `Read` 尝试读取 `CLAUDE.md`
2. 如果文件存在，检查是否包含增量更新标记：
   - 搜索 `<!-- AUTO-GENERATED:START -->` 和 `<!-- USER-DEFINED:START -->`
3. 根据检测结果询问用户：
   - **存在标记**：询问操作方式（更新自动生成区域（推荐）/ 全量覆盖 / 取消）
   - **不存在标记（旧格式）**：询问操作方式（迁移到新格式（推荐）/ 全量覆盖 / 取消）
   - **文件不存在**：直接进入分析步骤

---

### 2. 项目智能分析

**核心原则**: 完全基于检测结果，不做任何假设。所有检测失败的项目保留为"未检测到"，后续询问用户。

#### 2.1 检测 IDE/构建系统

按优先级依次检测：

**Keil MDK (*.uvprojx / *.uvproj)**:
```
Glob: **/*.uvprojx, **/*.uvproj
解析规则:
├─ MCU型号: Grep `<Device>(.+?)</Device>`
├─ 项目名: Grep `<OutputName>(.+?)</OutputName>`
├─ 宏定义: Grep `<Define>(.+?)</Define>`
├─ 编译器版本: 检查 `<uAC6>1</uAC6>` 判断 V6，否则 V5
├─ Include路径: Grep `<IncludePath>(.+?)</IncludePath>`
├─ 链接脚本: Grep `<ScatterFile>(.+?)</ScatterFile>`
└─ 构建目标: Grep `<TargetName>(.+?)</TargetName>`（支持多目标）
```

**IAR (*.ewp)**:
```
Glob: **/*.ewp
解析规则:
├─ MCU型号: Grep `<OGChipSelectEditMenu>(.+?)</OGChipSelectEditMenu>`
├─ 宏定义: 提取 `<state>` 标签中的 DEFINE 内容
├─ Include路径: 提取 ICCARM 相关 state 内容
└─ 链接脚本: Grep `<IlinkIcfFile>(.+?)</IlinkIcfFile>`
```

**STM32CubeMX (*.ioc)**:
```
Glob: **/*.ioc
解析规则:
├─ MCU型号: 搜索 `Mcu.UserName=` 或 `Mcu.Name=`
├─ 项目名: 搜索 `ProjectManager.ProjectName=`
├─ 外设配置: 搜索 `^[A-Z0-9]+\.` 开头的行
├─ DMA配置: 搜索 `Dma.` 开头的行
├─ 中断配置: 搜索 `NVIC.` 开头的行
└─ 时钟配置: 搜索 `RCC.` 开头的行
```

**STM32CubeIDE (.cproject)**:
```
Glob: **/.cproject
解析规则:
├─ MCU型号: Grep `<option.*superClass.*mcu.*value="(.+?)"`
├─ 宏定义: Grep `<listOptionValue.*value="(.+?)"`
└─ 链接脚本: Grep `<option.*superClass.*linker.*script`
```

**CMake (CMakeLists.txt)**:
```
Glob: **/CMakeLists.txt
解析规则:
├─ 项目名: Grep `project\((.+?)\)`
├─ MCU型号: Grep `MCPU|CHIP|MCU|DEVICE` 相关变量
├─ 工具链: 检查 CMAKE_TOOLCHAIN_FILE 或 arm-none-eabi
└─ 链接脚本: Grep `LINKER_SCRIPT|LD_SCRIPT` 或 `.ld` 文件引用
```

#### 2.2 检测 RTOS

```
FreeRTOS:
├─ 头文件: Glob `**/FreeRTOS.h`
├─ 配置: Glob `**/FreeRTOSConfig.h` → 提取 configTOTAL_HEAP_SIZE, configMAX_PRIORITIES
└─ 代码: Grep `xTaskCreate|vTaskDelay|xQueueSend`

RT-Thread:
├─ 头文件: Glob `**/rtthread.h`
├─ 配置: Glob `**/rtconfig.h`
└─ 代码: Grep `rt_thread_create|rt_sem_take`

ThreadX (Azure RTOS):
├─ 头文件: Glob `**/tx_api.h`
└─ 代码: Grep `tx_thread_create|tx_queue_send`

裸机: 以上都未检测到
```

#### 2.3 检测中间件

```
LWIP:      Glob `**/lwip/**` 或 Grep `#include.*lwip`
FatFS:     Glob `**/fatfs/**` 或 `**/ff.h`
USB:       Glob `**/usb*/**` 或 Grep `USB_OTG|USBD_`
mbedTLS:   Glob `**/mbedtls/**`
FreeModbus: Grep `eMBInit|eMBPoll`
CAN协议栈:  Grep `CAN_FilterConfig|HAL_CAN_`
```

#### 2.4 解析链接脚本

```
检测 .ld / .sct 文件:
├─ 内存区域: Grep `MEMORY\s*\{` 后的内容，提取 ORIGIN 和 LENGTH
├─ Flash大小/起始地址
├─ RAM分区（DTCM/SRAM/AXI_SRAM 等，如果有）
└─ 栈/堆大小: Grep `_Min_Heap_Size|_Min_Stack_Size|Stack_Size|Heap_Size`
```

#### 2.5 扫描代码风格

```
扫描 *.c 和 *.h 文件（采样前 10 个文件），提取：
├─ 结构体命名: Grep `typedef struct` 后的命名模式（如 XX_TypeDef, ST_XX, xx_t）
├─ 枚举命名: Grep `typedef enum` 后的命名模式（如 EM_XX, XX_Enum, xx_e）
├─ 函数命名: 分析函数定义的命名风格（camelCase/snake_case/PascalCase）
├─ 错误码类型: Grep `typedef.*enum.*\b(status|result|error|ret)\b`i
├─ 文件组织: 分析目录结构，检测是否有分层
└─ 层前缀: 如果有分层，检测各层文件的命名前缀（如 drv_/srv_/bsp_/app_）
```

#### 2.6 检测项目架构

**不预设任何路径结构，从实际目录检测**：

1. 列出顶层目录
2. 识别常见架构模式：
   - 分层架构: 检测 service/driver/device/bsp/hal 等目录
   - HAL + App: 检测 Core/Drivers/Middlewares 等目录（CubeMX 风格）
   - 扁平结构: src/inc 或无明显分层
3. 生成实际的目录树描述

#### 2.7 检测已安装 Skills

1. 检查 `~/.claude/plugins/` 目录
2. 或使用 Glob 搜索项目中 `.claude/plugins/` 目录
3. 识别已安装的嵌入式相关 Skills
4. 只在模板中包含已安装且相关的 skill

---

### 3. 收集补充信息

使用 `AskUserQuestion` 询问：

1. **用户称呼**（必须）:
   - 选项：您好 / Hi / 不需要称呼 / 其他（自定义）

2. **语言偏好**（必须）:
   - 选项：中文（推荐）/ English / 跟随用户输入语言

3. **如有检测失败项**（按需）:
   - MCU 型号（如果未检测到）
   - RTOS（如果未检测到）
   - 构建工具（如果未检测到）

---

### 4. 生成 CLAUDE.md

#### 4.1 通用层（固定内容）

以下内容适用于所有嵌入式项目，保持固定：

```markdown
<!-- AUTO-GENERATED:START - 以下内容由 /project-init 自动生成，重新运行会更新 -->

# CLAUDE.md（嵌入式项目）

> 由 /project-init 智能生成

---

## Response requirement

- 每个回复前都必须包含 `{用户称呼}`.
- **{语言偏好}响应**
- **完整链路分析**: 分析任何功能时，必须追踪完整调用链和数据流
- **遵循层职责边界**: 修改代码前必须确认当前操作属于哪一层
- **遵循项目代码习惯**: 修改前先分析现有代码规范（数据结构位置、接口模式、文件组织）
- **防幻觉工作流**:
  - 关键事实必须验证，不得猜测
  - 缺少关键上下文时，使用 `AskUserQuestion` 询问后再继续
  - 输出中标注：已验证/未验证，给出依据来源
  - 只做最小必要改动，高风险改动先询问确认
  - 方案必须切实可行，禁止生成无法实现的内容
- **代码修改流程**: 修改前必须先使用 `/plan` 模式制定计划，获得用户同意后再实施

## 核心原则

- **KISS**: 保持简单，拒绝过度设计
- **DRY**: 避免重复，优先复用现有代码
- **SoC**: 关注点分离，各层各司其职
- **最小影响**: 只改必须改的地方，避免引入 bug

## 工作流程编排

1. **计划模式默认**: 非简单任务（3+ 步或涉及架构决策）进入计划模式
2. **子代理策略**: 将调研、探索和并行分析交给子代理，保持主上下文干净
3. **完成前验证**: 未证明可用之前，绝不标记任务完成
4. **自主修复**: 收到 bug 报告直接修复，不等手把手指导

## 质量门

| 阶段              | 检查点                                       |
| ----------------- | -------------------------------------------- |
| **Gate 0 立项**   | 需求边界明确、KISS 四问已回答                |
| **Gate 1 方案**   | 调用链分析完整、层职责边界确认、风险分析明确 |
| **Gate 2 编码前** | grep 检查无重复定义、分层边界清晰            |
| **Gate 3 测试**   | 分层测试完成、边界条件已测试                 |

## 嵌入式防幻觉检查清单

**寄存器操作**:
- [ ] 修改寄存器前，引用参考手册章节号验证地址和位域
- [ ] 使用 Grep 在 HAL 头文件中确认寄存器宏定义存在

**时钟配置**:
- [ ] 修改时钟前，追踪完整时钟树路径（从 HSE/HSI 到目标外设）
- [ ] 确认 PLL 配置不会超出 VCO 频率范围

**DMA 配置**:
- [ ] 添加 DMA 前，检查通道/流是否已被占用（搜索现有 DMA 配置）
- [ ] 确认缓冲区在 DMA 可访问的内存区域

**中断配置**:
- [ ] 添加中断前，列出当前所有已配置中断及优先级
- [ ] 确认优先级不会造成优先级反转

**GPIO 配置**:
- [ ] 配置引脚前，确认该引脚没有被其他外设复用
- [ ] 参考 Datasheet 的 Pinout 表确认 AF 编号

## 禁止行为清单

- ❌ ISR 中执行阻塞操作或动态内存分配
- ❌ 跨层直接调用（必须经过相邻层）
- ❌ 反向依赖（低层模块依赖高层模块）
- ❌ 在非 DMA 安全的内存区域使用 DMA
- ❌ 跨层职责混淆
- ❌ 版本迭代（v1/v2/v3），必须就地更新
- ❌ 提交信息包含 AI 工具标识

## 交互规范

- **主动提问**: 数据不足或需要决策时，必须使用 `AskUserQuestion`
- **禁止主动创建文档**: 不主动创建 *.md 文件，除非用户明确要求
- **Git 提交**: 必须先询问用户同意，提交信息包含 What/Why/How

---

## 项目概述

{此处由动态层填充}

## 构建系统

{此处由动态层填充}

## 项目架构

{此处由动态层填充}

## 命名规范

{此处由动态层填充 - 从代码检测}

## 内存配置

{此处由动态层填充 - 从链接脚本检测}

## 关键配置文件

{此处由动态层填充 - 扫描实际存在的文件}

## Skills 技能使用规范

{此处由动态层填充 - 仅包含已安装的 skill}

<!-- AUTO-GENERATED:END -->

<!-- USER-DEFINED:START - 以下内容由用户自定义，重新运行不会覆盖 -->

## 补充说明

> 在此添加项目特有的规范、约定或注意事项。此区域在重新运行 /project-init 时会保留。

<!-- USER-DEFINED:END -->
```

#### 4.2 动态层填充规则

**项目概述**:
```markdown
## 项目概述

**{项目名}** - {如果检测到 README 则提取描述，否则留空}

### 核心技术栈

| 项目         | 配置                  |
| ------------ | --------------------- |
| **MCU**      | {检测到的 MCU 型号}   |
| **RTOS**     | {检测到的 RTOS}       |
| **编译器**   | {检测到的编译器}      |
| **构建工具** | {检测到的构建工具}    |

### 中间件
{仅当检测到中间件时显示此节}
- {中间件列表}
```

**构建系统**:
```markdown
## 构建系统

- **项目文件**: `{实际检测到的项目文件路径}`
- **编译器**: {实际检测到的编译器版本}
- **编译宏**: `{实际检测到的宏定义列表}`
- **链接脚本**: `{实际检测到的链接脚本路径}`
```

**项目架构**（从实际目录生成）:
```markdown
## 项目架构

{根据检测到的目录结构生成实际的架构图}

例如检测到 CubeMX 项目:
```
Core/
├── Inc/          ← 应用头文件
├── Src/          ← 应用源码
└── Startup/      ← 启动文件
Drivers/
├── STM32xxxx_HAL_Driver/  ← HAL 库
└── CMSIS/                 ← CMSIS
Middlewares/      ← 中间件（如有）
```

例如检测到分层架构:
```
app/              ← 应用层
service/          ← 服务层
driver/           ← 驱动层
bsp/              ← 板级支持
hal/              ← HAL 抽象
```
```

**命名规范**（从代码检测生成）:
```markdown
## 命名规范

> 以下规范从项目现有代码中提取，请遵循

| 类型     | 检测到的模式          | 示例                    |
| -------- | --------------------- | ----------------------- |
| 文件名   | {检测到的模式}        | {实际示例}              |
| 结构体   | {检测到的模式}        | {实际示例}              |
| 枚举     | {检测到的模式}        | {实际示例}              |
| 函数     | {检测到的模式}        | {实际示例}              |
| 错误码   | {检测到的类型}        | {实际示例}              |
```

**内存配置**（从链接脚本检测生成）:
```markdown
## 内存配置

{仅当检测到链接脚本时显示}

| 内存区域 | 起始地址   | 大小    | 用途        |
| -------- | ---------- | ------- | ----------- |
| {区域名} | {ORIGIN}   | {LENGTH}| {推断用途}  |

- 栈大小: {检测到的值}
- 堆大小: {检测到的值}
```

**关键配置文件**（扫描实际存在的文件）:
```markdown
## 关键配置文件

| 配置项     | 文件位置                    |
| ---------- | --------------------------- |
{扫描项目中实际存在的配置文件，如:}
| MCU 定义   | {实际路径}                  |
| 引脚定义   | {实际路径}                  |
| RTOS 配置  | {实际路径}                  |
```

**Skills 映射**（仅包含已安装的）:
```markdown
## Skills 技能使用规范

| 触发场景     | 关键词                  | 推荐技能                    | 状态   |
| ------------ | ----------------------- | --------------------------- | ------ |
{仅列出已安装的 skill，例如:}
| 嵌入式调试   | HardFault、崩溃、栈溢出 | `embedded-debug-assistant`  | ✅ 已安装 |
| 驱动性能分析 | DMA优化、CPU占用        | `embedded-perf-analyzer`    | ✅ 已安装 |

{如果没有检测到任何已安装的 skill，显示:}
> 未检测到已安装的嵌入式专用 Skills。如需使用，请先安装。
```

---

### 5. 生成后验证

生成 CLAUDE.md 后执行验证：

1. **文件路径验证**:
   - 检查所有引用的文件路径是否存在
   - 对不存在的路径输出警告

2. **MCU 验证**:
   - 如果检测到 MCU 型号，检查对应的 HAL 头文件是否存在

3. **Skills 验证**:
   - 确认列出的 Skills 确实已安装

4. **输出验证报告**:
```
📋 验证报告:
   ✅ 项目文件: {路径} - 存在
   ✅ 链接脚本: {路径} - 存在
   ⚠️ 配置文件: {路径} - 不存在（已从列表移除）
   ✅ Skills: {N} 个已验证
```

---

### 6. 输出结果

```
✅ CLAUDE.md 生成完成！

📋 检测结果:
   - MCU: {检测到的 MCU 型号 或 "未检测到（用户指定: xxx）"}
   - RTOS: {检测到的 RTOS}
   - 构建工具: {检测到的构建工具}
   - 中间件: {检测到的中间件列表 或 "无"}
   - 代码风格: {检测到的命名模式}

📂 文件已创建: ./CLAUDE.md

📝 增量更新标记已添加:
   - AUTO-GENERATED 区域: 重新运行会更新
   - USER-DEFINED 区域: 重新运行会保留

💡 建议:
   - 检查生成的命名规范是否准确
   - 在 USER-DEFINED 区域添加项目特有规范
   - 如有检测不准确的内容，可手动修正
```

---

## 增量更新逻辑

当检测到已存在的 CLAUDE.md 且包含增量更新标记时：

1. 读取现有文件
2. 提取 `<!-- USER-DEFINED:START -->` 和 `<!-- USER-DEFINED:END -->` 之间的内容
3. 重新执行项目分析
4. 生成新的 AUTO-GENERATED 区域
5. 保留原有的 USER-DEFINED 区域
6. 写入文件

---

## 注意事项

1. **零预设原则**: 不使用任何硬编码的前缀、路径或模式，全部从项目检测
2. **失败优雅处理**: 任何检测失败的项目标记为"未检测到"，不做假设
3. **验证闭环**: 生成后必须验证所有引用的文件路径
4. **用户可控**: USER-DEFINED 区域永不覆盖
5. **跨平台兼容**: 不依赖特定平台的命令（如 date）
