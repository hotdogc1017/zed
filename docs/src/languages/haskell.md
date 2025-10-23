# Haskell

可以通过 [Haskell 扩展](https://github.com/zed-extensions/haskell) 在 Zed 中启用 Haskell 支持。

- Tree-sitter：[tree-sitter-haskell](https://github.com/tree-sitter/tree-sitter-haskell)
- 语言服务器：[haskell-language-server](https://github.com/haskell/haskell-language-server)

## 安装 HLS

推荐使用 [ghcup](https://www.haskell.org/ghcup/install/) 安装 [haskell-language-server](https://haskell-language-server.readthedocs.io/en/latest/installation.html)（简称 HLS），命令如下：

```sh
curl --proto '=https' --tlsv1.2 -sSf https://get-ghcup.haskell.org | sh
ghcup install hls
which haskell-language-server-wrapper
```

## 配置 HLS

如需自定义 haskell-language-server（hls），可在 Zed 的 `settings.json` 中添加配置：

```json [settings]
{
  "lsp": {
    "hls": {
      "initialization_options": {
        "haskell": {
          "formattingProvider": "fourmolu"
        }
      }
    }
  }
}
```

更多选项请参阅官方的 [haskell-language-server 配置文档](https://haskell-language-server.readthedocs.io/en/latest/configuration.html)。

如果想使用特定的 hls 可执行文件，或尝试以 [static-ls](https://github.com/josephsumabat/static-ls) 作为替代，可以指定可执行路径与参数：

```json [settings]
{
  "lsp": {
    "hls": {
      "binary": {
        "path": "static-ls",
        "arguments": ["--experimentalFeatures"]
      }
    }
  }
}
```
