# Rust

Zed 原生支持 Rust。

- Tree-sitter：[tree-sitter/tree-sitter-rust](https://github.com/tree-sitter/tree-sitter-rust)
- 语言服务器：[rust-lang/rust-analyzer](https://github.com/rust-lang/rust-analyzer)
- 调试适配器：[CodeLLDB](https://github.com/vadimcn/codelldb)（首选）、[GDB](https://sourceware.org/gdb/)（次选，Apple Silicon 不支持）

## 内联提示

可通过以下配置调整 `rust-analyzer` 的内联提示：

```json [settings]
{
  "lsp": {
    "rust-analyzer": {
      "initialization_options": {
        "inlayHints": {
          "maxLength": null,
          "lifetimeElisionHints": {
            "enable": "skip_trivial",
            "useParameterNames": true
          },
          "closureReturnTypeHints": {
            "enable": "always"
          }
        }
      }
    }
  }
}
```

更多内容请参阅 Rust Analyzer 手册中的[内联提示](https://rust-analyzer.github.io/book/features.html#inlay-hints)。

## target 目录

可在 `initialization_options` 中设置 rust-analyzer 的 target 目录：

```json [settings]
{
  "lsp": {
    "rust-analyzer": {
      "initialization_options": {
        "rust": {
          "analyzerTargetDir": true
        }
      }
    }
  }
}
```

当值为 `true` 时，target 目录为 `target/rust-analyzer`；也可直接指定字符串（如 `"target/analyzer"`）。

## 可执行文件

可以控制 Zed 使用哪个 `rust-analyzer` 可执行文件。

默认情况下 Zed 会在 `$PATH` 查找 `rust-analyzer` 并尝试执行 `rust-analyzer --help`。若执行成功即使用该版本，否则回退到 Zed 自动安装的稳定版。

若想使用预发布版本，可在 `settings.json` 中设置 `pre_release`：

```json [settings]
{
  "lsp": {
    "rust-analyzer": {
      "fetch": {
        "pre_release": true
      }
    }
  }
}
```

若想禁止搜索系统版本，可将 `ignore_system_version` 设为 `true`：

```json [settings]
{
  "lsp": {
    "rust-analyzer": {
      "binary": {
        "ignore_system_version": true
      }
    }
  }
}
```

需要指定自定义位置时，可提供绝对路径及可选参数：

```json [settings]
{
  "lsp": {
    "rust-analyzer": {
      "binary": {
        "path": "/Users/example/bin/rust-analyzer",
        "arguments": []
      }
    }
  }
}
```

## 切换编译目标

若希望 rust-analyzer 在非当前平台（例如 macOS 开发时针对 Windows）生成诊断，可在设置中指定目标三元组：

```json [settings]
{
  "lsp": {
    "rust-analyzer": {
      "initialization_options": {
        "cargo": {
          "target": "x86_64-pc-windows-msvc"
        }
      }
    }
  }
}
```

使用 `rustup target list --installed` 可查看可用的目标三元组。

## LSP 任务

rust-analyzer 提供 LSP 扩展来查询文件相关任务，Zed 默认启用，可通过以下配置调整：

```json [settings]
"lsp": {
  "rust-analyzer": {
    "enable_lsp_tasks": true
  }
}
```

## 手动获取 Cargo 诊断

默认情况下，rust-analyzer 会在保存时执行 `cargo check --workspace --all-targets`（`checkOnSave: true`）。如果将 `checkOnSave` 设为 `false`，仍可通过 Rust 文件中的 `editor: run flycheck` 等命令手动刷新诊断，或者在项目诊断面板运行该命令。

## 进一步配置

Rust Analyzer [手册](https://rust-analyzer.github.io/book/) 详细介绍了各项功能与设置。

### 大型项目与性能

在大型项目中，以下配置的组合可能导致资源消耗过大：

- `rust-analyzer.checkOnSave`（默认 true）
- `rust-analyzer.check.workspace`（默认 true）
- `rust-analyzer.cargo.allTargets`（默认 true）

这意味着每次保存都会执行 `cargo check --workspace --all-targets`，对所有目标（lib、doc、test、bin、bench 等）进行检查。对于大型项目可能较慢。

替代方案：

- 使用 [tasks](../tasks.md)，例如内置的 `cargo check --workspace --all-targets` 任务，并通过终端输出跳转到错误位置。
- 限制或关闭保存时检查。

关闭保存检查后，rust-analyzer 仅返回自身的[内置诊断](https://rust-analyzer.github.io/book/diagnostics.html)。可以参考更多 `rust-analyzer.cargo.*`、`rust-analyzer.check.*`、`rust-analyzer.diagnostics.*` 设置实现更细粒度的控制。

示例配置：

```json [settings]
{
  "lsp": {
    "rust-analyzer": {
      "initialization_options": {
        "diagnostics": {
          "experimental": {
            "enable": true
          }
        },
        // 完全禁用保存时检查
        "checkOnSave": false,
        // 仅检查 lib 目标
        "cargo": {
          "allTargets": false
        },
        // 使用 -p 而非 --workspace
        "check": {
          "workspace": false
        }
      }
    }
  }
}
```

### 多项目工作区

若同一目录下存在多个未在 `[members]` 中声明的 Rust 项目，可通过 `linkedProjects` 指定：

```json [settings]
{
  "lsp": {
    "rust-analyzer": {
      "initialization_options": {
        "linkedProjects": ["./path/to/a/Cargo.toml", "./path/to/b/Cargo.toml"]
      }
    }
  }
}
```

### 代码片段

可使用自定义片段增强补全：

```json [settings]
{
  "lsp": {
    "rust-analyzer": {
      "initialization_options": {
        "completion": {
          "snippets": {
            "custom": {
              "Arc::new": {
                "postfix": "arc",
                "body": ["Arc::new(${receiver})"],
                "requires": "std::sync::Arc",
                "scope": "expr"
              },
              "Some": {
                "postfix": "some",
                "body": ["Some(${receiver})"],
                "scope": "expr"
              },
              "Ok": {
                "postfix": "ok",
                "body": ["Ok(${receiver})"],
                "scope": "expr"
              },
              "Rc::new": {
                "postfix": "rc",
                "body": ["Rc::new(${receiver})"],
                "requires": "std::rc::Rc",
                "scope": "expr"
              },
              "Box::pin": {
                "postfix": "boxpin",
                "body": ["Box::pin(${receiver})"],
                "requires": "std::boxed::Box",
                "scope": "expr"
              },
              "vec!": {
                "postfix": "vec",
                "body": ["vec![${receiver}]"] ,
                "description": "vec![]",
                "scope": "expr"
              }
            }
          }
        }
      }
    }
  }
}
```

## 调试

Zed 开箱即用地支持使用 CodeLLDB 与 GDB 调试 Rust 二进制与测试。执行 {#action debugger::Start}（{#kb debugger::Start}）即可选择内置任务。

若需更细致的控制，可在 `.zed/debug.json` 中添加自定义配置：

- [CodeLLDB 配置文档](https://github.com/vadimcn/codelldb/blob/master/MANUAL.md#starting-a-new-debug-session)
- [GDB 配置文档](https://sourceware.org/gdb/current/onlinedocs/gdb.html/Debugger-Adapter-Protocol.html)

### 构建后调试

```json [debug]
[
  {
    "label": "Build & Debug native binary",
    "build": {
      "command": "cargo",
      "args": ["build"]
    },
    "program": "$ZED_WORKTREE_ROOT/target/debug/binary",
    "sourceLanguages": ["rust"],
    "request": "launch",
    "adapter": "CodeLLDB"
  }
]
```

### 自动推断调试目标

当构建命令为 `cargo build` 或 `cargo test` 时，Zed 可推断输出的可执行文件路径：

```json [debug]
[
  {
    "label": "Build & Debug native binary",
    "adapter": "CodeLLDB",
    "build": {
      "command": "cargo",
      "args": ["build"]
    },
    "sourceLanguages": ["rust"]
  }
]
```
