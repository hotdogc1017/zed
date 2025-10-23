# 调试器

Zed 通过 [Debug Adapter Protocol (DAP)](https://microsoft.github.io/debug-adapter-protocol/) 实现跨语言调试。DAP 定义了调试器、编辑器与 IDE 之间的通讯方式，使 Zed 无需实现各语言的调试细节即可兼容不同运行时。Zed 充当协议的客户端，各类**调试适配器**负责服务器端逻辑。

借助该协议，你可以以一致的方式设置断点、单步执行、检查变量等操作，无论使用何种语言或运行时。

## 支持的语言

要调试特定语言，Zed 需要找到对应的调试适配器。有些适配器由 Zed 内置，另一些来自[调试器扩展](./extensions/debugger-extensions.md)。目前可用的语言如下（按字母排序）：

- [C](./languages/c.md#debugging)（内置）
- [C++](./languages/cpp.md#debugging)（内置）
- [Go](./languages/go.md#debugging)（内置）
- [JavaScript](./languages/javascript.md#debugging)（内置）
- [PHP](./languages/php.md#debugging)（内置）
- [Python](./languages/python.md#debugging)（内置）
- [Ruby](./languages/ruby.md#debugging)（扩展提供）
- [Rust](./languages/rust.md#debugging)（内置）
- [Swift](./languages/swift.md#debugging)（扩展提供）
- [TypeScript](./languages/typescript.md#debugging)（内置）

> 如果列表中没有你的语言，欢迎贡献调试适配器。请参考 [调试器扩展文档](./extensions/debugger-extensions.md)。

各语言页面提供适配器专属示例；下文介绍通用调试功能。

## 入门

多数语言最快的方式是运行 {#action debugger::Start}（{#kb debugger::Start}）。这会打开“新进程”面板，根据当前项目列出预配置调试任务（如 `main`、测试入口等）。点击调试面板右上角的 “+” 亦可打开同一窗口。

对于未提供预设任务的语言（如 C/C++ 等），可在项目根目录创建 `.zed/debug.json`，写入调试配置数组：

```json [debug]
[
  {
    "adapter": "CodeLLDB",
    "label": "First configuration"
  },
  {
    "adapter": "Debugpy",
    "label": "Second configuration"
  }
]
```

请参阅各语言文档获取示例。配置完成后会显示在“新进程”面板中。若 `.zed/debug.json` 为空，Zed 会读取 `.vscode/launch.json` 并展示其中的配置。

### 启动与附加

调试器通常有两种模式：

- **launch**：启动全新的进程进行调试，适合单元测试或调试构建。
- **attach**：附加到现有进程，适用于无法重启的线上程序等场景。

## 配置选项

每个调试任务必须指定 `adapter` 与 `label`。此外，Zed 支持在 `build` 字段定义预先运行的任务（见下文），并可通过 `tcp_connection` 字段连接到已有进程。其他字段由适配器定义，并可使用 [任务变量](./tasks.md#variables)。多数适配器支持以下键：

```json [debug]
[
  {
    "label": "Example Start debugger config",
    "adapter": "Example adapter name",
    "request": "launch",       // launch：启动进程；attach：附加到进程
    "program": "path_to_program",
    "cwd": "$ZED_WORKTREE_ROOT"
  }
]
```

请查阅适配器文档了解更多字段说明。

### 构建任务

在 `build` 字段中可嵌入一个 Zed 任务，调试前执行。例如：

```json [debug]
[
  {
    "label": "Build Binary",
    "adapter": "CodeLLDB",
    "program": "path_to_program",
    "request": "launch",
    "build": {
      "command": "make",
      "args": ["build", "-j8"]
    }
  }
]
```

也可以直接引用已有任务标签：

```json [debug]
[
  {
    "label": "Build Binary",
    "adapter": "CodeLLDB",
    "program": "path_to_program",
    "request": "launch",
    "build": "my build task"
  }
]
```

### 自动创建场景

若项目中已有 Zed 任务，调试器可自动生成调试场景，并支持从代码左侧的 “运行” 图标直接创建。目前适用于 Rust、Go、Python、JavaScript、TypeScript。

## 断点

点击编辑器行号左侧即可设置断点。右键断点图标可配置高级选项：

- 添加日志：命中时输出信息
- 条件断点：仅在条件满足时停下（语法依适配器而异）
- 命中次数：达到设定次数后触发
- 禁用断点：保留但不触发

部分适配器（如 CodeLLDB、JavaScript）会验证断点是否可命中，并在 UI 中突出显示。所有断点可在调试面板的 “Breakpoints” 项管理，部分适配器还支持配置异常断点。

## 设置项

调试器的配置集中在 `settings.json` 的 `debugger` 节点：

- `dock`：调试面板位置
- `stepping_granularity`：单步粒度
- `save_breakpoints`：是否在会话间保留断点
- `button`：是否在状态栏显示调试按钮
- `timeout`：连接 TCP 调试器时的超时时间（毫秒）
- `log_dap_communications`：是否记录 DAP 通讯
- `format_dap_log_messages`：日志中是否格式化 DAP 消息

### 面板停靠

- 描述：调试面板所在位置
- 默认：`bottom`
- 选项：`left` / `right` / `bottom`

```json [settings]
"debugger": {
  "dock": "bottom"
}
```

### 单步粒度

- 描述：单步执行的粒度
- 默认：`line`
- 设置：`debugger.stepping_granularity`

选项示例：

1. `statement`：按语句级单步，语句范围由适配器决定。

```json [settings]
{
  "debugger": {
    "stepping_granularity": "statement"
  }
}
```

2. `line`：按源代码行单步。

（更多选项视调试器支持情况而定。）

## 初始化远程调试服务

在执行调试前，Zed 会使用 `ssh` 建立控制连接，显示任何必要的提示（如主机指纹、私钥密码）。连接成功后会检查远端 `~/.zed_server` 是否存在与本地版本匹配的调试服务；若缺失则自动下载。也可通过设置 `"upload_binary_over_ssh": true` 先下载到本地再上传。

如需手动维护，可从 [GitHub Releases](https://github.com/zed-industries/zed/releases) 下载或使用 `cargo build -p remote_server --release` 编译，并放置到 `~/.zed_server/zed-remote-server-<channel>-<version>`，版本必须与本地 Zed 完全一致。

## 连接与重连

调试过程中，Zed 会复用控制连接派生子连接。当网络中断后会尝试自动重连；若失败，可稍后重新打开项目，未保存改动默认保存在本地。若遇异常，可通过 `cmd-shift-p` > `Open Log` 查看日志，或在 GitHub / Discord 寻求帮助。

## 支持的 SSH 选项

（若调试涉及远程连接）Zed 实际调用 `ssh`，支持常见参数：`-p/-l`、端口转发 `-L/-R`、密钥 `-i`、自定义 `-o`、代理 `-J/-w`、配置文件 `-F` 等。部分选项（如 `-t/-T`）由 Zed 内部管理，不允许覆盖。

## 已知限制

- 目前无法在远程终端中通过 `zed` 命令直接在本地打开文件。

## 反馈渠道

欢迎加入 [Zed Discord](https://zed.dev/community-links) 的 `#remoting-feedback` 频道，或在 GitHub 提交 Issue 与我们讨论调试体验。
