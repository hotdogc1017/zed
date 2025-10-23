# Ansible

Zed 对 Ansible 的支持由社区维护的 [Ansible 扩展](https://github.com/kartikvashistha/zed-ansible) 提供。

- Tree-sitter：[zed-industries/tree-sitter-yaml](https://github.com/zed-industries/tree-sitter-yaml)
- 语言服务器：[ansible/vscode-ansible](https://github.com/ansible/vscode-ansible/tree/main/packages/ansible-language-server)

## 设置

### 文件检测

为了避免将非 Ansible 的 YAML 文件误判成 Ansible，本语言默认不与任何文件扩展名关联。若要更改这一行为，可以在项目内的 Zed 设置（`.zed/settings.json`）或全局用户设置（`~/.config/zed/settings.json`）中添加 "file_types" 配置，以契合你的目录结构或命名约定。例如：

```json [settings]
"file_types": {
    "Ansible": [
      "**.ansible.yml",
      "**.ansible.yaml",
      "**/defaults/*.yml",
      "**/defaults/*.yaml",
      "**/meta/*.yml",
      "**/meta/*.yaml",
      "**/tasks/*.yml",
      "**/tasks/*.yaml",
      "**/handlers/*.yml",
      "**/handlers/*.yaml",
      "**/group_vars/*.yml",
      "**/group_vars/*.yaml",
      "**/host_vars/*.yml",
      "**/host_vars/*.yaml",
      "**/playbooks/*.yml",
      "**/playbooks/*.yaml",
      "**playbook*.yml",
      "**playbook*.yaml"
    ]
  }
```

你可以根据自己的需要调整上述列表。

#### 清单文件（Inventory）

如果你的清单文件是 YAML 格式，可以选择以下任意一种方式：

- 在清单文件顶部添加如下注释，将 `ansible-lint` 的清单 JSON Schema 附加到该文件：

```yml
# yaml-language-server: $schema=https://raw.githubusercontent.com/ansible/ansible-lint/main/src/ansiblelint/schemas/inventory.json
```

- 或者在 Zed 设置中配置 YAML 语言服务器，让它为符合你清单匹配模式的文件都使用该 Schema（[参考](https://zed.dev/docs/languages/yaml)）：

```json [settings]
"lsp": {
    "yaml-language-server": {
      "settings": {
        "yaml": {
          "schemas": {
            "https://raw.githubusercontent.com/ansible/ansible-lint/main/src/ansiblelint/schemas/inventory.json": [
              "./inventory/*.yaml",
              "hosts.yml",
            ]
          }
        }
      }
    }
},
```

### LSP 配置

默认情况下，Zed 会向 Ansible 语言服务器传递以下配置。它与 [nvim-lspconfig](https://github.com/neovim/nvim-lspconfig/blob/03bc581e05e81d33808b42b2d7e76d70adb3b595/lua/lspconfig/configs/ansiblels.lua) 中的默认值保持一致：

```json [settings]
{
  "ansible": {
    "ansible": {
      "path": "ansible"
    },
    "executionEnvironment": {
      "enabled": false
    },
    "python": {
      "interpreterPath": "python3"
    },
    "validation": {
      "enabled": true,
      "lint": {
        "enabled": true,
        "path": "ansible-lint"
      }
    }
  }
}
```

> [!NOTE]
> 若要启用 Lint，请确保 `ansible-lint` 已安装并且可通过 PATH 查找到。

如有需要，可以在 Zed 设置文件的 "lsp" 部分覆盖上述默认配置。例如：

```json [settings]
"lsp": {
  // 注意，Zed 的 Ansible 扩展会为所有设置添加 `ansible` 前缀，
  // 因此请使用 `ansible.path` 而不要使用 `ansible.ansible.path`。
  "ansible-language-server": {
    "settings": {
      "ansible": {
        "path": "ansible"
      },
      "executionEnvironment": {
        "enabled": false
      },
      "python": {
        "interpreterPath": "python3"
      },
      "validation": {
        "enabled": false, // 禁用验证
        "lint": {
          "enabled": false, // 禁用 ansible-lint
          "path": "ansible-lint"
        }
      }
    }
  }
}
```

更多可传递给语言服务器的选项可以在项目页面的[文档](https://github.com/ansible/vscode-ansible/blob/5a89836d66d470fb9d20e7ea8aa2af96f12f61fb/docs/als/settings.md)中找到，可根据实际需求调整参数。
