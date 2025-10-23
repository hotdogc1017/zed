# 模型上下文协议

Zed 使用[模型上下文协议](https://modelcontextprotocol.io/)与上下文服务器交互。

> 模型上下文协议（MCP）是一个开放协议，可实现 LLM 应用程序与外部数据源和工具之间的无缝集成。无论您是构建 AI 驱动的 IDE、增强聊天界面还是创建自定义 AI 工作流，MCP 都提供了一种标准化方式来将 LLM 与它们所需的上下文连接起来。

查看 [Anthropic 新闻文章](https://www.anthropic.com/news/model-context-protocol) 和 [Zed 博客文章](https://zed.dev/blog/mcp) 了解 MCP 的一般介绍。

## 安装 MCP 服务器

### 作为扩展

在 Zed 中使用 MCP 服务器的一种方式是将它们公开为扩展。
查看 [MCP 服务器扩展](../extensions/mcp-extensions.md) 页面了解如何创建您自己的扩展。

感谢我们出色的社区，许多 MCP 服务器已经作为扩展添加。
您可以通过以下任何途径检查哪些可用：

1. [Zed 网站](https://zed.dev/extensions?filter=context-servers)
2. 在应用程序中，打开命令面板并运行 `zed: extensions` 操作
3. 在应用程序中，转到代理面板的右上角菜单并查找"查看服务器扩展"菜单项

无论如何，以下是一些流行的可用服务器：

- [Context7](https://zed.dev/extensions/context7-mcp-server)
- [GitHub](https://zed.dev/extensions/github-mcp-server)
- [Puppeteer](https://zed.dev/extensions/puppeteer-mcp-server)
- [Gem](https://zed.dev/extensions/gem)
- [Brave Search](https://zed.dev/extensions/brave-search-mcp-server)
- [Prisma](https://github.com/aqrln/prisma-mcp-zed)
- [Framelink Figma](https://zed.dev/extensions/framelink-figma-mcp-server)
- [Linear](https://zed.dev/extensions/linear-mcp-server)
- [Resend](https://zed.dev/extensions/resend-mcp-server)

### 作为自定义服务器

创建扩展并不是在 Zed 中使用 MCP 服务器的唯一方式。
您可以通过将它们的命令直接添加到您的 `settings.json` 中来连接它们，如下所示：

```json [settings]
{
  "context_servers": {
    "your-mcp-server": {
      "source": "custom",
      "command": "some-command",
      "args": ["arg-1", "arg-2"],
      "env": {}
    }
  }
}
```

或者，您也可以通过访问代理面板的设置视图（也可以通过 `agent: open settings` 操作访问）来添加自定义服务器。
从那里，您可以通过点击"添加自定义服务器"按钮时出现的模态框来添加它。

## 使用 MCP 服务器

### 配置检查

无论您如何安装 MCP 服务器，无论是作为扩展还是直接添加它们，大多数服务器仍然需要某种配置作为设置过程的一部分。

对于扩展，安装后，Zed 将弹出一个模态框，显示您需要正确设置的内容。
例如，GitHub MCP 扩展要求您添加[个人访问令牌](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens)。

对于自定义服务器，请确保检查提供程序文档以确定需要向 JSON 添加什么类型的命令、参数和环境变量。

要检查您的 MCP 服务器是否正确配置，请转到代理面板的设置视图并观察其名称旁边的指示器点。
如果它们运行正确，指示器将为绿色，其工具提示将显示"服务器处于活动状态"。
如果没有，其他颜色和工具提示消息将指示正在发生的情况。

### 代理面板使用

安装完成后，您可以返回代理面板并开始提示。

在从 MCP 服务器获取工具方面，某些模型比其他模型更好。
提及您的服务器名称总是有助于模型获取它。

但是，如果您想_确保_使用给定的 MCP 服务器，您可以创建一个[自定义配置文件](./agent-panel.md#custom-profiles)，其中所有内置工具（或可能与服务器工具冲突的工具）都关闭，只有来自 MCP 服务器的工具才打开。

例如，[Dagger 团队建议](https://container-use.com/agent-integrations#zed)使用他们的 [Container Use MCP 服务器](https://zed.dev/extensions/mcp-server-container-use)这样做：

```json [settings]
"agent": {
  "profiles": {
    "container-use": {
      "name": "容器使用",
      "tools": {
        "fetch": true,
        "thinking": true,
        "copy_path": false,
        "find_path": false,
        "delete_path": false,
        "create_directory": false,
        "list_directory": false,
        "diagnostics": false,
        "read_file": false,
        "open": false,
        "move_path": false,
        "grep": false,
        "edit_file": false,
        "terminal": false
      },
      "enable_all_context_servers": false,
      "context_servers": {
        "container-use": {
          "tools": {
            "environment_create": true,
            "environment_add_service": true,
            "environment_update": true,
            "environment_run_cmd": true,
            "environment_open": true,
            "environment_file_write": true,
            "environment_file_read": true,
            "environment_file_list": true,
            "environment_file_delete": true,
            "environment_checkpoint": true
          }
        }
      }
    }
  }
}
```

### 工具批准

Zed 的代理面板包括 `agent.always_allow_tool_actions` 设置，如果设置为 `false`，将要求您对任何编辑尝试以及来自 MCP 服务器的工具调用给予许可。

您可以通过在 `settings.json` 中或通过代理面板的设置视图将此键设置为 `true` 来更改此设置。

### 外部代理

请注意，对于通过[代理客户端协议](https://agentclientprotocol.com/)连接的[外部代理](./external-agents.md)，从 Zed 安装的 MCP 服务器的访问权限可能因 ACP 代理实现而异。

关于内置代理，Claude Code 和 Codex 都支持它，而 Gemini CLI 尚不支持。
同时，通过[他们的文档](https://github.com/google-gemini/gemini-cli?tab=readme-ov-file#using-mcp-servers)了解如何向 Gemini CLI 添加 MCP 服务器支持。
