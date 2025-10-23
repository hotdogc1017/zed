# Dart

可通过 [Dart 扩展](https://github.com/zed-extensions/dart) 获得 Dart 支持。

- Tree-sitter：[UserNobody14/tree-sitter-dart](https://github.com/UserNobody14/tree-sitter-dart)
- 语言服务器：[dart language-server](https://github.com/dart-lang/sdk)

## 前置条件

需要先安装 Dart SDK。

你可以在 [dart.dev/get-dart](https://dart.dev/get-dart) 下载，或者通过 [Flutter Version Management CLI（fvm）](https://fvm.app/documentation/getting-started/installation) 安装。

## 配置

如果 `dart` 已在你的 PATH 中，扩展无需额外配置：

```sh
which dart
dart --version
```

若想使用特定的 dart 可执行文件，或通过 FVM 运行 dart，可以在 Zed 的 `settings.json` 中指定：

```json [settings]
{
  "lsp": {
    "dart": {
      "binary": {
        "path": "/opt/homebrew/bin/fvm",
        "arguments": ["dart", "language-server", "--protocol=lsp"]
      }
    }
  }
}
```

### 格式化

Dart 默认使用较为保守的最大行宽（80）。如果希望在自动格式化时允许更长的行宽，可以在 Zed 的 `settings.json` 中加入：

```json [settings]
{
  "lsp": {
    "dart": {
      "settings": {
        "lineLength": 140
      }
    }
  }
}
```

更多关于 [dart language-server 功能](https://github.com/dart-lang/sdk/blob/main/pkg/analysis_server/tool/lsp_spec/README.md) 的信息，请查阅官方文档。
