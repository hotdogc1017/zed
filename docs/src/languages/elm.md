# Elm

可以通过 [Elm 扩展](https://github.com/zed-extensions/elm) 在 Zed 中使用 Elm 支持。

- Tree-sitter：[elm-tooling/tree-sitter-elm](https://github.com/elm-tooling/tree-sitter-elm)
- 语言服务器：[elm-tooling/elm-language-server](https://github.com/elm-tooling/elm-language-server)

## 安装

要在 Zed 中使用 Elm，需要安装 `elm`、`elm-format` 与 `elm-review`。

1. [安装 Elm](https://guide.elm-lang.org/install/elm.html)（在 macOS 上可运行 `brew install elm`）。
2. 安装 `elm-review` 以获得代码检查能力：
   ```sh
   npm install elm-review --save-dev
   ```
3. 安装 `elm-format` 以启用自动格式化：
   ```sh
   npm install -g elm-format
   ```

## 配置 `elm-language-server`

可以通过 `settings.json` 配置 Elm 语言服务器，例如：

```json [settings]
{
  "lsp": {
    "elm-language-server": {
      "initialization_options": {
        "disableElmLSDiagnostics": true,
        "onlyUpdateDiagnosticsOnSave": false,
        "elmReviewDiagnostics": "warning"
      }
    }
  }
}
```

`elm-format`、`elm-review` 与 `elm` 需要安装在环境中（或在设置中指定路径）。完整配置项请查看[官方说明](https://github.com/elm-tooling/elm-language-server?tab=readme-ov-file#server-settings)。
