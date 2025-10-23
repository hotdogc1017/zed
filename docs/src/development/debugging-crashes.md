# 调试崩溃

当 Zed 发生恐慌或其他崩溃时，Zed 会向一个辅助进程发送消息，该进程检查崩溃编辑器的内存，在 `~/Library/Logs/Zed` 或 `$XDG_DATA_HOME/zed/logs` 中创建一个 [minidump](https://chromium.googlesource.com/breakpad/breakpad/+/master/docs/getting_started_with_breakpad.md#the-minidump-file-format) 文件。此 minidump 可用于为所有线程的堆栈生成回溯。

如果您启用了 Zed 的遥测功能，这些文件将在您重新启动应用程序时上传给我们。它们最终会出现在 [Slack 频道](https://zed-industries.slack.com/archives/C0977J9MA1Y) 和 [Sentry](https://zed-dev.sentry.io/issues) 中（两者都仅供 Zed 员工使用）。

这些崩溃报告包含丰富的信息；但由于不包含跨度和符号信息，因此很难阅读。您仍然可以通过下载 Zed 版本的源代码和未剥离的二进制文件（或单独的符号文件）并在本地处理它们：

```sh
zstd -d ~/.local/share/zed/<uuid>.dmp -o minidump.dmp
minidump-stackwalk minidump.dmp
```

在日志目录中的 minidump 文件旁边，应该有一个 `<uuid>.json` 文件，其中包含额外的元数据，如恐慌消息、跨度和系统规格。

## 使用调试器

如果您可以一致地重现崩溃，可以使用调试器检查程序在崩溃时的状态，通常可以提供有关崩溃原因的有用见解。

您可以在此处阅读有关设置和使用 Zed 调试器的更多信息，特别是关于调试崩溃的信息[此处](./debuggers.md#debugging-panics-and-crashes)
