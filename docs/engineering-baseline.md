# AI 修图智能体个人副本：工程基线

- 核对日期：2026-10-29。
- 基线分支：`main`；完整提交：`49f6ad8c96740a68b7e6228a47e8e2be6876eb34`。
- 文档工作分支：`codex/openspec-docs-20261029`。
- 本文为源码现状记录，不代表线上版本、生产可用性或独立开发贡献。

## 用途与来源

这是 `yuyuanweb/ai-picture-editor` 的个人副本，保留编程导航/鱼皮及上游 README 的署名和教程信息。本次仅补工程文档与 OpenSpec，不声称独立原创、毕业设计扩展或生产上线。源码包含图片素材、编辑会话、工具运行、Agent 计划与画布前端。

## 源码与能力映射

| 能力 | 源码 / 现有测试 |
| --- | --- |
| Cookie 当前用户与会话归属 | `backend/app/deps.py`、`backend/app/routers/sessions.py`；`backend/tests/test_auth.py`、`test_sessions.py` |
| 图层文档、历史、撤销重做 | `backend/app/services/sessions.py`、`backend/app/layers.py`；`backend/tests/test_sessions.py`、`test_edits.py` |
| Agent 规划、确认、取消、重试 | `backend/app/services/agent.py`、`backend/app/agent/plan.py`；`backend/tests/test_agent.py` |
| 后台工具执行 | `backend/app/worker.py`、`backend/app/tasks/tools.py`、`backend/app/services/tools.py` |
| 前端构建与代理 | `frontend/package.json`、`frontend/vite.config.ts` |

表中省略目录的测试名均在 `backend/tests/`。测试存在不表示本次已执行。

## 最短本地启动路径

需要 Python >=3.13、uv、兼容当前锁定 Vite/Node 依赖的 Node/npm，以及隔离的 PostgreSQL、Redis、S3/MinIO 服务。外部模型先选 mock，真实模型需自行配置 `<YOUR_API_KEY>`。

仓库根有 `.env.example`，`backend/.env.example` 不存在；不要照后文上游步骤在 backend 内复制不存在的示例。由维护者审阅字段后准备本地环境变量（`DATABASE_URL`、`REDIS_URL`、S3 相关字段和 `JWT_SECRET` 等），使用隔离资源，不提交真实值。

```bash
# 前提：依赖安装和本地基础设施准备已获授权
cd backend
uv sync --all-extras
# 独立测试库结构准备完成后，启动 API
uv run uvicorn app.main:app --reload --host 127.0.0.1 --port 7302
# 新终端，在 backend 目录启动 worker
uv run arq app.worker.WorkerSettings
# 新终端，从仓库根目录
cd frontend
npm install
npm run dev
```

前端 `http://127.0.0.1:7301` 代理 `/api`、`/events` 到后端 7302。建库、`alembic upgrade head` 和 Docker 服务启动均会写状态，须另行授权并确认环境；本次没有执行。项目带有 `backend/alembic.ini` 和 `backend/migrations/` 作为迁移入口。

## 验证与验收

后端：`cd backend && uv run pytest`（pytest 配置在 pyproject.toml）；前端：`cd frontend && npm run lint`、`npm run build`。先建立独立测试资源再运行：`backend/tests/conftest.py` 会准备对象存储 bucket，使用 Redis 测试库并按 `test_` 前缀删除数据库用户，不是纯内存/无副作用测试。图片 provider 被测试 fixture 替换为 mock，但不等于隔离了数据库和存储。

本次已完成：核对 Git 基线、阅读上述源码和命令定义；OpenSpec 1.14.2 严格校验 2/2 通过，`git diff --check` 通过，本次新增/改写文档的 11 个本地相对链接检查无缺失（保留的上游原文不计入）。
本次未运行：依赖安装、应用启动、构建、业务测试、真实平台/外部服务集成及生产部署。原因是本次范围仅限文档与流程，不写运行数据、不调用收费服务。

- [ ] 在隔离环境按上面的路径启动。
- [ ] 按每个 [核心 spec](../openspec/specs/) 验收正常、空输入/不存在和失败场景。
- [ ] 变更身份、权限或数据语义时核对所有消费者，补充原行为与失败路径验证。
- [ ] PR 记录实际命令、结果与未覆盖项，不将“可运行命令”写成“已通过”。

## 已知边界与重新评估条件

- 本次不使用过去的探索记录充当当前已实施功能；可复用编辑方案等毕业设计扩展未纳入此基线。
- 当前用户与素材/会话隔离必须保持；修改公共工具参数、图层文档、事件和会话协议需同时核对前端与 worker。
- 测试需要外部基础设施并执行清理；即使使用 mock 模型，也不可对真实业务库运行。
- 本次未测模型质量、SSE 断线、worker 故障、并发编辑和生产存储权限；公网部署前应另立验收范围。
- 上游 README 的运行/部署段落保留来源，但其环境路径、Node 版本建议和生产开放端口不能直接视为本副本已验证方案。

## 文档维护

[README](../README.md) 提供入口，[AGENTS](../AGENTS.md) 约束开发，[OpenSpec](../openspec/README.md) 保存行为与后续变更证据。源码或依赖变化后同步复核本基线。
