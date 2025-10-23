# 使用调试器

> **免责声明**：这不是关于[配置 Zed 的调试器](../debugger.md)的文档。
> 相反，它旨在为 Zed 员工和外部贡献者提供在开发 Zed 本身时如何使用调试器的信息。

## 使用 Zed 的内置调试器

当 Zed 项目打开时，您可以打开 `New Process Modal` 并选择 `Debug` 选项卡。在那里您可以看到用于调试 Zed 的调试配置，一个用于 GDB，一个用于 LLDB。选择您想要的配置，Zed 将构建并启动二进制文件。

请注意，GDB 在 arm Macbooks 上不受支持

## 发布构建配置文件考虑

默认情况下，使用发布配置文件（发布是用于生产构建的配置文件，即 nightly、preview 和 stable）的构建包含有限的调试信息。

这是通过将根 `Cargo.toml` 文件中的 `profile.(release).debug` 字段设置为 `"limited"` 来实现的。

`debug` 字段的官方文档可以在[此处](https://doc.rust-lang.org/cargo/reference/profiles.html#debug)找到。
但简而言之，`"limited"` 会剥离类型和变量级别的调试信息。

在发布构建中，这样做是为了减少二进制文件大小，因为类型和变量级别的调试信息不是必需的，并且不会影响生成的堆栈跟踪的可用性。

然而，虽然类型和变量级别的调试信息对于良好的堆栈跟踪不是必需的，但对于使用调试器的良好体验非常重要，
因为没有类型和变量级别的调试信息，调试器无法解析局部变量、检查它们、使用漂亮打印器格式化它们等。

因此，为了在调试发布构建时充分利用调试器，您必须编译一个具有完整调试信息的新 Zed 二进制文件。

最简单的方法是使用 `--config` 标志在运行 `cargo run` 或 `cargo build` 时覆盖根 `Cargo.toml` 文件中的 `debug` 字段，如下所示：

```sh
cargo run --config 'profile.release.debug="full"'
cargo build --config 'profile.release.debug="full"'
```

> 如果您希望避免在每次调用 `cargo` 时传递 `--config` 标志。您也可以更改 [根 `Cargo.toml`](https://github.com/zed-industries/zed/blob/main/Cargo.toml) 中的部分
>
> 从
>
> ```toml
> [profile.release]
> debug = "limited"
> ```
>
> 到
>
> ```toml
> [profile.release]
> debug = "full"
> ```
>
> 这将确保所有 `cargo run --release` 或 `cargo build --release` 的调用都将编译为具有完整调试信息。
>
> **警告：** 确保避免提交这些更改！

## 使用 shell 调试器 GDB/LLDB 运行 Zed

### 背景

通过 rustup 安装 Rust 时（开发 Zed 时的推荐方式，请参阅[此处](../development.md)的入门文档），
会安装一些额外的脚本并放在您的路径上，以帮助调试用 Rust 编译的二进制文件。

这些分别是 `rust-gdb` 和 `rust-lldb`。

如果您感兴趣，可以在此处阅读有关这些脚本及其有用性的更多信息[此处](https://michaelwoerister.github.io/2015/03/27/rust-xxdb.html)。

然而，总结来说，它们是包装标准 `gdb` 和 `lldb` 命令的简单 shell 脚本，注入相关命令和标志以启用额外的
Rust 特定功能，如漂亮打印器和类型信息。

因此，为了使用 `rust-gdb` 或 `rust-lldb`，您必须在系统上安装 `gdb` 或 `lldb`。如果您没有安装它们，需要以适合您平台的方式安装它们。

根据[先前链接的文章](https://michaelwoerister.github.io/2015/03/27/rust-xxdb.html)，"支持的最低调试器版本是 GDB 7.7 和 LLDB 310。然而，一般规则是：越新越好。"因此，如果可能，建议安装最新版本的 `gdb` 或 `lldb`。

> **注意**：`rust-gdb` 在 Windows 上默认不安装，因为 Windows 的 `gdb` 支持不太稳定。建议在 Windows 上使用 `lldb` 和 `rust-lldb`。

如果您不熟悉 `gdb` 或 `lldb`，可以分别在此处[此处](https://www.gnu.org/software/gdb/)和[此处](https://lldb.llvm.org/)了解有关它们的更多信息。

### 与 Zed 一起使用

在按照上述步骤在编译 Zed 时包含完整调试信息后，
您可以在使用 `cargo build` 构建编译后的 Zed 二进制文件后运行 `rust-gdb` 或 `rust-lldb`，通过运行以下命令之一：

```
rust-gdb target/debug/zed
rust-lldb target/debug/zed
```

或者，您可以通过运行以下命令之一附加到正在运行的 Zed 实例（例如使用 `cargo run` 启动的 Zed 实例）：

```
rust-gdb -p <pid>
rust-lldb -p <pid>
```

其中 `<pid>` 是您想要附加到的 Zed 实例的进程 ID。

要获取正在运行的 Zed 实例的进程 ID，您可以使用系统的进程管理工具，如 Windows 上的 `Task Manager` 或 macOS 上的 `Activity Monitor`。

或者，您可以在 macOS 和 Linux 上运行 `ps aux | grep zed` 命令，或在 Windows 上的 PowerShell 实例中运行 `Get-Process | Select-Object Id, ProcessName`。

#### 调试恐慌和崩溃

调试器可以是调试所有程序（包括 Zed）中恐慌和崩溃原因的绝佳工具。

默认情况下，当 `gdb` 或 `lldb` 附加的进程遇到异常（如恐慌）时，调试器会自动在恐慌点停止，并允许您检查程序的状态。

很可能，调试器停止的点将在 Rust 标准库恐慌或异常处理代码的深处，因此您需要向上导航堆栈跟踪以找到恐慌的实际原因。

这可以通过在 `lldb` 中使用 `backtrace` 命令结合 `frame select` 命令来完成，`gdb` 中有类似的命令可用。

一旦程序停止，您将无法像在遇到异常之前那样继续执行。但是，您可以在不同的堆栈帧之间跳转，并检查每个帧内的变量和表达式的值，
这对于识别崩溃的根本原因非常有用。

您可以在此处找到有关调试 Zed 崩溃的更多信息[此处](./debugging-crashes.md)。
