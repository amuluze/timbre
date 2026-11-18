# ADR-0001：桌面运行时选型

- 状态: Accepted
- 日期: 2026-11-18
- 决策者: Timbre 维护者
- 取代: 无
- 关联: `.specs/tasks/desktop-runtime-baseline.md`（待建）、`docs/research/axiom-page-migration.md`

## 背景

Timbre 是本地优先的桌面运营平台，需要一个承载 UI 与 Agent 运行时宿主的桌面运行时。两个候选：

- **Tauri 2 + React**：与 axiom 同栈，生态成熟，前端可直接复用 React 组件与既有代码路径；代价是 webview 的内存与启动开销。
- **MyGo + 原生 UI**（Go + `ui` 包自绘）：单个 Go 二进制，启动即时、内存低，Go 单语言覆盖宿主与界面；代价是生态年轻、组件覆盖不如 React、MyGo 处于 v0.x（官方明确允许破坏性变更）。

axiom 页面替换可行性调研（`docs/research/axiom-page-migration.md`，2026-11-18）给出的关键事实：

1. axiom 页面层 27,702 行生产代码，形态是「组件 + 经 specta 绑定的宿主命令 + 本地状态」，不与 React 生命周期强耦合；
2. axiom 的 **Agent Loop 与工具协议跑在 webview 内的 TypeScript 上**（`agent/core` 5,292、`agent/runtime` 6,726、`agent/tools` 6,453、`agent/transport` 4,871），Rust 只做特权宿主与命令——这条分界线决定了替换的代价分布；
3. MyGo v0.10.0 已覆盖 Timbre 需要的界面能力：markdown、code-editor、diff-viewer、rich-text-editor、editable-text、image、list、tabs、split、sidebar、inspector、text-input 等组件文档齐备；流式由 fetch 插件（SSE 与模型流式回答）与 bindings 的 channel / typed event 承担；SQLite 插件免 cgo；Terminal 插件用 Ghostty；
4. MyGo 侧的缺口是 **CDP 浏览器操控与 SSH**（Timbre MVP 不需要），自建成本高的是**无限画布与 PNG 导出**（Timbre 不需要设计画布）；
5. axiom 的 Rust 宿主层 59,800 行承载文件授权登记、密钥表、网络策略、审批流、PTY、CDP、SSH、SQLite，其中大部分超出 Timbre MVP 所需。

因此决定选型的不是「axiom 能不能整体搬到 MyGo」，而是「Timbre 需要多少宿主能力、愿意为哪种形态付代价」。

## 候选方案

| 方案 | 要点 | 代价 |
|---|---|---|
| A. Tauri 2 + React | 沿用 axiom 栈；Rust 宿主 + TS 前端；webview 承载 UI | webview 内存与启动开销；前端需 Node 工具链；Rust 与 TS 双语言；界面能力受 React 生态约束 |
| B. MyGo + 原生 UI | 单 Go 二进制；界面由 `ui` 包 GPU 自绘；Go 单语言覆盖宿主与界面 | axiom 宿主面需重写；MyGo v0.x 允许破坏性变更；组件覆盖不如 React；自建组件成本高 |
| C. 混合：MyGo 原生外壳 + webview 承载复杂页面 | MyGo 支持一窗两态混排（原生 UI 与 web 页面共存） | 两套渲染路径与两套心智模型；边界不清 |

## 决策

**选 B（MyGo + 原生 UI）作为 Timbre 的桌面运行时。**

理由：

1. **单语言闭环。** 界面与宿主同在 Go，Agent 运行时不再依赖 webview 内的 TypeScript（见背景第 2 条）。一个 Go 二进制，无 Node 工具链。
2. **形态与产品心智一致。** 腔调 skill 是可组合的文本与规则产物，原生 UI 自绘的紧凑布局比 webview 更贴近专业工具的信息密度诉求。
3. **代价可控且有边界。** 调研已列明 MyGo 的两个真缺口（CDP 浏览器操控、SSH）与高成本自建项（无限画布、PNG 导出）——这四项均在 Timbre MVP 之外，不构成阻塞。
4. **本地优先边界更紧。** 单二进制、无 web 运行时依赖，密钥与素材留在本地进程内的边界更清晰。

## 后果

- 变好的部分：
  - 界面与宿主统一在 Go，Agent 运行时、工具协议与 UI 状态共享同一语言与同一进程模型；
  - 单个 Go 二进制交付，无 Node 依赖，启动与内存优于 webview 方案；
  - 沿用 axiom 的**边界形态**（文件授权登记、密钥表、网络策略、逐次审批、会话持久化）而非其实现——需要的能力是 Timbre MVP 的那部分。
- 变差或必须承受的部分：
  - axiom 的 Rust 宿主层不可整体复用，需用 Go 重写 Timbre 所需的那部分特权边界；这是本次选型最大的实际工时项；
  - MyGo 处于 v0.x，官方明确「允许破坏性变更」，需锁版本并把升级当作例行任务；原生模块（native modules）官方标注为实验性，不得作为长期依赖锚点；
  - 界面组件覆盖不如 React，遇到 MyGo 没有的控件需自建；
  - **环境要求**：MyGo 的 `go.mod` 要求 Go 1.27.3+（本机当前为 Go 1.25.12），构建前需升级工具链；
  - **Agent 运行时形态被收窄**：原生 UI 下没有 webview 承载 TypeScript 运行时，Agent Loop 只能是 Go 进程内实现（或另起独立进程并重新引入 Node 运行时——与单二进制目标冲突，故不取）。
- 对本地优先边界的影响：
  - **无新增云端依赖。** 运行时选型不引入任何远端服务；模型调用沿用 axiom 的 provider 机制，属用户自有凭据，按既有 Task Spec 声明与本地回退执行。
  - 密钥仍只写本地存储，不随运行时选型改变存放位置。

## 未决问题

- Agent 运行时在 Go 进程内的形态（同步调用、goroutine 池、还是独立子进程）与取消/超时语义，另立 ADR。
- Timbre 所需的 Go 版特权边界（文件授权登记、密钥表、网络策略、审批流、会话持久化）的边界定义与实现顺序，需先有 Domain Spec 与 Task Spec。
- MyGo v0.x 的破坏性变更节奏是否会在若干年内使上述决定显著变贵——需要一次基于实测数据的复评（启动耗时、内存、宿主重写工时），本次决策未覆盖实测。
- MyGo 组件未覆盖时，自建组件的取舍阈值（何时值得自建、何时改用 MyGo 的 web 页面窗口）尚无判定标准。
