# 主题

Zed 附带了许多内置主题，还有更多主题可作为扩展使用。

## 选择主题

通过主题选择器查看已安装的主题并预览它们，你可以通过命令面板中的 "theme selector: Toggle"（在 macOS 上绑定到 `cmd-k cmd-t`，在 Linux 上绑定到 `ctrl-k ctrl-t`）打开主题选择器。

通过上下移动导航主题列表将实时更改主题，按回车键将保存到你的设置文件中。

## 安装更多主题

更多主题可从扩展页面获得，你可以通过命令面板中的 "zed: Extensions" 或 [Zed 网站](https://zed.dev/extensions)访问。

许多流行的主题已移植到 Zed，如果你难以选择，请访问 [zed-themes.com](https://zed-themes.com)，这是一个第三方画廊，包含许多主题的可见预览。

## 配置主题

你选择的主题存储在设置文件中。你可以通过命令面板中的 "zed: Open Settings"（在 macOS 上绑定到 `cmd-,`，在 Linux 上绑定到 `ctrl-,`）打开设置文件。

默认情况下，Zed 维护两个主题：一个用于浅色模式，一个用于深色模式。你可以将模式设置为 `"dark"` 或 `"light"` 以忽略当前系统模式。

```json [settings]
{
  "theme": {
    "mode": "system",
    "light": "One Light",
    "dark": "One Dark"
  }
}
```

## 主题覆盖

要覆盖主题的特定属性，请使用 `experimental.theme_overrides` 设置。

例如，如果你希望覆盖编辑器的背景颜色并将注释和文档注释显示为斜体，请将以下内容添加到你的 `settings.json` 中：

```json [settings]
{
  "experimental.theme_overrides": {
    "editor.background": "#333",
    "syntax": {
      "comment": {
        "font_style": "italic"
      },
      "comment.doc": {
        "font_style": "italic"
      }
    }
  }
}
```

要查看捕获的全面列表（如 `comment` 和 `comment.doc`），请参阅：[语言扩展：语法高亮](./extensions/languages.md#syntax-highlighting)。

要查看可用主题属性的列表，请查看你的主题的 JSON 文件。例如，默认的 One Dark 和 One Light 主题的 [assets/themes/one/one.json](https://github.com/zed-industries/zed/blob/main/assets/themes/one/one.json)。

## 本地主题

通过将新主题放置在 `~/.config/zed/themes` 目录（macOS 和 Linux）或 `%USERPROFILE%\AppData\Roaming\Zed\themes\`（Windows）中来本地存储新主题。

例如，要创建一个名为 `my-cool-theme` 的新主题，请在该目录中创建一个名为 `my-cool-theme.json` 的文件。它将在下次 Zed 加载时在主题选择器中可用。

在 [zed-themes.com](https://zed-themes.com) 上找到更多主题。

## 主题开发

请参阅：[开发 Zed 主题](./extensions/themes.md)
