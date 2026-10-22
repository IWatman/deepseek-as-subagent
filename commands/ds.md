---
description: 显式派工给 DeepSeek sub-agent（绕过 Claude 自动决策）。用法：/ds <task description>
---

# /ds — Delegate to DeepSeek

把后面跟的任务**强制**派给 DeepSeek 处理，绕过 Claude 的自动决策（"该派 / 不该派"）。
`/ds` 固定调用完整 coding API；明确只做静态文件分析的任务应由主 Agent 选择
`delegate_to_deepseek_readonly`。

模型选择遵循 `delegate-to-deepseek` skill：Flash 是强前沿 coding-agent 档，粗略处于高端 Sonnet 到 Opus 竞争区间，默认用于大多数 coding 任务；Pro 目标约为 Opus / Sol 级，用于复杂 debugging、架构推理、困难跨文件任务或 Flash 已失败后的升级。

注意：截至 2026-10-22，DeepSeek 官方说明 `deepseek-v4-pro` 在 V4.1-Pro 上线前会路由到 V4.1-Flash。如果配置的 Pro 槽位仍使用该别名，就不会产生实际能力升级。具体路由和任务边界见 skill 第 3 节。

## 你要做的

1. 按 `delegate-to-deepseek` skill 的准则准备 `task`、`context` 和必要时的 `model`：
   - 用 Glob / LS 收集涉及的文件路径
   - 摘要项目约定（命名规则、输出 schema、边界）
   - 写明成功标准
   - 普通任务保持默认 Flash；困难任务才显式选择 Pro

2. 调用 `mcp__deepseek__delegate_to_deepseek` 工具，把用户的请求当作 task 传入：

```
用户输入: $ARGUMENTS
```

3. 工具返回后**必须验证**：
   - Read 抽样产物文件
   - 检查数量 / schema sanity
   - 失败按 skill 的 fallback 策略处理

## 不要做的

- ❌ 不要在调用前问用户"你确定要派吗" —— 用户敲 `/ds` 已经是明确指令
- ❌ 不要因为 Pro 可用就默认选择 Pro
- ❌ 不要把真实 provider 模型名传给 `model` 参数
- ❌ 不要在工具返回 ERROR 时直接放弃 —— 按 skill 的 fallback 策略重试或接管
- ❌ 不要把 API key / 凭证塞进 task / context
