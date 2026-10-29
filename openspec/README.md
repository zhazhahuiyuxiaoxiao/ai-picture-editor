# OpenSpec 维护流程

本目录是既有项目的渐进基线补录，不代表曾经执行过的提案或开发历史。官方 [existing projects 指南](https://github.com/Fission-AI/OpenSpec/blob/main/docs/existing-projects.md) 支持逐步采用；本次仅选择核心流程，并未覆盖全部接口。

- `specs/<capability>/spec.md`：当前源码证实的行为，路径依据指向仓库根目录。
- `changes/<change-id>/`：待实施变更。没有已批准的新业务变更时仅保留 `.gitkeep`。
- `config.yaml`：项目语境及产物约束。

后续流程：先调查代码 → 编写 proposal（原因、范围、影响）→ 增量 specs（新增/修改/删除的需求与场景）→ design（技术方案与取舍）→ tasks（实施和验证）→ validate → 按已确认方案 apply → 核验行为和任务证据 → archive 并同步 specs。设计产物须按 schema 的依赖关系完成；归档不能代替用户验收。

本次使用现有 OpenSpec 1.14.2，在仓库根目录执行：

```bash
OPENSPEC_TELEMETRY=0 openspec validate --all --strict --no-interactive
git diff --check
```

这两项只检查规范结构和补丁格式。构建、测试、网络、数据库、外部模型、平台或生产行为仍需独立验证，参见 [工程基线](../docs/engineering-baseline.md)。已有未完成的 changes 必须保留，不因补录而强行归档。
