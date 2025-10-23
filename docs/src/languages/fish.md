# Fish

Zed 对 Fish 语言的支持由社区维护的 [Fish 扩展](https://github.com/hasit/zed-fish) 提供。
如需反馈问题，请前往：[https://github.com/hasit/zed-fish/issues](https://github.com/hasit/zed-fish/issues)

- Tree-sitter：[ram02z/tree-sitter-fish](https://github.com/ram02z/tree-sitter-fish)

### 格式化

Zed 支持使用外部工具（如 fish 自带的 [`fish_indent`](https://fishshell.com/docs/current/cmds/fish_indent.html)）对 Fish 代码进行自动格式化。

1. 确认 `fish_indent` 已加入 PATH，并检查版本：

```sh
which fish_indent
fish_indent --version
```

2. 在 Zed 中配置使用 `fish_indent` 自动格式化 Fish 代码：

```json [settings]
  "languages": {
    "Fish": {
      "formatter": {
        "external": {
          "command": "fish_indent"
        }
      }
    }
  },
```
