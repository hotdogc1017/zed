# Ruby

Zed 可通过 [Ruby 扩展](https://github.com/zed-extensions/ruby) 提供 Ruby 支持。

- Tree-sitter：
  - [tree-sitter-ruby](https://github.com/tree-sitter/tree-sitter-ruby)
  - [tree-sitter-embedded-template](https://github.com/tree-sitter/tree-sitter-embedded-template)
- 语言服务器：
  - [ruby-lsp](https://github.com/Shopify/ruby-lsp)
  - [solargraph](https://github.com/castwide/solargraph)
  - [rubocop](https://github.com/rubocop/rubocop)
  - [Herb](https://herb-tools.dev)
- 调试适配器：[`rdbg`](https://github.com/ruby/debug)

该扩展同样支持 ERB 文件。

## 语言服务器

Ruby 生态有多种语言服务器，Zed 主要支持以下两款：

- [solargraph](https://github.com/castwide/solargraph)
- [ruby-lsp](https://github.com/Shopify/ruby-lsp)

两者都提供补全、诊断、代码操作等重叠功能，需任选其一，不能同时启用。

此外，Zed 还支持：

- [rubocop](https://github.com/rubocop/rubocop)：Ruby 静态分析与 Lint 工具，Zed 将其作为语言服务器使用，以补充 solargraph 与 ruby-lsp。
- [sorbet](https://sorbet.org/)：Ruby 静态类型检查器，采用渐进类型系统。
- [steep](https://github.com/soutaro/steep)：使用 RBS 的 Ruby 静态类型检查器。
- [Herb](https://herb-tools.dev)：用于 ERB 文件的语言服务器。

配置语言服务器时，建议通过命令 `dev: Open Language Server Logs` 打开 LSP 日志窗口，选择对应实例即可查看日志。

## 语言服务器激活规则

[Ruby 扩展](https://github.com/zed-extensions/ruby) 同时支持 `solargraph` 与 `ruby-lsp`。所有 Ruby 相关语言服务器（`solargraph`、`ruby-lsp`、`rubocop`、`sorbet`、`steep`）遵循以下激活顺序：

1. 若项目 `Gemfile` 中声明了对应语言服务器，则通过 `bundle exec` 启动。
2. 否则在系统 `PATH` 中寻找可执行文件。
3. 若仍未找到，扩展会自动将其安装为全局 gem（注意：不会安装到当前 gemset）。

若想跳过第 1 步，强制使用系统可执行文件，可在设置中将 `use_bundler` 设为 `false`：

```json [settings]
{
  "lsp": {
    "<SERVER_NAME>": {
      "settings": {
        "use_bundler": false
      }
    }
  }
}
```

### 使用 `solargraph`

扩展默认启用 `solargraph`。

### 使用 `ruby-lsp`

若要切换到 `ruby-lsp`，在 `settings.json` 中添加：

```json [settings]
{
  "languages": {
    "Ruby": {
      "language_servers": ["ruby-lsp", "!solargraph", "!rubocop", "..."]
    }
  }
}
```

此配置会禁用 `solargraph` 与 `rubocop`，启用 `ruby-lsp`。

### 使用 `rubocop`

`rubocop` 可用于检测代码问题并尝试修复。若要启用，可在 `settings.json` 中添加：

```json [settings]
{
  "languages": {
    "Ruby": {
      "language_servers": ["ruby-lsp", "rubocop", "!solargraph", "..."]
    }
  }
}
```

或者，启用 `solargraph` + `rubocop`，禁用 `ruby-lsp`：

```json [settings]
{
  "languages": {
    "Ruby": {
      "language_servers": ["solargraph", "rubocop", "!ruby-lsp", "..."]
    }
  }
}
```

## 配置 `solargraph`

Solargraph 默认关闭格式化与诊断，可在 `settings.json` 启用：

```json [settings]
{
  "lsp": {
    "solargraph": {
      "initialization_options": {
        "diagnostics": true,
        "formatting": true
      }
    }
  }
}
```

其配置文件为项目根目录下的 `.solargraph.yml`，详情请参阅 [官方文档](https://solargraph.org/guides/configuration)。

## 配置 `ruby-lsp`

可通过 `initialization_options` 向 Ruby LSP 传递配置，例如：

```json [settings]
{
  "languages": {
    "Ruby": {
      "language_servers": ["ruby-lsp", "!solargraph", "..."]
    }
  },
  "lsp": {
    "ruby-lsp": {
      "initialization_options": {
        "enabledFeatures": {
          // "someFeature": false
        }
      }
    }
  }
}
```

LSP 的 `settings` 与 `initialization_options` 也可按项目单独配置。例如让某个项目使用 [standardrb/standard](https://github.com/standardrb/standard) 作为格式化与 Lint，可在仓库内的 `.zed/settings.json` 写入：

```json [settings]
{
  "lsp": {
    "ruby-lsp": {
      "initialization_options": {
        "formatter": "standard",
        "linters": ["standard"]
      }
    }
  }
}
```

## 配置 `rubocop` LSP

Rubocop 默认禁用“不安全”的自动修复，可在 `settings.json` 中启用：

```json [settings]
{
  "languages": {
    "Ruby": {
      // 使用 ruby-lsp 作为主语言服务器，rubocop 作为辅助
      "language_servers": ["ruby-lsp", "rubocop", "!solargraph", "..."]
    }
  },
  "lsp": {
    "rubocop": {
      "initialization_options": {
        "safeAutocorrect": false
      }
    },
    "ruby-lsp": {
      "initialization_options": {
        "enabledFeatures": {
          "diagnostics": false
        }
      }
    }
  }
}
```

## 配置 Sorbet

[Sorbet](https://sorbet.org/) 是广受欢迎的 Ruby 静态类型检查器，也提供语言服务器。

在 `settings.json` 中把 `"sorbet"` 加入 Ruby 的 `language_servers` 列表即可。若将 Sorbet 作为主语言服务器，建议禁用其他 LSP；或者仅与其它 LSP 搭配以补充类型检查。

```json [settings]
{
  "languages": {
    "Ruby": {
      "language_servers": [
        "ruby-lsp",
        "sorbet",
        "!rubocop",
        "!solargraph",
        "..."
      ]
    }
  }
}
```

有关 Sorbet 的安装、项目配置、使用方式，请参阅[官方文档](https://sorbet.org/docs/overview)。

## 配置 Steep

[Steep](https://github.com/soutaro/steep) 使用 RBS 文件来定义类型。

同样在 `settings.json` 中将 `"steep"` 加入 Ruby 的 `language_servers`：

```json [settings]
{
  "languages": {
    "Ruby": {
      "language_servers": [
        "ruby-lsp",
        "steep",
        "!solargraph",
        "!rubocop",
        "..."
      ]
    }
  }
}
```

## 配置 Herb

`Herb` 默认对 `HTML+ERB` 语言启用。

## Ruby 中使用 Tailwind CSS 语言服务器

可在 Ruby 与 ERB 文件中启用 [Tailwind CSS 语言服务器](https://github.com/tailwindlabs/tailwindcss-intellisense/tree/HEAD/packages/tailwindcss-language-server#readme)。

需要在 `settings.json` 中告知语言服务器如何在 Ruby/ERB 中匹配类名：

```json [settings]
{
  "languages": {
    "Ruby": {
      "language_servers": ["tailwindcss-language-server", "..."]
    }
  },
  "lsp": {
    "tailwindcss-language-server": {
      "settings": {
        "experimental": {
          "classRegex": ["\\bclass:\\s*['\"][^'\"]*['\"]"]
        }
      }
    }
  }
}
```

配置后，可在 ERB 中的 HTML 属性，以及 Ruby/ERB 字符串中 `class:` 键后获得补全：

```rb
# Ruby 文件
def method
  div(class: "pl-2 <在此补全>") do
    p(class: "mt-2 <在此补全>") { "Hello World" }
  end
end

# ERB 文件
<%= link_to "Hello", "/hello", class: "pl-2 <在此补全>" %>
<a href="/hello" class="pl-2 <在此补全>">Hello</a>
```

## 运行测试

可在本地 `.zed/tasks.json` 中创建自定义任务，以运行不同框架的测试（Minitest、RSpec、quickdraw、tldr 等）。以下是示例：

### Rails + Minitest

```json [tasks]
[
  {
    "label": "test $ZED_RELATIVE_FILE -n /$ZED_CUSTOM_RUBY_TEST_NAME/",
    "command": "bin/rails",
    "args": [
      "test",
      "$ZED_RELATIVE_FILE",
      "-n",
      "\"$ZED_CUSTOM_RUBY_TEST_NAME\""
    ],
    "cwd": "$ZED_WORKTREE_ROOT",
    "tags": ["ruby-test"]
  }
]
```

### Minitest

纯 Minitest 不支持按行号运行，只能按名称运行，因此使用 `$ZED_CUSTOM_RUBY_TEST_NAME`：

```json [tasks]
[
  {
    "label": "-Itest $ZED_RELATIVE_FILE -n /$ZED_CUSTOM_RUBY_TEST_NAME/",
    "command": "bundle",
    "args": [
      "exec",
      "ruby",
      "-Itest",
      "$ZED_RELATIVE_FILE",
      "-n",
      "\"$ZED_CUSTOM_RUBY_TEST_NAME\""
    ],
    "cwd": "$ZED_WORKTREE_ROOT",
    "tags": ["ruby-test"]
  }
]
```

### RSpec

```json [tasks]
[
  {
    "label": "test $ZED_RELATIVE_FILE:$ZED_ROW",
    "command": "bundle",
    "args": ["exec", "rspec", "\"$ZED_RELATIVE_FILE:$ZED_ROW\""],
    "cwd": "$ZED_WORKTREE_ROOT",
    "tags": ["ruby-test"]
  }
]
```

其他测试框架（如 `quickdraw`、`tldr`）可参照类似写法。

## 调试

Ruby 扩展提供调试适配器（名称 `rdbg`），底层基于 [`debug`](https://github.com/ruby/debug) gem，并沿用[语言服务器激活规则](#语言服务器激活规则)。

### 示例

#### 调试 Ruby 脚本

```json [debug]
[
  {
    "label": "Debug current file",
    "adapter": "rdbg",
    "request": "launch",
    "script": "$ZED_FILE",
    "cwd": "$ZED_WORKTREE_ROOT"
  }
]
```

#### 调试 Rails 服务器

```json [debug]
[
  {
    "label": "Debug Rails server",
    "adapter": "rdbg",
    "request": "launch",
    "command": "./bin/rails",
    "args": ["server"],
    "cwd": "$ZED_WORKTREE_ROOT",
    "env": {
      "RUBY_DEBUG_OPEN": "true"
    }
  }
]
```

## 格式化器

### `erb-formatter`

若需格式化 ERB 模板，可使用 [`erb-formatter`](https://rubygems.org/gems/erb-formatter) gem：

```json [settings]
{
  "HTML+ERB": {
    "formatter": {
      "external": {
        "command": "erb-formatter",
        "arguments": ["--stdin-filename", "{buffer_path}"]
      }
    }
  }
}
```
