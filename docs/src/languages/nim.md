# Nim

Zed 对 Nim 的语言支持由社区维护的 [Nim 扩展](https://github.com/foxoman/zed-nim) 提供。
如需反馈问题，请访问：[https://github.com/foxoman/zed-nim/issues](https://github.com/foxoman/zed-nim/issues)

- Tree-sitter：[alaviss/tree-sitter-nim](https://github.com/alaviss/tree-sitter-nim)
- 语言服务器：[nim-lang/langserver](https://github.com/nim-lang/langserver)

## 格式化

若要使用 [arnetheduck/nph](https://github.com/arnetheduck/nph) 作为格式化工具，请按照 [nph 安装指南](https://github.com/arnetheduck/nph?tab=readme-ov-file#installation) 安装，并在 Zed 的 `settings.json` 中添加：

```json [settings]
  "languages": {
    "Nim": {
      "formatter": {
        "external": {
          "command": "nph",
          "arguments": ["-"]
        }
      }
    }
  }
```
