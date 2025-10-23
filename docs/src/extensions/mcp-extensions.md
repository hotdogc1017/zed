# MCP 服务器扩展

[模型上下文协议服务器](../ai/mcp.md) 可以作为扩展公开，供代理面板使用。

## 定义 MCP 扩展

给定的扩展可以提供一个或多个 MCP 服务器。
每个 MCP 服务器必须在 `extension.toml` 中注册：

```toml
[context_servers.my-context-server]
```

然后，在您的扩展的 Rust 代码中，在您的扩展上实现 `context_server_command` 方法：

```rust
impl zed::Extension for MyExtension {
    fn context_server_command(
        &mut self,
        context_server_id: &ContextServerId,
        project: &zed::Project,
    ) -> Result<zed::Command> {
        Ok(zed::Command {
            command: get_path_to_context_server_executable()?,
            args: get_args_for_context_server()?,
            env: get_env_for_context_server()?,
        })
    }
}
```

此方法应返回启动 MCP 服务器的命令，以及使其正常运行所需的任何参数或环境变量。

如果您需要从外部源（如 GitHub Releases 或 npm）下载 MCP 服务器，也可以在此函数中执行此操作。

## 可用扩展

查看所有已作为扩展公开的 MCP 服务器 [在 Zed 网站上](https://zed.dev/extensions?filter=context-servers)。

我们建议查看它们的仓库，以了解它们通常是如何创建和构建的。

## 测试

要测试您的新 MCP 服务器扩展，您可以[将其安装为开发扩展](./developing-extensions.md#developing-an-extension-locally)。
