# 使用规则 {#using-rules}

规则本质上是一个提示，在与代理的每次交互开始时插入。
目前，Zed 支持目录根目录中的 `.rules` 文件和规则库，允许您存储多个规则以供按需使用。

## `.rules` 文件

Zed 支持在工作树的顶层包含 `.rules` 文件，它们充当项目级指令，包含在您与代理面板的所有交互中。
为了与其他代理兼容，也支持此文件的其他名称，但请注意将使用此列表中匹配的第一个文件：

- `.rules`
- `.cursorrules`
- `.windsurfrules`
- `.clinerules`
- `.github/copilot-instructions.md`
- `AGENT.md`
- `AGENTS.md`
- `CLAUDE.md`
- `GEMINI.md`

## 规则库 {#rules-library}

规则库是一个用于编写和管理规则的界面。与 Zed 中的其他文本驱动 UI 一样，它是一个具有语法高亮、键盘快捷键等的完整编辑器。

您可以直接在规则编辑器中使用内联助手，允许您自动化和重写规则。

### 打开规则库

1. 打开代理面板。
2. 单击右上角的代理菜单（`...`）。
3. 从下拉菜单中选择 `Rules...`。

您也可以在代理面板中使用 `agent: open rules library` 命令。

### 管理规则

选择规则文件后，您可以直接在内置编辑器中编辑它。其标题也可以从编辑器标题栏更改。

可以使用规则编辑器中的按钮复制、删除规则或将规则添加到默认规则。

### 创建规则 {#creating-rules}

要创建规则文件，只需打开 `Rules Library` 并单击 `+` 按钮。规则文件存储在本地，可以随时从库中访问。

拥有一系列专门针对提示工程定制的规则文件也可以帮助您编写一致且有效的规则。

以下是编写更好规则的一些有用资源：

- [Anthropic: Prompt Engineering](https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/overview)
- [OpenAI: Prompt Engineering](https://platform.openai.com/docs/guides/prompt-engineering)

### 编辑默认规则 {#default-rules}

Zed 允许您自定义与 LLM 交互时使用的默认规则。
或者更准确地说，它使用一系列组合形成默认规则的规则。

默认规则会自动包含在每个新线程的上下文中。
您还可以使用 `@rule` 命令手动将其他规则（未标记为默认的规则）添加为上下文。

## 从提示库迁移

以前，规则库被称为"提示库"。
新的规则系统取代了提示库，除了少数特定情况，如下所述。

### 规则中的斜杠命令

以前，可以在自定义提示（现在是规则）中使用斜杠命令（现在是 @-提及）。
目前不支持在规则文件中使用 @-提及，但是当与文本线程一起使用时，规则文件中支持斜杠命令。
有关更多信息，请参阅使用[规则中的斜杠命令](./text-threads.md#slash-commands-in-rules) 的文档。

### 提示模板

Zed 保持与其原始模板系统的向后兼容性，允许您自定义整个应用程序中使用的提示，包括内联助手。
虽然规则库现在是管理提示的主要方式，但您仍然可以使用这些遗留模板来覆盖默认提示。
有关更多详细信息，请参阅 [文本线程](./text-threads.md) 下的 [规则模板](./text-threads.md#rule-templates) 部分。
