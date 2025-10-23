# Erlang

可以通过 [Erlang 扩展](https://github.com/zed-extensions/erlang) 在 Zed 中启用 Erlang 支持。

- Tree-sitter：[WhatsApp/tree-sitter-erlang](https://github.com/WhatsApp/tree-sitter-erlang)
- 语言服务器：
  - [erlang-ls/erlang_ls](https://github.com/erlang-ls/erlang_ls)
  - [WhatsApp/erlang-language-platform](https://github.com/WhatsApp/erlang-language-platform)

## 选择语言服务器

Erlang 扩展支持 `erlang_ls` 与 `erlang-language-platform` 两种语言服务器。

默认启用 `erlang_ls`。

若要切换到 `erlang-language-platform`，在 `settings.json` 中加入：

```json [settings]
{
  "languages": {
    "Erlang": {
      "language_servers": ["elp", "!erlang-ls", "..."]
    }
  }
}
```

## 另请参阅

- [Elixir](./elixir.md)
- [Gleam](./gleam.md)
