# 发布说明

每当您打开一个 pull request 时，正文会根据这个 [pull request 模板](https://github.com/zed-industries/zed/blob/main/.github/pull_request_template.md) 自动填充。

```md
...

Release Notes:

- N/A _或_ Added/Fixed/Improved ...
```

在周三，我们运行一个 [`get-preview-channel-changes`](https://github.com/zed-industries/zed/blob/main/script/get-preview-channel-changes) 脚本，该脚本从预览版中的 pull requests 中提取 `Release Notes` 行，如我们的[发布](https://zed.dev/docs/development/releases)文档中所记录。

该脚本输出 `Release Notes` 行下面的所有内容，包括附加数据，如 pull request 作者（如果不是 Zed 团队成员）和指向 pull request 的链接。
如果您使用 `N/A`，脚本将完全跳过您的 pull request。

## 编写 `Release Notes` 行的指导原则

- 只有当用户能够在 Zed 中看到或感受到差异时，才应该编写 `Release Notes` 行。
- `Release Notes` 行应该以 Zed 用户能够理解的方式编写。
  不要假设用户了解技术编辑器开发术语；用他们作为文本编辑器用户能够理解的语言来描述您的更改。
- 如果您想包含供其他团队成员查看的技术细节，请在 `Release Notes` 行上方进行。
- 对文档的更改应标记为 `N/A`。
- 如果您的 pull request 添加/更改了设置或键绑定，请始终提及该设置或键绑定。
  不要让用户深入文档或 pull request 来查找此信息（尽管它也应该包含在文档中）。
- 对于还原的 pull requests：
  - 如果被还原的项目**已经发布**，请包含一个 `Release Notes` 行解释我们为什么还原，因为这是一个破坏性更改。
  - 如果被还原的项目**尚未发布**，请将原始 PR 的 `Release Notes` 行编辑为 `N/A`；否则，它将被包含在内，发布说明的编译者可能不知道跳过它，导致我们声称发布了实际上没有发布的内容的尴尬情况。
