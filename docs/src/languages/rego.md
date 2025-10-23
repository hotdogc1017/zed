# Rego

Zed 对 Rego 的语言支持由社区维护的 [Rego 扩展](https://github.com/StyraInc/zed-rego) 提供。

- Tree-sitter：[FallenAngel97/tree-sitter-rego](https://github.com/FallenAngel97/tree-sitter-rego)
- 语言服务器：[StyraInc/regal](https://github.com/StyraInc/regal)

## 安装

该扩展主要基于 [Regal](https://docs.styra.com/regal/language-server) 语言服务器，请先安装后再使用扩展。详细步骤请参阅 [快速上手指南](https://docs.styra.com/regal#getting-started)。

## 配置

扩展的行为可通过 `.regal/config.yaml` 配置文件控制。以下示例关闭了 `todo-comment` 规则、自定义了 `line-length` 规则，并在 `opa-fmt` 规则中忽略测试文件：

```yaml
rules:
  style:
    todo-comment:
      # 不对 TODO 注释报错
      level: ignore
    line-length:
      # 自定义规则配置
      max-line-length: 100
      # 行过长时仅警告，不视为失败
      level: warning
    opa-fmt:
      # 默认就是 error，显式声明更直观
      level: error
      # 可以为任意规则忽略特定文件
      # 此处示例忽略测试文件
      ignore:
        files:
          - "*_test.rego"
```

更多信息请查阅 Regal 的[配置文档](https://docs.styra.com/regal#configuration)。
