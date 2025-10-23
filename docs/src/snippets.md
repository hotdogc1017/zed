# 代码片段（Snippets）

使用 {#action snippets::ConfigureSnippets} 命令，可以为指定的[作用域](#scopes)创建或编辑代码片段文件。

所有片段文件位于 `~/.config/zed/snippets` 目录，可通过 {#action snippets::OpenFolder} 快捷打开。

## 配置示例

```json [settings]
{
  // 每个片段必须包含 name 与 body，prefix、description 可选。
  // prefix 是触发关键词；若省略则使用 name。
  // 通过 $1、$2 或 ${1:defaultValue} 等占位符定义跳转点。
  // $0 表示最终光标位置。
  // 相同编号的占位符会相互联动。
  "Log to console": {
    "prefix": "log",
    "body": ["console.info(\"Hello, ${1:World}!\")", "$0"],
    "description": "输出日志"
  }
}
```

## 作用域（Scopes）

片段文件名通常为语言名称的小写形式，如 Python 使用 `python.json`，Shell Script 使用 `shell script.json`。部分语言有例外：

| 作用域      | 文件名             |
| ----------- | ------------------ |
| 全局        | snippets.json      |
| JSX         | javascript.json    |
| 纯文本      | plaintext.json     |

若需为 JSX 创建片段，应写入 `javascript.json`，而非 `jsx.json`。TSX 与 TypeScript 则遵循常规命名规则。

## 已知限制

- 若同时提供多个 `prefix`，仅第一个生效。
- 目前仅支持 `json` 格式的片段文件，尽管 `simple-completion-language-server` 同时支持 `json` 与 `toml`。

## 其他说明

`simple-completion-language-server` 中的 `feature_paths` 选项默认关闭，可在 `settings.json` 中启用：

```json [settings]
{
  "lsp": {
    "snippet-completion-server": {
      "settings": {
        "feature_paths": true
      }
    }
  }
}
```

更多配置详见 [`simple-completion-language-server` 项目说明](https://github.com/zed-industries/simple-completion-language-server/tree/main)。
