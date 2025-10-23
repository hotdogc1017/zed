# 安装扩展

您可以通过以下方式搜索扩展：按 {#kb zed::Extensions} 启动 Zed 扩展库，打开命令面板并选择 {#action zed::Extensions}，或从菜单栏中选择 "Zed > Extensions"。

在这里，您可以查看当前已安装的扩展，或搜索并安装新的扩展。

## 安装位置

- 在 macOS 上，扩展安装在 `~/Library/Application Support/Zed/extensions`。
- 在 Linux 上，它们安装在 `$XDG_DATA_HOME/zed/extensions` 或 `~/.local/share/zed/extensions`。

此目录包含两个子目录：

- `installed`，包含每个扩展的源代码。
- `work`，包含扩展本身创建的文件，例如下载的语言服务器。

## 自动安装

要自动化扩展安装/卸载，请参阅 [auto_install_extensions](../configuring-zed.md#auto-install-extensions) 的文档。
