# Svelte

可以通过 [Svelte 扩展](https://github.com/zed-extensions/svelte) 在 Zed 中使用 Svelte。

- Tree-sitter：[tree-sitter-grammars/tree-sitter-svelte](https://github.com/tree-sitter-grammars/tree-sitter-svelte)
- 语言服务器：[sveltejs/language-tools](https://github.com/sveltejs/language-tools)

## 额外主题样式配置

可以通过设置控制指令、修饰符等属性部分的呈现效果：

```json [settings]
"syntax": {
  // 指令（如 `class:foo`、`on:click`）中前缀部分的样式
  "attribute.function": {
    "color": "#ff0000"
  },
  // 属性结尾修饰符的样式，例如 `on:<click|preventDefault|stopPropagation>`
  "attribute.special": {
    "color": "#00ff00"
  }
}
```

## 内联提示

启用内联提示时，Zed 会向语言服务器发送以下初始化配置：

```json [settings]
"inlayHints": {
  "parameterNames": {
    "enabled": "all",
    "suppressWhenArgumentMatchesName": false
  },
  "parameterTypes": {
    "enabled": true
  },
  "variableTypes": {
    "enabled": true,
    "suppressWhenTypeMatchesName": false
  },
  "propertyDeclarationTypes": {
    "enabled": true
  },
  "functionLikeReturnTypes": {
    "enabled": true
  },
  "enumMemberValues": {
    "enabled": true
  }
}
```

若需覆盖这些设置，可按如下方式配置：

```json [settings]
"lsp": {
  "svelte-language-server": {
    "initialization_options": {
      "configuration": {
        "typescript": {
          // ......
        },
        "javascript": {
          // ......
        }
      }
    }
  }
}
```

更多可用选项见 [TypeScript 语言服务器的 `package.json`](https://github.com/microsoft/vscode/blob/main/extensions/typescript-language-features/package.json)。
