# Markdown

Zed 原生支持 Markdown。

- Tree-sitter：[tree-sitter-markdown](https://github.com/tree-sitter-grammars/tree-sitter-markdown)
- 语言服务器：无

## 代码块语法高亮

Zed 会使用各语言的 [tree-sitter 语法](../extensions/languages.md#grammar) 来高亮 Markdown 中的代码块。所有 [Zed 支持的语言](../languages.md)，包括官方与社区扩展提供的语言，都可以在 Markdown 代码块中使用。只需在开头的 <kbd>```</kbd> 后指定语言名称，例如：

````python
```python
import functools as ft

@ft.lru_cache(maxsize=500)
def fib(n):
    return n if n < 2 else fib(n - 1) + fib(n - 2)
```
````

## 配置

### 格式化

Zed 支持使用 Prettier 自动格式化 Markdown 文档。可以通过 {#action editor::Format} 动作或 {#kb editor::Format} 快捷键手动触发。若希望自动格式化，可在 `settings.json` 中启用 [`format_on_save`](../configuring-zed.md#format-on-save)：

```json [settings]
  "languages": {
    "Markdown": {
      "format_on_save": "on"
    }
  },
```

### 行尾空白

默认情况下 Zed 会在保存时移除行尾空白。如果你依赖 Markdown 中的不可见空格（例如用于转换成 `<br />`），可以这样关闭该行为：

```json [settings]
  "languages": {
    "Markdown": {
      "remove_trailing_whitespace_on_save": false
    }
  },
```
