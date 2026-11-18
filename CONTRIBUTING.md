# 贡献指南

腔调（Timbre）处于设计阶段，当前最有价值的贡献是**领域知识与规格**，而不是代码。

## 当前最需要什么

| 方向 | 说明 | 入口 |
|---|---|---|
| 平台腔调修订 | 你在某个平台做内容运营，能指出矩阵里的腔调要点不准、过时或缺失 | 改 `docs/platform-matrix.md` 并写清依据 |
| 规格收敛 | 目标用户、skill 包形态、本地优先边界的具体化 | `.specs/tasks/`、`.specs/domain/` |
| 技术选型论证 | Tauri 2 与 MyGo 的真实取舍证据（启动、内存、内嵌预览、多窗口） | `docs/adr/` |
| 实现 | 选型 ADR 与 Task Spec 落地后才开始 | 见「流程」 |

## 流程

1. **先看现状**：`README.md`、`AGENTS.md`、`.specs/`、`docs/adr/`。项目陈述以这些文件为准，不要按推测补功能。
2. **要改行为，先写 Spec**：功能实现前必须有 Task Spec（`.specs/tasks/<task-id>.md`）；长期业务边界写 Domain Spec（`.specs/domain/<domain-id>.md`）。模板与命名规则见 [`.specs/tasks/README.md`](.specs/tasks/README.md)、[`.specs/domain/README.md`](.specs/domain/README.md)。
3. **涉及运行时/语言，先写 ADR**：用 [`docs/adr/0000-template.md`](docs/adr/0000-template.md) 写成 `docs/adr/<NNNN>-<topic>.md`，并在 [`docs/adr/README.md`](docs/adr/README.md) 索引里登记状态。
4. **提 PR**：一个变更一个分支，不直接在 `main` 上提交。PR 描述里写清改了什么、依据是什么、验证方式。
5. **记录变更**：显著变更在 `CHANGELOG.md` 的 `Unreleased` 下加一行。

## 文档约定

- 语言：中文为主；代码标识符、repo 名、二进制名用英文小写（`timbre`）
- 品牌书写：中文「腔调」，英文「Timbre」，合并书写「腔调（Timbre）」
- 事实与意向分开写：未经验证的判断标注「设计意向」；引用外部调研给出归档路径与日期，只写结论不算依据
- 不虚构能力：文档里不出现尚未实现的特性、命令或界面
- 命名禁忌：`docs/naming.md` 中已排除的名称（Varia、千面、分身、矩阵等）不再引入作为功能名

## 平台腔调贡献的额外要求

改 `docs/platform-matrix.md` 时：

- 在原条目上修订，说明你依据什么改（平台公开规则、数据表现、你的运营实践）
- 在「修订记录」追加一行：日期、变更、依据
- 新增平台按「增删平台规则」走，先登记矩阵，再谈实现

## 边界（不可作为贡献被接受）

- 承诺「自动发布」为默认能力的改动
- 未在 Task Spec 中声明、且没有本地回退方案的云端依赖
- 把密钥或素材默认上传到任何远端的设计
- 绕过「发布动作保留人工确认」的自动化

更完整的安全约束见 [`SECURITY.md`](SECURITY.md)。

## 许可

贡献即同意以 MIT 许可（见 [`LICENSE`](LICENSE)）发布。
