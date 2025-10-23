# Helm

Zed 对 Helm 的支持由社区维护的 [Helm 扩展](https://github.com/cabrinha/helm.zed) 提供。

- Tree-sitter：[tree-sitter-go-template](https://github.com/ngalaiko/tree-sitter-go-template/tree/master)
- 语言服务器：[mrjosh/helm-ls](https://github.com/mrjosh/helm-ls)

## 设置

可在 `.zed/settings.json` 中加入以下配置，让 Helm 文件使用 Helm 语言：

```json [settings]
  "file_types": {
    "Helm": [
      "**/templates/**/*.tpl",
      "**/templates/**/*.yaml",
      "**/templates/**/*.yml",
      "**/helmfile.d/**/*.yaml",
      "**/helmfile.d/**/*.yml",
      "**/values*.yaml"
    ]
  }
```

这样也会将 values.yaml 文件标记为 Helm 类型，因为 helm-ls 支持解析它们。
