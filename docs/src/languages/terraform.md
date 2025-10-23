# Terraform

可以通过 [Terraform 扩展](https://github.com/zed-extensions/terraform) 在 Zed 中使用 Terraform。

- Tree-sitter：[MichaHoffmann/tree-sitter-hcl](https://github.com/MichaHoffmann/tree-sitter-hcl)
- 语言服务器：[hashicorp/terraform-ls](https://github.com/hashicorp/terraform-ls)

## 配置

可在 `settings.json` 中配置 Terraform 语言服务器，例如：

```json [settings]
{
  "lsp": {
    "terraform-ls": {
      "initialization_options": {
        "experimentalFeatures": {
          "prefillRequiredFields": true
        }
      }
    }
  }
}
```

完整配置项请参阅[官方文档](https://github.com/hashicorp/terraform-ls/blob/main/docs/SETTINGS.md)。
