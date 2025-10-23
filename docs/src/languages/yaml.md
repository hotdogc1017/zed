# YAML

Zed 原生支持 YAML。

- Tree-sitter：[zed-industries/tree-sitter-yaml](https://github.com/zed-industries/tree-sitter-yaml)
- 语言服务器：[redhat-developer/yaml-language-server](https://github.com/redhat-developer/yaml-language-server)

## 配置

可在 `settings.json` 的 `lsp.yaml-language-server` 段中配置各项 [yaml-language-server 设置](https://github.com/redhat-developer/yaml-language-server?tab=readme-ov-file#language-server-settings)。示例：

```json [settings]
  "lsp": {
    "yaml-language-server": {
      "settings": {
        "yaml": {
          "keyOrdering": true,
          "format": {
            "singleQuote": true
          },
          "schemas": {
            "http://json.schemastore.org/composer": ["/*"],
            "../relative/path/schema.json": ["/config*.yaml"]
          }
        }
      }
    }
  }
```

注意：设置键需按层级嵌套，例如 `yaml.keyOrdering` 写作 `{ "yaml": { "keyOrdering": true } }`。

## 格式化

默认情况下，Zed 使用 Prettier 格式化 YAML。

### Prettier 格式化

可在 `.prettierrc` 定制行为，例如关闭单引号：

```json [settings]
{
  "overrides": [
    {
      "files": ["*.yaml", "*.yml"],
      "options": {
        "singleQuote": false
      }
    }
  ]
}
```

### 使用 yaml-language-server 格式化

若想用 `yaml-language-server` 取代 Prettier：

```json [settings]
  "languages": {
    "YAML": {
      "formatter": "language_server"
    }
  }
```

## Schema

`yaml-language-server` 默认会尝试自动检测并从 [JSON Schema Store](https://schemastore.org/) 获取合适的 Schema。

如需覆盖自动匹配，可通过 `schemas` 设置（见上例），或在文件顶部使用行内 Schema：

```yaml
# yaml-language-server: $schema=https://json.schemastore.org/github-action.json
name: Issue Assignment
on:
  issues:
    types: [opened]
```

也可以禁用自动检测与下载：

```json [settings]
  "lsp": {
    "yaml-language-server": {
      "settings": {
        "yaml": {
          "schemaStore": {
            "enable": false
          }
        }
      }
    }
  }
```

## 自定义标签

yaml-language-server 支持[自定义标签](https://github.com/redhat-developer/yaml-language-server#adding-custom-tags)，可用于在运行时扩展 YAML 功能。

例如，为支持 AWS CloudFormation 特有标签，可在设置中添加：

```json [settings]
  "lsp": {
    "yaml-language-server": {
      "settings": {
        "yaml": {
          "customTags": [
            "!And scalar",
            "!And mapping",
            "!And sequence",
            "!If scalar",
            "!If mapping",
            "!If sequence",
            "!Not scalar",
            "!Not mapping",
            "!Not sequence",
            "!Equals scalar",
            "!Equals mapping",
            "!Equals sequence",
            "!Or scalar",
            "!Or mapping",
            "!Or sequence",
            "!FindInMap scalar",
            "!FindInMap mapping",
            "!FindInMap sequence",
            "!Base64 scalar",
            "!Base64 mapping",
            "!Base64 sequence",
            "!Cidr scalar",
            "!Cidr mapping",
            "!Cidr sequence",
            "!Ref scalar",
            "!Ref mapping",
            "!Ref sequence",
            "!Sub scalar",
            "!Sub mapping",
            "!Sub sequence",
            "!GetAtt scalar",
            "!GetAtt mapping",
            "!GetAtt sequence",
            "!GetAZs scalar",
            "!GetAZs mapping",
            "!GetAZs sequence",
            "!ImportValue scalar",
            "!ImportValue mapping",
            "!ImportValue sequence",
            "!Select scalar",
            "!Select mapping",
            "!Select sequence",
            "!Split scalar",
            "!Split mapping",
            "!Split sequence",
            "!Join scalar",
            "!Join mapping",
            "!Join sequence",
            "!Condition scalar",
            "!Condition mapping",
            "!Condition sequence"
          ]
        }
      }
    }
  }
```
