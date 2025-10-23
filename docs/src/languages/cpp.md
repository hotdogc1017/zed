# C++

Zed 原生支持 C++。

- Tree-sitter：[tree-sitter/tree-sitter-cpp](https://github.com/tree-sitter/tree-sitter-cpp)
- 语言服务器：[clangd/clangd](https://github.com/clangd/clangd)

## 可执行文件

你可以控制 Zed 应使用哪个 `clangd` 可执行文件。

默认情况下，Zed 会在你的 `$PATH` 中查找 `clangd` 并尝试使用它。如果加载成功，就继续使用该版本；如果失败，Zed 会退回到下载自身捆绑的 `clangd` 版本。

如果希望安装预发布版本的 `clangd`，可以在 `settings.json` 中将 `pre_release` 设置为 `true`：

```json [settings]
{
  "lsp": {
    "clangd": {
      "fetch": {
        "pre_release": true
      }
    }
  }
}
```

若想禁止 Zed 搜索系统中的 `clangd`，可以在 `settings.json` 中将 `ignore_system_version` 设为 `true`：

```json [settings]
{
  "lsp": {
    "clangd": {
      "binary": {
        "ignore_system_version": true
      }
    }
  }
}
```

如果你希望使用自定义位置的可执行文件，可以指定 `path` 以及可选的 `arguments`：

```json [settings]
{
  "lsp": {
    "clangd": {
      "binary": {
        "path": "/path/to/clangd",
        "arguments": []
      }
    }
  }
}
```

这里的 `"path"` 必须是绝对路径。

## 启动参数

可以向 clangd 传递任意数量的参数。完整选项可通过命令行执行 `clangd --help` 查看。例如指定 `--function-arg-placeholders=0` 时，自动补全只会插入函数调用的括号；默认值 `--function-arg-placeholders=1` 会同时插入形参占位符。

```json [settings]
{
  "lsp": {
    "clangd": {
      "binary": {
        "path": "/path/to/clangd",
        "arguments": ["--function-arg-placeholders=0"]
      }
    }
  }
}
```

## 格式化

默认情况下，Zed 使用 `clangd` 来格式化 C++ 代码，其行为与 `clang-format` CLI 相同。你可以通过在项目中添加 `.clang-format` 文件进行配置，例如：

```yaml
---
BasedOnStyle: LLVM
IndentWidth: 4
---
Language: Cpp
# 强制指针符号贴近类型。
DerivePointerAlignment: false
PointerAlignment: Left
---
```

完整选项列表参见 [Clang-Format Style Options](https://clang.llvm.org/docs/ClangFormatStyleOptions.html)。

你可以通过 {#kb editor::Format}、命令面板中的 `editor: format`，或在 Zed 设置中为 C++ 启用 `format_on_save` 来触发格式化：

```json [settings]
  "languages": {
    "C++": {
      "format_on_save": "on",
      "tab_size": 2
    }
  }
```

## 更多服务器配置

通常会在项目根目录创建 `.clangd` 文件来提供额外配置：

```text
CompileFlags:
  Add:
    - "--include-directory=/path/to/include"
Diagnostics:
  MissingIncludes: Strict
  UnusedIncludes: Strict
```

关于 clangd 配置文件的高级用法，请参阅其[官方页面](https://clangd.llvm.org/config.html)。

## 编译命令

在某些项目中，clangd 需要 `compile_commands.json` 文件才能正确分析代码。该文件包含编译数据库，指导 clangd 如何构建项目。

### CMake 编译命令

使用 CMake 时，可在 `CMakeLists.txt` 中添加：

```cmake
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
```

完成构建后，CMake 会在构建目录生成 `compile_commands.json`，clangd 会自动加载。

## 调试

可以使用 CodeLLDB 或 GDB 调试原生二进制文件（请确保构建时向编译器传入 `-g` 以生成调试信息）。可在 `.zed/debug.json` 中加入如下示例配置：

- [CodeLLDB 配置文档](https://github.com/vadimcn/codelldb/blob/master/MANUAL.md#starting-a-new-debug-session)
- [GDB 配置文档](https://sourceware.org/gdb/current/onlinedocs/gdb.html/Debugger-Adapter-Protocol.html)
  - GDB 版本需至少为 14.1

### 构建并调试二进制

```json [debug]
[
  {
    "label": "Debug native binary",
    "build": {
      "command": "make",
      "args": ["-j8"],
      "cwd": "$ZED_WORKTREE_ROOT"
    },
    "program": "$ZED_WORKTREE_ROOT/build/prog",
    "request": "launch",
    "adapter": "CodeLLDB"
  }
]
```
