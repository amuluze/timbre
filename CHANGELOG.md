# Changelog

本项目的所有显著变更将记录于此。格式遵循 [Keep a Changelog](https://keepachangelog.com/zh-CN/1.1.0/)，版本遵循 [SemVer](https://semver.org/lang/zh-CN/)。

## [Unreleased]

## [0.0.1] - 2026-11-18

> 0.0.1 对应仓库首个提交（tag `v0.0.1`），无更早的版本历史。

### Added

- 项目奠基：命名（腔调 / Timbre）、定位（本地优先开源运营平台，平台腔调 skill 体系）、平台腔调矩阵设计意向、技术选型候选（Tauri 2 / MyGo）与 Roadmap。
- 命名调研归档 `docs/naming.md`（撞名证据与工程标识决策）。
- 文档体系骨架：`.specs/tasks/`、`.specs/domain/`（模板与命名规则）、`docs/adr/`（索引与 `0000-template.md`）、`CONTRIBUTING.md`、`SECURITY.md`。
- 平台腔调矩阵自 `README.md` 迁出为 `docs/platform-matrix.md`，确立其为权威来源，附修订记录、增删平台规则与「设计意向 / 已验证」依据状态。

### Changed

- `README.md`：技术选型段的选型记录错位引用（原指向命名调研）改为指向 ADR 流程；axiom 页面替换可行性调研标注为正文尚未归档；矩阵表改为摘要并指向权威来源。
- `AGENTS.md`：平台清单、矩阵修订、规格与 ADR 路径改为引用单一来源；补「事实与意向分开写」约定。
- `CHANGELOG.md`：矩阵修订不再记录于此（改由 `docs/platform-matrix.md` 承担），仅在平台增删时记一行。
