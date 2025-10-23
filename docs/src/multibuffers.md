# 多缓冲区（Multibuffer）

多缓冲区让你可以同时编辑多个文件，配合多光标后能大幅加快跨文件的重构效率。

## 在多缓冲区中编辑

<div class="video" style="position: relative; padding-top: 71.71314741035857%;">
  <iframe
    src="https://customer-snccc0j9v3kfzkif.cloudflarestream.com/bda0a6584c19f4b39e58a263c0ae4358/iframe?muted=true&preload=true&loop=true&autoplay=true&poster=https%3A%2F%2Fcustomer-snccc0j9v3kfzkif.cloudflarestream.com%2Fbda0a6584c19f4b39e58a263c0ae4358%2Fthumbnails%2Fthumbnail.jpg%3Ftime%3D%26height%3D600&controls=false"
    style="border: none; position: absolute; top: 0; left: 0; height: 100%; width: 100%;"
    allow="accelerometer; gyroscope; autoplay; encrypted-media; picture-in-picture;"
    allowfullscreen="true"
  ></iframe>
</div>

多缓冲区的编辑体验与普通文件一致，改动会实时反映到对应文件中。可以通过 `editor: Save`（macOS `cmd-s`、Windows/Linux `ctrl-s`、Vim 模式 `:w`）保存所有修改。

若希望一次性编辑多处内容，可结合多光标使用：

- 鼠标：`option-click`（macOS）或 `alt-click`（Windows/Linux）
- 快捷键：`cmd-d`（macOS）、`ctrl-d`（Windows/Linux）、Vim 模式 `g l` 会选择下一个与光标单词匹配的实例
- 全选所有匹配：`editor: Select All Matches`（macOS `cmd-shift-l`、Windows/Linux `ctrl-shift-l`、Vim 模式 `g a`）

## 打开源文件

多缓冲区中的摘录可随时跳转到源文件：

- 点击摘录之间的分隔线；或
- 将光标放在摘录中执行 `editor: open excerpts`

若使用多光标，命令会针对每个光标打开对应文件。想通过鼠标双击打开，可在设置中启用：

```json [settings]
"double_click_in_multibuffer": "open"
```

## 项目搜索

执行 `pane: Toggle Search`（macOS `cmd-shift-f`、Windows/Linux `ctrl-shift-f`、Vim 模式 `g/`）发起项目搜索。搜索结果会以多缓冲区形式展示，每个匹配行对应一段摘录。

## 诊断面板

若启用了语言服务器，可通过状态栏图标或 `diagnostics: Deploy`（macOS `cmd-shift-m`、Windows/Linux `ctrl-shift-m`、Vim 模式 `:clist`）打开诊断面板，查看项目中的全部错误，同样以多缓冲区形式呈现。

## 查找引用

语言服务器支持下，可使用 `editor: Find References`（macOS `cmd-click`、Windows/Linux `ctrl-click`、Vim 模式 `g A`）查看符号引用。若存在多个定义，`editor: Go To Definition`、`editor: Go To Type Definition` 等命令也会以多缓冲区形式列出各候选位置。
