# 配置支持的语言

Zed 为所支持的编程语言提供了丰富的自定义选项。本指南将带你逐步了解如何根据个人偏好与项目需求调整语言体验。

Zed 的语言能力由两大组件组成：

1. **Tree-sitter**：负责语法高亮及基于语法树的功能（例如大纲面板）。
2. **语言服务器协议（LSP）**：提供语义层功能，如补全、诊断等。

通过合理配置这两部分，你可以获得强大的语言支援。本指南涵盖：

- 语言专属设置
- 文件类型关联
- 语言服务器管理
- 代码格式化与静态检查
- 语法高亮与主题定制
- 其他高级能力

阅读完成后，你将掌握在 Zed 中定制各语言体验的常用方法。欲查看支持语言的完整列表与专属说明，请访问[支持的语言](./languages.md)。若想扩展或增强语言功能，也可参考[语言扩展开发指南](./extensions/languages.md)。

## 语言专属设置

在 `settings.json` 的 `languages` 区块中，可以覆盖某种语言的默认配置。例如：

```json [settings]
"languages": {
  "Python": {
    "tab_size": 4,
    "formatter": "language_server",
    "format_on_save": "on"
  },
  "JavaScript": {
    "tab_size": 2,
    "formatter": {
      "external": {
        "command": "prettier",
        "arguments": ["--stdin-filepath", "{buffer_path}"]
      }
    }
  }
}
```

常见可覆写选项：

- [`tab_size`](./configuring-zed.md#tab-size)：缩进宽度
- [`formatter`](./configuring-zed.md#formatter)：格式化工具
- [`format_on_save`](./configuring-zed.md#format-on-save)：保存时自动格式化
- [`enable_language_server`](./configuring-zed.md#enable-language-server)：启用/禁用语言服务器
- [`hard_tabs`](./configuring-zed.md#hard-tabs)：缩进是否使用 `\t`
- [`preferred_line_length`](./configuring-zed.md#preferred-line-length)：推荐行宽
- [`soft_wrap`](./configuring-zed.md#soft-wrap)：软换行策略
- [`show_completions_on_input`](./configuring-zed.md#show-completions-on-input)：输入时自动弹出补全
- [`show_completion_documentation`](./configuring-zed.md#show-completion-documentation)：补全列表中显示文档

## 文件类型关联

虽然 Zed 会自动根据扩展名识别文件类型，但你也可以通过 [`file_types`](./configuring-zed.md#file-types) 设置自行映射：

```json [settings]
"file_types": {
  "C++": ["c"],
  "TOML": ["MyLockFile"],
  "Dockerfile": ["Dockerfile*"]
}
```

上述配置会让 `.c` 文件按 C++ 处理、识别名为 `MyLockFile` 的文件为 TOML、以及让以 `Dockerfile` 开头的文件应用 Dockerfile 语法。`file_types` 支持 glob，因此可应对各种命名约定。

## 使用语言服务器

语言服务器提供自动补全、跳转、实时诊断等智能功能，是 Zed 语言支持的核心。

### 什么是语言服务器？

语言服务器实现了 Language Server Protocol（LSP），它规范了编辑器与语言工具之间的通信。通过这一协议，Zed 可以在无需针对每种语言重新实现所有功能的情况下，获得下列能力：

- 代码补全
- 错误检查与诊断
- 跳转定义、查找引用
- 代码操作（重命名、提取函数等）
- 悬停信息
- 工作区符号检索

### 管理语言服务器

Zed 会自动为你处理大部分工作：

1. **自动下载**：当你打开某种语言的文件时，Zed 会自动下载匹配的语言服务器；若需要额外扩展，也会提示安装。
2. **存储位置**：
   - macOS：`~/Library/Application Support/Zed/languages`
   - Linux：`$XDG_DATA_HOME/zed/languages` 或 `$HOME/.local/share/zed/languages`
3. **自动更新**：语言服务器会随 Zed 自动保持最新。

### 选择语言服务器

部分语言同时存在多个语言服务器。若你安装了多个扩展，可能会出现能力重叠的情况，可以通过 `language_servers` 设置指定优先顺序：

```json [settings]
{
  "languages": {
    "TypeScript": {
      "language_servers": [
        "vtsls",
        "!typescript-language-server",
        "..."
      ]
    }
  }
}
```

- 列表按顺序尝试，`"..."` 代表保留默认值。
- 在名称前加 `!` 表示禁用该语言服务器。

### 语言服务器设置

每个语言服务器都可以通过 `lsp` 区块进一步定制：

```json [settings]
{
  "lsp": {
    "rust-analyzer": {
      "initialization_options": {
        "check": {
          "command": "clippy"
        }
      }
    }
  }
}
```

- `initialization_options`：启动时生效的配置，修改后需要重启语言服务器。
- `settings`：运行时可热更新的配置。

更多示例可参考语言文档或语言服务器官方说明。

## 格式化与代码检查

Zed 支持多种格式化方式：

1. 语言服务器提供的格式化（`"language_server"`）。
2. 外部命令（`"external"`），通过标准输入输出处理代码。例如：

```json [settings]
{
  "languages": {
    "Python": {
      "formatter": {
        "external": {
          "command": "black",
          "arguments": ["--stdin-filepath", "{buffer_path}", "-"]
        }
      },
      "format_on_save": "on"
    }
  }
}
```

3. 使用语言服务器提供的代码操作链（`"code_actions"`），可串联多个操作。示例：

```json [settings]
{
  "languages": {
    "JavaScript": {
      "formatter": [
        { "code_action": "source.fixAll.eslint" },
        { "code_action": "source.organizeImports" }
      ]
    }
  }
}
```

此外，你可以组合多种格式化器顺序执行，只要确保每一步都能从标准输入读取并输出结果。

## 语法高亮与主题

如果你想进一步自定义高亮风格，可以结合 Tree-sitter 的语法节点与主题配置：

- 通过主题覆盖（见 [主题文档](./visual-customization.md)）调整某类语法的颜色。
- 使用语言扩展，为某些结构增加额外的语义标识。

## 高级功能

除了上述基础设置外，Zed 还提供多种进阶能力：

- **多工作区工具链**：为不同语言选择不同编译器/解释器链，详见 [工具链文档](./toolchains.md)。
- **任务运行器**：通过 `.zed/tasks.json` 调用语言工具、测试套件（参阅 [任务](./tasks.md)）。
- **调试器支持**：在 `.zed/debug.json` 中设定语言特定的调试流程，参见 [调试器](./debugger.md)。
- **REPL 集成**：在 [REPL 文档](./repl.md) 中了解如何连接交互式解释器。

## 小结

- 在 `languages` 和 `lsp` 区块中覆盖语言相关设置。
- 使用 `file_types` 绑定文件与语言。
- 根据需要启用或禁用指定语言服务器，或自定义其初始化参数。
- 配置格式化、静态检查与主题，让 Zed 符合你的开发风格。

通过上述方法，你可以在 Zed 中为任意语言打造契合团队规范、顺手高效的工作流。
