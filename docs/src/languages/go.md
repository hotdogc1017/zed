# Go

Zed 原生支持 Go。

- Tree-sitter：[tree-sitter/tree-sitter-go](https://github.com/tree-sitter/tree-sitter-go)
- 语言服务器：[golang/tools/tree/master/gopls](https://github.com/golang/tools/tree/master/gopls)
- 调试适配器：[delve](https://github.com/go-delve/delve)

## 安装

我们推荐通过 Go 自带的包管理器安装 gopls，而不是使用 Homebrew 或 Linux 发行版的软件源。

1. 确保卸载所有通过包管理器安装的 gopls 版本：

```sh
# macOS（Homebrew）
brew remove gopls
# Ubuntu
sudo apt-get remove gopls
sudo snap remove gopls
# Arch
sudo pacman -R gopls
```

2. 使用 go module 工具安装/更新最新版本的 `gopls`：

```sh
go install golang.org/x/tools/gopls@latest
```

3. 确保 `gopls` 已加入 PATH：

```sh
which gopls
gopls version
```

若提示找不到 `gopls`，可以在 `.zshrc` 或 `.bash_profile` 中添加 `export PATH="$PATH:$HOME/go/bin"`。

## 内联提示

Zed 会为 gopls 设置以下初始化选项，以便在设置中启用内联提示时获取相关信息：

```json [settings]
"hints": {
    "assignVariableTypes": true,
    "compositeLiteralFields": true,
    "compositeLiteralTypes": true,
    "constantValues": true,
    "functionTypeParameters": true,
    "parameterNames": true,
    "rangeVariableTypes": true
}
```

如需覆盖这些设置，可在 `settings.json` 中加入：

```json [settings]
"lsp": {
    "gopls": {
        "initialization_options": {
            "hints": {
                // ....
            }
        }
    }
}
```

更多详情可参考 [gopls 内联提示文档](https://github.com/golang/tools/blob/master/gopls/doc/inlayHints.md)。

## 调试

Zed 支持使用 Delve 对 Go 测试与入口函数（`func main`）进行零配置调试。执行 {#action debugger::Start}（{#kb debugger::Start}）即可看到上下文相关的预设调试任务列表。

若需要更多控制，可以在 `.zed/debug.json` 中添加调试配置，示例如下。

- [Delve 配置文档](https://github.com/go-delve/delve/blob/master/Documentation/api/dap/README.md#launch-and-attach-configurations)

### 调试 Go 包

若要调试特定包，可将 Delve 的模式设为 `debug`，此时 `program` 应设置为包名。

```json [debug]
[
  {
    "label": "Go (Delve)",
    "adapter": "Delve",
    "program": "$ZED_FILE",
    "request": "launch",
    "mode": "debug"
  },
  {
    "label": "Run server",
    "adapter": "Delve",
    "request": "launch",
    "mode": "debug",
    // 对于 Delve，program 可以是包名
    "program": "./cmd/server"
    // "args": [],
    // "buildFlags": [],
  }
]
```

### 调试 Go 测试

要调试某个包的测试，将 Delve 模式设为 `test`。`program` 仍然是包名，可通过 `buildFlags` 指定标签等选项，通过 `args` 为测试二进制传参（详见 `go help testflags`）。

```json [debug]
[
  {
    "label": "Run integration tests",
    "adapter": "Delve",
    "request": "launch",
    "mode": "test",
    "program": ".",
    "buildFlags": ["-tags", "integration"]
    // 若只运行光标所在的测试：
    // "args": ["-test.run", "$ZED_SYMBOL"]
  }
]
```

### 先构建再调试

如果需要使用特定命令构建应用，可以使用 Delve 的 `exec` 模式。在这种情况下，`program` 指向可执行文件，`build` 部分负责构建该文件。

```json [debug]
[
  {
    "label": "Debug Prebuilt Unit Tests",
    "adapter": "Delve",
    "request": "launch",
    "mode": "exec",
    "program": "${ZED_WORKTREE_ROOT}/__debug_unit",
    "args": ["-test.v", "-test.run=${ZED_SYMBOL}"],
    "build": {
      "command": "go",
      "args": [
        "test",
        "-c",
        "-tags",
        "unit",
        "-gcflags\"all=-N -l\"",
        "-o",
        "__debug_unit",
        "./pkg/..."
      ]
    }
  }
]
```

### 连接已运行的 Delve 实例

若需连接到正在运行（甚至不在本机）的 Delve，可通过 `tcp_connection` 配置让 Zed 接入该实例。

```json [debug]
[
  {
    "adapter": "Delve",
    "label": "Connect to a running Delve instance",
    "program": "/Users/zed/Projects/language_repositories/golang/hello/hello",
    "cwd": "/Users/zed/Projects/language_repositories/golang/hello",
    "args": [],
    "env": {},
    "request": "launch",
    "mode": "exec",
    "stopOnEntry": false,
    "tcp_connection": { "host": "127.0.0.1", "port": 53412 }
  }
]
```

此时 Zed 不会再启动新的 Delve 进程，而是直接使用已有实例。因此 Zed 中不会打开终端，需要直接与该 Delve 实例交互，它负责调试进程的标准输入输出。

## Go Mod

- Tree-sitter：[camdencheek/tree-sitter-go-mod](https://github.com/camdencheek/tree-sitter-go-mod)
- 语言服务器：无

## Go Sum

- Tree-sitter：[amaanq/tree-sitter-go-sum](https://github.com/amaanq/tree-sitter-go-sum)
- 语言服务器：无

## Go Work

- Tree-sitter：[tree-sitter-go-work](https://github.com/d1y/tree-sitter-go-work)
- 语言服务器：无
