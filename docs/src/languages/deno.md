# Deno

可以通过 [Deno 扩展](https://github.com/zed-extensions/deno) 在 Zed 中获得 Deno 支持。

- 语言服务器：[Deno Language Server](https://docs.deno.com/runtime/manual/advanced/language_server/overview/)

## Deno 配置

如果希望在 TypeScript 与 TSX 文件中使用 Deno 语言服务器，你很可能需要禁用默认语言服务器并启用 Deno。可在 `settings.json` 中加入以下内容：

```json [settings]
{
  "lsp": {
    "deno": {
      "settings": {
        "deno": {
          "enable": true
        }
      }
    }
  },
  "languages": {
    "JavaScript": {
      "language_servers": [
        "deno",
        "!typescript-language-server",
        "!vtsls",
        "!eslint"
      ],
      "formatter": "language_server"
    },
    "TypeScript": {
      "language_servers": [
        "deno",
        "!typescript-language-server",
        "!vtsls",
        "!eslint"
      ],
      "formatter": "language_server"
    },
    "TSX": {
      "language_servers": [
        "deno",
        "!typescript-language-server",
        "!vtsls",
        "!eslint"
      ],
      "formatter": "language_server"
    }
  }
}
```

更多介绍请参阅 Zed 文档中的[配置受支持的语言](../configuring-languages.md)。

<!--
待补充：Deno TypeScript REPL 使用说明 [docs/repl#typescript-deno](../repl.md#typescript-deno)
-->

## 配置文件自动补全

若希望在 `deno.json` 或 `package.json` 中获得补全，可以在 `settings.json` 中添加以下配置（更多信息见 https://zed.dev/docs/languages/json）：

```json [settings]
"lsp": {
    "json-language-server": {
      "settings": {
        "json": {
          "schemas": [
            {
              "fileMatch": [
                "deno.json",
                "deno.jsonc"
              ],
              "url": "https://raw.githubusercontent.com/denoland/deno/refs/heads/main/cli/schemas/config-file.v1.json"
            },
            {
              "fileMatch": [
                "package.json"
              ],
              "url": "http://json.schemastore.org/package"
            }
          ]
        }
      }
    }
  }
```

## DAP 支持

要调试 Deno 程序，可在 `.zed/debug.json` 中加入：

```json [debug]
[
  {
    "adapter": "JavaScript",
    "label": "Deno",
    "request": "launch",
    "type": "pwa-node",
    "cwd": "$ZED_WORKTREE_ROOT",
    "program": "$ZED_FILE",
    "runtimeExecutable": "deno",
    "runtimeArgs": ["run", "--allow-all", "--inspect-wait"],
    "attachSimplePort": 9229
  }
]
```

## 可运行任务支持

若想从界面中运行诸如测试之类的 Deno 任务，可在 `.zed/tasks.json` 中添加：

```json [tasks]
[
  {
    "label": "deno test",
    "command": "deno test -A --filter '/^$ZED_CUSTOM_DENO_TEST_NAME$/' '$ZED_FILE'",
    "tags": ["js-test"]
  }
]
```

## 另请参阅

- [TypeScript](./typescript.md)
- [JavaScript](./javascript.md)
