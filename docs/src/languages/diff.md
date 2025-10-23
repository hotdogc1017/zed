# Diff

Zed 原生支持 Diff。

- Tree-sitter：[zed-industries/the-mikedavis/tree-sitter-diff](https://github.com/the-mikedavis/tree-sitter-diff)

## 配置

Zed 不会尝试格式化 diff 文件，并且会将 [`remove_trailing_whitespace_on_save`](https://zed.dev/docs/configuring-zed#remove-trailing-whitespace-on-save) 与 [`ensure-final-newline-on-save`](https://zed.dev/docs/configuring-zed#ensure-final-newline-on-save) 默认设为 false。

Zed 会自动把扩展名为 `patch` 或 `diff` 的文件识别为 Diff 文件。若需识别其他扩展名，可以在 Zed 的 `settings.json` 中为 `file_types` 添加：

```json [settings]
  "file_types": {
    "Diff": ["dif"]
  },
```
