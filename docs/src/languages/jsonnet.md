# Jsonnet

Zed 对 Jsonnet 的语言支持由社区维护的 [Jsonnet 扩展](https://github.com/narqo/zed-jsonnet) 提供。

- Tree-sitter：[sourcegraph/tree-sitter-jsonnet](https://github.com/sourcegraph/tree-sitter-jsonnet)
- 语言服务器：[grafana/jsonnet-language-server](https://github.com/grafana/jsonnet-language-server)

## 配置

可以通过 `settings.json` 中的 `lsp` 配置向语言服务器传递工作区选项。

以下示例演示了如何在 `jsonnet-language-server` 中启用 [tanka](https://tanka.dev) 导入路径解析：

```json [settings]
{
  "lsp": {
    "jsonnet-language-server": {
      "settings": {
        "resolve_paths_with_tanka": true
      }
    }
  }
}
```
