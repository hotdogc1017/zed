# JavaScript

Zed 原生支持 JavaScript。

- Tree-sitter：[tree-sitter/tree-sitter-javascript](https://github.com/tree-sitter/tree-sitter-javascript)
- 语言服务器：[typescript-language-server/typescript-language-server](https://github.com/typescript-language-server/typescript-language-server)
- 调试适配器：[vscode-js-debug](https://github.com/microsoft/vscode-js-debug)

## 代码格式化

JavaScript 默认启用保存时格式化，使用 TypeScript 自带的格式化器。不过许多项目会使用 [Prettier](https://prettier.io/) 等命令行格式化工具。若要使用此类工具，可在设置中为 JavaScript 指定外部格式化器。更多信息见[配置 Zed](../configuring-zed.md)。

例如，如果已安装 Prettier 并且位于 `PATH` 中，可以在 `settings.json` 中添加以下内容来格式化 JavaScript 文件：

```json [settings]
{
  "languages": {
    "JavaScript": {
      "formatter": {
        "external": {
          "command": "prettier",
          "arguments": ["--stdin-filepath", "{buffer_path}"]
        }
      }
    }
  }
}
```

## JSX

Zed 内置 JSX 语法高亮。

在 JSX 字符串内，Zed 会通过 [`tailwindcss-language-server`](./tailwindcss.md) 提供 Tailwind CSS 类名的自动补全。

## JSDoc

Zed 支持在 JavaScript 与 TypeScript 注释中使用 JSDoc 语法，并通过 [tree-sitter/tree-sitter-jsdoc](https://github.com/tree-sitter/tree-sitter-jsdoc) 进行解析与高亮。

## ESLint

可以通过在格式化时运行 ESLint 的代码操作来实现 `eslint --fix`：

```json [settings]
{
  "languages": {
    "JavaScript": {
      "code_actions_on_format": {
        "source.fixAll.eslint": true
      }
    }
  }
}
```

也可以在使用 `fixAll` 时仅执行某条 ESLint 规则：

```json [settings]
{
  "languages": {
    "JavaScript": {
      "code_actions_on_format": {
        "source.fixAll.eslint": true
      }
    }
  },
  "lsp": {
    "eslint": {
      "settings": {
        "codeActionOnSave": {
          "rules": ["import/order"]
        }
      }
    }
  }
}
```

> **注意：** 其他格式化器仍会在 ESLint 之后运行。如果语言服务器或 Prettier 的格式化规则与 ESLint 不一致，它们可能会覆盖 ESLint 的修改并再次产生错误。

如果只想在保存时运行 ESLint，可以将代码操作配置为格式化器：

```json [settings]
{
  "languages": {
    "JavaScript": {
      "code_actions_on_format": {
        "source.fixAll.eslint": true
      }
    }
  }
}
```

### 配置 ESLint 的 `nodePath`

可以如下设置 ESLint 的 `nodePath`：

```json [settings]
{
  "lsp": {
    "eslint": {
      "settings": {
        "nodePath": ".yarn/sdks"
      }
    }
  }
}
```

### 配置 ESLint 的 `problems`

以下示例展示如何设置 `problems.shortenToSingleLine`：

```json [settings]
{
  "lsp": {
    "eslint": {
      "settings": {
        "problems": {
          "shortenToSingleLine": true
        }
      }
    }
  }
}
```

### 配置 ESLint 的 `rulesCustomizations`

```json [settings]
{
  "lsp": {
    "eslint": {
      "settings": {
        "rulesCustomizations": [
          // 将所有 ESLint 错误/警告显示为警告
          { "rule": "*", "severity": "warn" }
        ]
      }
    }
  }
}
```

### 配置 ESLint 的 `workingDirectory`

```json [settings]
{
  "lsp": {
    "eslint": {
      "settings": {
        "workingDirectory": {
          "mode": "auto"
        }
      }
    }
  }
}
```

## 调试

Zed 通过 `vscode-js-debug` 开箱即用地支持调试 JavaScript。
无需额外配置即可调试：

- `package.json` 中定义的任务
- 使用 Jest、Mocha、Vitest、Jasmine、Bun、Node 等常见框架编写的测试

执行 {#action debugger::Start}（{#kb debugger::Start}）可查看上下文相关的预设调试任务列表。

> **注意：** 当 `package.json` 中存在 `@types/bun` 时会自动检测 Bun 测试。
>
> **注意：** 当 `package.json` 中存在 `@types/node` 时会自动检测 Node 测试（需要 Node.js 20+）。

如常见情况一样，`.vscode/launch.json` 中的配置也会在 Zed 中使用。

如果这些预设无法满足需求，可以在 `.zed/debug.json` 中添加自定义调试配置，示例如下。

### 配置 JavaScript 调试任务

JavaScript 调试比其他语言复杂，因为它既可能在 Node.js 中运行，也可能在浏览器中运行。`vscode-js-debug` 提供 `type` 字段用于指定环境，常见的值为 `node` 或 `chrome`。

- [vscode-js-debug 配置文档](https://github.com/microsoft/vscode-js-debug/blob/main/OPTIONS.md)

### 使用 Node 调试当前文件

```json [debug]
[
  {
    "adapter": "JavaScript",
    "label": "Debug JS file",
    "type": "node",
    "request": "launch",
    "program": "$ZED_FILE",
    "skipFiles": ["<node_internals>/**"]
  }
]
```

### 在 Chrome 中启动 Web 应用调试

```json [debug]
[
  {
    "adapter": "JavaScript",
    "label": "Debug app in Chrome",
    "type": "chrome",
    "request": "launch",
    "file": "$ZED_WORKTREE_ROOT/index.html",
    "webRoot": "$ZED_WORKTREE_ROOT",
    "console": "integratedTerminal",
    "skipFiles": ["<node_internals>/**"]
  }
]
```

## 另请参阅

- [Yarn 文档](./yarn.md)：介绍如何将项目配置为使用 Yarn
- [TypeScript 文档](./typescript.md)
