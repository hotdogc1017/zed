# C#

在 Zed 设置中，语言名称应写作 "CSharp"，而不是 "C#"。

可通过 [C# 扩展](https://github.com/zed-extensions/csharp) 获得 C# 支持。

- Tree-sitter：[tree-sitter/tree-sitter-c-sharp](https://github.com/tree-sitter/tree-sitter-c-sharp)
- 语言服务器：[OmniSharp/omnisharp-roslyn](https://github.com/OmniSharp/omnisharp-roslyn)

## 配置

可以在 Zed 设置文件中为 `OmniSharp` 可执行文件提供自定义路径：

```json [settings]
{
  "lsp": {
    "omnisharp": {
      "binary": {
        "path": "/path/to/OmniSharp",
        "arguments": ["optional", "additional", "args", "-lsp"]
      }
    }
  }
}
```
