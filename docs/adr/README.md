# ADR 索引

架构决策记录（Architecture Decision Record）。凡影响运行时、语言、数据边界、外部依赖的决策，先落 ADR 再动代码。

## 命名与状态

- 路径：`docs/adr/<NNNN>-<topic>.md`，四位序号从 `0001` 起，`topic` 用小写英文短横线
- 状态：`Proposed`（提议中）→ `Accepted`（已采纳）/ `Rejected`（已否决）；被取代时改为 `Superseded by ADR-<NNNN>`
- ADR 一旦 `Accepted` 不再改写正文；决策变化写新 ADR 并在旧文件顶部标注取代关系
- 模板见 [`0000-template.md`](0000-template.md)

## 索引

| 序号 | 标题 | 状态 | 日期 |
|---|---|---|---|
| [0001](0001-desktop-runtime.md) | 桌面运行时选型 | Proposed | 2026-11-18 |

## 待写清单

| 主题 | 触发条件 | 现状 |
|---|---|---|
| Agent 运行时语言与形态 | ADR-0001 采纳后（Roadmap 0.2） | 参考 axiom 形态，未决策 |
| 腔调 skill 包的执行形态 | 0.1 规格收敛后 | 未决策 |

## 已归档材料

- **axiom 页面替换可行性调研**：正文见 [`docs/research/axiom-page-migration.md`](../research/axiom-page-migration.md)（2026-11-18），已作为 ADR-0001 的证据输入。

上表在相应 ADR 落地后逐条划除。
