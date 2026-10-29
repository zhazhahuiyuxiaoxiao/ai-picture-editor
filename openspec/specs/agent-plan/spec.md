# Agent 计划执行

## Purpose
记录自然语言规划后的执行门槛与错误状态，避免把计划生成误认为工具执行完成。依据：`backend/app/services/agent.py`、`backend/app/agent/plan.py`、`backend/app/routers/sessions.py`；现有测试：`backend/tests/test_agent.py`。

## Requirements

### Requirement: 多步计划等待确认
Agent 服务 SHALL 将超过一步的计划保存为待确认状态，再由确认入口推进。

#### Scenario: 生成多步计划
- **WHEN** 规划得到超过一个有效步骤
- **THEN** 对话状态为 queued，未确认前不派发步骤执行

#### Scenario: 确认计划
- **WHEN** 对话处于 queued 或存在 waiting 步骤，且调用确认入口
- **THEN** 待确认步骤转为 pending 并标记 approved，对话开始推进可执行步骤

#### Scenario: 没有待确认内容
- **WHEN** 对话不在 queued 且没有 waiting 步骤
- **THEN** 确认接口返回 409

### Requirement: 显式记录规划失败
Agent 服务 SHALL 保存规划不可用或规划异常的失败结果。

#### Scenario: 规划器不可用
- **WHEN** 规划器报告 PlannerUnavailable
- **THEN** 对话状态为 failed，保存错误内容而不启动工具步骤

#### Scenario: 其他规划异常
- **WHEN** 规划期间发生其他异常
- **THEN** 服务回滚当时事务并保存带“规划失败，请重试”的失败对话
