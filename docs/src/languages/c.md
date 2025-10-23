# C

Zed 原生支持 C 语言。

- Tree-sitter：[tree-sitter/tree-sitter-c](https://github.com/tree-sitter/tree-sitter-c)
- 语言服务器：[clangd/clangd](https://github.com/clangd/clangd)
- 调试适配器：[CodeLLDB](https://github.com/vadimcn)（首选），[GDB](https://sourceware.org/gdb/)（次选，Apple Silicon 不可用）

## Clangd：强制识别为 C

Clangd 默认会假设项目可能同时包含 C 与 C++。如果你正在处理纯 C 项目，可以使用 `-xc` 标志告知 clangd 将所有文件视为 C。只需在项目根目录下创建 `.clangd` 文件，并写入：

```yaml
CompileFlags:
  Add: [-xc]
```

按照惯例，clang 和 gcc 会把 `*.C` 与 `*.H`（大写扩展名）识别为 C++ 而非 C，Zed 也遵循这一约定。如果你的纯 C 项目包含类似 `FILENAME.C` 的旧式大写扩展名，可以在设置中覆盖这一行为：

```json [settings]
{
  "file_types": {
    "C": ["C", "H"]
  }
}
```

## 格式化

默认情况下，Zed 会调用 `clangd` 语言服务器来格式化 C 代码，其效果与 `clang-format` 命令行工具一致。要自定义格式，将 `.clang-format` 文件添加到项目中，例如：

```yaml
---
BasedOnStyle: GNU
IndentWidth: 2
---
```

完整的配置项可参见 [Clang-Format Style Options](https://clang.llvm.org/docs/ClangFormatStyleOptions.html)。

你可以通过 {#kb editor::Format}、命令面板中的 `editor: format`，或在 Zed 设置里为 C 启用 `format_on_save` 来触发格式化：

```json [settings]
  "languages": {
    "C": {
      "format_on_save": "on",
      "tab_size": 2
    }
  }
```

## 编译命令

在某些项目中，clangd 需要 `compile_commands.json` 文件来正确分析代码。该文件包含编译数据库，用于告诉 clangd 如何构建你的项目。

### CMake 编译命令

使用 CMake 时，只需在 `CMakeLists.txt` 中加入以下语句，即可自动生成 `compile_commands.json`：

```cmake
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
```

完成构建后，CMake 会在构建目录中生成 `compile_commands.json`，clangd 会自动读取。

## 调试

你可以使用 CodeLLDB 或 GDB 对原生二进制进行调试（请确保在编译时向编译器传入 `-g` 以生成调试信息）。在 `.zed/debug.json` 中添加如下示例配置：

- [CodeLLDB 配置文档](https://github.com/vadimcn/codelldb/blob/master/MANUAL.md#starting-a-new-debug-session)
- [GDB 配置文档](https://sourceware.org/gdb/current/onlinedocs/gdb.html/Debugger-Adapter-Protocol.html)

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
