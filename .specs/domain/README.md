# Domain Spec 索引

长期业务边界：产品「是什么」与「不做什么」的权威陈述。Task Spec 描述单次任务，Domain Spec 描述持续成立的事实与边界，二者冲突时以 Domain Spec 为准，或先修订 Domain Spec。

## 命名与位置

- 路径：`.specs/domain/<domain-id>.md`
- `domain-id`：小写英文短横线主题词（如 `content-variant`、`platform-skill`、`local-first-boundary`），按业务域划分，不按页面或模块划分
- 一个业务域一份文件；边界扩大时拆新域，不无限膨胀单个文件

## 模板

```markdown
# <业务域名>

- domain-id: <domain-id>
- 最近核对: YYYY-MM-DD

## 这个域是什么

用一两句陈述业务对象与它解决的问题。

## 长期边界（不做）

明确排除项。每条给出理由，避免后来者反复试探同一边界。

## 事实与不变量

跨任务持续成立的事实（例如「发布动作保留人工确认」）。每条应可在代码或文档中定位。

## 已废弃

曾经成立、现已失效的陈述，保留时间与原因，不要直接删除。
```

## 待建清单

| domain-id | 范围 | 依据 |
|---|---|---|
| `local-first-boundary` | 本地数据边界、密钥与素材存放、云端依赖的准入条件 | `AGENTS.md` 边界段 |
| `platform-skill` | 腔调 skill 的包形态、平台清单的增删规则 | `docs/platform-matrix.md` |

上表随 0.1 里程碑（Task Spec 收敛）补全。
