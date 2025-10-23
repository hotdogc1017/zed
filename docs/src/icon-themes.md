# 图标主题

Zed 自带一套默认图标主题，也可以通过扩展获取更多样式。

## 选择图标主题

使用命令面板执行 “icon theme selector: toggle” 打开图标主题选择器。上下移动即可实时预览不同主题，按 `Enter` 会将当前选择写入设置文件。

## 安装额外主题

更多图标主题可在扩展页面获取：

- 命令面板执行 “zed: Extensions”
- 或访问 [Zed 扩展网站](https://zed.dev/extensions)

## 配置图标主题

主题配置存放在 `settings.json`。通过命令面板执行 “zed: open settings”（macOS 默认 `cmd-,`，Linux/Windows `ctrl-,`）打开文件。与界面主题类似，可为浅色/深色模式分别指定不同图标主题，并可强制使用特定模式：

```json [settings]
{
  "icon_theme": {
    "mode": "system",
    "light": "Light Icon Theme",
    "dark": "Dark Icon Theme"
  }
}
```

## 开发图标主题

详见：[开发 Zed 图标主题](./extensions/icon-themes.md)。
