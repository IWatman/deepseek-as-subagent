# DeepSeek model selection

The public delegation API supports exactly two stable model profiles:

- `flash`
- `pro`

All four delegation entry points default to `model="flash"` when the argument is omitted. Flash is the default strong frontier coding-agent tier, roughly high-end Sonnet to Opus-competitive depending on task. DeepSeek V4.1-Flash-1017 beats Opus 5 on DeepSWE v1.1, Automation-Bench, Agents' Last Exam, and Apex Bench, but trails on Terminal-Bench 4.0, ProgramBench, and NL2Repo-Bench (official model card: https://huggingface.co/deepseek-ai/DeepSeek-V4.1-Flash-1017). Pro is the intended flagship difficult-task tier, roughly Opus/Sol-class, for complex debugging, architecture, difficult cross-file reasoning, or retry after Flash is insufficient. As of 2026-10-22, DeepSeek says `deepseek-v4-pro` requests route to V4.1-Flash until V4.1-Pro launches; if the configured Pro slot still uses that alias, it is not a real capability upgrade: https://www.deepseek.com/en/news/deepseek-v4-1-flash/.

The actual provider model IDs and reasoning effort for each slot are user-configurable in `~/.deepseek-mcp/config.json`:

```json
{
  "flash": "deepseek-flash",
  "flash_reasoning_effort": "high",
  "pro": "deepseek-v4-pro",
  "pro_reasoning_effort": "high",
  "_reasoning_effort_options": ["provider-default", "none", "low", "high", "max"]
}
```

`_reasoning_effort_options` is a documentation hint only and is ignored at runtime. The effective fields are `flash_reasoning_effort` and `pro_reasoning_effort`. Supported values are `provider-default`, `none`, `low`, `high`, and `max`; `provider-default` sends no reasoning controls, `none` disables thinking, and the other values enable thinking at the selected effort.

Reasoning controls are sent only when the corresponding `*_reasoning_effort` field is explicitly present. When absent, the provider's default applies. New installer-generated configs set both slots to `high`.

The host passes only `model="flash"` or `model="pro"`. The configured provider model IDs and reasoning effort remain internal to deepseek-mcp.

A background job keeps the profile, resolved provider model, and configured reasoning behavior selected when it starts. Steering messages do not change them for an already-running job.

A single `model` config field maps to both slots; do not combine it with `flash` or `pro`.
