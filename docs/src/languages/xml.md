# XML

可以通过 [XML 扩展](https://github.com/sweetppro/zed-xml/) 在 Zed 中使用 XML。

- Tree-sitter：[tree-sitter-grammars/tree-sitter-xml](https://github.com/tree-sitter-grammars/tree-sitter-xml)

## 配置

若有其他扩展名未被自动识别为 XML，可在 Zed 设置的 [file_types](../configuring-zed.md#file-types) 中添加：

```json [settings]
  "file_types": {
    "XML": ["rdf", "gpx", "kml"]
  }
```
