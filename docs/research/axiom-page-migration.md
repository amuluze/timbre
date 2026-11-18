# 调研：axiom 页面层能否替换为 MyGo 原生 UI

- 主题：以 MyGo 原生 UI 替换 axiom 的页面层（React 页面 + Tauri webview）的可行性
- 日期：2026-11-18
- 作者：Timbre 维护者（AI 协作产出）
- 状态：结论已归档，作为 `docs/adr/0001-desktop-runtime.md` 的证据输入
- 调查对象：`amuluze/axiom`（本地 checkout，commit `f674cc0c`，v0.7.8）与 `egoist/mygo`（v0.10.0，2026-11-17 发布）
- 许可前提：axiom 为 MIT（Copyright 2026 amuluze），其代码与设计可被本项目参考与移植

## 1. 为什么要做这个调研

README 曾写「此前已完成 axiom 页面替换可行性调研」，但仓库内没有正文、没有日期、没有作者，结论无从核对。本文件补齐正文：把「能不能把 axiom 的页面层换成 MyGo 原生 UI」拆成可测量的部分，给出数据、给出结论、并明确结论不覆盖什么。

## 2. 方法

1. 在 axiom 本地 checkout 上按目录统计生产代码行数（排除 `*.test.*` / `__fixtures__` / `*_tests.rs`），定位页面层与宿主层的实际体量；
2. 读 axiom 的关键实现，识别页面层依赖的宿主能力（渲染、导出、捕获）与平台能力（PTY、CDP、SSH、SQLite）；
3. 读 MyGo 上游 `docs/`（`ui/*`、`plugins/*`、`native-modules.md`、`architecture.md`）与 release 信息，逐项核对它对上述能力的覆盖情况；
4. 只把「两边都有文档或代码佐证」的部分写进结论，其余标注为未覆盖。

## 3. axiom 现状（测量值）

| 层 | 位置 | 生产代码行数 |
|---|---|---|
| 页面层（React 组件） | `apps/desktop/src/components/**` | 27,702 |
| 页面层之外的前端主干 | `agent` 47,584 / `stores` 11,648 / `platform` 5,555 / `i18n` 3,337 / `persistence` 3,044 / `config` 411 | — |
| 前端合计（含测试 176,185） | `apps/desktop/src/**` | 99,096 |
| 宿主层（Rust） | `apps/desktop/src-tauri/src/**`，76 个模块 | 59,800 |
| 设计面（页面 + agent 侧） | `components/design` + `agent/design` | 15,457 |
| 文档 / 官网 | `website/` | 另计，不在替换范围 |

页面层之外的前端主干说明一件事：axiom 的 Agent Loop 与工具协议（`agent/core` 5,292、`agent/runtime` 6,726、`agent/tools` 6,453、`agent/transport` 4,871）**跑在 webview 里的 TypeScript 上**，Rust 只做特权宿主与命令。这条分界线决定了替换的代价分布。

## 4. 页面层实际依赖什么

逐项读代码后，页面层对宿主的依赖收敛为五类：

| 能力 | axiom 实现 | 页面层怎么用 |
|---|---|---|
| `.ax` 设计稿渲染 | `components/design/ax/renderPageHost.tsx` + SVG / Canvas 2D 双轨（`PenNodeView.tsx` 用 SVG，`exportPagePng.ts`、`nativePageCapture.ts` 用 Canvas 2D） | 无限画布渲染、页面 PNG 导出、原生页面捕获 |
| 宿主命令通道 | `specta_bindings.rs`：Rust 类型经 tauri-specta 生成 TS invoke 绑定 | 所有特权调用 |
| 终端 | `@xterm/xterm`（web 内实现），Rust 侧 `portable-pty` 供真实 PTY | 终端面板 |
| 外部连接 | Rust `browser_session.rs`（CDP over WebSocket）、`ssh.rs` / `ssh_session.rs` | 浏览器面板、SSH 面板 |
| 存储 | Rust `sqlx` + SQLite，`session_repository/` | 会话与产物持久化 |

## 5. MyGo 侧的覆盖核对

| 能力 | MyGo 对应物 | 来源 | 判断 |
|---|---|---|---|
| 页面运行时 | 窗口内可直接跑系统 webview（WKWebView / WebView2 / WebKitGTK），也可用 `ui` 包原生 UI | `docs/README.md`、`docs/frontend.md` | 覆盖，且支持一窗两态混排 |
| TS 客户端生成 | `mygo generate` 从 Go 结构体生成带类型的 TS 客户端，支持 channel 流式返回与 typed events | `docs/bindings.md` | 覆盖，与 axiom 的 specta 路径同构 |
| 流式 HTTP / SSE | fetch 插件，body 为 Go 填充的 `ReadableStream`，明确支持 SSE 与模型流式回答 | `docs/plugins/fetch.md` | 覆盖 |
| 本地数据库 | SQLite 插件，Zig 原生模块实现，桌面端免 cgo | `docs/plugins/sqlite.md` | 覆盖 |
| 终端 | Terminal 插件，用 Ghostty 终端模拟器 | `docs/plugins/terminal.md` | 覆盖，且优于 web 内 xterm |
| Markdown / 代码编辑 / diff / 富文本 | `docs/ui/markdown.md`、`code-editor.md`、`diff-viewer.md`、`rich-text-editor.md` | 官方 UI 组件文档 | 覆盖 |
| 无限画布 / 节点图编辑器 | 无现成组件；`docs/ui/drawing.md` 提供 `Painter`（路径、图片、文本），`custom-widgets.md` 提供自绘组件机制 | 官方 UI 组件文档 | **需自建** |
| 页面 PNG 导出 / 原生捕获 | 无同类 API | — | **需自建** |
| CDP 浏览器操控 | 无；`plugins/automation.md` 面向驱动自家应用 | — | **缺口** |
| SSH | 无同类插件 | — | **缺口** |
| Rust 既有代码复用 | `docs/native-modules.md`：Go 接口可用 Rust crate 实现，免 cgo | 官方文档 | 可行，但该文档开头写明「实验性，任何版本都可能破坏性变更」 |

版本与风险面：

- MyGo v0.10.0（2026-11-17），MIT，6.5k star；Tauri v2.12.6（2026-11-16），113k star。
- MyGo 要求 Go 1.27.3+ 与 Bun；本机当前为 Go 1.25.12 / Node 22 / Bun 1.4.2 / Rust 1.96。
- MyGo 原生模块被官方标注为实验性、允许破坏性变更。

## 6. 结论

**可行，但代价集中在两处：设计画布与运行时宿主。**

1. **页面层可整体替换。** axiom 页面层 27,702 行生产代码，形态是「组件 + 经 specta 绑定的宿主命令 + 本地状态」，不与 React 生命周期耦合的部分可按 MyGo 的 `ui` 模型重写；MyGo 的组件目录已覆盖 markdown、代码编辑、diff、富文本等本项目真正需要的部分。
2. **重活一：设计画布。** 15,457 行的设计面依赖 SVG 与 Canvas 2D 双轨渲染、页面 PNG 导出与原生页面捕获，MyGo 没有对应组件，需要自建绘图与导出层，且导出质量（字体度量、混合模式）要逐项对齐才能通过 axiom 自己的设计对拍门禁。
3. **重活二：运行时宿主。** 59,800 行 Rust 承载特权边界（文件授权登记、密钥表、网络策略、审批流、PTY、CDP、SSH、SQLite）。迁到 MyGo 等于用 Go 重写这一层，或把 Rust 作为原生模块挂载——后者官方标注实验性，且 axiom 的 Rust 代码并未按「可被外部调用」的形态切分。
4. **两个真缺口。** CDP 浏览器操控与 SSH 在 MyGo 侧没有等价物；若项目需要，只能自建或继续保留 web 侧实现。
5. **对 Timbre 的实际含义。** axiom 的完整宿主面远大于 Timbre MVP 所需（Timbre 需要的是简报输入、流式产出、变体审阅；不需要设计画布、SSH、CDP 浏览器）。因此 axiom 侧的替换代价不能整体平移到 Timbre，真正需要复用的是「特权宿主 + 本地优先边界」这一层的**形态**而非全部功能。

## 7. 本调研不覆盖

- 未做性能实测：MyGo 原生 UI 与 Tauri webview 的启动耗时、内存占用没有在本机测量，文档中的「启动即时、内存低」沿用 MyGo 官方表述，未获独立验证。
- 未验证 axiom Rust 代码在 MyGo 原生模块下的实际编译与调用体验。
- 未评估 MyGo 0.x 升级节奏对 Timbre 长期维护成本的影响，只记录「允许破坏性变更」这一事实。
- 未做 UI 视觉与交互的逐页对拍，只做能力层核对。

## 8. 复现方式

```sh
cd ~/Desktop/github/axiom/apps/desktop
find src -type f \( -name '*.ts' -o -name '*.tsx' \) -not -name '*.test.*' \
  -not -path '*__fixtures__*' -exec cat {} + | wc -l
find src-tauri/src -name '*.rs' -not -name '*_tests.rs' \
  -not -path '*session_repository_tests*' -exec cat {} + | wc -l
```

上游文档核对：MyGo `docs/README.md`、`docs/ui/`、`docs/plugins/`、`docs/native-modules.md`（v0.10.0）。
