# 键位配置

Zed 拥有高度可定制的键位系统，可以根据个人习惯进行全面调整。

## 预设键位方案

如果你习惯某些编辑器的默认布局，可以在 [settings.json](./configuring-zed.md) 中设置 `base_keymap`。当前支持：

- VS Code（默认）
- Atom
- Emacs（测试版）
- JetBrains
- Sublime Text
- TextMate
- Cursor
- None（禁用所有默认绑定）

也可通过命令面板执行 `zed: toggle base keymap selector` 切换方案。

此外，启用 `vim_mode` 或 `helix_mode` 后会增加模态键位，详见 [Vim 模式](./vim.md) 与 [Helix 模式](./helix.md)。

## 用户键位文件

键位文件位置：

- macOS/Linux：`~/.config/zed/keymap.json`
- Windows：`%AppData%\Roaming\Zed\keymap.json`

可通过命令 {#action zed::OpenKeymapFile} 直接打开，或使用 {#action zed::OpenKeymap} / {#kb zed::OpenKeymap} 进入内置键位编辑器。

`keymap.json` 是对象数组，每个对象包含 `bindings`，可选 `context`。未指定上下文则全局生效，指定时仅在匹配条件下启用（见[上下文](#contexts)）。

每个 binding 将一个[按键序列](#键位语法)映射至一个[动作](#actions)。当存在冲突时，按照[优先级规则](#precedence)处理。

若使用非 QWERTY 的拉丁字符键盘，可将 `use_key_equivalents` 设为 `true`，详见[非 QWERTY 键盘](#非-qwerty-键盘)。

示例：

```json [keymap]
[
  {
    "bindings": {
      "ctrl-right": "editor::SelectLargerSyntaxNode",
      "ctrl-left": "editor::SelectSmallerSyntaxNode"
    }
  },
  {
    "context": "ProjectPanel && not_editing",
    "bindings": {
      "o": "project_panel::Open"
    }
  }
]
```

默认键位可参阅：

- [macOS](https://github.com/zed-industries/zed/blob/main/assets/keymaps/default-macos.json)
- [Windows](https://github.com/zed-industries/zed/blob/main/assets/keymaps/default-windows.json)
- [Linux](https://github.com/zed-industries/zed/blob/main/assets/keymaps/default-linux.json)

若自定义键位出现问题，可使用命令 `dev: Open Key Context View` 调试。如发现 bug，欢迎在 [GitHub](https://github.com/zed-industries/zed) 提交 Issue。

## 键位语法

Zed 支持匹配按键序列（多个按键依次按下），`bindings` 中的每个键位组合用空格分隔。

每个按键由修饰键 + 键构成，修饰键包括：

- `ctrl-`
- `cmd-` / `win-` / `super-`（平台键）
- `alt-`（macOS 对应 Option）
- `shift-`
- `fn-`
- `secondary-`（macOS 等价于 `cmd`，Windows/Linux 等价于 `ctrl`）

键位可以是键盘生成的任意 Unicode 字符（`a`、`0`、`£`、`ç` 等），也可以是命名键（`tab`、`f1`、`shift`、`cmd` 等）。非拉丁布局（如西里尔文）可以绑定字符本身，也可以绑定按住 `cmd` 时生成的拉丁字符。

示例：

```json [settings]
"bindings": {
  "cmd-k cmd-s": "zed::OpenKeymap",
  "space e": "editor::Complete",
  "ç": "editor::Complete",
  "shift shift": "file_finder::Toggle"
}
```

注意：

- `shift-` 仅用于字母，表示其大写形式，例如 `shift-g` 匹配 `G`。
- 在许多键盘上，`shift` 常与符号组合输入（如 `(`），此时视为未修饰，因此 `shift-(` 不会匹配。
- `alt-` 可用于产生新字符，如 macOS US 布局中 `alt-c` 等于 `ç`，可以二者任选。
- 某些快捷键可绑定单独的修饰键（如 `shift shift`），此类绑定在松开按键时触发。

## 上下文 {#contexts}

指定 `"context"` 时，绑定仅在满足上下文的情境下有效。上下文构成树形结构，根节点为 `Workspace`，其下包含 `Pane`、`Panel`、`Editor` 等。使用命令 `dev: open key context view` 可查看当前上下文。

示例：

```
Workspace os=macos keyboard_layout=com.apple.keylayout.QWERTY
  Pane
    Editor mode=full extension=md vim_mode=insert

Workspace os=macos
  Dock
    ProjectPanel not_editing
```

上下文表达式支持：

- `X && Y`、`X || Y`
- `!X`
- `(X)`
- `X > Y`：匹配祖先为 X、当前节点为 Y

示例：

- `"context": "Editor"`：任意编辑器
- `"context": "Editor && mode=full"`：主编辑器
- `"context": "!Editor && !Terminal"`：排除编辑器与终端
- `"context": "os=macos > Editor"`：仅在 macOS 上的编辑器

属性仅在定义节点有效，例如想在“调试器暂停”且“Vim 正常模式”下生效，需要写 `debugger_stopped > vim_mode == normal`。

自 Zed v0.197.x 起，`!` 会递归检测，`>` 表示“祖先”。如果使用 Vim 模式，请参阅 [Vim 模式的上下文说明](./vim.md#contexts)。Helix 模式基于 Vim，上下文规则相同。

## 动作 {#actions}

几乎所有功能都封装为“动作”。可在命令面板搜索，或通过默认键位文件、键位编辑器的补全功能发现。动作通常无需参数，可直接写成字符串。若需要参数，写为数组，例如：

```json [keymap]
"cmd-1": ["workspace::ActivatePane", 0]
"ctrl-a": ["pane::DeploySearch", { "replace_enabled": true }]
```

## 优先级 {#precedence}

当多个绑定使用同一按键且同时符合上下文，优先级规则为：

1. 越接近叶节点的上下文优先（例如 `Editor` 覆盖 `Workspace`）
2. 若处于同一层级，则后定义的覆盖先前定义；用户键位在系统键位之后加载，因此会覆盖默认值

另一类冲突是“前缀”关系，如：

```json
"ctrl-w": "editor::DeleteToNextWordEnd"
"ctrl-w left": "editor::DeleteToEndOfLine"
```

此时 Zed 会在按下 `ctrl-w` 后等待 1 秒，判断是否继续输入 `left`；若无输入或按其他键，触发第一个动作；若在超时前输入 `left`，触发第二个动作。

## 非 QWERTY 键盘

非 QWERTY 支持仍在完善中：

- 若键盘可直接输出 ASCII（Dvorak、Colemak 等），快捷键将如预期工作。
- 若布局无法直接输出 ASCII（如 Cyrillic），请将 `use_key_equivalents` 设为 `true`：

```json [settings]
"use_key_equivalents": true
```

此时按键将映射到对应的 QWERTY 位置；若想保持按键字符本身，请设为 `false`。

> 例如德语键盘上 `-` 与 `ß` 位置不同，将 `use_key_equivalents` 设为 `true` 后，按下 `-` 会触发英文布局下的 `÷`，若想直接绑定符号本身请保持 `false`。

如果仍然遇到无法绑定的情况，请向我们反馈。
