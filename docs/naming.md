# 命名调研：腔调 / Timbre

> 2026-11-18 定稿。本文档归档命名决策、撞名证据与工程标识规则，供后续改名评估与商标清查引用。

## 定稿

| 项 | 值 |
|---|---|
| 品牌（中文） | 腔调 |
| 英文名 | Timbre |
| 口号 | 一稿千面，各凭腔调 |
| Repo / Go 二进制 | `timbre` |
| JS/TS 配套包名 | `@timbre/cli`（scoped，npm 裸名 `timbre` 已占用） |
| 域名候选 | `timbre.fm`、`qiangdiao.dev`（DNS 无解析记录，大概率可注） |

语义：timbre 即音色——同一旋律经不同乐器发出不同的声；腔调即同一件事在不同平台的讲法。产品心智与名字同构。

## 撞名证据（2026-11-18 调研）

| 候选 | 证据 | 结论 |
|---|---|---|
| Varia / 变奏 | VaryContent（同品类竞品：一稿转多平台）；Varia AI 公司 ×3 | 弃 |
| 千面 | 千面AI（杭州，电商创意引擎）、千面动捕（QuickMagic）、千面AI模特 | 弃 |
| 分身 | 已成品类通用词：Second Me 自称「AI 分身平台」，扣子、FansNetwork 同用 | 弃（不可独占） |
| 矩阵 | 新榜「矩阵通」在先；且为行业黑话，无法商标化 | 弃 |
| 涟漪 / Echo / Relay / Beacon / Chorus | 英文侧重度占用（Ripple、AI-echo、relay.app、GitLab Beacon 等） | 弃 |
| 号手 | 搜索未见直接撞名 | 备选（本土传播强，英文弱） |
| 母题 / Motif | 搜索未见强撞名 | 备选（国际化好，本土传播弱） |
| **腔调 / Timbre** | 搜索未见同名 AI 内容产品；npm `timbre` 为 timbre.js（JS 音频合成库，MIT，已沉寂，mohayonao/timbre.js） | **定稿** |

## 已知同名与处理

- **timbre.js / npm `timbre`**：不同领域（音频合成 vs 运营平台）、项目沉寂，混淆成本可接受。Go 生态无冲突；JS/TS 一律 scoped 包名。
- **域名**：`timbre.dev/.sh/.app/.cc` 有解析记录，按已注册处理；候选用 `timbre.fm` / `qiangdiao.dev`（注册前以 registrar 查询为准）。

## 遗留清查（对外发布前）

- [ ] 中国商标网 9 / 35 / 38 / 42 类检索「腔调」「Timbre」。
- [ ] GitHub 全站同名 repo 活跃度复查。
- [ ] App Store / 各平台应用商店重名检索。
