# Luau

[Luau](https://luau.org/) 是一种源自 Lua 的脚本语言，具有快速、体积小、安全、渐进类型与可嵌入等特性，由 Roblox 开发并以 MIT 许可证发布。

Zed 对 Luau 的语言支持由社区维护的 [Luau 扩展](https://github.com/4teapo/zed-luau) 提供。
如需反馈问题，请访问：[https://github.com/4teapo/zed-luau/issues](https://github.com/4teapo/zed-luau/issues)

- Tree-sitter：[4teapo/tree-sitter-luau](https://github.com/4teapo/tree-sitter-luau)
- 语言服务器：[JohnnyMorganz/luau-lsp](https://github.com/JohnnyMorganz/luau-lsp)

## 配置

相关配置说明请参阅 [Luau Zed 扩展 README](https://github.com/4teapo/zed-luau)。

## 格式化

可使用 Lua 代码格式化工具 [JohnnyMorganz/StyLua](https://github.com/JohnnyMorganz/StyLua) 来自动格式化代码。

安装方式：

```sh
# macOS（Homebrew）
brew install stylua
# 或通过 Cargo
cargo install stylua --features lua52,lua53,lua54,luau
```

然后在 Zed 的 `settings.json` 中加入：

```json [settings]
  "languages": {
    "Luau": {
      "formatter": {
        "external": {
          "command": "stylua",
          "arguments": ["-"]
        }
      }
    }
  }
```
