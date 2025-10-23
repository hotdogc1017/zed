# 代理设置

了解您可以在 Zed 代理面板中自定义的所有设置。

## 模型设置 {#model-settings}

### 默认模型 {#default-model}

如果您使用 [Zed 托管的 LLM 服务](./subscription.md)，它会将 `claude-sonnet-4` 设置为代理工作的默认模型（代理面板、内联助手），并将 `gpt-5-nano` 设置为默认的"快速"模型（线程摘要、Git 提交消息）。如果您没有订阅或想要更改这些默认值，可以手动编辑设置中的 `default_model` 对象：

```json [settings]
{
  "agent": {
    "default_model": {
      "provider": "openai",
      "model": "gpt-4o"
    }
  }
}
```

### 功能特定模型 {#feature-specific-models}

您可以为以下 AI 功能分配不同的特定模型：

- 线程摘要模型：用于生成线程摘要
- 内联助手模型：用于内联助手功能
- 提交消息模型：用于生成 Git 提交消息

```json [settings]
{
  "agent": {
    "default_model": {
      "provider": "zed.dev",
      "model": "claude-sonnet-4"
    },
    "inline_assistant_model": {
      "provider": "anthropic",
      "model": "claude-3-5-sonnet"
    },
    "commit_message_model": {
      "provider": "openai",
      "model": "gpt-4o-mini"
    },
    "thread_summary_model": {
      "provider": "google",
      "model": "gemini-2.0-flash"
    }
  }
}
```

> 如果未为这些功能之一设置自定义模型，它们会自动回退使用默认模型。

### 内联助手的替代模型 {#alternative-assists}

内联助手功能特别具有使用不同模型并行执行多次生成的能力。
通过为其分配多个模型，可以将上面显示的配置更进一步。

配置后，内联助手 UI 将显示控件，用于在由每个模型生成的输出之间循环。

您在此处指定的模型始终是_附加_到您的[默认模型](#default-model)使用的。

例如，以下配置将为每个助手生成两个输出。
一个使用 Claude Sonnet 4（默认模型），一个使用 GPT-5-mini。

```json [settings]
{
  "agent": {
    "default_model": {
      "provider": "zed.dev",
      "model": "claude-sonnet-4"
    },
    "inline_alternatives": [
      {
        "provider": "zed.dev",
        "model": "gpt-4-mini"
      }
    ]
  }
}
```

### 模型温度

为提供程序和/或模型指定自定义温度：

```json [settings]
"model_parameters": [
  // 为所有对 OpenAI 模型的请求设置参数：
  {
    "provider": "openai",
    "temperature": 0.5
  },
  // 为所有请求设置通用参数：
  {
    "temperature": 0
  },
  // 为特定提供程序和模型设置参数：
  {
    "provider": "zed.dev",
    "model": "claude-sonnet-4",
    "temperature": 1.0
  }
],
```

## 代理面板设置 {#agent-panel-settings}

请注意，其中一些设置也会在代理面板的设置 UI 中显示，您可以通过 `agent: open settings` 操作或通过面板右上角的下拉菜单访问。

### 默认视图

使用 `default_view` 设置更改代理面板的默认视图。
您可以在 `thread`（默认）和 `text_thread` 之间选择：

```json [settings]
{
  "agent": {
    "default_view": "text_thread"
  }
}
```

### 字体大小

使用 `agent_font_size` 设置更改面板中渲染的代理响应的字体大小。

```json [settings]
{
  "agent": {
    "agent_font_size": 18
  }
}
```

> 代理面板中的编辑器——无论是主消息文本区域还是先前消息——都使用等宽字体，因此受 `buffer_font_size` 设置控制，该设置在您的 `settings.json` 中全局定义。

### 自动运行命令

控制是否允许代理在未经您许可的情况下运行命令。
默认值为 `false`。

```json [settings]
{
  "agent": {
    "always_allow_tool_actions": true
  }
}
```

### 单文件审查

控制是否在代理完成编辑后在单个缓冲区中显示审查操作（接受和拒绝）。
默认值为 `false`。

```json [settings]
{
  "agent": {
    "single_file_review": true
  }
}
```

当设置为 false 时，这些控件仅在多缓冲区审查选项卡中可用。

### 声音通知

控制当代理完成生成更改或需要您的输入时是否听到通知声音。
默认值为 `false`。

```json [settings]
{
  "agent": {
    "play_sound_when_agent_done": true
  }
}
```

### 消息编辑器大小

使用 `message_editor_min_lines` 设置控制代理消息编辑器应具有的最小行数高度。
默认设置为 `4`，最大行数始终是最小值的两倍。

```json [settings]
{
  "agent": {
    "message_editor_min_lines": 4
  }
}
```

### 发送修饰键

使修饰键（macOS 上的 `cmd`，Linux 上的 `ctrl`）成为发送消息的必要条件。
这鼓励更周到的提示制作。
默认值为 `false`。

```json [settings]
{
  "agent": {
    "use_modifier_to_send": true
  }
}
```

### 编辑卡片

使用 `expand_edit_card` 设置控制编辑卡片是否在代理面板中显示完整差异。
默认设置为 `true`，但如果设置为 false，卡片的高度将限制在一定行数内，需要点击才能展开。

```json [settings]
{
  "agent": {
    "expand_edit_card": false
  }
}
```

### 终端卡片

使用 `expand_terminal_card` 设置控制终端卡片是否在代理面板中显示命令输出。
默认设置为 `true`，但如果设置为 false，卡片将在命令运行时完全折叠，需要点击才能展开。

```json [settings]
{
  "agent": {
    "expand_terminal_card": false
  }
}
```

### 反馈控件

控制是否在每个代理响应的底部显示点赞/点踩按钮，允许向 Zed 提供有关代理性能的反馈。
默认值为 `true`。

```json [settings]
{
  "agent": {
    "enable_feedback": false
  }
}
```
