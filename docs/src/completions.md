# 补全

Zed 支持两种补全来源：

1. 由语言服务器（LSP）提供的"代码补全"，这些服务器由 Zed 自动安装或通过 [Zed 语言扩展](languages.md) 安装。
2. 由 Zed 自己的 Zeta 模型或外部提供商（如 [GitHub Copilot](#github-copilot) 或 [Supermaven](#supermaven)）提供的"编辑预测"。

## 语言服务器代码补全 {#code-completions}

当有适当的语言服务器可用时，Zed 将提供当前文件中变量名、函数和其他符号的补全。您可以通过将以下内容添加到您的 Zed `settings.json` 文件来禁用这些补全：

```json [settings]
"show_completions_on_input": false
```

您可以使用 `ctrl-space` 或通过从命令面板触发 `editor::ShowCompletions` 操作来手动触发补全。

有关更多信息，请参阅：

- [配置支持的语言](./configuring-languages.md)
- [Zed 支持的语言列表](./languages.md)

## 编辑预测 {#edit-predictions}

Zed 内置支持通过 [Zeta](https://huggingface.co/zed-industries/zeta)（Zed 的开源和开放数据模型）一次预测多个编辑。
编辑预测在您输入时出现，大多数情况下，您可以通过按 `tab` 键接受它们。

有关如何设置和配置 Zed 的编辑预测的更多信息，请参阅[编辑预测文档](./ai/edit-prediction.md)。
