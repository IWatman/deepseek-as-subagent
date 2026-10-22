# DeepSeek model selection

The public delegation API supports exactly two stable model profiles:

- `flash`
- `pro`

All four delegation entry points default to `model="flash"` when the argument is omitted. Treat `flash` as the default execution profile and `pro` as an escalation intent for complex debugging, architecture work, high-risk cross-file reasoning, or a retry after Flash is insufficient. These are stable routing slots, not guaranteed model tiers: the actual provider IDs and reasoning effort are user-configurable, and both slots may resolve to the same model. DeepSeek's [2026-09-10 official notice](https://www.deepseek.com/en/news/deepseek-v4-1-flash/) stated that `deepseek-v4-pro` requests would route to V4.1-Flash until V4.1-Pro launches, so a `pro` config value alone does not prove that a stronger model is being used. Check current provider routing before relying on Pro as a capability upgrade.

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
