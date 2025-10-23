# 大纲面板

除了快捷大纲（`cmd-shift-o`）之外，Zed 还提供持久化的大纲面板。可通过 `cmd-shift-b`（命令面板 `outline panel: toggle focus`）或状态栏按钮打开。

在单文件缓冲区（tab 中只有一个文件）中，大纲面板与大纲弹窗类似，基于 tree-sitter 显示当前文件的符号结构。点击任意条目即可跳转，面板也会随光标所在位置自动滚动定位。

![单文件下的大纲面板](https://zed.dev/img/outline-panel/singleton.png)

## 搭配多缓冲区

在多缓冲区场景下，大纲面板尤为强大，可快速浏览大量结果：

- **项目搜索**：总览搜索结果，按条目跳转
- **项目诊断**：汇总语言服务器返回的错误/警告
- **查找引用**：配合 `editor: find all references` 快速定位所有引用

![搜索结果的多缓冲区大纲](https://zed.dev/img/outline-panel/project-search.png)

![诊断结果的大纲面板](https://zed.dev/img/outline-panel/project-diagnostics.png)

![查看引用的大纲面板](https://zed.dev/img/outline-panel/find-all-references.png)

使用大纲面板可以在大型结果集中保持上下文，快速跳转到关心的片段。
