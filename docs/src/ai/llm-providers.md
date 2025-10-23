# LLM 提供程序

要在 Zed 中使用 AI，您需要至少设置一个大型语言模型提供程序。

您可以通过订阅 [Zed 的某个计划](./plans-and-usage.md)，或使用您已有的受支持提供程序的 API 密钥来实现。

## 使用您自己的密钥 {#use-your-own-keys}

如果您已经拥有现有 LLM 提供程序（如 Anthropic 或 OpenAI）的 API 密钥，您可以将它们添加到 Zed 中，并**_免费_**使用代理面板的全部功能。

要将现有 API 密钥添加到给定的提供程序，请转到代理面板设置（`agent: open settings`），找到所需的提供程序，将密钥粘贴到输入框中，然后按回车键。

> 注意：API 密钥_不会_以纯文本形式存储在您的 `settings.json` 中，而是存储在您操作系统的安全凭据存储中。

## 支持的提供程序

Zed 提供广泛的"使用您自己的密钥" LLM 提供程序列表

- [Amazon Bedrock](#amazon-bedrock)
- [Anthropic](#anthropic)
- [DeepSeek](#deepseek)
- [GitHub Copilot Chat](#github-copilot-chat)
- [Google AI](#google-ai)
- [LM Studio](#lmstudio)
- [Mistral](#mistral)
- [Ollama](#ollama)
- [OpenAI](#openai)
- [OpenAI API 兼容](#openai-api-compatible)
- [OpenRouter](#openrouter)
- [Vercel](#vercel-v0)
- [xAI](#xai)

### Amazon Bedrock {#amazon-bedrock}

> 支持使用支持流式工具使用的模型的工具使用。
> 更多详细信息可以在 [Amazon Bedrock 的工具使用文档](https://docs.aws.amazon.com/bedrock/latest/userguide/conversation-inference-supported-models-features.html) 中找到。

要使用 Amazon Bedrock 的模型，需要 AWS 身份验证。
确保您的凭据设置了以下权限：

- `bedrock:InvokeModelWithResponseStream`
- `bedrock:InvokeModel`

您的 IAM 策略应类似于：

```json [settings]
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "bedrock:InvokeModel",
        "bedrock:InvokeModelWithResponseStream"
      ],
      "Resource": "*"
    }
  ]
}
```

完成后，选择两种身份验证方法之一：

#### 通过命名配置文件进行身份验证（推荐）

1. 确保您已安装 AWS CLI 并使用命名配置文件配置
2. 打开您的 `settings.json`（`zed: open settings`）并在 `language_models` 下包含 `bedrock` 键，使用以下设置：
   ```json [settings]
   {
     "language_models": {
       "bedrock": {
         "authentication_method": "named_profile",
         "region": "your-aws-region",
         "profile": "your-profile-name"
       }
     }
   }
   ```

#### 通过静态凭据进行身份验证

虽然可以通过在代理面板设置 UI 中直接输入您的 AWS 访问密钥和密钥来配置，但我们建议使用命名配置文件以获得更好的安全实践。
为此：

1. 在 [IAM 控制台](https://us-east-1.console.aws.amazon.com/iam/home?region=us-east-1#/users) 中创建一个您可以承担的 IAM 用户。
2. 为该用户创建安全凭据，保存并妥善保管。
3. 使用 (`agent: open settings`) 打开代理配置并转到 Amazon Bedrock 部分
4. 将步骤 2 中的凭据复制到相应的 **访问密钥 ID**、**秘密访问密钥** 和 **区域** 字段中。

#### 跨区域推理

Zed 的 Amazon Bedrock 实现对所有支持它的模型和区域组合使用[跨区域推理](https://docs.aws.amazon.com/bedrock/latest/userguide/cross-region-inference.html)。
通过跨区域推理，您可以将流量分布到多个 AWS 区域，从而实现更高的吞吐量。

例如，如果您从 `us-east-1` 使用 `Claude Sonnet 3.7 Thinking`，它可能会在美国区域之间处理，即：`us-east-1`、`us-east-2` 或 `us-west-2`。
跨区域推理请求保持在数据原始所在地理位置的 AWS 区域内。
例如，在美国内发出的请求保持在美国的 AWS 区域内。

尽管数据仅存储在源区域中，但您的输入提示和输出结果在跨区域推理期间可能会移动到源区域之外。
所有数据将通过 Amazon 的安全网络加密传输。

我们将尽最大努力支持每个模型的跨区域推理，请参阅 [跨区域推理方法代码](https://github.com/zed-industries/zed/blob/main/crates/bedrock/src/models.rs#L297)。

有关最新支持的地区和模型，请参阅 [跨区域推理支持的模型和区域](https://docs.aws.amazon.com/bedrock/latest/userguide/inference-profiles-support.html)。

### Anthropic {#anthropic}

您可以通过在代理面板的模型下拉菜单中选择 Anthropic 模型来使用它们。

1. 注册 Anthropic 并[创建 API 密钥](https://console.anthropic.com/settings/keys)
2. 确保您的 Anthropic 账户有积分
3. 打开设置视图（`agent: open settings`）并转到 Anthropic 部分
4. 输入您的 Anthropic API 密钥

即使您为 Claude Pro 付费，您仍然需要[支付额外积分](https://console.anthropic.com/settings/plans)才能通过 API 使用它。

如果定义了 `ANTHROPIC_API_KEY` 环境变量，Zed 也会使用它。

#### 自定义模型 {#anthropic-custom-models}

您可以通过将以下内容添加到您的 Zed `settings.json` 中来向 Anthropic 提供程序添加自定义模型：

```json [settings]
{
  "language_models": {
    "anthropic": {
      "available_models": [
        {
          "name": "claude-3-5-sonnet-20240620",
          "display_name": "Sonnet 2024-六月",
          "max_tokens": 128000,
          "max_output_tokens": 2560,
          "cache_configuration": {
            "max_cache_anchors": 10,
            "min_total_token": 10000,
            "should_speculate": false
          },
          "tool_override": "some-model-that-supports-toolcalling"
        }
      ]
    }
  }
}
```

自定义模型将列在代理面板的模型下拉菜单中。

您可以通过将模型配置中的模式更改为 `thinking` 来配置模型使用[扩展思维](https://docs.anthropic.com/en/docs/about-claude/models/extended-thinking-models)（如果支持），例如：

```json [settings]
{
  "name": "claude-sonnet-4-latest",
  "display_name": "claude-sonnet-4-thinking",
  "max_tokens": 200000,
  "mode": {
    "type": "thinking",
    "budget_tokens": 4096
  }
}
```

### DeepSeek {#deepseek}

1. 访问 DeepSeek 平台并[创建 API 密钥](https://platform.deepseek.com/api_keys)
2. 打开设置视图（`agent: open settings`）并转到 DeepSeek 部分
3. 输入您的 DeepSeek API 密钥

DeepSeek API 密钥将保存在您的钥匙串中。

如果定义了 `DEEPSEEK_API_KEY` 环境变量，Zed 也会使用它。

#### 自定义模型 {#deepseek-custom-models}

Zed 代理预配置为使用常见模型的最新版本（DeepSeek Chat、DeepSeek Reasoner）。
如果您希望使用替代模型或自定义 API 端点，可以通过将以下内容添加到您的 Zed `settings.json` 中来实现：

```json [settings]
{
  "language_models": {
    "deepseek": {
      "api_url": "https://api.deepseek.com",
      "available_models": [
        {
          "name": "deepseek-chat",
          "display_name": "DeepSeek Chat",
          "max_tokens": 64000
        },
        {
          "name": "deepseek-reasoner",
          "display_name": "DeepSeek Reasoner",
          "max_tokens": 64000,
          "max_output_tokens": 4096
        }
      ]
    }
  }
}
```

自定义模型将列在代理面板的模型下拉菜单中。
如果需要，您也可以修改 `api_url` 以使用自定义端点。

### GitHub Copilot Chat {#github-copilot-chat}

您可以通过在代理面板的模型下拉菜单中选择 GitHub Copilot Chat 来将其与 Zed 代理一起使用。

1. 打开设置视图（`agent: open settings`）并转到 GitHub Copilot Chat 部分
2. 点击 `Sign in to use GitHub Copilot`，按照模态框中显示的步骤操作。

或者，您可以通过 `GH_COPILOT_TOKEN` 环境变量提供 OAuth 令牌。

> **注意**：如果您在下拉菜单中看不到特定模型，您可能需要在 [GitHub Copilot 设置](https://github.com/settings/copilot/features) 中启用它们。

要将 Copilot Enterprise 与 Zed 一起使用（用于代理和补全），您必须按照 [配置 GitHub Copilot Enterprise](./edit-prediction.md#github-copilot-enterprise) 中所述配置您的企业端点。

### Google AI {#google-ai}

您可以通过在代理面板的模型下拉菜单中选择 Google AI 来将 Gemini 模型与 Zed 代理一起使用。

1. 访问 Google AI Studio 网站并[创建 API 密钥](https://aistudio.google.com/app/apikey)
2. 打开设置视图（`agent: open settings`）并转到 Google AI 部分
3. 输入您的 Google AI API 密钥并按回车键

Google AI API 密钥将保存在您的钥匙串中。

如果定义了 `GEMINI_API_KEY` 环境变量，Zed 也会使用它。有关更多信息，请参阅 Gemini 文档中的[使用 Gemini API 密钥](https://ai.google.dev/gemini-api/docs/api-key)。

#### 自定义模型 {#google-ai-custom-models}

默认情况下，Zed 将使用模型的 `stable` 版本，但您可以使用特定版本的模型，包括[实验性模型](https://ai.google.dev/gemini-api/docs/models/experimental-models)。您可以通过向模型添加 `mode` 配置来配置模型使用[思维模式](https://ai.google.dev/gemini-api/docs/thinking)（如果支持）。这对于控制推理令牌使用和响应速度很有用。如果未指定，Gemini 将自动选择思维预算。

以下是您可以添加到 Zed `settings.json` 中的自定义 Google AI 模型示例：

```json [settings]
{
  "language_models": {
    "google": {
      "available_models": [
        {
          "name": "gemini-2.5-flash-preview-05-20",
          "display_name": "Gemini 2.5 Flash (Thinking)",
          "max_tokens": 1000000,
          "mode": {
            "type": "thinking",
            "budget_tokens": 24000
          }
        }
      ]
    }
  }
}
```

自定义模型将列在代理面板的模型下拉菜单中。

### LM Studio {#lmstudio}

1. 下载并安装[最新版本的 LM Studio](https://lmstudio.ai/download)
2. 在应用程序中按 `cmd/ctrl-shift-m` 并下载至少一个模型（例如，qwen2.5-coder-7b）。或者，您可以通过 LM Studio CLI 获取模型：

   ```sh
   lms get qwen2.5-coder-7b
   ```

3. 通过执行以下命令确保 LM Studio API 服务器正在运行：

   ```sh
   lms server start
   ```

提示：将 [LM Studio 设置为登录项](https://lmstudio.ai/docs/advanced/headless#run-the-llm-service-on-machine-login) 以自动运行 LM Studio 服务器。

### Mistral {#mistral}

1. 访问 Mistral 平台并[创建 API 密钥](https://console.mistral.ai/api-keys/)
2. 打开配置视图（`agent: open settings`）并导航到 Mistral 部分
3. 输入您的 Mistral API 密钥

Mistral API 密钥将保存在您的钥匙串中。

如果定义了 `MISTRAL_API_KEY` 环境变量，Zed 也会使用它。

#### 自定义模型 {#mistral-custom-models}

Zed 代理预配置了几个 Mistral 模型（codestral-latest、mistral-large-latest、mistral-medium-latest、mistral-small-latest、open-mistral-nemo 和 open-codestral-mamba）。
所有默认模型都支持工具使用。
如果您希望使用替代模型或自定义其参数，可以通过将以下内容添加到您的 Zed `settings.json` 中来实现：

```json [settings]
{
  "language_models": {
    "mistral": {
      "api_url": "https://api.mistral.ai/v1",
      "available_models": [
        {
          "name": "mistral-tiny-latest",
          "display_name": "Mistral Tiny",
          "max_tokens": 32000,
          "max_output_tokens": 4096,
          "max_completion_tokens": 1024,
          "supports_tools": true,
          "supports_images": false
        }
      ]
    }
  }
}
```

自定义模型将列在代理面板的模型下拉菜单中。

### Ollama {#ollama}

从 [ollama.com/download](https://ollama.com/download) 下载并安装 Ollama（Linux 或 macOS），并使用 `ollama --version` 确保其正在运行。

1. 下载一个[可用模型](https://ollama.com/models)，例如，对于 `mistral`：

   ```sh
   ollama pull mistral
   ```

2. 确保 Ollama 服务器正在运行。您可以通过运行 Ollama.app（macOS）或启动以下命令来启动它：

   ```sh
   ollama serve
   ```

3. 在代理面板中，使用模型下拉菜单选择其中一个 Ollama 模型。

#### Ollama 上下文长度 {#ollama-context}

Zed 预配置了最大上下文长度（`max_tokens`）以匹配常见模型的能力。
Zed 对 Ollama 的 API 请求将其作为 `num_ctx` 参数包含，但默认值不超过 `16384`，因此拥有约 16GB RAM 的用户能够开箱即用大多数模型。

有关完整的默认值，请参阅 [ollama.rs 中的 get_max_tokens](https://github.com/zed-industries/zed/blob/main/crates/ollama/src/ollama.rs)。

> **注意**：代理面板中显示的令牌计数仅为估计值，将与模型的本机分词器不同。

根据您的硬件或使用情况，您可能希望通过 settings.json 限制或增加特定模型的上下文长度：

```json [settings]
{
  "language_models": {
    "ollama": {
      "api_url": "http://localhost:11434",
      "available_models": [
        {
          "name": "qwen2.5-coder",
          "display_name": "qwen 2.5 coder 32K",
          "max_tokens": 32768,
          "supports_tools": true,
          "supports_thinking": true,
          "supports_images": true
        }
      ]
    }
  }
}
```

如果您指定的上下文长度对您的硬件来说太大，Ollama 将记录错误。
您可以通过运行以下命令查看这些日志：`tail -f ~/.ollama/logs/ollama.log`（macOS）或 `journalctl -u ollama -f`（Linux）。
根据您机器上的可用内存，您可能需要将上下文长度调整为较小的值。

您还可以选择为每个可用模型指定 `keep_alive` 的值。
这可以是一个整数（秒），也可以是字符串形式的持续时间，如 "5m"、"10m"、"1h"、"1d" 等。
例如，`"keep_alive": "120s"` 将允许远程服务器在 120 秒后卸载模型（释放 GPU VRAM）。

`supports_tools` 选项控制模型是否将使用其他工具。
如果模型在 Ollama 目录中标记为 `tools`，则应提供此选项，并且可以使用内置配置文件 `Ask` 和 `Write`。
如果模型在 Ollama 目录中未标记为 `tools`，仍可以将此选项设置为 `true`；但是，请注意只有 `Minimal` 内置配置文件会起作用。

`supports_thinking` 选项控制模型是否在执行最终答案之前执行显式的"思考"（推理）过程。
如果模型在 Ollama 目录中标记为 `thinking`，请设置此选项，您可以在 Zed 中使用它。

`supports_images` 选项启用模型的视觉功能，允许其处理对话上下文中包含的图像。
如果模型在 Ollama 目录中标记为 `vision`，请设置此选项，您可以在 Zed 中使用它。

#### Ollama 认证

除了在您自己的硬件上运行 Ollama（通常不需要认证）之外，Zed 还支持连接到远程 Ollama 实例。API 密钥需要用于认证。

其中一个服务是 [Ollama Turbo](https://ollama.com/turbo)。要将 Zed 配置为使用 Ollama Turbo：

1. 登录您的 Ollama 账户并订阅 Ollama Turbo
2. 访问 [ollama.com/settings/keys](https://ollama.com/settings/keys) 并创建 API 密钥
3. 打开设置视图（`agent: open settings`）并转到 Ollama 部分
4. 粘贴您的 API 密钥并按回车键
5. 对于 API URL，输入 `https://ollama.com`

如果定义了 `OLLAMA_API_KEY` 环境变量，Zed 也会使用它。

### OpenAI {#openai}

1. 访问 OpenAI 平台并[创建 API 密钥](https://platform.openai.com/account/api-keys)
2. 确保您的 OpenAI 账户有积分
3. 打开设置视图（`agent: open settings`）并转到 OpenAI 部分
4. 输入您的 OpenAI API 密钥

OpenAI API 密钥将保存在您的钥匙串中。

如果定义了 `OPENAI_API_KEY` 环境变量，Zed 也会使用它。

#### 自定义模型 {#openai-custom-models}

Zed 代理预配置为使用常见模型的最新版本（GPT-5、GPT-5 mini、o4-mini、GPT-4.1 等）。
要使用替代模型（可能是预览版），或者如果您希望控制请求参数，可以通过将以下内容添加到您的 Zed `settings.json` 中来实现：

```json [settings]
{
  "language_models": {
    "openai": {
      "available_models": [
        {
          "name": "gpt-5",
          "display_name": "gpt-5 high",
          "reasoning_effort": "high",
          "max_tokens": 272000,
          "max_completion_tokens": 20000
        },
        {
          "name": "gpt-4o-2024-08-06",
          "display_name": "GPT 4o Summer 2024",
          "max_tokens": 128000
        }
      ]
    }
  }
}
```

您必须在 `max_tokens` 参数中提供模型的上下文窗口；这可以在 [OpenAI 模型文档](https://platform.openai.com/docs/models) 中找到。

OpenAI `o1` 模型还应设置 `max_completion_tokens` 以避免产生高昂的推理令牌成本。
自定义模型将列在代理面板的模型下拉菜单中。

### OpenAI API 兼容 {#openai-api-compatible}

Zed 支持通过为 OpenAI 提供程序指定自定义 `api_url` 和 `available_models` 来使用 [OpenAI 兼容 API](https://platform.openai.com/docs/api-reference/chat)。
这对于连接到其他托管服务（如 Together AI、Anyscale 等）或本地模型很有用。

您可以通过 UI 或编辑 `settings.json` 来添加自定义的 OpenAI 兼容模型。

要通过 UI 执行此操作，请转到代理面板设置（`agent: open settings`）并查找 "LLM Providers" 部分标题右侧的 "Add Provider" 按钮。
然后，填写模态框中可用的输入字段。

要通过 `settings.json` 执行此操作，请在 `language_models` 下添加以下代码片段：

```json [settings]
{
  "language_models": {
    "openai_compatible": {
      // 使用 Together AI 作为示例
      "Together AI": {
        "api_url": "https://api.together.xyz/v1",
        "available_models": [
          {
            "name": "mistralai/Mixtral-8x7B-Instruct-v0.1",
            "display_name": "Together Mixtral 8x7B",
            "max_tokens": 32768,
            "capabilities": {
              "tools": true,
              "images": false,
              "parallel_tool_calls": false,
              "prompt_cache_key": false
            }
          }
        ]
      }
    }
  }
}
```

默认情况下，OpenAI 兼容模型继承以下功能：

- `tools`: true（支持工具/函数调用）
- `images`: false（不支持图像输入）
- `parallel_tool_calls`: false（不支持 `parallel_tool_calls` 参数）
- `prompt_cache_key`: false（不支持 `prompt_cache_key` 参数）

请注意，LLM API 密钥不会存储在您的设置文件中。
因此，请确保您已在环境变量中设置了它（`<PROVIDER_NAME>_API_KEY=<your api key>`），以便您的设置可以获取它。在上面的示例中，它将是 `TOGETHER_AI_API_KEY=<your api key>`。

### OpenRouter {#openrouter}

OpenRouter 通过单个 API 提供对多个 AI 模型的访问。它支持兼容模型的工具使用。

1. 访问 [OpenRouter](https://openrouter.ai) 并创建账户
2. 从您的 [OpenRouter 密钥页面](https://openrouter.ai/keys) 生成 API 密钥
3. 打开设置视图（`agent: open settings`）并转到 OpenRouter 部分
4. 输入您的 OpenRouter API 密钥

OpenRouter API 密钥将保存在您的钥匙串中。

如果定义了 `OPENROUTER_API_KEY` 环境变量，Zed 也会使用它。

#### 自定义模型 {#openrouter-custom-models}

您可以通过将以下内容添加到您的 Zed `settings.json` 中，将自定义模型添加到 OpenRouter 提供程序：

```json [settings]
{
  "language_models": {
    "open_router": {
      "api_url": "https://openrouter.ai/api/v1",
      "available_models": [
        {
          "name": "google/gemini-2.0-flash-thinking-exp",
          "display_name": "Gemini 2.0 Flash (Thinking)",
          "max_tokens": 200000,
          "max_output_tokens": 8192,
          "supports_tools": true,
          "supports_images": true,
          "mode": {
            "type": "thinking",
            "budget_tokens": 8000
          }
        }
      ]
    }
  }
}
```

每个模型的可用配置选项包括：

- `name`（必需）：OpenRouter 使用的模型标识符
- `display_name`（可选）：在 UI 中显示的人类可读名称
- `max_tokens`（必需）：模型的上下文窗口大小
- `max_output_tokens`（可选）：模型可以生成的最大令牌数
- `max_completion_tokens`（可选）：最大完成令牌数
- `supports_tools`（可选）：模型是否支持工具/函数调用
- `supports_images`（可选）：模型是否支持图像输入
- `mode`（可选）：思维模型的特殊模式配置

您可以在 [OpenRouter 模型页面](https://openrouter.ai/models) 上找到可用的模型及其规格。

自定义模型将列在代理面板的模型下拉菜单中。

#### 提供程序路由

您可以选择通过每个模型条目上的 `provider` 对象来控制 OpenRouter 如何在底层上游提供程序之间路由给定的自定义模型请求。

支持的字段（全部可选）：

- `order`: 要首先尝试的提供程序 slug 数组，按顺序（例如 `["anthropic", "openai"]`）
- `allow_fallbacks`（默认：`true`）：如果首选提供程序不可用，是否可以使用备用提供程序
- `require_parameters`（默认：`false`）：仅使用支持您提供的每个参数的提供程序
- `data_collection`（默认：`allow`）：`"allow"` 或 `"disallow"`（控制可能存储数据的提供程序的使用）
- `only`: 此请求允许的提供程序 slug 白名单
- `ignore`: 要跳过的提供程序 slug
- `quantizations`: 限制为特定的量化变体（例如 `["int4","int8"]`）
- `sort`: 候选提供程序的排序策略（例如 `"price"` 或 `"throughput"`）

向模型添加路由偏好的示例：

```json [settings]
{
  "language_models": {
    "open_router": {
      "api_url": "https://openrouter.ai/api/v1",
      "available_models": [
        {
          "name": "openrouter/auto",
          "display_name": "Auto Router (Tools Preferred)",
          "max_tokens": 2000000,
          "supports_tools": true,
          "provider": {
            "order": ["anthropic", "openai"],
            "allow_fallbacks": true,
            "require_parameters": true,
            "only": ["anthropic", "openai", "google"],
            "ignore": ["cohere"],
            "quantizations": ["int8"],
            "sort": "price",
            "data_collection": "allow"
          }
        }
      ]
    }
  }
}
```

这些路由控制让您可以微调成本、功能和可靠性之间的权衡，而无需更改您在 UI 中选择的模型名称。

### Vercel v0 {#vercel-v0}

[Vercel v0](https://vercel.com/docs/v0/api) 是一个用于生成全栈应用程序的专家模型，具有针对现代堆栈（如 Next.js 和 Vercel）优化的框架感知补全功能。
它支持文本和图像输入，并提供快速流式响应。

v0 模型是 [OpenAI 兼容模型](/#openai-api-compatible)，但 Vercel 在面板的设置视图中被列为一流提供程序。

要开始将其与 Zed 一起使用，请确保您首先创建了 [v0 API 密钥](https://v0.dev/chat/settings/keys)。
一旦拥有它，请直接将其粘贴到面板设置视图中的 Vercel 提供程序部分。

然后您应该在代理面板的模型下拉菜单中找到它作为 `v0-1.5-md`。

### xAI {#xai}

Zed 对 [xAI](https://x.ai/) 模型提供一流支持。您可以使用自己的 API 密钥访问 Grok 模型。

1. [在 xAI 控制台中创建 API 密钥](https://console.x.ai/team/default/api-keys)
2. 打开设置视图（`agent: open settings`）并转到 **xAI** 部分
3. 输入您的 xAI API 密钥

xAI API 密钥将保存在您的钥匙串中。如果定义了 `XAI_API_KEY` 环境变量，Zed 也会使用它。

> **注意：** 虽然 xAI API 是 OpenAI 兼容的，但 Zed 将其作为专用提供程序提供一流支持。为了获得最佳体验，我们建议使用专用的 `x_ai` 提供程序配置，而不是 [OpenAI API 兼容](#openai-api-compatible) 方法。

#### 自定义模型 {#xai-custom-models}

Zed 代理预配置了常见的 Grok 模型。如果您希望使用替代模型或自定义其参数，可以通过将以下内容添加到您的 Zed `settings.json` 中来实现：

```json [settings]
{
  "language_models": {
    "x_ai": {
      "api_url": "https://api.x.ai/v1",
      "available_models": [
        {
          "name": "grok-1.5",
          "display_name": "Grok 1.5",
          "max_tokens": 131072,
          "max_output_tokens": 8192
        },
        {
          "name": "grok-1.5v",
          "display_name": "Grok 1.5V (Vision)",
          "max_tokens": 131072,
          "max_output_tokens": 8192,
          "supports_images": true
        }
      ]
    }
  }
}
```

## 自定义提供程序端点 {#custom-provider-endpoint}

您可以为不同的提供程序使用自定义 API 端点，只要它与提供程序的 API 结构兼容。
为此，请将以下内容添加到您的 `settings.json` 中：

```json [settings]
{
  "language_models": {
    "some-provider": {
      "api_url": "http://localhost:11434"
    }
  }
}
```

目前，`some-provider` 可以是以下任何值：`anthropic`、`google`、`ollama`、`openai`。

这与为例如 [OpenAI 兼容](#openai-api-compatible) 模型提供动力的基础设施相同。
