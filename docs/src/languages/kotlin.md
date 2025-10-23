# Kotlin

Zed 对 Kotlin 的语言支持由社区维护的 [Kotlin 扩展](https://github.com/zed-extensions/kotlin) 提供。
如需反馈问题，请前往：[https://github.com/zed-extensions/kotlin/issues](https://github.com/zed-extensions/kotlin/issues)

- Tree-sitter：[fwcd/tree-sitter-kotlin](https://github.com/fwcd/tree-sitter-kotlin)
- 语言服务器：[fwcd/kotlin-language-server](https://github.com/fwcd/kotlin-language-server)

## 配置

可以在 `settings.json` 的 lsp 设置中向语言服务器传递工作区配置选项。

完整的 lsp `settings` 位于 [`Configuration` 类](https://github.com/fwcd/kotlin-language-server/blob/main/server/src/main/kotlin/org/javacs/kt/Configuration.kt) 中，`initialization_options` 则在同文件的 `InitializationOptions` 类中定义。

### JVM 目标版本

以下示例将 JVM 目标版本从默认的 `default`（即 1.8）修改为 `17`：

```json [settings]
{
  "lsp": {
    "kotlin-language-server": {
      "settings": {
        "compiler": {
          "jvm": {
            "target": "17"
          }
        }
      }
    }
  }
}
```

### JAVA_HOME

如需指定特定的 Java 安装路径，可以通过设置 `JAVA_HOME` 环境变量实现：

```json [settings]
{
  "lsp": {
    "kotlin-language-server": {
      "binary": {
        "env": {
          "JAVA_HOME": "/Users/whatever/Applications/Work/Android Studio.app/Contents/jbr/Contents/Home"
        }
      }
    }
  }
}
```
