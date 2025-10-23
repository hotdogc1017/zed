# Java

Zed 对 Java 的语言支持包括：

- Zed Java 扩展：[zed-extensions/java](https://github.com/zed-extensions/java)
- Tree-sitter：[tree-sitter/tree-sitter-java](https://github.com/tree-sitter/tree-sitter-java)
- 语言服务器：[eclipse-jdtls/eclipse.jdt.ls](https://github.com/eclipse-jdtls/eclipse.jdt.ls)

## 安装 OpenJDK

需要先安装 Java 运行时（OpenJDK）。

- macOS：`brew install openjdk`
- Ubuntu：`sudo add-apt-repository ppa:openjdk-23 && sudo apt-get install openjdk-23`
- Windows：`choco install openjdk`
- Arch Linux：`sudo pacman -S jre-openjdk-headless`

或手动下载与安装 [OpenJDK 23](https://jdk.java.net/23/)。

## 安装扩展

打开 {#action zed::Extensions}（{#kb zed::Extensions}）并搜索 `java` 即可安装。

## 设置与初始化选项

扩展会自动下载语言服务器。如希望自行管理，可参见下文的[手动安装 JDTLS](#manual-jdtls-install)。

可用的 `initialization_options` 请参阅 [Eclipse.jdt.ls Wiki 的 Initialize Request 章节](https://github.com/eclipse-jdtls/eclipse.jdt.ls/wiki/Running-the-JAVA-LS-server-from-the-command-line#initialize-request)。

可以通过 {#action zed::OpenSettings}（{#kb zed::OpenSettings}）打开设置，或在项目中创建 `.zed/settings.json` 来添加以下自定义配置。

### Zed Java 设置

```json [settings]
{
  "lsp": {
    "jdtls": {
      "initialization_options": {}
    }
  }
}
```

## 配置示例

### JDTLS 可执行文件

默认情况下 Zed 会在 `PATH` 中查找 `jdtls`。若想指定不同的可执行文件，可以在设置中写入：

```json [settings]
  "lsp": {
    "jdtls": {
      "binary": {
        "path": "/path/to/java/bin/jdtls",
        // "arguments": [],
        // "env": {},
        "ignore_system_version": true
      }
    }
  }
```

### Zed Java 初始化选项

还可以直接向语言服务器传递更多选项，例如：

```json [settings]
{
  "lsp": {
    "jdtls": {
      "initialization_options": {
        "bundles": [],
        "workspaceFolders": ["file:///home/snjeza/Project"],
        "settings": {
          "java": {
            "home": "/usr/local/jdk-9.0.1",
            "errors": {
              "incompleteClasspath": {
                "severity": "warning"
              }
            },
            "configuration": {
              "updateBuildConfiguration": "interactive",
              "maven": {
                "userSettings": null
              }
            },
            "trace": {
              "server": "verbose"
            },
            "import": {
              "gradle": {
                "enabled": true
              },
              "maven": {
                "enabled": true
              },
              "exclusions": [
                "**/node_modules/**",
                "**/.metadata/**",
                "**/archetype-resources/**",
                "**/META-INF/maven/**",
                "/**/test/**"
              ]
            },
            "jdt": {
              "ls": {
                "lombokSupport": {
                  "enabled": false // 为 true 时启用 Lombok 支持
                }
              }
            },
            "referencesCodeLens": {
              "enabled": false
            },
            "signatureHelp": {
              "enabled": false
            },
            "implementationsCodeLens": {
              "enabled": false
            },
            "format": {
              "enabled": true
            },
            "saveActions": {
              "organizeImports": false
            },
            "contentProvider": {
              "preferred": null
            },
            "autobuild": {
              "enabled": false
            },
            "completion": {
              "favoriteStaticMembers": [
                "org.junit.Assert.*",
                "org.junit.Assume.*",
                "org.junit.jupiter.api.Assertions.*",
                "org.junit.jupiter.api.Assumptions.*",
                "org.junit.jupiter.api.DynamicContainer.*",
                "org.junit.jupiter.api.DynamicTest.*"
              ],
              "importOrder": ["java", "javax", "com", "org"]
            }
          }
        }
      }
    }
  }
}
```

## 手动安装 JDTLS

如果愿意，也可以自行安装 JDTLS，并在扩展中配置使用。

- macOS：`brew install jdtls`
- Arch：[AUR 上的 `jdtls`](https://aur.archlinux.org/packages/jdtls)

或手动下载：

- [JDTLS Milestone Builds](http://download.eclipse.org/jdtls/milestones/)（每两周更新）
- [JDTLS Snapshot Builds](https://download.eclipse.org/jdtls/snapshots/)（更频繁的更新）

## 另请参阅

- [Zed Java 仓库](https://github.com/zed-extensions/java)
- [Zed Java 问题反馈](https://github.com/zed-extensions/java/issues)
