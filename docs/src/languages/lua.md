# Lua

可以通过 [Lua 扩展](https://github.com/zed-extensions/lua) 在 Zed 中使用 Lua。

- Tree-sitter：[tree-sitter-grammars/tree-sitter-lua](https://github.com/tree-sitter-grammars/tree-sitter-lua)
- 语言服务器：[LuaLS/lua-language-server](https://github.com/LuaLS/lua-language-server)

## luarc.json

可以在工作区根目录创建 `.luarc.json` 来配置 LuaLS。

```json [settings]
{
  "$schema": "https://raw.githubusercontent.com/LuaLS/vscode-lua/master/setting/schema.json",
  "runtime.version": "Lua 5.4",
  "format.enable": true,
  "workspace.library": ["../somedir/library"]
}
```

完整配置选项请参阅 [LuaLS 设置文档](https://luals.github.io/wiki/settings/)。在 Zed 中编辑该文件时，会自动补全各项可用值（例如 `runtime.version` 可选 "Lua 5.1"、"Lua 5.2"、"Lua 5.3"、"Lua 5.4"、"LuaJIT" 等）。注意如果从 VS Code 设置迁移，需要移除 `Lua.` 前缀（使用 `runtime.version` 而非 `Lua.runtime.version`）。

### LuaCATS 定义

借助 LuaCATS（Lua Comment and Type System）定义，LuaLS 可以提供更丰富的补全建议与类型校验。多种常用库已有相应定义，可通过在 `luarc.json` 的 `workspace.library` 中指定本地路径来使用。若将这些定义仓库检出到项目的父目录（如 `../playdate-luacats`、`../love2d`），即可使用相对路径；也可以将各个 LuaCATS 仓库作为子模块引入项目。

### LÖVE（Love2D）{#love2d}

若要在 Zed 中使用 [LÖVE（Love2D）](https://love2d.org/)，可以将 [LuaCATS/love2d](https://github.com/LuaCATS/love2d) 克隆到项目父目录中的 `love2d-luacats`：

```sh
cd .. && git clone https://github.com/LuaCATS/love2d love2d-luacats
```

然后在 `.luarc.json` 中添加：

```
{
  "$schema": "https://raw.githubusercontent.com/LuaLS/vscode-lua/master/setting/schema.json",
  "runtime.version": "Lua 5.4",
  "workspace.library": ["../love2d-luacats"],
  "runtime.special": {
    "love.filesystem.load": "loadfile"
  }
}
```

### PlaydateSDK

若要在 Zed 中使用 [Playdate Lua SDK](https://play.date/dev/)，可以将 [playdate-luacats](https://github.com/notpeter/playdate-luacats) 克隆到项目父目录：

```sh
cd .. && git clone https://github.com/notpeter/playdate-luacats
```

然后在 `.luarc.json` 中配置：

```json [settings]
{
  "$schema": "https://raw.githubusercontent.com/LuaLS/vscode-lua/master/setting/schema.json",
  "runtime.version": "Lua 5.4",
  "runtime.nonstandardSymbol": [
    "+=",
    "-=",
    "*=",
    "/=",
    "//=",
    "%=",
    "<<=",
    ">>=",
    "&=",
    "|=",
    "^="
  ],
  "diagnostics.severity": { "duplicate-set-field": "Hint" },
  "diagnostics.globals": ["import"],
  "workspace.library": ["../playdate-luacats"],
  "format.defaultConfig": {
    "indent_style": "space",
    "indent_size": "4"
  },
  "format.enable": true,
  "runtime.builtin": { "io": "disable", "os": "disable", "package": "disable" }
}
```

### 内联提示

要在 Zed 中为 LuaLS 启用[内联提示](../configuring-languages.md#inlay-hints)：

1. 在 Zed 的 `settings.json` 中添加：

```json [settings]
  "languages": {
    "Lua": {
      "inlay_hints": {
        "enabled": true,
        "show_type_hints": true,
        "show_parameter_hints": true,
        "show_other_hints": true
      }
    }
  }
```

2. 在 `.luarc.json` 中加入 `"hint.enable": true`。

## 格式化

### LuaLS 格式化

若要使用 LuaLS（基于 [CppCXY/EmmyLuaCodeStyle](https://github.com/CppCXY/EmmyLuaCodeStyle)）进行自动格式化，确保 `.luarc.json` 中包含 `"format.enable": true`：

```json [settings]
{
  "$schema": "https://raw.githubusercontent.com/sumneko/vscode-lua/master/setting/schema.json",
  "format.enable": true
}
```

然后在 Zed 的 `settings.json` 中添加：

```json [settings]
{
  "languages": {
    "Lua": {
      "format_on_save": "on",
      "formatter": "language_server"
    }
  }
}
```

你可以通过 `.editorconfig` 自定义 EmmyLuaCodeStyle 的格式选项，参见 [lua.template.editorconfig](https://github.com/CppCXY/EmmyLuaCodeStyle/blob/master/lua.template.editorconfig)。

### StyLua 格式化

若想改用 [StyLua](https://github.com/JohnnyMorganz/StyLua) 进行自动格式化：

1. 安装 [StyLua](https://github.com/JohnnyMorganz/StyLua)：`brew install stylua` 或 `cargo install stylua --features lua52,lua53,lua54,luau,luajit`（可根据需要删除不必要的 Lua 版本）。
2. 在 `settings.json` 中添加：

```json [settings]
{
  "languages": {
    "Lua": {
      "format_on_save": "on",
      "formatter": {
        "external": {
          "command": "stylua",
          "arguments": [
            "--syntax=Lua54",
            "--respect-ignores",
            "--stdin-filepath",
            "{buffer_path}",
            "-"
          ]
        }
      }
    }
  }
}
```

可在命令行参数（如 `--syntax=Lua54`）或项目根目录的 `stylua.toml` 中设置 StyLua 的各种选项：

```toml
syntax = "Lua54"
column_width = 100
line_endings = "Unix"
indent_type = "Spaces"
indent_width = 4
quote_style = "AutoPreferDouble"
call_parentheses = "Always"
collapse_simple_statement = "All"

[sort_requires]
enabled = true
```

更多可配置选项详见 [StyLua Options](https://github.com/JohnnyMorganz/StyLua?tab=readme-ov-file#options)。
