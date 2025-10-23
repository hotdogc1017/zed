# Swift

Zed 对 Swift 的语言支持由社区维护的 [Swift 扩展](https://github.com/zed-extensions/swift) 提供。
如需反馈问题，请访问：[https://github.com/zed-extensions/swift/issues](https://github.com/zed-extensions/swift/issues)

- Tree-sitter：[alex-pinkus/tree-sitter-swift](https://github.com/alex-pinkus/tree-sitter-swift)
- 语言服务器：[swiftlang/sourcekit-lsp](https://github.com/swiftlang/sourcekit-lsp)
- 调试适配器：[`lldb-dap`](https://github.com/swiftlang/llvm-project/blob/next/lldb/tools/lldb-dap/README.md)

## 语言服务器配置

可以在主目录或项目根目录创建 `.sourcekit-lsp/config.json` 来调整 SourceKit LSP 的行为。详见 [SourceKit-LSP 配置文档](https://github.com/swiftlang/sourcekit-lsp/blob/main/Documentation/Configuration%20File.md)。

## 调试

Swift 扩展提供名为 `Swift` 的调试适配器，底层使用 Swift 工具链中的 [`lldb-dap`](https://github.com/swiftlang/llvm-project/blob/next/lldb/tools/lldb-dap/README.md)。扩展会按顺序尝试通过 `swiftly`、`xcrun` 和 `$PATH` 查找 `lldb-dap`，不会自动下载缺失的可执行文件。

- [lldb-dap 配置文档](https://github.com/llvm/llvm-project/blob/main/lldb/tools/lldb-dap/README.md#configuration-settings-reference)

### 示例

#### 构建并调试 Swift 可执行文件

```json [debug]
[
  {
    "label": "Debug Swift",
    "build": {
      "command": "swift",
      "args": ["build"]
    },
    "program": "$ZED_WORKTREE_ROOT/swift-app/.build/arm64-apple-macosx/debug/swift-app",
    "request": "launch",
    "adapter": "Swift"
  }
]
```
