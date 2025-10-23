# 诊断

Zed 从语言服务器获取其诊断信息，并支持 LSP 的推送和拉取变体，这使得它与所有现有的语言服务器兼容。

# 常规诊断

默认情况下，Zed 在编辑器和滚动条中将所有诊断显示为带下划线的文本。

编辑器诊断可以通过以下编辑器设置进行过滤：

```json [settings]
"diagnostics_max_severity": null
```

（可能的值：`"off"`、`"error"`、`"warning"`、`"info"`、`"hint"`、`null`（默认，所有诊断））

滚动条诊断通过以下配置进行配置：

```json [settings]
"scrollbar": {
  "diagnostics": "all",
}
```

（可能的值：`"none"`、`"error"`、`"warning"`、`"information"`、`"all"`（默认））

可以悬停在诊断上以显示包含完整渲染诊断消息的工具提示。
或者，可以使用 `editor::GoToDiagnostic` 和 `editor::GoToPreviousDiagnostic` 在编辑器中的诊断之间导航，为当前活动的诊断显示弹出窗口。

# 内联诊断（错误透镜）

Zed 支持在代码右侧将诊断显示为透镜。
默认情况下禁用此功能，但可以使用编辑器菜单临时打开（或关闭），或使用以下设置永久启用：

```json [settings]
"diagnostics": {
  "inline": {
    "enabled": true,
    "max_severity": null, // 与编辑器设置中的 `diagnostics_max_severity` 相同的值
  }
}
```

# 其他 UI 位置

## 项目面板

项目面板可以根据文件中诊断的严重程度为其条目着色。

要配置，请使用：

```json [settings]
"project_panel": {
  "show_diagnostics": "all",
}
```

配置（可能的值：`"off"`、`"errors"`、`"all"`（默认））

## 编辑器标签

与项目面板类似，编辑器标签可以使用以下配置进行着色：

```json [settings]
"tabs": {
  "show_diagnostics": "off",
}
```

配置（可能的值：`"off"`（默认）、`"errors"`、`"all"`）
