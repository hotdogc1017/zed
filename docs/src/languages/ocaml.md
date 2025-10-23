# OCaml

可以通过 [OCaml 扩展](https://github.com/zed-extensions/ocaml) 在 Zed 中使用 OCaml。

- Tree-sitter：[tree-sitter/tree-sitter-ocaml](https://github.com/tree-sitter/tree-sitter-ocaml)
- 语言服务器：[ocaml/ocaml-lsp](https://github.com/ocaml/ocaml-lsp)

## 安装指南

如果你的开发环境已经就绪，可以直接跳到[启动 Zed](#launching-zed)。

### 使用 Opam

Opam 是 OCaml 官方推荐的包管理器，也是入门 OCaml 的首选。请按照[官方指南](https://ocaml.org/install) 进行安装。

完成 opam 的安装并按照说明创建开发环境的 switch 后即可继续。

### 启动 Zed {#launching-zed}

此时你应该已安装 `ocamllsp`，可以在终端执行下列命令验证：

```sh
ocamllsp --help
```

如果能看到帮助信息，说明安装成功；否则请重新检查 `ocamllsp` 的安装步骤，确保其已正确安装。

接下来即可启动 Zed。考虑到 OCaml 包管理的工作方式，建议从终端启动 Zed，因此请先安装 [Zed CLI](https://zed.dev/features#cli)（如果尚未安装）。

安装完成后，在终端进入项目目录并运行：

```sh
zed .
```

这样就能在 Zed 中启用 OCaml 支持，无需额外配置。
