# JSON

Zed 原生支持 JSON。

- Tree-sitter：[tree-sitter/tree-sitter-json](https://github.com/tree-sitter/tree-sitter-json)
- 语言服务器：[zed-industries/json-language-server](https://github.com/zed-industries/json-language-server)

## JSONC

Zed 也支持 JSON 的超集 JSONC，允许在文件中使用单行注释（`//`）。在编辑此类文件时，可以通过 `cmd-/`（macOS）或 `ctrl-/`（Linux）来切换当前行或选区的注释状态。

## JSONC 与 Prettier 格式化

当对 `*.jsonc` 文件执行 “Format Document” 或启用 `format_on_save` 时，Zed 会调用 Prettier 进行格式化。但 Prettier 存在一个[未解决的问题](https://github.com/prettier/prettier/issues/15956)：它会为 `jsonc` 扩展名的文件添加尾随逗号。同样内容若使用 `.json` 扩展名则不会受影响。

解决方法是在 `.prettierrc` 中加入以下配置：

```json [settings]
{
  "overrides": [
    {
      "files": ["*.jsonc"],
      "options": {
        "parser": "json",
        "trailingComma": "none"
      }
    }
  ]
}
```

## JSON 语言服务器

Zed 默认会对 `package.json` 与 `tsconfig.json` 进行 JSON Schema 验证；此外，`json-language-server` 还能使用项目文件中的 Schema、[JSON Schema Store](https://www.schemastore.org) 或其他公开 URL 来完成验证。

### 在文件中内联指定 Schema

可以在 JSON 文件顶部添加 `$schema` 字段，直接指定 Schema 路径。

例如为 [lua-language-server](https://github.com/LuaLS/lua-language-server/) 使用的 `.luarc.json`：

```json [settings]
{
  "$schema": "https://raw.githubusercontent.com/sumneko/vscode-lua/master/setting/schema.json",
  "runtime.version": "Lua 5.4"
}
```

### 通过设置指定 Schema

也可以在 Zed 的 LSP 设置中，将 JSON Schema 与文件路径关联：

```json [settings]
"lsp": {
  "json-language-server": {
    "settings": {
      "json": {
        "schemas": [
          {
            "fileMatch": ["*/*.luarc.json"],
            "url": "https://raw.githubusercontent.com/sumneko/vscode-lua/master/setting/schema.json"
          }
        ]
      }
    }
  }
}
```

同时可以在 `settings.json` 中传递 [json-language-server 支持的任意设置](https://github.com/Microsoft/vscode/blob/main/extensions/json-language-features/server/README.md#settings)。

<!--
待补充：Formatter（Prettier）相关设置（自动格式化、tab_size 等）
-->
