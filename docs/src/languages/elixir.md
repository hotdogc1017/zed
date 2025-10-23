# Elixir

可通过 [Elixir 扩展](https://github.com/zed-extensions/elixir) 在 Zed 中使用 Elixir 支持。

- Tree-sitter：
  - [elixir-lang/tree-sitter-elixir](https://github.com/elixir-lang/tree-sitter-elixir)
  - [phoenixframework/tree-sitter-heex](https://github.com/phoenixframework/tree-sitter-heex)
- 语言服务器：
  - [elixir-lang/expert](https://github.com/elixir-lang/expert)
  - [elixir-lsp/elixir-ls](https://github.com/elixir-lsp/elixir-ls)
  - [elixir-tools/next-ls](https://github.com/elixir-tools/next-ls)
  - [lexical-lsp/lexical](https://github.com/lexical-lsp/lexical)

## 选择语言服务器

Elixir 扩展提供 `expert`、`elixir-ls`、`next-ls` 与 `lexical` 语言服务器支持。

默认启用 `elixir-ls`。

### Expert

若要切换到 `expert`，在 `settings.json` 中加入：

```json [settings]
  "languages": {
    "Elixir": {
      "language_servers": ["expert", "!elixir-ls", "!next-ls", "!lexical", "..."]
    },
    "HEEX": {
      "language_servers": ["expert", "!elixir-ls", "!next-ls", "!lexical", "..."]
    }
  }
```

### Next LS

若要切换到 `next-ls`，在 `settings.json` 中加入：

```json [settings]
  "languages": {
    "Elixir": {
      "language_servers": ["next-ls", "!expert", "!elixir-ls", "!lexical", "..."]
    },
    "HEEX": {
      "language_servers": ["next-ls", "!expert", "!elixir-ls", "!lexical", "..."]
    }
  }
```

### Lexical

若要切换到 `lexical`，在 `settings.json` 中加入：

```json [settings]
  "languages": {
    "Elixir": {
      "language_servers": ["lexical", "!expert", "!elixir-ls", "!next-ls", "..."]
    },
    "HEEX": {
      "language_servers": ["lexical", "!expert", "!elixir-ls", "!next-ls", "..."]
    }
  }
```

## 配置 `elixir-ls`

1. 安装 `elixir`：

```sh
brew install elixir
```

2. 安装 `elixir-ls`：

```sh
brew install elixir-ls
```

3. 重启 Zed。

> 如果在 Elixir 项目中 `elixir-ls` 未能运行，请通过命令面板执行 `zed: open log` 查看日志。当日志中出现 `invalid LSP message header "Shall I install Hex? (if running non-interactively, use \"mix local.hex --force\") [Yn]"`，说明需要安装 [`Hex`](https://hex.pm)。可在命令行运行 `elixir-ls` 并接受安装 Hex 的提示。

### 使用 Mix 进行格式化

如果你更习惯使用 [Mix](https://hexdocs.pm/mix/Mix.html) 格式化代码，可以在 `settings.json` 中配置外部格式化工具，保存文件时将自动格式化：

```json [settings]
{
  "languages": {
    "Elixir": {
      "format_on_save": "on",
      "formatter": {
        "external": {
          "command": "mix",
          "arguments": ["format", "--stdin-filename", "{buffer_path}", "-"]
        }
      }
    }
  }
}
```

### 额外的工作区配置选项

可以在 `settings.json` 的 lsp 配置中传递更多 elixir-ls 工作区选项。

以下示例用于禁用 Dialyzer：

```json [settings]
  "lsp": {
    "elixir-ls": {
      "settings": {
        "dialyzerEnabled": false
      }
    }
  }
```

更多选项请参见 [ElixirLS 配置说明](https://github.com/elixir-lsp/elixir-ls#elixirls-configuration-settings)。

### HEEx

Zed 也支持 HEEx 模板。HEEx 是 [EEx](https://hexdocs.pm/eex/1.12.3/EEx.html)（嵌入式 Elixir）与 HTML 的组合，常用于 Phoenix LiveView 应用。

- Tree-sitter：[phoenixframework/tree-sitter-heex](https://github.com/phoenixframework/tree-sitter-heex)
