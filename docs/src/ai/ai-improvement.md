# Zed AI 改进

## 代理面板

### 选择加入

当您通过以下任何方式使用代理面板时：

- [Zed 托管的模型](./subscription.md)
- [通过 API 密钥连接非 Zed AI 服务](./llm-providers.md)
- 使用[外部代理](./external-agents.md)

Zed 不会持久存储用户内容或使用用户内容来评估和/或改进我们的 AI 功能，除非明确与 Zed 共享。每次共享都是选择加入的，共享一次不会导致未来的内容或数据再次共享。

> 请注意，评分响应会将与该响应相关的数据发送到 Zed 的服务器。
> **_如果您不希望数据持久存储在 Zed 的服务器上，请不要评分_**。没有您明确评分响应，我们不会收集数据来改进我们的代理服务。

当通过 Zed 托管的模型使用上游服务时，我们要求服务提供商保证您的用户内容不会被用于训练模型。

| 提供商    | 无训练保证                                              | 零数据保留 (ZDR)                                                                                                                              |
| --------- | ------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------- |
| Anthropic | [是](https://www.anthropic.com/legal/commercial-terms) | [是](https://privacy.anthropic.com/en/articles/8956058-i-have-a-zero-data-retention-agreement-with-anthropic-what-products-does-it-apply-to) |
| Google    | [是](https://cloud.google.com/terms/service-terms)     | **否**，正在进行中                                                                                                                            |
| OpenAI    | [是](https://openai.com/enterprise-privacy/)           | [是](https://platform.openai.com/docs/guides/your-data)                                                                                      |

> Zed 目前通过 [Google AI Studio](https://ai.google.dev/aistudio) 支持使用 Gemini 模型，该平台**_不_**支持 ZDR。我们正在迁移到 [Vertex AI](https://cloud.google.com/vertex-ai?hl=en)，该平台**_支持_** ZDR，完成该迁移后，我们将为所有使用 Zed 托管的 Google/Gemini 模型的用户提供 ZDR。

> 如果上游模型提供商的 ZDR 对您很重要，_请暂时不要使用 Gemini 模型_。但是，您的数据永远不会被 Zed 托管的任何模型提供商用于训练目的。

当您使用自己的 API 密钥或外部代理时，**Zed 无法控制该服务提供商如何使用您的数据。**
您应参考与每个服务提供商的协议，了解适用的条款和条件。

### 我们收集的数据

对于您明确与我们共享的提示，Zed 可能会存储这些提示的副本以及有关代理面板特定使用的其他数据。

此数据包括：

- 提供给代理的提示
- 您包含的任何评论
- 关于代理线程的产品遥测数据
- 关于您的 Zed 安装的元数据

### 数据处理

收集的数据存储在 Snowflake 中，这是一个我们跟踪其他指标的私有数据库。我们定期审查此数据，以改进我们的整体代理方法并通过我们的系统提示、工具使用等来完善产品。我们确保任何包含的数据都是匿名的，并且不包含敏感信息（访问令牌、用户 ID、电子邮件地址等）。

## 编辑预测

默认情况下，当使用 Zed 编辑预测时，Zed 不会持久存储用户内容或使用用户内容来训练其模型。

### 选择加入

从事开源许可项目的用户可以选择加入提供模型改进反馈。此选择加入是按项目进行的。如果您从事多个开源项目并希望提供模型改进反馈，您必须为每个单独的项目选择加入。

在您未选择加入的其他项目上工作时，Zed 不会持久存储用户内容或使用用户内容来训练其模型。

您可以在以下位置确切查看 Zed 如何检测开源许可证：[license_detection.rs](https://github.com/zed-industries/zed/blob/main/crates/zeta/src/license_detection.rs)。

### 排除项

Zed 会故意将某些文件完全排除在预测编辑之外，即使您已选择加入模型改进反馈。

您可以通过从命令面板打开 `zed: open default settings` 来检查此排除列表：

```json [settings]
{
  "edit_predictions": {
    // 表示应禁用编辑预测的文件的 glob 列表。
    // 已经包含了一个合理的默认 glob 列表。
    // 对此列表的任何添加都将与默认列表合并。
    "disabled_globs": [
      "**/.env*",
      "**/*.pem",
      "**/*.key",
      "**/*.cert",
      "**/*.crt",
      "**/secrets.yml"
    ]
  }
}
```

用户可以通过在其 Zed settings.json 中的 [`edit_predictions.disabled_globs`](https://zed.dev/docs/configuring-zed#edit-predictions) 添加其他路径和/或文件扩展名来明确排除它们：

```json [settings]
{
  "edit_predictions": {
    "disabled_globs": ["secret_dir/*", "**/*.log"]
  }
}
```

### 我们收集的数据

对于您已选择加入的开源项目，Zed 可能会存储对 Zed AI 预测服务的请求和响应的副本。

此数据包括：

- 编辑预测
- 光标周围的缓冲区内容的一部分
- 最近的几次编辑
- 当前缓冲区大纲
- 来自语言服务器的诊断（错误、警告等）

### 数据处理

收集的数据存储在 Snowflake 中，这是一个我们跟踪其他指标的私有数据库。我们定期审查此数据，以选择训练样本包含在我们的模型训练数据集中。我们确保任何包含的数据都是匿名的，并且不包含敏感信息（访问令牌、用户 ID、电子邮件地址等）。此训练数据集在 [huggingface.co/datasets/zed-industries/zeta](https://huggingface.co/datasets/zed-industries/zeta) 公开可用。

### 模型输出

然后我们使用此训练数据集来微调 [Qwen2.5-Coder-7B](https://huggingface.co/Qwen/Qwen2.5-Coder-7B)，并将生成的模型在 [huggingface.co/zed-industries/zeta](https://huggingface.co/zed-industries/zeta) 提供。

## 适用条款

请参阅 [Zed 服务条款](https://zed.dev/terms-of-service) 了解更多信息。
