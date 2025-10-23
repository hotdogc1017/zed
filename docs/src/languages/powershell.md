# PowerShell

Zed 对 PowerShell 的语言支持由社区维护的 [Zed PowerShell 扩展](https://github.com/wingyplus/zed-powershell) 提供。如需反馈问题，请访问：[github.com/wingyplus/zed-powershell/issues](https://github.com/wingyplus/zed-powershell/issues)

- Tree-sitter：[airbus-cert/tree-sitter-powershell](https://github.com/airbus-cert/tree-sitter-powershell)
- 语言服务器：[PowerShell/PowerShellEditorServices](https://github.com/PowerShell/PowerShellEditorServices)

## 安装

### 安装 PowerShell 7+ {#powershell-install}

- macOS：`brew install powershell/tap/powershell`
- Alpine：[在 Alpine Linux 上安装 PowerShell](https://learn.microsoft.com/en-us/powershell/scripting/install/install-alpine)
- Debian：[在 Debian Linux 上安装 PowerShell](https://learn.microsoft.com/en-us/powershell/scripting/install/install-debian)
- RedHat：[在 RHEL 上安装 PowerShell](https://learn.microsoft.com/en-us/powershell/scripting/install/install-rhel)
- Ubuntu：[在 Ubuntu 上安装 PowerShell](https://learn.microsoft.com/en-us/powershell/scripting/install/install-ubuntu)
- Windows：[在 Windows 上安装 PowerShell](https://learn.microsoft.com/en-us/powershell/scripting/install/installing-powershell-on-windows)

Zed PowerShell 扩展默认使用 PATH 中的 `pwsh` 可执行文件。

### 安装 PowerShell Editor Services（可选）{#powershell-editor-services}

扩展会尝试自动下载 [PowerShell Editor Services](https://github.com/PowerShell/PowerShellEditorServices)。

如果希望使用特定版本的可执行文件，可在 Zed 的 `settings.json` 中指定：

```json [settings]
  "lsp": {
    "powershell-es": {
      "binary": {
        "path": "/path/to/PowerShellEditorServices"
      }
    }
  }
```
