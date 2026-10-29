# AI 修图智能体个人副本：开发约定

## 范围与事实来源

先核对分支、工作区和 [工程基线](docs/engineering-baseline.md)。阅读调用入口、服务及消费者后再修改。保留上游署名、许可与有效说明；快照不用于证明个人原创或历史贡献。本次文档整理不授权改变业务行为。

技术栈与命令以 `backend/pyproject.toml`、锁文件和 `frontend/package.json` 为准。更改 Cookie、素材/会话归属、工具注册表、图层修订号、SSE 消息或 worker 输入时必须核对前后端及异步消费者。测试会操作数据库、Redis 和对象存储，禁止指向真实资源。

## 变更与安全边界

- 登录、权限、删除、数据结构、支付或公开接口变化，先列出需改变和需保留的行为；调查所有调用方，确认业务取舍再实现。
- 不读取或提交真实凭据、私有环境配置、上传资源、运行数据库或日志；文档示例用 `<YOUR_VALUE>` 占位。
- 不自动运行迁移、种子初始化、部署、收费模型调用或破坏性操作。未授权时不提交、推送、合并。
- 外部服务、文件、平台能力及存储失败需有明确结果；不能把 mock、代码阅读或格式校验当成业务验收。

## 验证

后端：`cd backend && uv run pytest`（pytest 配置在 pyproject.toml）；前端：`cd frontend && npm run lint`、`npm run build`。先建立独立测试资源再运行：`backend/tests/conftest.py` 会准备对象存储 bucket，使用 Redis 测试库并按 `test_` 前缀删除数据库用户，不是纯内存/无副作用测试。图片 provider 被测试 fixture 替换为 mock，但不等于隔离了数据库和存储。

文档修改执行 `git diff --check` 和 `OPENSPEC_TELEMETRY=0 openspec validate --all --strict --no-interactive`。业务检查应在隔离环境、依赖准备完成后执行，并报告未运行项。现有测试不等于完整覆盖。

## OpenSpec

按 [OpenSpec 流程](openspec/README.md) 从已实现基线维护 `specs/`；行为变更先写 `changes/<id>/proposal.md`、增量 specs、design 和 tasks。记录验证证据再归档，不把未来计划写成已实现功能。
