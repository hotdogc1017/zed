# 任务（Tasks）

Zed 支持通过集成终端执行命令，并在需要时重复运行。这些命令可以读取 Zed 提供的少量上下文信息（如当前文件路径、选中文本等）。

```json [tasks]
[
  {
    "label": "Example task",
    "command": "for i in {1..5}; do echo \"Hello $i/5\"; sleep 1; done",
    // "args": [],
    // 覆盖命令所用环境变量，会在设置中的终端环境基础上追加。
    "env": { "foo": "bar" },
    // 命令执行时的工作目录，默认使用当前项目根目录。
    // "cwd": "/path/to/working/directory",
    // 是否在新的终端标签页中运行任务，默认 false（复用现有标签）。
    "use_new_terminal": false,
    // 是否允许多个实例并行运行。默认 false，会等待已有任务完成。
    "allow_concurrent_runs": false,
    // 任务启动后如何处理终端面板/标签：
    // * "always" — 总是展示面板并聚焦对应标签（默认）
    // * "no_focus" — 展示面板并添加标签，但不聚焦
    // * "never" — 不改变焦点，仅复用或创建标签
    "reveal": "always",
    // 任务结束后如何处理终端面板/标签：
    // * "never" — 不做任何操作（默认）
    // * "always" — 隐藏标签，如果是面板内最后一个标签则隐藏面板
    // * "on_success" — 仅在成功时隐藏标签，其余行为同 "always"
    "hide": "never",
    // 指定运行任务所使用的 shell，可选：
    // 1. "system"（默认）使用 /etc/passwd 中的默认 shell
    // 2. 指定程序：
    //    "shell": { "program": "sh" }
    // 3. 指定程序与参数：
    //    "shell": {
    //      "with_arguments": {
    //        "program": "/bin/bash",
    //        "args": ["--login"]
    //      }
    //    }
    "shell": "system",
    // 是否在输出中显示任务摘要行，默认 true。
    "show_summary": true,
    // 是否在输出中显示命令行本身，默认 true。
    "show_command": true
    // 用于内联 runnable 指示器或同标签批量运行的标签。
    // "tags": []
  }
]
```

任务相关的两个核心命令是 `task: spawn` 与 `task: rerun`：

- `task: spawn` 会弹出任务面板，列出当前可用的所有任务。
- `task: rerun` 会重新运行最近一次触发的任务，你也可以在任务面板中重新执行某项任务。

默认情况下，重跑任务会复用同一个终端标签页（因为 `"use_new_terminal": false`），并会等待先前的任务结束（因为 `"allow_concurrent_runs": false`）。如果希望重跑时中断旧任务，可以保持 `"use_new_terminal": false`，并设置 `"allow_concurrent_runs": true`。

## 任务模板

任务可定义于以下位置：

- 全局 `tasks.json`：位于 `~/.config/zed/tasks.json`（或对应平台路径），适用于所有项目。可通过 `zed: open tasks` 打开。
- 项目本地 `.zed/tasks.json`：仅在该工作区内可用。可通过 `zed: open project tasks` 编辑。
- [临时任务](#oneshot-任务)：一次性创建，随会话结束而清除。
- 语言扩展：某些扩展会自带可用任务。

## 变量

任务执行环境与普通 shell 相同，因此可以使用 `$VAR_NAME` 语法引用环境变量。Zed 会额外提供一些变量，方便在任务中使用当前编辑器上下文：

- `ZED_COLUMN`：当前光标列号
- `ZED_ROW`：当前光标行号
- `ZED_FILE`：当前文件的绝对路径
- `ZED_FILENAME`：当前文件名
- `ZED_DIRNAME`：当前文件所在目录（绝对路径）
- `ZED_RELATIVE_FILE`：相对于工作树根目录的文件路径
- `ZED_RELATIVE_DIR`：相对于工作树根目录的目录路径
- `ZED_STEM`：文件名（不含扩展名）
- `ZED_SYMBOL`：当前选中的符号（与面包屑导航一致）
- `ZED_SELECTED_TEXT`：当前选中文本
- `ZED_WORKTREE_ROOT`：当前工作树根目录
- `ZED_CUSTOM_RUST_PACKAGE`：Rust 专用变量，表示源文件所在的父包名称

在任务中使用变量时，以 `$` 前缀：

```json [settings]
{
  "label": "echo current file's path",
  "command": "echo $ZED_FILE"
}
```

也可以使用 `${VAR:default}` 形式为缺省情况提供默认值。变量同样可用于任务的 `cwd`、`args`、`label` 字段。

### 变量转义

若变量代表的路径包含空格等特殊字符，请确保正确转义。比如不要直接写：

```json [settings]
{
  "label": "stat current file",
  "command": "stat $ZED_FILE"
}
```

应改为传参：

```json [settings]
{
  "label": "stat current file",
  "command": "stat",
  "args": ["$ZED_FILE"]
}
```

或手动加引号：

```json [settings]
{
  "label": "stat current file",
  "command": "stat \"$ZED_FILE\""
}
```

### 基于变量过滤任务

若任务模板引用了当下不可用的变量，则这条任务会被过滤掉。例如，下例只有在存在选中文本时才会出现在任务面板：

```json [settings]
{
  "label": "selected text",
  "command": "echo \"$ZED_SELECTED_TEXT\""
}
```

如希望始终显示，可为变量设置默认值：

```json [settings]
{
  "label": "selected text with default",
  "command": "echo \"${ZED_SELECTED_TEXT:no text selected}\""
}
```

## Oneshot 任务

通过 `task: spawn` 打开的任务面板也支持直接执行任意命令：在输入框中键入命令，按 `opt-enter` 即可运行。会话期间，这些临时命令会被记住，`task: rerun` 同样可重新执行上一条临时任务。你还可以对任务模板按 `tab` 进行调整，从而以临时任务的方式运行。

### 临时（Ephemeral）任务

在任务面板中按住 `cmd`（或 `ctrl`）触发运行，任务将不会增加使用计数（不会被 `task: rerun` 复用，也不会在面板中排序靠前）。适合在持续使用 `task: rerun` 时保持流程顺畅。

### 更多重跑控制

默认情况下，任务在第一次运行时会解析变量并缓存结果，之后重跑将重复使用该上下文。若希望每次重跑都重新解析，可将任务的 `"reevaluate_context"` 设为 `true`：

```json [keymap]
{
  "context": "Workspace",
  "bindings": {
    "alt-t": ["task::Rerun", { "reevaluate_context": true }]
  }
}
```

## 自定义任务快捷键

可以在 `keymap.json` 中为任务绑定组合键。例如，为上文的 `echo current file's path` 绑定 `alt-g`：

```json [keymap]
{
  "context": "Workspace",
  "bindings": {
    "alt-g": ["task::Spawn", { "task_name": "echo current file's path" }]
  }
}
```

任务还可指定 `reveal_target` 来控制输出位置，如在中央区域启动终端应用：

```json [tasks]
// tasks.json
{
  "label": "start lazygit",
  "command": "lazygit -p $ZED_WORKTREE_ROOT"
}
```

```json [keymap]
// keymap.json
{
  "context": "Workspace",
  "bindings": {
    "alt-g": [
      "task::Spawn",
      { "task_name": "start lazygit", "reveal_target": "center" }
    ]
  }
}
```

## 将 runnable 标签绑定到任务模板

Zed 允许通过工作区或全局 `tasks.json` 重写内联 runnable 指示器的默认动作，优先级如下：

1. 工作区 `tasks.json`
2. 全局 `tasks.json`
3. 语言扩展默认绑定

在任务模板中增加 `tags` 字段即可为其绑定标签：

```json [settings]
{
  "label": "echo current file's path",
  "command": "echo $ZED_FILE",
  "tags": ["rust-test"]
}
```

这样即可替换某个 runnable 指示器默认触发的任务。

## 通过快捷键运行 runnable 对应任务

当任务与某个 runnable 标签关联后，你可以通过 [代码操作](https://zed.dev/docs/configuring-languages?#code-actions) 快速执行：使用 `editor: Toggle Code Actions` 或 `cmd-.`/`ctrl-.` 打开菜单，任务会排在首位；如无其他候选项会直接执行。
