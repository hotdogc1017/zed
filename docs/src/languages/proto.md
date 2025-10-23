# Proto

可以通过 [Proto 扩展](https://github.com/zed-industries/zed/tree/main/extensions/proto) 在 Zed 中使用 Proto/Proto3（Protocol Buffers 定义语言）。

- Tree-sitter：[coder3101/tree-sitter-proto](https://github.com/coder3101/tree-sitter-proto)
- 语言服务器：[protobuf-language-server](https://github.com/lasorda/protobuf-language-server)

<!--
待补充：使用哪种语言服务器 / 功能支持范围

## 安装

### 安装 protobuf-language-server

安装 protobuf-language-server，并确保它位于 PATH 中：

```
go install github.com/lasorda/protobuf-language-server@latest
which protobuf-language-server
```

### 安装 ProtoLS

安装 protols，并确保它位于 PATH 中：

```
cargo install protols
which protols
```

## 配置

```json [settings]
"lsp": {
  "protobuf-language-server": {
    "binary": {
      "path": "protols"
    }
  }
}
```

## 格式化

如果已安装 `clang-format`，ProtoLS 支持自动格式化。

```sh
# macOS
brew install clang-format
# Ubuntu
sudo apt-get install clang-format
# Fedora
sudo dnf install clang-tools-extra
```

可通过创建 `.clang-format` 文件自定义格式，例如：

```clang-format
IndentWidth: 4
ColumnLimit: 120
```

或者在设置中直接将 `clang-format` 配置为 [formatter](https://zed.dev/docs/configuring-zed#formatter)：

```json [settings]
  "languages": {
    "Proto": {
      "format_on_save": "on",
      "tab_size": 4,
      "formatter": {
        "external": {
          "command": "clang-format",
          "arguments": ["-style={IndentWidth: 4, ColumnLimit: 0}"]
        }
      }
    },
  }
```
-->
