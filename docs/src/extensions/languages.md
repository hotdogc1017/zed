# 语言扩展

Zed 中的语言支持包含几个组件：

- 语言元数据和配置
- 语法
- 查询
- 语言服务器

## 语言元数据

Zed 支持的每种语言必须在您的扩展的 `languages` 目录内的子目录中定义。

此子目录必须包含一个名为 `config.toml` 的文件，具有以下结构：

```toml
name = "My Language"
grammar = "my-language"
path_suffixes = ["myl"]
line_comments = ["# "]
```

- `name`（必需）是将在选择语言下拉列表中显示的人类可读名称。
- `grammar`（必需）是语法的名称。语法是单独注册的，如下所述。
- `path_suffixes` 是应与此语言关联的文件后缀数组。与设置中的 `file_types` 不同，这不支持 glob 模式。
- `line_comments` 是用于识别语言中行注释的字符串数组。这用于 `editor::ToggleComments` 键绑定：{#kb editor::ToggleComments} 用于切换代码行。
- `tab_size` 定义用于此语言的缩进/制表符大小（默认为 `4`）。
- `hard_tabs` 是否使用制表符（`true`）或空格（`false`，默认值）进行缩进。
- `first_line_pattern` 是一个正则表达式，除了 `path_suffixes`（上面）或设置中的 `file_types` 之外，还可以用于匹配应使用此语言的文件。例如，Zed 使用它通过匹配脚本第一行中的 [shebangs 行](https://github.com/zed-industries/zed/blob/main/crates/languages/src/bash/config.toml) 来识别 Shell 脚本。
- `debuggers` 是用于识别语言中调试器的字符串数组。当启动调试器的 `New Process Modal` 时，Zed 将按此数组中条目的顺序对可用调试器进行排序。

<!--
TBD: Document `language_name/config.toml` keys

- autoclose_before
- brackets (start, end, close, newline, not_in: ["comment", "string"])
- word_characters
- prettier_parser_name
- opt_into_language_servers
- code_fence_block_name
- scope_opt_in_language_servers
- increase_indent_pattern, decrease_indent_pattern
- collapsed_placeholder
- auto_indent_on_paste, auto_indent_using_last_non_empty_line
- overrides: `[overrides.element]`, `[overrides.string]`
-->

## 语法

Zed 使用 [Tree-sitter](https://tree-sitter.github.io) 解析库来提供内置的语言特定功能。有许多语言的语法可用，您也可以[开发自己的语法](https://tree-sitter.github.io/tree-sitter/creating-parsers#writing-the-grammar)。越来越多的 Zed 功能是使用 Tree-sitter 查询在语法树上进行模式匹配构建的。如上所述，在扩展中定义的每种语言必须指定用于解析的 Tree-sitter 语法的名称。然后这些语法在扩展的 `extension.toml` 文件中单独注册，如下所示：

```toml
[grammars.gleam]
repository = "https://github.com/gleam-lang/tree-sitter-gleam"
rev = "58b7cac8fc14c92b0677c542610d8738c373fa81"
```

`repository` 字段必须指定应从其加载 Tree-sitter 语法的存储库，`rev` 字段必须包含要使用的 Git 修订版本，例如 Git 提交的 SHA。如果您在本地开发扩展并希望从本地文件系统加载语法，您可以为 `repository` 使用 `file://` URL。扩展可以通过引用多个 tree-sitter 存储库来提供多个语法。

## Tree-sitter 查询

Zed 使用 [Tree-sitter](https://tree-sitter.github.io) 查询语言生成的语法树来实现
几个功能：

- 语法高亮
- 括号匹配
- 代码大纲/结构
- 自动缩进
- 代码注入
- 语法覆盖
- 文本脱敏
- 可运行代码检测
- 选择类、函数等

以下部分详细说明 [Tree-sitter 查询](https://tree-sitter.github.io/tree-sitter/using-parsers#query-syntax) 如何在 Zed 中启用这些
功能，使用 [JSON 语法](https://www.json.org/json-en.html) 作为指导示例。

### 语法高亮

在 Tree-sitter 中，`highlights.scm` 文件定义了特定语法的语法高亮规则。

以下是 JSON 的 `highlights.scm` 示例：

```scheme
(string) @string

(pair
  key: (string) @property.json_key)

(number) @number
```

此查询标记字符串、对象键和数字以进行高亮显示。以下是主题支持的捕获的完整列表：

| 捕获                      | 描述                             |
| ------------------------ | -------------------------------------- |
| @attribute               | 捕获属性                    |
| @boolean                 | 捕获布尔值                |
| @comment                 | 捕获注释                      |
| @comment.doc             | 捕获文档注释        |
| @constant                | 捕获常量                     |
| @constructor             | 捕获构造函数                  |
| @embedded                | 捕获嵌入内容              |
| @emphasis                | 捕获强调文本               |
| @emphasis.strong         | 捕获强烈强调文本      |
| @enum                    | 捕获枚举                  |
| @function                | 捕获函数                     |
| @hint                    | 捕获提示                         |
| @keyword                 | 捕获关键字                      |
| @label                   | 捕获标签                        |
| @link_text               | 捕获链接文本                     |
| @link_uri                | 捕获链接 URI                     |
| @number                  | 捕获数值                |
| @operator                | 捕获运算符                     |
| @predictive              | 捕获预测文本               |
| @preproc                 | 捕获预处理器指令       |
| @primary                 | 捕获主要元素              |
| @property                | 捕获属性                    |
| @punctuation             | 捕获标点符号                   |
| @punctuation.bracket     | 捕获括号                      |
| @punctuation.delimiter   | 捕获分隔符                    |
| @punctuation.list_marker | 捕获列表标记                  |
| @punctuation.special     | 捕获特殊标点符号           |
| @string                  | 捕获字符串字面量               |
| @string.escape           | 捕获字符串中的转义字符 |
| @string.regex            | 捕获正则表达式           |
| @string.special          | 捕获特殊字符串               |
| @string.special.symbol   | 捕获特殊符号               |
| @tag                     | 捕获标签                          |
| @tag.doctype             | 捕获文档类型（例如，在 HTML 中）      |
| @text.literal            | 捕获字面文本                  |
| @title                   | 捕获标题                        |
| @type                    | 捕获类型                         |
| @variable                | 捕获变量                     |
| @variable.special        | 捕获特殊变量             |
| @variant                 | 捕获变体                      |

### 括号匹配

`brackets.scm` 文件定义了匹配的括号。

以下是 JSON 的 `brackets.scm` 文件示例：

```scheme
("[" @open "]" @close)
("{" @open "}" @close)
("\"" @open "\"" @close)
```

此查询识别开括号、闭括号、大括号和引号。

| 捕获 | 描述                                   |
| ------- | --------------------------------------------- |
| @open   | 捕获开括号、大括号和引号 |
| @close  | 捕获闭括号、大括号和引号 |

### 代码大纲/结构

`outline.scm` 文件定义了代码大纲的结构。

以下是 JSON 的 `outline.scm` 文件示例：

```scheme
(pair
  key: (string (string_content) @name)) @item
```

此查询捕获对象键以用于大纲结构。

| 捕获        | 描述                                                                          |
| -------------- | ------------------------------------------------------------------------------------ |
| @name          | 捕获对象键的内容                                                  |
| @item          | 捕获整个键值对                                                   |
| @context       | 捕获为大纲项提供上下文的元素                          |
| @context.extra | 捕获大纲项的额外上下文信息                      |
| @annotation    | 捕获注释大纲项的节点（文档注释、属性、装饰器）[^1] |

[^1]: 这些注释在 Assistant 生成代码修改步骤时使用。

### 自动缩进

`indents.scm` 文件定义了缩进规则。

以下是 JSON 的 `indents.scm` 文件示例：

```scheme
(array "]" @end) @indent
(object "}" @end) @indent
```

此查询标记数组和对象的结尾以用于缩进目的。

| 捕获 | 描述                                        |
| ------- | -------------------------------------------------- |
| @end    | 捕获闭括号和大括号               |
| @indent | 捕获整个数组和对象以用于缩进 |

### 代码注入

`injections.scm` 文件定义了将一种语言嵌入到另一种语言中的规则，例如 Markdown 中的代码块或 Python 字符串中的 SQL 查询。

以下是 Markdown 的 `injections.scm` 文件示例：

```scheme
(fenced_code_block
  (info_string
    (language) @injection.language)
  (code_fence_content) @injection.content)

((inline) @content
 (#set! injection.language "markdown-inline"))
```

此查询识别围栏代码块，捕获信息字符串中指定的语言和块内的内容。它还捕获内联内容并将其语言设置为 "markdown-inline"。

| 捕获             | 描述                                                |
| ------------------- | ---------------------------------------------------------- |
| @injection.language | 捕获代码块的语言标识符          |
| @injection.content  | 捕获要被视为不同语言的内容 |

请注意，我们无法在此处使用 JSON 作为示例，因为它不支持语言注入。

### 语法覆盖

`overrides.scm` 文件定义了语法_作用域_，可用于在特定语言构造中覆盖某些编辑器设置。

例如，有一个语言特定设置称为 `word_characters`，它控制哪些非字母字符被视为单词的一部分，例如当您双击选择变量时。在 JavaScript 中，"$" 和 "#" 被视为单词字符。

还有一个语言特定设置称为 `completion_query_characters`，它控制哪些字符触发自动完成建议。在 JavaScript 中，当您的光标在_字符串_内时，"-" 应被视为完成查询字符。为了实现这一点，JavaScript `overrides.scm` 文件包含以下模式：

```scheme
[
  (string)
  (template_string)
] @string
```

而 JavaScript `config.toml` 包含此设置：

```toml
word_characters = ["#", "$"]

[overrides.string]
completion_query_characters = ["-"]
```

您还可以在特定作用域中禁用某些自动关闭括号。例如，要防止在字符串内自动关闭 `'`，您可以在 JavaScript `config.toml` 中放入以下内容：

```toml
brackets = [
  { start = "'", end = "'", close = true, newline = false, not_in = ["string"] },
  # 其他对...
]
```

#### 范围包含性

默认情况下，`overrides.scm` 中定义的范围是_排他性的_。因此，在上述情况下，如果您的光标在分隔字符串的引号_之外_，`string` 作用域将不会生效。有时，您可能希望使范围_包含性的_。您可以通过在查询中的捕获名称后添加 `.inclusive` 后缀来实现这一点。

例如，在 JavaScript 中，我们还禁用了注释内单引号的自动关闭。注释作用域必须一直延伸到行注释后的换行符。为了实现这一点，JavaScript `overrides.scm` 包含以下模式：

```scheme
(comment) @comment.inclusive
```

### 文本对象

`textobjects.scm` 文件定义了按文本对象导航的规则。这是在 Zed v0.165 中添加的，目前仅用于 Vim 模式。

Vim 提供了两个级别的粒度来在文件中导航。使用 `[]` 等逐节导航，以及使用 `]m` 等逐方法导航。即使不支持函数和类的语言也可以通过定义类似的概念来很好地工作。例如，CSS 将规则集定义为方法，将媒体查询定义为类。

对于具有闭包的语言，这些通常不应在 Zed 中计为函数。然而，这是尽力而为的，因为像 JavaScript 这样的语言在语法上不区分闭包和顶级函数声明。

对于具有类似 C 声明的语言，提供匹配 `@class.around` 或 `@function.around` 的查询。如果没有内部，`if` 和 `ic` 文本对象将默认为这些。

如果您不确定在 textobjects.scm 中放什么，[nvim-treesitter-textobjects](https://github.com/nvim-treesitter/nvim-treesitter-textobjects) 和 [Helix 编辑器](https://github.com/helix-editor/helix) 都有许多语言的查询。您可以参考 Zed [内置语言](https://github.com/zed-industries/zed/tree/main/crates/languages/src) 来了解如何适应这些。

| 捕获          | 描述                                                             | Vim 模式                                         |
| ---------------- | ----------------------------------------------------------------------- | ------------------------------------------------ |
| @function.around | 整个函数定义或文件的等效小部分。    | `[m`, `]m`, `[M`,`]M` 动作。 `af` 文本对象  |
| @function.inside | 函数体（大括号内的内容）。                        | `if` 文本对象                                 |
| @class.around    | 整个类定义或文件的等效大部分。       | `[[`, `]]`, `[]`, `][` 动作。 `ac` 文本对象 |
| @class.inside    | 类定义的内容。                                     | `ic` 文本对象                                 |
| @comment.around  | 整个注释（例如所有相邻的行注释，或块注释） | `gc` 文本对象                                 |
| @comment.inside  | 注释的内容                                               | `igc` 文本对象（很少支持）             |

例如：

```scheme
; 仅将方法的内容包含在函数中
(method_definition
    body: (_
        "{"
        (_)* @function.inside
        "}")) @function.around

; 为没有主体的声明匹配 function.around
(function_signature_item) @function.around

; 将所有相邻的注释合并为一个
(comment)+ @comment.around
```

### 文本脱敏

`redactions.scm` 文件定义了文本脱敏规则。在协作和共享屏幕时，它确保某些语法节点以脱敏模式呈现，以避免它们泄露。

以下是 JSON 的 `redactions.scm` 文件示例：

```scheme
(pair value: (number) @redact)
(pair value: (string) @redact)
(array (number) @redact)
(array (string) @redact)
```

此查询标记键值对和数组中的数字和字符串值以进行脱敏。

| 捕获 | 描述                    |
| ------- | ------------------------------ |
| @redact | 捕获要脱敏的值 |

### 可运行代码检测

`runnables.scm` 文件定义了检测可运行代码的规则。

以下是 JSON 的 `runnables.scm` 文件示例：

```scheme
(
    (document
        (object
            (pair
                key: (string
                    (string_content) @_name
                    (#eq? @_name "scripts")
                )
                value: (object
                    (pair
                        key: (string (string_content) @run @script)
                    )
                )
            )
        )
    )
    (#set! tag package-script)
    (#set! tag composer-script)
)
```

此查询检测 package.json 和 composer.json 文件中的可运行脚本。

The `@run` capture specifies where the run button should appear in the editor. Other captures, except those prefixed with an underscore, are exposed as environment variables with a prefix of `ZED_CUSTOM_$(capture_name)` when running the code.

| Capture | Description                                            |
| ------- | ------------------------------------------------------ |
| @\_name | Captures the "scripts" key                             |
| @run    | Captures the script name                               |
| @script | Also captures the script name (for different purposes) |

<!--
TBD: `#set! tag`
-->

## Language Servers

Zed uses the [Language Server Protocol](https://microsoft.github.io/language-server-protocol/) to provide advanced language support.

An extension may provide any number of language servers. To provide a language server from your extension, add an entry to your `extension.toml` with the name of your language server and the language(s) it applies to. The entry in the list of `languages` has to match the `name` field from the `config.toml` file for that language:

```toml
[language_servers.my-language-server]
name = "My Language LSP"
languages = ["My Language"]
```

Then, in the Rust code for your extension, implement the `language_server_command` method on your extension:

```rust
impl zed::Extension for MyExtension {
    fn language_server_command(
        &mut self,
        language_server_id: &LanguageServerId,
        worktree: &zed::Worktree,
    ) -> Result<zed::Command> {
        Ok(zed::Command {
            command: get_path_to_language_server_executable()?,
            args: get_args_for_language_server()?,
            env: get_env_for_language_server()?,
        })
    }
}
```

You can customize the handling of the language server using several optional methods in the `Extension` trait. For example, you can control how completions are styled using the `label_for_completion` method. For a complete list of methods, see the [API docs for the Zed extension API](https://docs.rs/zed_extension_api).

### Multi-Language Support

If your language server supports additional languages, you can use `language_ids` to map Zed `languages` to the desired [LSP-specific `languageId`](https://microsoft.github.io/language-server-protocol/specifications/lsp/3.17/specification/#textDocumentItem) identifiers:

```toml

[language-servers.my-language-server]
name = "Whatever LSP"
languages = ["JavaScript", "HTML", "CSS"]

[language-servers.my-language-server.language_ids]
"JavaScript" = "javascript"
"TSX" = "typescriptreact"
"HTML" = "html"
"CSS" = "css"
```
