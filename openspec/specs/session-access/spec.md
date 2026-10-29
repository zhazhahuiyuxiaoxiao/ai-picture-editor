# 编辑会话归属

## Purpose
记录服务端当前用户解析及编辑会话访问边界，维护既有身份和数据归属语义。依据：`backend/app/deps.py`、`backend/app/routers/sessions.py`；现有测试：`backend/tests/test_sessions.py`。

## Requirements

### Requirement: 要求有效登录
会话接口 SHALL 通过 CurrentUser 解析登录 Cookie 和本地用户记录。

#### Scenario: 未登录或无效 Cookie
- **WHEN** Cookie 缺失、token 无效或对应用户不存在
- **THEN** 依赖返回 401，不继续访问会话

### Requirement: 按用户限制会话和素材
会话接口 SHALL 只加载当前用户可访问的会话和素材。

#### Scenario: 创建自己的会话
- **WHEN** 已登录用户以自己的有效素材创建会话
- **THEN** 系统返回 201 及新会话详情

#### Scenario: 访问另一个用户的会话
- **WHEN** 当前用户请求其他用户会话 ID
- **THEN** 接口返回 404，且该会话不出现在当前用户列表

#### Scenario: 素材不存在
- **WHEN** 创建会话指定不存在的当前素材
- **THEN** 接口返回 404，不创建成功会话
