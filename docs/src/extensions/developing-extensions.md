# 开发扩展

## 扩展功能

扩展可以为 Zed 添加以下功能：

- [语言](./languages.md)
- [调试器](./debugger-extensions.md)
- [主题](./themes.md)
- [图标主题](./icon-themes.md)
- [斜杠命令](./slash-commands.md)
- [MCP 服务器](./mcp-extensions.md)

## 本地开发扩展

在开始为 Zed 开发扩展之前，请确保[通过 rustup 安装 Rust](https://www.rust-lang.org/tools/install)。

> Rust 必须通过 rustup 安装。如果您通过 homebrew 或其他方式安装了 Rust，安装开发扩展将无法工作。

在开发扩展时，您可以通过将其安装为_开发扩展_来在 Zed 中使用它，而无需发布它。

从扩展页面，点击 `Install Dev Extension` 按钮（或 {#action zed::InstallDevExtension} 操作）并选择包含您的扩展的目录。

如果需要故障排除，您可以检查 Zed.log（{#action zed::OpenLog}）以获取额外的输出。对于调试输出，请使用 `zed --foreground` 从命令行关闭并重新启动 zed，这将显示更详细的 INFO 级别日志记录。

如果您已经安装了已发布的扩展版本，则在安装开发扩展之前将卸载已发布的版本。成功安装后，`Extensions` 页面将指示上游扩展被"开发扩展覆盖"。

## Zed 扩展的目录结构

Zed 扩展是一个包含 `extension.toml` 的 Git 存储库。此文件必须包含有关扩展的一些基本信息：

```toml
id = "my-extension"
name = "My extension"
version = "0.0.1"
schema_version = 1
authors = ["Your Name <you@example.com>"]
description = "My cool extension"
repository = "https://github.com/your-name/my-zed-extension"
```

除此之外，还有几个其他可选文件和目录可用于向 Zed 扩展添加功能。提供所有功能的扩展的示例目录结构如下：

```
my-extension/
  extension.toml
  Cargo.toml
  src/
    lib.rs
  languages/
    my-language/
      config.toml
      highlights.scm
  themes/
    my-theme.json
```

## WebAssembly

扩展的过程部分用 Rust 编写并编译为 WebAssembly。要开发包含自定义代码的扩展，请包含一个像这样的 `Cargo.toml`：

```toml
[package]
name = "my-extension"
version = "0.0.1"
edition = "2021"

[lib]
crate-type = ["cdylib"]

[dependencies]
zed_extension_api = "0.1.0"
```

使用 crates.io 上可用的最新版本的 [`zed_extension_api`](https://crates.io/crates/zed_extension_api)。确保它仍然与您想要支持的 [Zed 版本兼容](https://github.com/zed-industries/zed/blob/main/crates/extension_api#compatible-zed-versions)。

在您的 Rust crate 的 `src/lib.rs` 文件中，您需要为您的扩展定义一个结构体并实现 `Extension` trait，以及使用 `register_extension!` 宏来注册您的扩展：

```rs
use zed_extension_api as zed;

struct MyExtension {
    // ... state
}

impl zed::Extension for MyExtension {
    // ...
}

zed::register_extension!(MyExtension);
```

> `stdout`/`stderr` 直接转发到 Zed 进程。为了查看来自您的扩展的 `println!`/`dbg!` 输出，您可以在终端中使用 `--foreground` 标志启动 Zed。

## Forking 和克隆存储库

1. Fork 存储库

> 注意：如果您将 `zed-industries/extensions` 存储库 fork 到个人 GitHub 账户而不是 GitHub 组织，这将非常有帮助，因为这允许 Zed 工作人员将任何需要的更改推送到您的 PR 以加快发布过程。

2. 将存储库克隆到本地机器

```sh
# 在此处替换您的 fork 的 url：
# git clone https://github.com/zed-industries/extensions
cd extensions
git submodule init
git submodule update
```

## 扩展许可证要求

自 2025 年 10 月 1 日起，扩展存储库必须包含以下许可证之一：

- [MIT](https://opensource.org/license/mit)
- [Apache 2.0](https://www.apache.org/licenses/LICENSE-2.0)

这允许我们将从您的扩展代码生成的二进制文件分发给我们的用户。
没有有效的许可证，在以下步骤中添加或更新您的扩展的拉取请求将无法通过 CI。

您的许可证文件应位于扩展存储库的根目录。任何以 `LICENCE` 或 `LICENSE` 为前缀（不区分大小写）的文件名将被检查以确保它匹配其中一个接受的许可证。请参阅[许可证验证源代码](https://github.com/zed-industries/extensions/blob/main/src/lib/license.js)。

> 此许可证要求仅适用于您的扩展代码本身（编译到扩展二进制文件中的代码）。
> 它不适用于您的扩展可能下载或与之交互的任何工具，例如语言服务器或其他外部依赖项。
> 如果您的存储库同时包含扩展代码和其他项目（如语言服务器），您不需要重新许可这些其他项目——只有扩展代码需要是上述接受的许可证之一。

## 发布您的扩展

要发布扩展，请向 [the `zed-industries/extensions` repo](https://github.com/zed-industries/extensions) 打开一个 PR。

在您的 PR 中，执行以下操作：

1. 将您的扩展作为 Git 子模块添加到 `extensions/` 目录中

```sh
git submodule add https://github.com/your-username/foobar-zed.git extensions/foobar
git add extensions/foobar
```

> 所有扩展子模块必须使用 HTTPS URL，而不是 SSH URL（`git@github.com`）。

2. 向顶层的 `extensions.toml` 文件添加一个新条目，包含您的扩展：

```toml
[my-extension]
submodule = "extensions/my-extension"
version = "0.0.1"
```

> 如果您的扩展位于子模块内的子目录中，您可以使用 `path` 字段指向扩展所在的位置。

3. 运行 `pnpm sort-extensions` 以确保 `extensions.toml` 和 `.gitmodules` 已排序

一旦您的 PR 被合并，扩展将被打包并发布到 Zed 扩展注册表。

> 扩展 ID 和名称不应包含 `zed` 或 `Zed`，因为它们都是 Zed 扩展。

## 更新扩展

要更新扩展，请向 [the `zed-industries/extensions` repo](https://github.com/zed-industries/extensions) 打开一个 PR。

在您的 PR 中执行以下操作：

1. 将扩展的子模块更新到新版本的提交。
2. 更新 `extensions.toml` 中扩展的 `version` 字段
   - 确保 `version` 与特定提交中 `extension.toml` 中设置的版本匹配。

如果您想自动化此过程，可以使用[社区 GitHub Action](https://github.com/huacnlee/zed-extension-action)。

> **注意：** 如果您的扩展存储库有不同的许可证，您需要在发布更新之前将其更新为[接受的扩展许可证](#extension-license-requirements)之一。
