# 配置

在 Zed 中使用 AI 时，您可以配置多个维度：

1. 您可以使用哪些 LLM 提供商
   - Zed 的托管模型，需要[身份验证](../accounts.md)和[订阅](./subscription.md)
   - [使用您自己的 API 密钥](./llm-providers.md)，不需要订阅
   - 使用[外部代理如 Claude Code](./external-agents.md)，不需要订阅
2. [模型参数和使用](./agent-settings.md#model-settings)
3. [与代理面板的交互](./agent-settings.md#agent-panel-settings)

## 完全关闭 AI

我们希望尊重那些希望完全不与 AI 交互的用户。
为此，请将以下键添加到您的 `settings.json` 中：

```json [settings]
{
  "disable_ai": true
}
```

阅读[以下博客文章](https://zed.dev/blog/disable-ai-features)了解更多关于我们推广此功能的动机，同时我们也鼓励用户探索 AI 辅助编程。
