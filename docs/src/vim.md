# Vim 模式

Zed 包含一个称为 "vim 模式" 的 Vim 模拟层。在本页中，你将学习如何打开或关闭 Zed 的 vim 模式，Zed 提供了哪些工具和命令来帮助你导航和编辑代码，以及如何在 Zed 中充分利用 vim 模式。

你将学习如何：

- 理解 Zed 的 vim 模式与传统 Vim 之间的核心差异
- 启用或禁用 vim 模式
- 在 vim 模式中充分利用 Zed 特定功能
- 自定义 vim 模式键绑定
- 配置 vim 模式设置

无论你是 vim 模式的新手还是经验丰富的 Vim 用户，希望优化你的 Zed 体验，本指南都将帮助你充分利用 Zed 中的模态编辑功能。

## Zed 的 vim 模式设计

Vim 模式试图为 Vim 用户提供熟悉的体验：它在有意义时精确复制运动和命令的行为，并使用 Zed 特定功能来提供"开箱即用"的编辑体验，无需你进行配置。

这包括对语义导航、多光标或其他通常由插件（如环绕文本）提供的功能的支持。

因此，Zed 的 vim 模式不是一对一地复制 Vim，而是将 Vim 的模态设计与 Zed 的现代功能相结合，以提供更流畅的体验。它也是可配置的，因此你可以添加自己的键绑定或覆盖默认值。

### 核心差异

vim 模式中有四种类型的功能使用 Zed 的核心功能，导致行为上的一些差异：

1. **运动**：vim 模式使用 Zed 的语义解析来调整每种语言的运动行为。例如，在 Rust 中，使用 `%` 跳转到匹配括号适用于管道字符 `|`。在 JavaScript 中，`w` 将 `$` 视为单词字符。
2. **可视块选择**：vim 模式使用 Zed 的多光标来模拟可视块选择，使块选择更加灵活。例如，你在块选择后插入的任何内容都会实时更新到每一行，并且你可以随时添加或删除光标。
3. **宏**：vim 模式使用 Zed 的录制系统来处理 vim 宏。因此，你可以捕获和重放更复杂的操作，如自动完成。
4. **搜索和替换**：vim 模式使用 Zed 的搜索系统，因此正则表达式的语法与 Vim 略有不同。有关详细信息，请[前往正则表达式差异部分](#regex-differences)。

> **注意：** Zed 的 vim 模式基础应该已经涵盖了许多用例，我们一直在努力改进它。如果你发现工作流中依赖的功能缺失，请[在 GitHub 上提交问题](https://github.com/zed-industries/zed/issues)。

## 启用和禁用 vim 模式

当你第一次打开 Zed 时，你会在欢迎屏幕上看到一个复选框，允许你启用 vim 模式。

如果你错过了这个，你可以随时通过打开命令面板并使用工作区命令 `toggle vim mode` 来切换 vim 模式的开关。

> **注意**：此命令在你的用户设置中切换以下属性：
>
> ```json [settings]
> {
>   "vim_mode": true
> }
> ```

## Zed 特定功能

Zed 建立在现代基础之上，它（除其他外）使用 Tree-sitter 和语言服务器来理解你正在编辑的文件内容，并开箱即用地支持多光标。

Vim 模式有几个"核心 Zed"键绑定，将帮助你充分利用 Zed 的特定功能集。

### 语言服务器

以下命令使用语言服务器来帮助你导航和重构代码。

| 命令                                  | 默认快捷键 |
| ---------------------------------------- | ---------------- |
| 转到定义                         | `g d`            |
| 转到声明                        | `g D`            |
| 转到类型定义                    | `g y`            |
| 转到实现                     | `g I`            |
| 重命名（更改定义）               | `c d`            |
| 转到当前单词的所有引用 | `g A`            |
| 在当前文件中查找符号              | `g s`            |
| 在整个项目中查找符号            | `g S`            |
| 转到下一个诊断                    | `g ]` 或 `] d`   |
| 转到上一个诊断                | `g [` 或 `[ d`   |
| 显示内联错误（悬停）                | `g h`            |
| 打开代码操作菜单               | `g .`            |

### Git

| 命令                         | 默认快捷键 |
| ------------------------------- | ---------------- |
| 转到下一个 git 更改           | `] c`            |
| 转到上一个 git 更改       | `[ c`            |
| 展开差异代码块                | `d o`            |
| 切换暂存                   | `d O`            |
| 暂存并下一个（在差异视图中）   | `d u`            |
| 取消暂存并下一个（在差异视图中） | `d U`            |
| 恢复更改                  | `d p`            |

### Tree-sitter

Tree-sitter 是 Zed 用来理解代码结构的强大工具。Zed 提供改变当前光标位置的运动，以及可用作操作目标的文本对象。

| 命令                         | 默认快捷键            |
| ------------------------------- | --------------------------- |
| 转到下一个/上一个方法      | `] m` / `[ m`               |
| 转到下一个/上一个方法结束  | `] M` / `[ M`               |
| 转到下一个/上一个部分     | `] ]` / `[ [`               |
| 转到下一个/上一个部分结束 | `] [` / `[ ]`               |
| 转到下一个/上一个注释     | `] /`, `] *` / `[ /`, `[ *` |
| 选择更大的语法节点     | `[ x`                       |
| 选择更小的语法节点    | `] x`                       |

| 文本对象                                               | 默认快捷键 |
| ---------------------------------------------------------- | ---------------- |
| 围绕类、定义等                           | `a c`            |
| 在类、定义等内部                           | `i c`            |
| 围绕函数、方法等                             | `a f`            |
| 在函数、方法等内部                            | `i f`            |
| 注释                                                  | `g c`            |
| 参数或列表项等                            | `i a`            |
| 参数或列表项等（包括尾随逗号） | `a a`            |
| 围绕 HTML 类标签                                    | `a t`            |
| 在 HTML 类标签内部                                    | `i t`            |
| 当前缩进级别，以及前后一行    | `a I`            |
| 当前缩进级别，以及前一行              | `a i`            |
| 当前缩进级别                                   | `i i`            |

请注意，`[m` 系列运动的目标定义与 `af` 定义的边界相同。`[[` 的目标与 `ac` 定义的目标相同，尽管如果没有类，则也会使用函数。类似地，`gc` 用于查找 `[ /`。

函数、类和注释的定义是语言相关的，可以通过添加 [`textobjects.scm`] 来为扩展添加支持。参数和标签的定义在 Tree-sitter 级别操作，但在解析树中查找某些模式，目前不能按语言配置。

### 多光标

这些命令帮助你在 Zed 中管理多个光标。

| 命令                                                      | 默认快捷键 |
| ------------------------------------------------------------ | ---------------- |
| 添加选择当前单词下一个副本的光标     | `g l`            |
| 添加选择当前单词上一个副本的光标 | `g L`            |
| 跳过最新的单词选择，并添加下一个                     | `g >`            |
| 跳过最新的单词选择，并添加上一个                 | `g <`            |
| 为当前单词的每个副本添加可视选择    | `g a`            |

### 窗格管理

这些命令打开新窗格或跳转到特定窗格。

| 命令                                    | 默认快捷键   |
| ------------------------------------------ | ------------------ |
| 打开项目范围搜索                 | `g /`              |
| 打开当前搜索摘录            | `g <space>`        |
| 在拆分中打开当前搜索摘录 | `<ctrl-w> <space>` |
| 在拆分中转到定义                | `<ctrl-w> g d`     |
| 在拆分中转到类型定义           | `<ctrl-w> g D`     |

### 在插入模式中

以下命令帮助你调出 Zed 的完成菜单、请求 GitHub Copilot 的建议或打开内联 AI 助手，而无需离开插入模式。

| 命令                                                                      | 默认快捷键 |
| ---------------------------------------------------------------------------- | ---------------- |
| 打开完成菜单                                                     | `ctrl-x ctrl-o`  |
| 请求 GitHub Copilot 建议（需要配置 GitHub Copilot） | `ctrl-x ctrl-c`  |
| 打开内联 AI 助手（需要配置助手）               | `ctrl-x ctrl-a`  |
| 打开代码操作菜单                                                   | `ctrl-x ctrl-l`  |
| 隐藏所有建议                                                        | `ctrl-x ctrl-z`  |

### 支持的插件

Zed 的 vim 模式包含一些通常在 Vim 生态系统中由非常流行的插件提供的功能：

- 你可以用 `ys`（yank surround）环绕文本对象，用 `cs` 更改环绕，用 `ds` 删除环绕。
- 你可以在可视模式中用 `gc` 注释和取消注释选择，在普通模式中用 `gcc`。
- 项目面板支持许多模仿 Vim 插件 `netrw` 的快捷键：用 `hjkl` 导航，用 `o` 打开文件，用 `t` 在新标签中打开文件等。
- 你可以向键映射添加键绑定来导航 "camelCase" 名称。[前往可选键绑定部分](#optional-key-bindings)了解如何操作。
- 你可以使用 `gR` 执行 [ReplaceWithRegister](https://github.com/vim-scripts/ReplaceWithRegister)。
- 你可以使用 `cx` 获得 [vim-exchange](https://github.com/tommcdo/vim-exchange) 功能。请注意，它在可视模式中没有默认绑定，但你可以向键映射添加一个（请参阅[可选键绑定](#optional-key-bindings)部分）。
- 你可以使用 [indent wise](https://github.com/jeetsukumaran/vim-indentwise) 插件 `[-`、`]-`、`[+`、`]+`、`[=`、`]=` 导航到相对于光标的缩进深度。
- 你可以使用 AnyQuotes 和 AnyBrackets 文本对象选择引号文本和括号文本。Zed 还提供 MiniQuotes 和 MiniBrackets，它们基于 [mini.ai](https://github.com/echasnovski/mini.nvim/blob/main/readmes/mini-ai.md) Neovim 插件提供替代选择行为。有关详细信息，请参阅下面的[引号和括号文本对象](#quote-and-bracket-text-objects)部分。
- 你可以配置 AnyQuotes、AnyBrackets、MiniQuotes 和 MiniBrackets 文本对象，使用不同的选择策略选择引号和括号文本。有关详细信息，请参阅下面的[任意括号功能](#any-bracket-functionality)部分。

### 任意括号功能

Zed 提供两种不同的策略来选择被任何引号或任何括号包围的文本。这些文本对象**默认未启用**，必须在键映射中配置才能使用。

#### 包含的字符

每种文本对象类型适用于特定字符：

| 文本对象              | 字符                                                                             |
| ------------------------ | -------------------------------------------------------------------------------------- |
| AnyQuotes/MiniQuotes     | 单引号 (`'`), 双引号 (`"`), 反引号 (`` ` ``)                             |
| AnyBrackets/MiniBrackets | 圆括号 (`()`), 方括号 (`[]`), 花括号 (`{}`), 尖括号 (`<>`) |

"Any" 和 "Mini" 变体都适用于相同的字符集，但在选择策略上有所不同。

#### AnyQuotes 和 AnyBrackets（传统 Vim 行为）

这些文本对象实现传统 Vim 行为：

- **选择优先级**：首先找到最内层（最近）的引号或括号
- **回退机制**：如果未找到，则回退到当前行
- **基于字符的匹配**：仅关注开闭字符，不考虑语法
- **与原始 Vim 相似**：AnyBrackets 匹配原始 Vim 中 `ci<`、`ci(` 等命令的行为，包括潜在的边缘情况（如将 `=>` 中的 `>` 视为结束分隔符）

#### MiniQuotes 和 MiniBrackets（mini.ai 行为）

这些文本对象实现 [mini.ai](https://github.com/echasnovski/mini.nvim/blob/main/readmes/mini-ai.md) Neovim 插件的行为：

- **选择优先级**：首先搜索当前行，然后向外扩展
- **Tree-sitter 集成**：使用 Tree-sitter 查询进行更上下文感知的选择
- **语法感知匹配**：可以区分实际括号和其他上下文中的类似字符（如 `=>` 中的 `>`）

#### 选择方法

- 使用 **AnyQuotes/AnyBrackets** 如果你：

  - 偏好传统 Vim 行为
  - 想要一致的基于字符的选择，优先考虑最内层分隔符
  - 需要与原始 Vim 的文本对象密切匹配的行为

- 使用 **MiniQuotes/MiniBrackets** 如果你：
  - 偏好 mini.ai 插件行为
  - 想要使用 Tree-sitter 进行更上下文感知的选择
  - 搜索时偏好当前行优先级

#### 示例配置

要使用这些文本对象，你需要向键映射添加绑定。以下是一个示例配置，在使用文本对象操作符（`i` 和 `a`）或更改环绕（`cs`）时使它们可用：

```json [settings]
{
  "context": "vim_operator == a || vim_operator == i || vim_operator == cs",
  "bindings": {
    // 传统 Vim 行为
    "q": "vim::AnyQuotes",
    "b": "vim::AnyBrackets",

    // mini.ai 插件行为
    "Q": "vim::MiniQuotes",
    "B": "vim::MiniBrackets"
  }
}
```

使用此配置，你可以使用以下命令：

- `cib` - 使用 AnyBrackets 行为更改括号内部
- `ciB` - 使用 MiniBrackets 行为更改括号内部
- `ciq` - 使用 AnyQuotes 行为更改引号内部
- `ciQ` - 使用 MiniQuotes 行为更改引号内部

## 命令面板

Vim 模式允许你用 `:` 打开 Zed 的命令面板。然后你可以输入以访问任何常规的 Zed 命令。此外，vim 模式为流行的 Vim 命令添加了别名，以确保你的肌肉记忆转移到 Zed。例如，你可以写 `:w` 或 `:write` 来保存文件。

下面，你将找到可以在命令面板中使用的命令表。我们将可选字符放在方括号中，表示你可以省略它们。

> **注意**：我们尚未模拟 Vim 命令行的全部功能。特别是，命令目前不支持参数。当你发现命令面板中缺少内容时，请[在 GitHub 上提交问题](https://github.com/zed-industries/zed)。

### File and window management

This table shows commands for managing windows, tabs, and panes. As commands don't support arguments currently, you cannot specify a filename when saving or creating a new file.

| Command        | Description                                          |
| -------------- | ---------------------------------------------------- |
| `:w[rite][!]`  | Save the current file                                |
| `:wq[!]`       | Save the file and close the buffer                   |
| `:q[uit][!]`   | Close the buffer                                     |
| `:wa[ll][!]`   | Save all open files                                  |
| `:wqa[ll][!]`  | Save all open files and close all buffers            |
| `:qa[ll][!]`   | Close all buffers                                    |
| `:[e]x[it][!]` | Close the buffer                                     |
| `:up[date]`    | Save the current file                                |
| `:cq`          | Quit completely (close all running instances of Zed) |
| `:vs[plit]`    | Split the pane vertically                            |
| `:sp[lit]`     | Split the pane horizontally                          |
| `:new`         | Create a new file in a horizontal split              |
| `:vne[w]`      | Create a new file in a vertical split                |
| `:tabedit`     | Create a new file in a new tab                       |
| `:tabnew`      | Create a new file in a new tab                       |
| `:tabn[ext]`   | Go to the next tab                                   |
| `:tabp[rev]`   | Go to previous tab                                   |
| `:tabc[lose]`  | Close the current tab                                |
| `:ls`          | Show all buffers                                     |

> **Note:** The `!` character is used to force the command to execute without saving changes or prompting before overwriting a file.

### Ex commands

These ex commands open Zed's various panels and windows.

| Command                      | Default Shortcut |
| ---------------------------- | ---------------- |
| Open the project panel       | `:E[xplore]`     |
| Open the collaboration panel | `:C[ollab]`      |
| Open the chat panel          | `:Ch[at]`        |
| Open the AI panel            | `:A[I]`          |
| Open the git panel           | `:G[it]`         |
| Open the debug panel         | `:D[ebug]`       |
| Open the notifications panel | `:No[tif]`       |
| Open the feedback window     | `:fe[edback]`    |
| Open the diagnostics window  | `:cl[ist]`       |
| Open the terminal            | `:te[rm]`        |
| Open the extensions window   | `:Ext[ensions]`  |

### Navigating diagnostics

These commands navigate diagnostics.

| Command                  | Description                    |
| ------------------------ | ------------------------------ |
| `:cn[ext]` or `:ln[ext]` | Go to the next diagnostic      |
| `:cp[rev]` or `:lp[rev]` | Go to the previous diagnostics |
| `:cc` or `:ll`           | Open the errors page           |

### Git

These commands interact with the version control system git.

| Command         | Description                                             |
| --------------- | ------------------------------------------------------- |
| `:dif[fupdate]` | View the diff under the cursor (`d o` in normal mode)   |
| `:rev[ert]`     | Revert the diff under the cursor (`d p` in normal mode) |

### Jump

These commands jump to specific positions in the file.

| Command             | Description                         |
| ------------------- | ----------------------------------- |
| `:<number>`         | Jump to a line number               |
| `:$`                | Jump to the end of the file         |
| `:/foo` and `:?foo` | Jump to next/prev line matching foo |

### Replacement

This command replaces text. It emulates the substitute command in vim. The substitute command uses regular expressions, and Zed uses a slightly different syntax than vim. You can learn more about Zed's syntax below, [in the regex differences section](#regex-differences). Zed will replace only the first occurrence of the search pattern in the current line. To replace all matches append the `g` flag.

| Command                 | Description                       |
| ----------------------- | --------------------------------- |
| `:[range]s/foo/bar/[g]` | Replace instances of foo with bar |

### Editing

These commands help you edit text.

| Command           | Description                                             |
| ----------------- | ------------------------------------------------------- |
| `:j[oin]`         | Join the current line                                   |
| `:d[elete][l][p]` | Delete the current line                                 |
| `:s[ort] [i]`     | Sort the current selection (with i, case-insensitively) |
| `:y[ank]`         | Yank (copy) the current selection or line               |

### Set

These commands modify editor options locally for the current buffer.

| Command                         | Description                                                                                   |
| ------------------------------- | --------------------------------------------------------------------------------------------- |
| `:se[t] [no]wrap`               | Lines longer than the width of the window will wrap and displaying continues on the next line |
| `:se[t] [no]nu[mber]`           | Print the line number in front of each line                                                   |
| `:se[t] [no]r[elative]nu[mber]` | Changes the displayed number to be relative to the cursor                                     |
| `:se[t] [no]i[gnore]c[ase]`     | Controls whether the buffer and project search use case-sensitive matching                    |

### Command mnemonics

As any Zed command is available, you may find that it's helpful to remember mnemonics that run the correct command. For example:

- `:diffs` for "toggle all hunk diffs"
- `:cpp` for "copy path to file"
- `:crp` for "copy relative path"
- `:reveal` for "reveal in finder"
- `:zlog` for "open zed log"
- `:clank` for "cancel language server work"

## Customizing key bindings

In this section, we'll learn how to customize the key bindings of Zed's vim mode. You'll learn:

- How to select the correct context for your new key bindings.
- Useful contexts for vim mode key bindings.
- Common key bindings to customize for extra productivity.

### Selecting the correct context

Zed's key bindings are evaluated only when the `"context"` property matches your location in the editor. For example, if you add key bindings to the `"Editor"` context, they will only work when you're editing a file. If you add key bindings to the `"Workspace"` context, they will work everywhere in Zed. Here's an example of a key binding that saves when you're editing a file:

```json [settings]
{
  "context": "Editor",
  "bindings": {
    "ctrl-s": "file::Save"
  }
}
```

Contexts are nested, so when you're editing a file, the context is the `"Editor"` context, which is inside the `"Pane"` context, which is inside the `"Workspace"` context. That's why any key bindings you add to the `"Workspace"` context will work when you're editing a file. Here's an example:

```json [keymap]
// This key binding will work when you're editing a file. It comes built into Zed by default as the workspace: save command.
{
  "context": "Workspace",
  "bindings": {
    "ctrl-s": "workspace::Save"
  }
}
```

Contexts are expressions. They support boolean operators like `&&` (and) and `||` (or). For example, you can use the context `"Editor && vim_mode == normal"` to create key bindings that only work when you're editing a file _and_ you're in vim's normal mode.

Vim mode adds several contexts to the `"Editor"` context:

| Operator             | Description                                                                                                                                                                        |
| -------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| VimControl           | Indicates that vim keybindings should work. Currently an alias for `vim_mode == normal \|\| vim_mode == visual \|\| vim_mode == operator`, but the definition may change over time |
| vim_mode == normal   | Normal mode                                                                                                                                                                        |
| vim_mode == visual   | Visual mode                                                                                                                                                                        |
| vim_mode == insert   | Insert mode                                                                                                                                                                        |
| vim_mode == replace  | Replace mode                                                                                                                                                                       |
| vim_mode == waiting  | Waiting for an arbitrary key (e.g., after typing `f` or `t`)                                                                                                                       |
| vim_mode == operator | Waiting for another binding to trigger (e.g., after typing `c` or `d`)                                                                                                             |
| vim_operator         | Set to `none` unless `vim_mode == operator`, in which case it is set to the current operator's default keybinding (e.g., after typing `d`, `vim_operator == d`)                    |

> **Note**: Contexts are matched only on one level at a time. So it is possible to use the expression `"Editor && vim_mode == normal"`, but `"Workspace && vim_mode == normal"` will never match because we set the vim context at the `"Editor"` level.

### Useful contexts for vim mode key bindings

Here's a template with useful vim mode contexts to help you customize your vim mode key bindings. You can copy it and integrate it into your user keymap.

```json [keymap]
[
  {
    "context": "VimControl && !menu",
    "bindings": {
      // Put key bindings here if you want them to work in normal & visual mode.
    }
  },
  {
    "context": "vim_mode == normal && !menu",
    "bindings": {
      // "shift-y": ["workspace::SendKeystrokes", "y $"] // Use neovim's yank behavior: yank to end of line.
    }
  },
  {
    "context": "vim_mode == insert",
    "bindings": {
      // "j k": "vim::NormalBefore" // In insert mode, make jk escape to normal mode.
    }
  },
  {
    "context": "EmptyPane || SharedScreen",
    "bindings": {
      // Put key bindings here (in addition to the context above) if you want them to
      // work when no editor exists.
      // "space f": "file_finder::Toggle"
    }
  }
]
```

> **Note**: If you would like to emulate Vim's `map` commands (`nmap`, etc.), you can use the action `workspace::SendKeystrokes` in the correct context.

### Optional key bindings

By default, you can navigate between the different files open in the editor with shortcuts like `ctrl+w` followed by one of `hjkl` to move to the left, down, up, or right, respectively.

But you cannot use the same shortcuts to move between all the editor docks (the terminal, project panel, assistant panel, ...). If you want to use the same shortcuts to navigate to the docks, you can add the following key bindings to your user keymap.

```json [settings]
{
  "context": "Dock",
  "bindings": {
    "ctrl-w h": "workspace::ActivatePaneLeft",
    "ctrl-w l": "workspace::ActivatePaneRight",
    "ctrl-w k": "workspace::ActivatePaneUp",
    "ctrl-w j": "workspace::ActivatePaneDown"
    // ... or other keybindings
  }
}
```

Subword motion, which allows you to navigate and select individual words in camelCase or snake_case, is not enabled by default. To enable it, add these bindings to your keymap.

```json [settings]
{
  "context": "VimControl && !menu && vim_mode != operator",
  "bindings": {
    "w": "vim::NextSubwordStart",
    "b": "vim::PreviousSubwordStart",
    "e": "vim::NextSubwordEnd",
    "g e": "vim::PreviousSubwordEnd"
  }
}
```

Vim mode comes with shortcuts to surround the selection in normal mode (`ys`), but it doesn't have a shortcut to add surrounds in visual mode. By default, `shift-s` substitutes the selection (erases the text and enters insert mode). To use `shift-s` to add surrounds in visual mode, you can add the following object to your keymap.

```json [settings]
{
  "context": "vim_mode == visual",
  "bindings": {
    "shift-s": "vim::PushAddSurrounds"
  }
}
```

In non-modal text editors, cursor navigation typically wraps when moving past line ends. Zed, however, handles this behavior exactly like Vim by default: the cursor stops at line boundaries. If you prefer your cursor to wrap between lines, override these keybindings:

```json [settings]
// In VimScript, this would look like this:
// set whichwrap+=<,>,[,],h,l
{
  "context": "VimControl && !menu",
  "bindings": {
    "left": "vim::WrappingLeft",
    "right": "vim::WrappingRight",
    "h": "vim::WrappingLeft",
    "l": "vim::WrappingRight"
  }
}
```

The [Sneak motion](https://github.com/justinmk/vim-sneak) feature allows for quick navigation to any two-character sequence in your text. You can enable it by adding the following keybindings to your keymap. By default, the `s` key is mapped to `vim::Substitute`. Adding these bindings will override that behavior, so ensure this change aligns with your workflow preferences.

```json [settings]
{
  "context": "vim_mode == normal || vim_mode == visual",
  "bindings": {
    "s": "vim::PushSneak",
    "shift-s": "vim::PushSneakBackward"
  }
}
```

The [vim-exchange](https://github.com/tommcdo/vim-exchange) feature does not have a default binding for visual mode, as the `shift-x` binding conflicts with the default `shift-x` binding for visual mode (`vim::VisualDeleteLine`). To assign the default vim-exchange binding, add the following keybinding to your keymap:

```json [settings]
{
  "context": "vim_mode == visual",
  "bindings": {
    "shift-x": "vim::Exchange"
  }
}
```

### Restoring common text editing and Zed keybindings

If you're using vim mode on Linux or Windows, you may find it overrides keybindings you can't live without: `ctrl+v` to paste, `ctrl+f` to search, etc. You can restore them by copying this data into your keymap:

```json [keymap]
{
  "context": "Editor && !menu",
  "bindings": {
    "ctrl-c": "editor::Copy",               // vim default: return to normal mode
    "ctrl-x": "editor::Cut",                // vim default: decrement
    "ctrl-v": "editor::Paste",              // vim default: visual block mode
    "ctrl-y": "editor::Undo",               // vim default: line up
    "ctrl-f": "buffer_search::Deploy",      // vim default: page down
    "ctrl-o": "workspace::Open",            // vim default: go back
    "ctrl-s": "workspace::Save",            // vim default: show signature
    "ctrl-a": "editor::SelectAll",          // vim default: increment
    "ctrl-b": "workspace::ToggleLeftDock"   // vim default: down
  }
},
```

## Changing vim mode settings

You can change the following settings to modify vim mode's behavior:

| Property                     | Description                                                                                                                                                                                   | Default Value |
| ---------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------- |
| default_mode                 | The default mode to start in. One of "normal", "insert", "replace", "visual", "visual_line", "visual_block", "helix_normal".                                                                  | "normal"      |
| use_system_clipboard         | Determines how system clipboard is used:<br><ul><li>"always": use for all operations</li><li>"never": only use when explicitly specified</li><li>"on_yank": use for yank operations</li></ul> | "always"      |
| use_multiline_find           | deprecated                                                                                                                                                                                    |
| use_smartcase_find           | If `true`, `f` and `t` motions are case-insensitive when the target letter is lowercase.                                                                                                      | false         |
| toggle_relative_line_numbers | If `true`, line numbers are relative in normal mode and absolute in insert mode, giving you the best of both options.                                                                         | false         |
| custom_digraphs              | An object that allows you to add custom digraphs. Read below for an example.                                                                                                                  | {}            |
| highlight_on_yank_duration   | The duration of the highlight animation(in ms). Set to `0` to disable                                                                                                                         | 200           |

Here's an example of adding a digraph for the zombie emoji. This allows you to type `ctrl-k f z` to insert a zombie emoji. You can add as many digraphs as you like.

```json [settings]
{
  "vim": {
    "custom_digraphs": {
      "fz": "🧟‍♀️"
    }
  }
}
```

Here's an example of these settings changed:

```json [settings]
{
  "vim": {
    "default_mode": "insert",
    "use_system_clipboard": "never",
    "use_smartcase_find": true,
    "toggle_relative_line_numbers": true,
    "highlight_on_yank_duration": 50,
    "custom_digraphs": {
      "fz": "🧟‍♀️"
    }
  }
}
```

## Useful core Zed settings for vim mode

Here are a few general Zed settings that can help you fine-tune your Vim experience:

| Property                | Description                                                                                                                                                   | Default Value        |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------- |
| cursor_blink            | If `true`, the cursor blinks.                                                                                                                                 | `true`               |
| relative_line_numbers   | If `true`, line numbers in the left gutter are relative to the cursor.                                                                                        | `false`              |
| scrollbar               | Object that controls the scrollbar display. Set to `{ "show": "never" }` to hide the scroll bar.                                                              | `{ "show": "auto" }` |
| scroll_beyond_last_line | If set to `"one_page"`, allows scrolling up to one page beyond the last line. Set to `"off"` to prevent this behavior.                                        | `"one_page"`         |
| vertical_scroll_margin  | The number of lines to keep above or below the cursor when scrolling. Set to `0` to allow the cursor to go up to the edges of the screen vertically.          | `3`                  |
| gutter.line_numbers     | Controls the display of line numbers in the gutter. Set the `"line_numbers"` property to `false` to hide line numbers.                                        | `true`               |
| command_aliases         | Object that defines aliases for commands in the command palette. You can use it to define shortcut names for commands you use often. Read below for examples. | `{}`                 |

Here's an example of these settings changed:

```json [settings]
{
  // Disable cursor blink
  "cursor_blink": false,
  // Use relative line numbers
  "relative_line_numbers": true,
  // Hide the scroll bar
  "scrollbar": { "show": "never" },
  // Prevent the buffer from scrolling beyond the last line
  "scroll_beyond_last_line": "off",
  // Allow the cursor to reach the edges of the screen
  "vertical_scroll_margin": 0,
  "gutter": {
    // Disable line numbers completely:
    "line_numbers": false
  },
  "command_aliases": {
    "W": "w",
    "Wq": "wq",
    "Q": "q"
  }
}
```

The `command_aliases` property is a single object that maps keys or key sequences to vim mode commands. The example above defines multiple aliases: `W` for `w`, `Wq` for `wq`, and `Q` for `q`.

## Regex differences

Zed uses a different regular expression engine from Vim. This means that you will have to use a different syntax in some cases. Here are the most common differences:

- **Capture groups**: Vim uses `\(` and `\)` to represent capture groups, in Zed these are `(` and `)`. On the flip side, in Vim, `(` and `)` represent literal parentheses, but in Zed these must be escaped to `\(` and `\)`.
- **Matches**: When replacing, Vim uses the backslash character followed by a number to represent a matched capture group. For example, `\1`. Zed uses the dollar sign instead. So, when in Vim you use `\0` to represent the entire match, in Zed the syntax is `$0` instead. Same for numbered capture groups: `\1` in Vim is `$1` in Zed.
- **Global option**: By default, in Vim, regex searches only match the first occurrence on a line, and you append `/g` at the end of your query to find all matches. In Zed, regex searches are global by default.
- **Case sensitivity**: Vim uses `/i` to indicate a case-insensitive search. In Zed you can either write `(?i)` at the start of the pattern or toggle case-sensitivity with the shortcut {#kb search::ToggleCaseSensitive}.

> **Note**: To help with the transition, the command palette will fix parentheses and replace groups for you when you write a Vim-style substitute command, `:%s//`. So, Zed will convert `%s:/\(a\)(b)/\1/` into a search for "(a)\(b\)" and a replacement of "$1".

For the full syntax supported by Zed's regex engine [see the regex crate documentation](https://docs.rs/regex/latest/regex/#syntax).
