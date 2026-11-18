# AGENTS.md

给在本仓库工作的 AI Agent（与人类协作者）的项目说明。

## 项目

腔调（Timbre）：本地优先的开源运营平台。内置各平台（平台清单以 `docs/platform-matrix.md` 为准）的内容腔调 skill（小写），一次创作，按平台产出变体；发布由人掌控。参考 axiom 的形态（自研 Agent 运行时 + skill 体系 + 本地工作区）。

**当前状态：设计阶段，无代码。** 一切陈述以 README 与 docs/ 为准，不要虚构已实现的能力。

## 约定

- 文档语言：中文为主；代码标识符与 repo 名用英文小写（`timbre`）。
- 品牌书写：中文「腔调」，英文「Timbre」，合并书写「腔调（Timbre）」；repo/二进制一律 `timbre`。
- 规格先行：功能实现前先有 Task Spec（`.specs/tasks/<task-id>.md`，task-id 为小写英文短横线主题词，一个任务一份文件），长期业务边界写 Domain Spec（`.specs/domain/<domain-id>.md`）。模板与命名规则见 [`.specs/tasks/README.md`](.specs/tasks/README.md)、[`.specs/domain/README.md`](.specs/domain/README.md)。
- 技术选型：桌面运行时已由 `docs/adr/0001-desktop-runtime.md` 收敛为 MyGo + 原生 UI（Go + `ui` 包，状态 Accepted）；界面与宿主统一在 Go，无 Node 工具链。Agent 运行时在 Go 进程内的形态尚未决策。涉及运行时/语言的改动，先补 ADR（`docs/adr/<NNNN>-<topic>.md`，模板 `docs/adr/0000-template.md`）再动代码。
- 平台腔调矩阵是设计意向而非事实：权威来源是 `docs/platform-matrix.md`，修订只改该文件并在其「修订记录」中注明依据；`CHANGELOG.md` 仅在平台增删时记一行。
- 事实与意向分开写：未经验证的判断标「设计意向」；引用外部调研须给出归档路径与日期，只写结论不算依据。

## 边界

- 不承诺「自动发布」为默认能力：实际发布动作保留人工确认；平台 API 自动化的合规边界在 0.5 里程碑评估。
- 密钥与素材留在本地；任何云端依赖需在 Task Spec 中显式声明并给出本地回退。
- 命名撞名调研结论见 `docs/naming.md`：Varia/千面/分身/矩阵等已排除，勿再引入作为功能名。
- 安全边界与漏洞上报见 `SECURITY.md`；贡献流程与不可接受的改动见 `CONTRIBUTING.md`。
