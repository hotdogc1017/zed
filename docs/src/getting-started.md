# 入门指南

欢迎使用 Zed！我们非常高兴与你见面。本页旨在帮助你快速上手。

## 下载 Zed

### macOS

你可以在 [下载页面](https://zed.dev/download) 获取最新稳定版。如需体验预览版，请前往 [预览发布页](https://zed.dev/releases/preview)。首次手动安装后，Zed 会定期检查更新。

也可以通过 Homebrew 安装稳定版：

```sh
brew install --cask zed
```

或安装预览版：

```sh
brew install --cask zed@preview
```

### Windows

前往 [下载页面](https://zed.dev/download) 获取最新稳定版。若想体验预览功能，可在 [预览发布页](https://zed.dev/releases/preview) 下载。首次手动安装完成后，Zed 会定期提示更新。

### Linux

对多数 Linux 用户来说，使用官方脚本是最快速的安装方式：

```sh
curl -f https://zed.dev/install.sh | sh
```

如果你愿意帮助我们测试新功能，也可以安装预览版：

```sh
curl -f https://zed.dev/install.sh | ZED_CHANNEL=preview sh
```

该脚本支持 `x86_64` 与 `AArch64` 架构，并适配常见发行版（Ubuntu、Arch、Debian、RedHat、CentOS、Fedora 等）。

通过脚本安装的 Zed 可以随时卸载：执行 `zed --uninstall`，Shell 会提示是否保留个性化设置或一并删除。选择完毕后会看到“Zed 已成功卸载”的提示。

若脚本不适用于你的场景、运行 Zed 时遇到问题，或卸载出现错误，请参考[针对 Linux 的说明](./linux.md)。

## 命令面板

命令面板是访问 Zed 功能的主要方式，你应该首先熟悉它的快捷键。使用 {#kb command_palette::Toggle} 打开命令面板。

![命令面板](https://zed.dev/img/features/command-palette.jpg)

试试看：打开命令面板并输入 `new file`，列表会被过滤到 `workspace: new file`，回车即可创建新缓冲区。

凡是说明中出现 `zed: ...`、`editor: ...` 等形式的命令，均表示需要在命令面板中执行。

## CLI

Zed 提供命令行工具：

- 在 Linux 上通常随发行版的软件包一起安装（可执行文件名称可能因发行版而异，以下统一用 `zed` 表示）。
- 在 macOS 上，CLI 与编辑器程序位于同一包内，可通过 `cli: install` 命令将其安装到系统中（会在 `/usr/local/bin/zed` 创建符号链接）。
- 也可以直接在源码仓库的 `cli` crate 中自行构建。

使用 `zed --help` 查看完整能力，常见示例如下：

- 打开一个空白 Zed 窗口：`zed`
- 在 Zed 中打开文件或目录：`zed /path/to/entry`（使用 `-n` 在新窗口打开）
- 从标准输入读取内容：`ps axf | zed -`
- 在前台启动并输出日志：`zed --foreground`
- 卸载 Zed 及相关文件：`zed --uninstall`

## 自定义 Zed

需要调整字体、格式化、按语言设置等内容时，可通过 {#kb zed::OpenSettings} 打开个人设置文件。

想查看所有可用选项，可打开命令面板（{#kb command_palette::Toggle}）并搜索 `zed: open default settings`。
