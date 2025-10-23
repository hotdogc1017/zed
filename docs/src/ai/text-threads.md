# 文本线程

## 概述 {#overview}

[代理面板](./agent-panel.md) 中的文本线程功能类似于任何其他编辑器。您可以使用自定义键绑定并使用多个光标，允许在编码和与语言模型进行讨论之间无缝切换。

然而，文本线程的不同之处在于包含了消息块。这些块充当文本的容器，对应于上下文中的不同角色。这些角色包括：

- `You`
- `Assistant`
- `System`

要开始，请在 `You` 块中输入消息。

![提问](https://zed.dev/img/assistant/ask-a-question.png)

当您输入时，所选模型的剩余令牌计数会更新。

从编辑器中插入文本就像高亮显示文本并运行 `agent: quote selection` ({#kb agent::QuoteSelection}) 一样简单；如果它是代码，Zed 会将其包装在围栏代码块中。

![引用选择](https://zed.dev/img/assistant/quoting-a-selection.png)

要提交消息，请使用 {#kb assistant::Assist}(`assistant: assist`)。与普通线程不同，在普通线程中按 <kbd>enter</kbd> 会提交消息，而在文本线程中，我们的目标是使其感觉尽可能接近常规编辑器。因此，按 {#kb editor::Newline} 只会插入一个新行。

提交消息后，响应将在下面的 `Assistant` 消息块中流式传输。

![接收答案](https://zed.dev/img/assistant/receiving-an-answer.png)

可以在任何时候使用 <kbd>escape</kbd> 取消流。如果您早期意识到响应不是您想要的，这很有用。

如果您想随时开始新对话，可以按 <kbd>cmd-n|ctrl-n</kbd> 或使用面板左上角汉堡菜单中的 `New Chat` 菜单选项。

简单的来回对话在文本线程中效果很好。但是，可能会有时候您想要修改对话中的先前文本并将其引导到不同的方向。

## 编辑文本线程 {#edit-text-thread}

文本线程让您灵活地控制上下文。
您可以自由编辑任何先前的文本，包括来自 LLM 的响应。
如果您想完全删除消息块，只需将光标放在块的开头并使用 `delete` 键。
典型的工作流程可能涉及在整个上下文中进行编辑和调整，以完善您的查询或提供额外信息。以下是一个示例：

1. 在 `You` 块中写入文本。
2. 使用 {#kb assistant::Assist} 提交消息。
3. 收到不符合您期望的 `Assistant` 响应。
4. 使用 <kbd>escape</kbd> 取消响应。
5. 擦除 `Assistant` 消息块的内容并完全删除该块。
6. 向您的原始消息添加额外上下文。
7. 使用 {#kb assistant::Assist} 提交消息。

能够编辑先前的消息让您可以控制令牌的使用方式。您不需要启动新聊天来纠正错误或添加额外信息，也不必通过提交后续更正来浪费令牌。

> **注意**：编辑过去消息的行为在语言模型上下文中通常被称为"重写历史"。

需要记住的一些额外要点：

- 您可以通过单击角色来循环消息块的角色，当您在 `Assistant` 块中收到想要编辑并作为 `You` 块发送回去的响应时，这很有用。

## 命令概述 {#commands}

斜杠命令增强了助手的功能。在行首输入 `/` 以查看可用命令列表：

- `/default`: 插入默认规则
- `/diagnostics`: 注入项目语言服务器报告的错误
- `/fetch`: 获取网页内容并插入
- `/file`: 插入单个文件或文件目录
- `/now`: 插入当前日期和时间
- `/prompt`: 向上下文添加自定义配置的提示（[参见规则库](./rules.md#rules-library)）
- `/symbols`: 插入当前标签页的活动符号
- `/tab`: 插入活动标签页或所有打开标签页的内容
- `/terminal`: 插入终端输出的选定行数
- `/selection`: 插入选定的文本

> **注意：** 请记住，命令仅在创建文本线程或插入命令时进行评估，因此像 `/now` 这样的命令不会持续更新，或者 `/file` 命令不会保持其内容最新。

### `/default`

有关 `/default` 的更多信息，请参阅 [规则：编辑默认规则](./rules.md#default-rules) 部分。

用法：`/default`

### `/diagnostics`

`/diagnostics` 命令将项目语言服务器报告的错误注入到上下文中。这对于获取项目中当前问题的概述很有用。

用法：`/diagnostics [--include-warnings] [path]`

- `--include-warnings`：可选标志，除了错误外还包括警告。
- `path`：可选路径，将诊断限制到特定文件或目录。

### `/file`

`/file` 命令将单个文件或文件目录的内容插入到上下文中。这允许您在与助手的对话中引用项目的特定部分。

用法：`/file <path>`

您可以使用 glob 模式匹配多个文件或目录。

示例：

- `/file src/index.js` - 将 `src/index.js` 的内容插入到上下文中。
- `/file src/*.js` - 将 `src` 目录中所有 `.js` 文件的内容插入到上下文中。
- `/file src` - 将 `src` 目录中所有文件的内容插入到上下文中。

### `/now`

`/now` 命令将当前日期和时间插入到上下文中。这对于让语言模型知道当前时间（进而知道其当前知识库的年龄）很有用。

用法：`/now`

### `/prompt`

`/prompt` 命令从提示库中插入一个提示到上下文中。它也可以用于在提示中嵌套提示。

用法：`/prompt <prompt_name>`

相关：`/default`

### `/symbols`

`/symbols` 命令将当前标签页中的活动符号（函数、类等）插入到上下文中。这对于获取当前文件结构的概述很有用。

用法：`/symbols`

### `/tab`

`/tab` 命令将活动标签页或所有打开标签页的内容插入到上下文中。这允许您引用当前正在处理的内容。

用法：`/tab [tab_name|all]`

- `tab_name`：要插入的特定标签页的可选名称。
- `all`：插入所有打开标签页的内容。

示例：

- `/tab` - 插入活动标签页的内容。
- `/tab "index.js"` - 插入名为 "index.js" 的标签页的内容。
- `/tab all` - 插入所有打开标签页的内容。

### `/terminal`

`/terminal` 命令将终端输出的选定行数插入到上下文中。这对于引用最近的命令输出或日志很有用。

用法：`/terminal [<number>]`

- `<number>`：指定要插入的行数的可选参数（默认为 50）。

### `/selection`

`/selection` 命令将编辑器中选择的文本插入到上下文中。这对于引用代码的特定部分很有用。

这等同于 `agent: quote selection` 命令 ({#kb agent::QuoteSelection})。

用法：`/selection`

## 规则库中的命令 {#slash-commands-in-rules}

[命令](#commands) 可以在规则库（以前称为提示库）中的规则中使用，以插入动态内容或执行操作。
例如，如果您想创建一个规则，其中模型知道日期很重要，您可以使用 `/now` 命令插入当前日期。

> **警告：** 规则中的斜杠命令**仅**在文本线程中使用时有效。不支持在非文本线程中使用它们。

> **注意：** 规则中的斜杠命令**必须**在自己的行上。

有关命令的更多信息以及可用的斜杠命令，请参阅上面的[命令列表](#commands)。

### 示例

```plaintext
You are an expert Rust engineer. The user has asked you to review their project and answer some questions.

Here is some information about their project:

/file Cargo.toml
```

在上面的示例中，`@file` 命令用于将 `Cargo.toml` 文件（或项目中存在的所有 `Cargo.toml` 文件）的内容插入到规则中。

## 嵌套规则

类似于向默认规则添加规则，您可以使用 `/prompt` 命令在其他规则中嵌套规则（目前仅在文本线程中支持）。

您可能想要嵌套规则以：

- 动态创建模板
- 将文档或引用等集合分解为更小、可混合搭配的部分
- 创建类似规则的变体（例如，`Async Rust - Tokio` 与 `Async Rust - Async-std`）

### 示例：

```plaintext
Title: Zed-Flavored Rust

## About Zed

/prompt Zed: Zed (a rule about what Zed is)

## Rust - Zed Style

/prompt Rust: Async - Async-std (zed doesn't use tokio)
/prompt Rust: Zed-style Crates (we have some unique conventions)
/prompt Rust - Workspace deps (bias towards reusing deps from the workspace)
```

_上面括号中的文本是注释，不是规则的一部分。_

> **注意：** 虽然从技术上讲您_可以_将规则嵌套在自身中，但我们不建议这样做（强烈不建议）。风险自负！

通过使用嵌套规则，您可以创建模块化和可重用的规则组件，这些组件可以以各种方式组合以适应不同的场景。

> **注意：** 当使用斜杠命令引入额外上下文时，注入的内容可以直接在文本线程中内联编辑——此处的编辑不会传播到保存的规则。

## 可扩展性

额外的斜杠命令可以由扩展提供。

请参阅 [扩展：斜杠命令](../extensions/slash-commands.md) 了解如何创建自己的命令。

## 高级概念

### 规则模板 {#rule-templates}

Zed 使用规则模板来驱动内部助手功能，如终端助手或内联助手中使用的内容规则。

Zed 有以下内部规则模板：

- `content_prompt.hbs`：用于在编辑器中生成内容。
- `terminal_assistant_prompt.hbs`：用于终端助手功能。

目前尚不清楚我们是否会进一步扩展模板以允许用户创建。

### 覆盖模板

> **注意：** 除非您知道自己在做什么，否则不建议覆盖模板。如果操作不当，编辑模板会破坏您的助手。

Zed 允许您通过在 `~/.config/zed/prompt_overrides` 目录中放置自定义 Handlebars (.hbs) 模板来覆盖用于各种助手功能的默认规则。

可以覆盖以下模板：

1. [`content_prompt.hbs`](https://github.com/zed-industries/zed/tree/main/assets/prompts/content_prompt.hbs)：用于在编辑器中生成内容。

2. [`terminal_assistant_prompt.hbs`](https://github.com/zed-industries/zed/tree/main/assets/prompts/terminal_assistant_prompt.hbs)：用于终端助手功能。

> **注意：** 请确保您想要覆盖这些模板，因为您将错过我们内置功能的迭代。这应该主要在开发 Zed 时使用。

您可以自定义这些模板以更好地满足您的需求，同时保持 Zed 使用的核心结构和变量。当您的提示覆盖在磁盘上更改时，Zed 会自动重新加载它们。

请查阅 Zed 的 [assets/prompts](https://github.com/zed-industries/zed/tree/main/assets/prompts) 目录以获取您可以使用的当前版本。

### 历史 {#history}

在文本线程中提交第一条消息后，语言模型会为您的上下文生成一个名称，并且上下文会自动保存到您的文件系统中：

- `~/.config/zed/conversations` (macOS)
- `~/.local/share/zed/conversations` (Linux)
- `%LocalAppData%\Zed\conversations` (Windows)

您可以通过单击代理面板左上角的历史按钮来访问和加载先前的上下文。

![查看助手历史](https://zed.dev/img/assistant/assistant-history.png)
