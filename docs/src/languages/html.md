# HTML

可以通过 [HTML 扩展](https://github.com/zed-industries/zed/tree/main/extensions/html) 在 Zed 中使用 HTML 支持。

- Tree-sitter：[tree-sitter/tree-sitter-html](https://github.com/tree-sitter/tree-sitter-html)
- 语言服务器：[microsoft/vscode-html-languageservice](https://github.com/microsoft/vscode-html-languageservice)

该扩展会自动安装；如果不想使用它，可以在设置中加入：

```json [settings]
{
  "auto_install_extensions": {
    "html": false
  }
}
```

## 格式化

Zed 默认使用 [Prettier](https://prettier.io/) 来格式化 HTML。

若想禁用 `format_on_save`，可在 Zed 的 `settings.json` 中添加：

```json [settings]
  "languages": {
    "HTML": {
      "format_on_save": "off"
    }
  }
```

即便禁用自动格式化，你仍可以通过 {#kb editor::Format}，或打开[命令面板](../getting-started.md#command-palette)（{#kb command_palette::Toggle}）并选择 “Format Document” 来手动格式化。

### 使用语言服务器格式化

如果倾向于使用 `vscode-html-language-server` 提供的自动格式化，而非 Prettier，可在 Zed 设置中添加：

```json [settings]
  "languages": {
    "HTML": {
      "formatter": "language_server"
    }
  }
```

你可以在 `settings.json` 中自定义 `vscode-html-language-server` 的各类[格式化选项](https://code.visualstudio.com/docs/languages/html#_formatting)：

```json [settings]
  "lsp": {
    "vscode-html-language-server": {
      "settings": {
        "html": {
          "format": {
            // 在 <html> 与 <head> 内缩进（默认：false）
            "indentInnerHtml": true,
            // 禁止格式化 <svg> 与 <script> 内的内容
            "contentUnformatted": "svg,script",
            // 在 <div> 与 <p> 前额外添加空行
            "extraLiners": "div,p"
          }
        }
      }
    }
  }
```

## 另请参阅

- [CSS](./css.md)
- [JavaScript](./javascript.md)
- [TypeScript](./typescript.md)
