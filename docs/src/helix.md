# Helix 模式

> **注意：** 功能仍在完善中，暂未覆盖 Helix 的全部键绑定。

Helix 模式是 Zed 中的仿真层，引入了 Helix 风格的键位与模态编辑。它建立在 Zed 的 [Vim 模式](./vim.md) 之上，启用 `helix_mode` 时会自动开启 `vim_mode`，两者共享大部分核心功能。若想了解 Vim 模式的基础能力，请先阅读 [Vim 模式文档](./vim.md)。

想查看 Helix 模式的最新进展或反馈缺失功能，可访问社区讨论：[“Are we Helix yet?”](https://github.com/zed-industries/zed/discussions/33580)。Helix 默认键位的完整列表见[官方文档](https://docs.helix-editor.com/keymap.html)。

## 核心差异

- 所有可与 `m i` 或 `m a` 配合使用的文本对象，同样支持 `]` 或 `[` 前缀，例如 `] (` 会选择光标之后的下一对括号。

更多 Helix 特性将持续补充，欢迎在社区提出建议。
