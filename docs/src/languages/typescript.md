# TypeScript

Zed 原生支持 TypeScript 与 TSX。

- Tree-sitter：[tree-sitter/tree-sitter-typescript](https://github.com/tree-sitter/tree-sitter-typescript)
- 语言服务器：[yioneko/vtsls](https://github.com/yioneko/vtsls)
- 备用语言服务器：[typescript-language-server/typescript-language-server](https://github.com/typescript-language-server/typescript-language-server)
- 调试适配器：[vscode-js-debug](https://github.com/microsoft/vscode-js-debug)

## 语言服务器

默认情况下，Zed 针对 TypeScript、TSX、JavaScript 使用 [vtsls](https://github.com/yioneko/vtsls)。若想改用 [typescript-language-server](https://github.com/typescript-language-server/typescript-language-server)，可在设置中为各语言指定：

```json [settings]
{
  "languages": {
    "TypeScript": {
      "language_servers": ["typescript-language-server", "!vtsls", "..."]
    },
    "TSX": {
      "language_servers": ["typescript-language-server", "!vtsls", "..."]
    },
    "JavaScript": {
      "language_servers": ["typescript-language-server", "!vtsls", "..."]
    }
  }
}
```

TypeScript 默认使用 Prettier 进行格式化。如需禁用：

```json [settings]
{
  "languages": {
    "TypeScript": {
      "prettier": { "allowed": false }
    }
  }
}
```

## 大型项目

`vtsls` 在超大型项目中可能耗尽内存。Zed 默认将内存上限从 3072 调整为 8092（8 GiB），如仍不足，可进一步调高：

```json [settings]
{
  "lsp": {
    "vtsls": {
      "settings": {
        "typescript": { "tsserver": { "maxTsServerMemory": 16184 } },
        "javascript": { "tsserver": { "maxTsServerMemory": 16184 } }
      }
    }
  }
}
```

## 内联提示

Zed 启用内联提示时，会向语言服务器发送相应初始化参数。若使用 `typescript-language-server`，可在 `settings.json` 中重写：

```json [settings]
{
  "lsp": {
    "typescript-language-server": {
      "initialization_options": {
        "preferences": {
          "includeInlayParameterNameHints": "all",
          "includeInlayParameterNameHintsWhenArgumentMatchesName": true,
          "includeInlayFunctionParameterTypeHints": true,
          "includeInlayVariableTypeHints": true,
          "includeInlayVariableTypeHintsWhenTypeMatchesName": true,
          "includeInlayPropertyDeclarationTypeHints": true,
          "includeInlayFunctionLikeReturnTypeHints": true,
          "includeInlayEnumMemberValueHints": true
        }
      }
    }
  }
}
```

更多信息参见 [typescript-language-server 内联提示文档](https://github.com/typescript-language-server/typescript-language-server?tab=readme-ov-file#inlay-hints-textdocumentinlayhint)。

若使用 `vtsls`：

```json [settings]
{
  "lsp": {
    "vtsls": {
      "settings": {
        "javascript": {
          "inlayHints": {
            "parameterNames": {
              "enabled": "all",
              "suppressWhenArgumentMatchesName": false
            },
            "parameterTypes": { "enabled": true },
            "variableTypes": {
              "enabled": true,
              "suppressWhenTypeMatchesName": true
            },
            "propertyDeclarationTypes": { "enabled": true },
            "functionLikeReturnTypes": { "enabled": true },
            "enumMemberValues": { "enabled": true }
          }
        },
        "typescript": {
          "inlayHints": {
            "parameterNames": {
              "enabled": "all",
              "suppressWhenArgumentMatchesName": false
            },
            "parameterTypes": { "enabled": true },
            "variableTypes": {
              "enabled": true,
              "suppressWhenTypeMatchesName": true
            },
            "propertyDeclarationTypes": { "enabled": true },
            "functionLikeReturnTypes": { "enabled": true },
            "enumMemberValues": { "enabled": true }
          }
        }
      }
    }
  }
}
```

## 调试

Zed 通过 `vscode-js-debug` 开箱即用地调试 TypeScript。无需额外配置即可调试：

- `package.json` 中的任务
- 使用 Jest、Mocha、Vitest、Jasmine、Bun、Node 等框架编写的测试

执行 {#action debugger::Start}（{#kb debugger::Start}）即可查看预设调试任务。

> **注意：** 若 `package.json` 中存在 `@types/bun`，会自动识别 Bun 测试。
>
> **注意：** 若 `package.json` 中存在 `@types/node`（需 Node.js 20+），会自动识别 Node 测试。

同时，`.vscode/launch.json` 中的配置也可直接在 Zed 中使用。

如需更多自定义，请在 `.zed/debug.json` 中添加调试配置。示例如下。

### 配置 JavaScript 调试任务

JavaScript 调试涉及 Node 与浏览器两个环境，`vscode-js-debug` 通过 `type` 字段区分（`node` 或 `chrome`）。

- [vscode-js-debug 配置文档](https://github.com/microsoft/vscode-js-debug/blob/main/OPTIONS.md)

### 附加到运行在浏览器中的服务器（例如 `npx serve`）

对于外部启动的 Web 服务器，可附加调试：

```json [debug]
[
  {
    "label": "Launch Chrome (TypeScript)",
    "adapter": "JavaScript",
    "type": "chrome",
    "request": "launch",
    "url": "http://localhost:5500",
    "program": "$ZED_FILE",
    "webRoot": "${ZED_WORKTREE_ROOT}",
    "build": {
      "command": "npx",
      "args": ["tsc"]
    },
    "skipFiles": ["<node_internals>/**"]
  }
]
```

## 另请参阅

- [Zed Yarn 文档](./yarn.md)
- [Zed Deno 文档](./deno.md)
