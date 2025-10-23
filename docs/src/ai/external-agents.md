# 外部代理

Zed 通过[代理客户端协议 (ACP)](https://agentclientprotocol.com)支持基于终端的代理。

目前，[Gemini CLI](https://github.com/google-gemini/gemini-cli) 作为参考实现。
[Claude Code](https://www.anthropic.com/claude-code) 和 [Codex](https://developers.openai.com/codex) 也默认包含，您还可以[添加自定义的 ACP 兼容代理](#add-custom-agents)。

> 请注意，Zed 对外部代理的支持严格基于 UI；计费和法律/条款安排直接发生在您和代理提供商之间。
> Zed 不对外部代理的使用收费，我们的[零数据保留协议/隐私保证](./ai-improvement.md) **_仅_** 适用于 Zed 的托管模型。

## Gemini CLI {#gemini-cli}

Zed 提供了直接在[代理面板](./agent-panel.md)中运行 [Gemini CLI](https://github.com/google-gemini/gemini-cli) 的能力。

在底层，我们在后台运行 Gemini CLI，并通过 ACP 与其通信。
这意味着您运行的是真正的 Gemini CLI，具有其所有优势，但您可以在编辑器中查看和与文件交互。

### 开始使用

从 [Zed Stable v0.201.5](https://zed.dev/releases/stable/0.201.5) 开始，您应该能够直接从 Zed 使用 Gemini CLI。首先使用 {#kb agent::ToggleFocus} 打开代理面板，然后使用右上角的 `+` 按钮开始一个新的 Gemini CLI 线程。

如果您想将其绑定到键盘快捷键，可以通过 `zed: open keymap` 命令编辑您的 `keymap.json` 文件来包含：

```json [keymap]
[
  {
    "bindings": {
      "cmd-alt-g": ["agent::NewExternalAgentThread", { "agent": "gemini" }]
    }
  }
]
```

#### 安装

第一次创建 Gemini CLI 线程时，Zed 将安装 [@google/gemini-cli](https://github.com/zed-industries/claude-code-acp)。此安装仅对 Zed 可用，并在您使用代理时保持最新。

默认情况下，即使您全局安装了 Gemini CLI，Zed 也会使用此托管版本。但是，您可以通过将此添加到您的设置中来配置它使用 `PATH` 中的版本：

```json [settings]
{
  "agent_servers": {
    "gemini": {
      "ignore_system_version": false
    }
  }
}
```

#### 身份验证

运行 Gemini CLI 后，系统将提示您选择身份验证方法。

大多数用户应点击"使用 Google 登录"。这将导致浏览器窗口弹出并直接与 Gemini CLI 进行身份验证。在这种情况下，Zed 不会看到您的 OAuth 或访问令牌。

您也可以使用"Gemini API 密钥"。如果您选择此选项并设置了 `GEMINI_API_KEY`，我们将使用该密钥。否则，Zed 将提示您输入 API 密钥，该密钥将安全地存储在您的钥匙串中，并用于从 Zed 内部启动 Gemini CLI。

"Vertex AI"选项适用于使用 [Vertex AI](https://cloud.google.com/vertex-ai) 并已正确配置其环境的用户。

有关更多信息，请参阅 [Gemini CLI 文档](https://github.com/google-gemini/gemini-cli/blob/main/docs/index.md)。

### 使用

与 Zed 的第一方代理类似，您可以使用 Gemini CLI 执行任何您需要的操作。
为了给它提供上下文，您可以 @-提及文件、最近的线程、符号或获取网页。

> 请注意，某些第一方代理功能尚未与 Gemini CLI 配合使用：编辑过去的消息、从历史记录恢复线程和检查点。
> 我们希望在未来添加这些功能。

## Claude Code

与 Gemini CLI 类似，您也可以直接通过 Zed 的[代理面板](./agent-panel.md)运行 [Claude Code](https://www.anthropic.com/claude-code)。
在底层，Zed 运行 Claude Code 并通过[专用适配器](https://github.com/zed-industries/claude-code-acp)通过 ACP 与其通信。

### 开始使用

使用 {#kb agent::ToggleFocus} 打开代理面板，然后使用右上角的 `+` 按钮开始一个新的 Claude Code 线程。

如果您想将其绑定到键盘快捷键，可以通过 `zed: open keymap` 命令编辑您的 `keymap.json` 文件来包含：

```json [keymap]
[
  {
    "bindings": {
      "cmd-alt-c": ["agent::NewExternalAgentThread", { "agent": "claude_code" }]
    }
  }
]
```

### 身份验证

从版本 `0.202.7`（稳定版）和 `0.203.2`（预览版）开始，Zed 的 Claude Code 安装的身份验证与 Zed 的代理完全解耦。也就是说，通过 [Zed 代理设置](./llm-providers.md#anthropic) 添加的 Anthropic API 密钥将 **_不_** 被 Claude Code 用于身份验证和计费。

为确保您使用选择的计费方式，[打开一个新的 Claude Code 线程](./agent-panel.md#new-thread)。然后，运行 `/login`，并通过 API 密钥或通过`使用 Claude Code 登录`来使用 Claude Pro/Max 订阅进行身份验证。

#### 安装

第一次创建 Claude Code 线程时，Zed 将安装 [@zed-industries/claude-code-acp](https://github.com/zed-industries/claude-code-acp)。此安装仅对 Zed 可用，并在您使用代理时保持最新。

即使您全局安装了 Claude Code，Zed 也会始终使用此托管版本的 Claude Code 适配器，其中包括 Claude Code CLI 的供应商版本。

如果您想覆盖适配器使用的可执行文件，可以在设置中将 `CLAUDE_CODE_EXECUTABLE` 环境变量设置为您首选可执行文件的路径。

```json
{
  "agent_servers": {
    "claude": {
      "env": {
        "CLAUDE_CODE_EXECUTABLE": "/path/to/alternate-claude-code-executable"
      }
    }
  }
}
```

### 使用

与 Zed 的第一方代理类似，您可以使用 Claude Code 执行任何您需要的操作。
为了给它提供上下文，您可以 @-提及文件、最近的线程、符号或获取网页。

除了通过 [ACP](https://agentclientprotocol.com) 与其通信外，Zed 还依赖 [Claude Code SDK](https://docs.anthropic.com/en/docs/claude-code/sdk/sdk-overview) 来支持其某些特定功能。
但是，SDK 尚未公开完全支持所有功能所需的一切：

- 斜杠命令：支持[内置命令](https://docs.anthropic.com/en/docs/claude-code/slash-commands#built-in-slash-commands)的子集，而[自定义斜杠命令](https://docs.anthropic.com/en/docs/claude-code/slash-commands#custom-slash-commands)完全支持。
- [子代理](https://docs.anthropic.com/en/docs/claude-code/sub-agents) 支持。
- [钩子](https://docs.anthropic.com/en/docs/claude-code/hooks-guide) 目前**_不_** 支持。

> 另请注意，某些[第一方代理](./agent-panel.md)功能尚未与 Claude Code 配合使用：编辑过去的消息、从历史记录恢复线程和检查点。
> 我们希望在未来添加这些功能。

#### CLAUDE.md

Zed 中的 Claude Code 将自动使用在项目根目录、项目子目录或根 `.claude` 目录中找到的任何 `CLAUDE.md` 文件。

如果您没有 `CLAUDE.md` 文件，可以通过 `init` 斜杠命令要求 Claude Code 为您创建一个。

## Codex CLI

您也可以直接通过 Zed 的[代理面板](./agent-panel.md)运行 [Codex CLI](https://github.com/openai/codex)。
在底层，Zed 运行 Codex CLI 并通过[专用适配器](https://github.com/zed-industries/codex-acp)通过 ACP 与其通信。

### 开始使用

从 Zed Stable v0.208 开始，您应该能够直接从 Zed 使用 Codex。使用 {#kb agent::ToggleFocus} 打开代理面板，然后使用右上角的 `+` 按钮开始一个新的 Codex 线程。

如果您想将其绑定到键盘快捷键，可以通过 `zed: open keymap` 命令编辑您的 `keymap.json` 文件来包含：

```json
[
  {
    "bindings": {
      "cmd-alt-c": ["agent::NewExternalAgentThread", { "agent": "codex" }]
    }
  }
]
```

### 身份验证

Zed 的 Codex 安装的身份验证与 Zed 的代理完全解耦。也就是说，通过 [Zed 代理设置](./llm-providers.md#openai) 添加的 OpenAI API 密钥将 **_不_** 被 Codex 用于身份验证和计费。

为确保您使用选择的计费方式，[打开一个新的 Codex 线程](./agent-panel.md#new-thread)。第一次您将被提示使用以下三种方法之一进行身份验证：

1. 使用 ChatGPT 登录 - 允许您使用现有的付费 ChatGPT 订阅。_注意：此方法目前在远程项目中不受支持_
2. `CODEX_API_KEY` - 使用您在环境变量 `CODEX_API_KEY` 下设置的 API 密钥。
3. `OPENAI_API_KEY` - 使用您在环境变量 `OPENAI_API_KEY` 下设置的 API 密钥。

如果您已登录并想要更改身份验证方法，请在线程中键入 `/logout` 并重新进行身份验证。

#### 安装

第一次创建 Codex 线程时，Zed 将安装 [codex-acp](https://github.com/zed-industries/codex-acp)。此安装仅对 Zed 可用，并在您使用代理时保持最新。

即使您全局安装了 Codex，Zed 也会始终使用此托管版本。

### 使用

与 Zed 的第一方代理类似，您可以使用 Codex 执行任何您需要的操作。
为了给它提供上下文，您可以 @-提及文件、符号或获取网页。

> 请注意，某些第一方代理功能尚未与 Codex 配合使用：编辑过去的消息、从历史记录恢复线程和检查点。
> 我们希望在未来添加这些功能。

## 添加自定义代理 {#add-custom-agents}

您可以通过如下更改设置来运行任何支持 ACP 的代理：

```json [settings]
{
  "agent_servers": {
    "Custom Agent": {
      "command": "node",
      "args": ["~/projects/agent/index.js", "--acp"],
      "env": {}
    }
  }
}
```

如果您正在开发一个支持该协议的新代理并想要调试它，这也很有用。

您还可以使用 `claude` 和 `gemini` 名称为内置集成指定自定义路径、参数或环境。

## 调试代理

在 Zed 中使用外部代理时，您可以通过命令面板中的 `dev: open acp logs` 访问调试视图。这使您可以看到 Zed 和代理之间发送和接收的消息。

![ACP 日志的调试视图。](https://zed.dev/img/acp/acp-logs.webp)

## MCP 服务器

请注意，对于外部代理，访问[从 Zed 安装的 MCP 服务器](./mcp.md)可能因 ACP 代理实现而异。

关于内置代理，Claude Code 和 Codex 都支持它，而 Gemini CLI 目前还不支持。
同时，通过[他们的文档](https://github.com/google-gemini/gemini-cli?tab=readme-ov-file#using-mcp-servers)了解如何为 Gemini CLI 添加 MCP 服务器支持。
