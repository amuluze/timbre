# Task Spec 索引

功能实现前的规格。一个任务一份文件，先有 Spec，再谈实现。

## 命名与位置

- 路径：`.specs/tasks/<task-id>.md`
- `task-id`：小写英文短横线主题词（如 `platform-skill-pack`、`variant-review-flow`），不复用、不编号
- 一个文件只描述一个可独立验收的任务；跨多任务的工作拆成多份，用「依赖」字段串联

## 模板

```markdown
# <任务标题>

- task-id: <task-id>
- 状态: draft | ready | in-progress | done
- 关联 Domain Spec: `.specs/domain/<domain-id>.md`（无则写「待建」）

## 背景

为什么现在要做这件事；不做的代价。

## 目标与范围

做什么、不做什么（显式排除项）。

## 验收标准

可验证的条目，逐条可检查。避免「体验良好」这类不可判定描述。

## 依赖与风险

前置任务、未决 ADR、外部约束（合规、平台 API 限制）。

## 本地优先边界

是否引入云端依赖。若有，逐项写明用途与本地回退方案（无云端依赖则写「无」）。
```

## 约定

- 涉及运行时或语言的改动，先有 ADR（`.specs/tasks/` 之前先落 `docs/adr/`），再写 Task Spec
- 云端依赖必须在本文件中显式声明并给出本地回退，未声明的实现不予合入
- 完成后把「状态」改为 `done`，并在 `CHANGELOG.md` 对应版本记录一行
