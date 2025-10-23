---
description: Zed 是一个支持大量 Git 功能的文本编辑器
title: Zed 编辑器 Git 集成文档
---

# Git

Zed 目前提供了一套基础的 Git 功能，未来将支持更高级的功能，如冲突解决工具、逐行暂存等。

以下是当前支持的所有功能概述：

- 提交
- 暂存、推送、拉取和获取
- 项目差异：所有更改的多缓冲区视图
- 缓冲区和编辑器滚动条中的差异指示器
- 编辑器中未暂存更改的内联差异切换和恢复
- 项目面板中的 Git 状态
- 分支创建和切换
- Git 追溯查看
- Git 存储的弹出、应用、删除和查看

## Git 面板

Git 面板让你可以鸟瞰工作树和 Git 暂存区的状态。

你可以使用 {#action git_panel::ToggleFocus} 或点击状态栏中的 Git 图标来打开 Git 面板。

在面板中，你可以一目了然地看到项目的状态——哪个仓库和分支处于活动状态，哪些文件已更改以及每个文件的当前暂存状态。

Zed 会监控你的仓库，以便你在命令行上所做的更改会立即反映出来。

## 项目差异

你可以通过打开项目差异 ({#kb git::Diff}) 来查看 Git 在 Zed 中捕获的所有更改，可通过命令面板或 Git 面板中的 {#action git::Diff} 操作访问。

项目差异中显示的所有更改的行为与任何其他多缓冲区完全相同：它们都是文件的可编辑摘录。

你可以通过点击标签栏上的按钮或相应的键绑定来暂存或取消暂存每个代码块以及整个文件。

<!-- 添加媒体 -->

## 获取、推送和拉取

通过 Git 面板上可用的按钮或通过命令面板查找相应的操作：{#action git::Fetch}、{#action git::Push} 和 {#action git::Pull}，在 Zed 中获取、推送或拉取你的 Git 仓库。

## 暂存工作流

Zed 有两种主要的暂存工作流，使用项目差异或直接使用面板。

### 使用项目差异

在项目差异视图中，你可以专注于每个代码块，并通过点击标签栏按钮或通过键绑定 {#action git::StageAndNext} ({#kb git::StageAndNext}) 单独暂存它们。

类似地，使用 {#action git::StageAll} ({#kb git::StageAll}) 键绑定同时暂存所有代码块，然后立即使用 {#action git::Commit} ({#kb git::Commit}) 提交。

### 使用 Git 面板

从面板中，你可以简单地输入提交消息并点击提交按钮，或使用 {#action git::Commit}。这将自动暂存所有跟踪的文件（在条目的复选框中用 `[·]` 表示）并提交它们。

<!-- 显示默认暂存的更改集 -->

可以使用每个单独条目的复选框来暂存条目。可以使用面板顶部的按钮或 {#action git::StageAll} 来暂存所有更改。

<!-- 添加媒体 -->

## 提交

Zed 提供两个提交文本区域：

1. 第一个直接在 Git 面板底部可用。点击 {#kb git::Commit} 立即提交所有暂存的更改。
2. 第二个可通过操作 {#action git::ExpandCommitEditor} 或在 Git 面板提交文本区域中获得焦点时点击 {#kb git::ExpandCommitEditor} 来使用。

### 撤销提交

一旦你在 Zed 中提交，在 Git 面板中，你会在提交文本区域下方看到一个栏，显示最近提交的提交。
在那里，你可以使用 "Uncommit" 按钮，它执行 `git reset HEADˆ--soft` 命令。

## 存储

Git 存储允许你临时保存未提交的更改并将工作目录恢复到干净状态。当你需要快速切换分支或拉取更新而不提交不完整的工作时，这特别有用。

### 创建存储

要存储所有当前更改，请使用 {#action git::StashAll} 操作。这将保存暂存和未暂存的更改到新的存储条目并清理你的工作目录。

### 管理存储

Zed 提供了一个全面的存储选择器，可通过 {#action git::ViewStash} 访问。从存储选择器中，你可以：

- **查看存储列表**：浏览所有保存的存储及其描述和时间戳
- **打开差异**：查看每个存储中存储的确切更改
- **应用存储**：将存储更改应用到工作目录，同时保留存储条目
- **弹出存储**：应用存储更改并从列表中删除存储条目
- **删除存储**：删除不需要的存储条目而不应用它们

### 快速存储操作

为了更快的工作流，Zed 提供了直接操作来处理最近的存储：

- **应用最新存储**：使用 {#action git::StashApply} 应用最近的存储而不删除它
- **弹出最新存储**：使用 {#action git::StashPop} 应用并删除最近的存储

### 存储差异视图

在差异视图中查看特定存储时，你可以通过界面获得其他选项：

- 将当前存储应用到工作目录
- 弹出当前存储（应用并删除）
- 删除存储而不应用更改

要打开存储差异视图，从存储选择器中选择一个存储，并使用 {#action stash_picker::ShowStashItem} ({#kb stash_picker::ShowStashItem}) 键绑定。

## Git 中的 AI 支持

Zed 目前支持基于 LLM 的提交消息生成。
你可以通过聚焦 Git 面板内的消息编辑器并点击左下角的铅笔图标，或使用 {#action git::GenerateCommitMessage} ({#kb git::GenerateCommitMessage}) 键绑定来要求 AI 生成提交消息。

> 注意，你需要为计费目的配置 LLM 提供者，无论是通过你自己的 API 密钥还是试用/支付 Zed 的托管 AI 模型。访问 [AI 配置页面](./ai/configuration.md) 了解如何操作。

你可以通过提供 `commit_message_model` 代理设置来指定你喜欢的模型。有关更多信息，请参阅 [功能特定模型](./ai/agent-settings.md#feature-specific-models)。

```json [settings]
{
  "agent": {
    "version": "2",
    "commit_message_model": {
      "provider": "anthropic",
      "model": "claude-3-5-haiku"
    }
  }
}
```

<!-- 添加媒体 -->

未来可能会推出更高级的 AI 与 Git 功能集成。

## Git 集成

Zed 与流行的 Git 托管服务集成，以确保 Git 提交哈希以及对 Issues、Pull Requests 和 Merge Requests 的引用成为可点击的链接。

Zed 目前支持以下托管版本的链接：
[GitHub](https://github.com)、
[GitLab](https://gitlab.com)、
[Bitbucket](https://bitbucket.org)、
[SourceHut](https://sr.ht) 和
[Codeberg](https://codeberg.org)。

Zed 还有一个复制永久链接功能，用于在你的 Git 托管服务上创建代码片段的永久链接。
这些链接对于在特定提交中共享文件中的特定行或行范围很有用。
通过 [命令面板](./getting-started.md#command-palette)（搜索 `permalink`）、
创建到 `editor::CopyPermalinkToLine` 或 `editor::OpenPermalinkToLine` 操作的
[自定义键绑定](key-bindings.md#custom-key-bindings)
或简单地在编辑器中右键单击并选择 `Copy Permalink` 来触发此操作。

## 差异代码块键盘快捷键

当查看有更改的文件时，Zed 显示可以展开或折叠以进行详细审查的差异代码块：

- **展开所有差异代码块**：{#action editor::ExpandAllDiffHunks} ({#kb editor::ExpandAllDiffHunks})
- **折叠所有差异代码块**：按 `Escape`（绑定到 {#action editor::Cancel}）
- **切换选定的差异代码块**：{#action editor::ToggleSelectedDiffHunks} ({#kb editor::ToggleSelectedDiffHunks})
- **在代码块之间导航**：{#action editor::GoToHunk} 和 {#action editor::GoToPreviousHunk}

> **提示：** `Escape` 键是折叠所有展开的差异代码块并返回到更改概述的最快方式。

## 操作参考

| 操作                                    | 键绑定                            |
| ----------------------------------------- | ------------------------------------- |
| {#action git::Add}                        | {#kb git::Add}                        |
| {#action git::StageAll}                   | {#kb git::StageAll}                   |
| {#action git::UnstageAll}                 | {#kb git::UnstageAll}                 |
| {#action git::ToggleStaged}               | {#kb git::ToggleStaged}               |
| {#action git::StageAndNext}               | {#kb git::StageAndNext}               |
| {#action git::UnstageAndNext}             | {#kb git::UnstageAndNext}             |
| {#action git::Commit}                     | {#kb git::Commit}                     |
| {#action git::ExpandCommitEditor}         | {#kb git::ExpandCommitEditor}         |
| {#action git::Push}                       | {#kb git::Push}                       |
| {#action git::ForcePush}                  | {#kb git::ForcePush}                  |
| {#action git::Pull}                       | {#kb git::Pull}                       |
| {#action git::Fetch}                      | {#kb git::Fetch}                      |
| {#action git::Diff}                       | {#kb git::Diff}                       |
| {#action git::Restore}                    | {#kb git::Restore}                    |
| {#action git::RestoreFile}                | {#kb git::RestoreFile}                |
| {#action git::Branch}                     | {#kb git::Branch}                     |
| {#action git::Switch}                     | {#kb git::Switch}                     |
| {#action git::CheckoutBranch}             | {#kb git::CheckoutBranch}             |
| {#action git::Blame}                      | {#kb git::Blame}                      |
| {#action git::StashAll}                   | {#kb git::StashAll}                   |
| {#action git::StashPop}                   | {#kb git::StashPop}                   |
| {#action git::StashApply}                 | {#kb git::StashApply}                 |
| {#action git::ViewStash}                  | {#kb git::ViewStash}                  |
| {#action editor::ToggleGitBlameInline}    | {#kb editor::ToggleGitBlameInline}    |
| {#action editor::ExpandAllDiffHunks}      | {#kb editor::ExpandAllDiffHunks}      |
| {#action editor::ToggleSelectedDiffHunks} | {#kb editor::ToggleSelectedDiffHunks} |

> 并非所有操作都有默认键绑定，但可以通过[自定义键映射](./key-bindings.md#user-keymaps)来绑定。

## Git CLI 配置

如果你还想在从命令行提交时使用 Zed 作为你的 [git 提交消息编辑器](https://git-scm.com/book/en/v2/Customizing-Git-Git-Configuration#_core_editor)，你可以使用 `zed --wait`：

```sh
git config --global core.editor "zed --wait"
```

或者将以下内容添加到你的 shell 环境（在 `~/.zshrc`、`~/.bashrc` 等中）：

```sh
export GIT_EDITOR="zed --wait"
```
