# PicoClaw 项目上下文速查（新会话优先）

最后更新：2026-03-06  
适用仓库：`/Users/xuhui/Work/mowang/picoclaw`

## 0. 使用原则

- 文档与代码冲突时，以代码为准（source of truth）。
- 先看入口与配置模型，再看具体 channel 实现。
- 对“某字段能填什么”的问题，优先查 `pkg/config/config.go` 结构体定义和调用逻辑。

## 1. 运行入口与主链路

- CLI 入口：`cmd/picoclaw/main.go`
- `agent` 命令：`cmd/picoclaw/internal/agent/helpers.go`
- `gateway` 命令：`cmd/picoclaw/internal/gateway/helpers.go`

Gateway 模式核心链路：

1. `LoadConfig` 读取配置（支持环境变量覆盖）
2. `providers.CreateProvider` 创建模型 Provider
3. 创建 `bus.MessageBus`
4. 创建 `agent.AgentLoop`
5. 创建 `channels.Manager` 并启动所有 channel
6. `AgentLoop.Run` 消费入站消息并产出出站消息

关键组件：

- 消息总线：`pkg/bus/bus.go`, `pkg/bus/types.go`
- Agent 主循环：`pkg/agent/loop.go`
- Channel 编排：`pkg/channels/manager.go`

## 2. 配置系统定位

核心配置定义：

- `pkg/config/config.go`
- 默认值：`pkg/config/defaults.go`
- 示例：`config/config.example.json`

加载流程（`pkg/config/config.go`）：

1. 先加载 `DefaultConfig()`
2. 再合并用户 `config.json`
3. 再用环境变量覆盖（`env.Parse`）
4. 执行迁移逻辑（如 `mention_only -> group_trigger.mention_only`）
5. 校验 `model_list`

路径覆盖：

- `PICOCLAW_HOME`：数据目录根（默认 `~/.picoclaw`）
- `PICOCLAW_CONFIG`：配置文件路径（默认 `~/.picoclaw/config.json`）

## 3. Agent 上下文在新会话时会读什么

`pkg/agent/context.go` 会把以下文件纳入 system prompt（自动）：

- `workspace/AGENTS.md`
- `workspace/SOUL.md`
- `workspace/USER.md`
- `workspace/IDENTITY.md`
- `workspace/memory/MEMORY.md`
- 技能摘要（来自 skills 目录）

本文件位于仓库根目录 `PROJECT_CONTEXT.md`，用于开发者/AI 阅读项目，不会默认注入运行时工作区上下文（避免影响最终用户行为）。

## 4. Channel 架构与消息处理

统一抽象：

- `BaseChannel`：`pkg/channels/base.go`
- 管理器：`pkg/channels/manager.go`
- 可选能力接口：`pkg/channels/interfaces.go`

统一群聊触发逻辑入口：

- `BaseChannel.ShouldRespondInGroup(isMentioned, content)`

逻辑优先级（严格按代码）：

1. `isMentioned == true`：一定响应
2. 否则若 `mention_only == true`：不响应
3. 否则若配置了 `prefixes`：仅当前缀命中时响应，且剥离前缀
4. 若都未配置：默认响应所有群消息（宽松默认）

注意：`mention_only=true` 时，未被 @ 的情况下不会继续检查 `prefixes`。

## 5. `group_trigger` 字段可填值（全局结论）

配置模型定义在 `pkg/config/config.go`：

```go
type GroupTriggerConfig struct {
    MentionOnly bool     `json:"mention_only,omitempty"`
    Prefixes    []string `json:"prefixes,omitempty"`
}
```

可填值：

- `mention_only`: `true | false`
- `prefixes`: 字符串数组，如 `["/bot", "/ask "]`

示例：

```json
{
  "channels": {
    "feishu": {
      "enabled": true,
      "app_id": "cli_xxx",
      "app_secret": "xxx",
      "group_trigger": {
        "mention_only": false,
        "prefixes": ["/bot", "/ask "]
      }
    }
  }
}
```

支持 `group_trigger` 的 channel（见 config 结构体）：

- `telegram`, `discord`, `qq`, `dingtalk`, `slack`, `line`, `onebot`, `wecom`, `wecom_app`, `feishu`

兼容迁移：

- `discord.mention_only` 会迁移到 `discord.group_trigger.mention_only`
- `onebot.group_trigger_prefix` 会迁移到 `onebot.group_trigger.prefixes`

## 6. Feishu 快速定位（重点）

实现文件：

- `pkg/channels/feishu/feishu_64.go`
- `pkg/channels/feishu/common.go`
- 工厂注册：`pkg/channels/feishu/init.go`
- 32 位限制：`pkg/channels/feishu/feishu_32.go`

群消息处理流程（`handleMessageReceive`）：

1. 提取 `sender_id/chat_id/message_id`
2. 早期 allowlist 过滤
3. 提取文本与媒体
4. 群聊场景：
   - `isBotMentioned` 判断是否 @ 机器人（依赖 bot open_id）
   - `stripMentionPlaceholders` 去除 `@_user_N` 占位符
   - 调用 `ShouldRespondInGroup`

关键风险点：

- Feishu 文档 `docs/channels/feishu/README.zh.md` 目前未写 `group_trigger`，但代码已支持。
- `Start()` 会先 `fetchBotOpenID`；若失败，`isBotMentioned` 可能恒为 false，`mention_only=true` 时群里会更容易“看起来不触发”。

## 7. 常见问题直达索引

- “`feishu.group_trigger` 能填哪些值？”  
  查：`pkg/config/config.go` 的 `GroupTriggerConfig`

- “群里什么时候会回复？”  
  查：`pkg/channels/base.go` 的 `ShouldRespondInGroup`

- “Feishu 为何 @ 后也不触发？”  
  查：`pkg/channels/feishu/feishu_64.go` 中 `fetchBotOpenID` + `isBotMentioned`

- “哪些 channel 由共享 HTTP 网关处理？”  
  查：`pkg/channels/manager.go` 的 `SetupHTTPServer`（Feishu 是 WebSocket 模式，不走共享 webhook）

- “新会话系统提示词从哪里拼装？”  
  查：`pkg/agent/context.go` 的 `BuildSystemPrompt` / `sourcePaths`

## 8. 实用检索命令

```bash
# 查 group_trigger 定义与使用
rg -n "GroupTrigger|group_trigger|ShouldRespondInGroup" pkg/config pkg/channels

# 查 feishu 群触发相关路径
rg -n "isBotMentioned|fetchBotOpenID|handleMessageReceive|group_trigger" pkg/channels/feishu

# 查配置迁移逻辑
rg -n "migrateChannelConfigs|mention_only|group_trigger_prefix" pkg/config/config.go
```
