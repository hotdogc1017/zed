# 通配符（Globs）

Zed 支持 [Glob](<https://en.wikipedia.org/wiki/Glob_(programming)>) 模式，即 Unix Shell 常用的路径通配符（如 `*.md`、`docs/src/**/*.md`）。它与 [正则表达式](https://en.wikipedia.org/wiki/Regular_expression) 类似但不相同，在 Zed 中主要用于匹配文件名。

## 实现口味

Zed 使用两个 Rust crate 处理通配符：

- [`ignore`](https://docs.rs/ignore/latest/ignore/)：解析 `.gitignore` 文件
- [`glob`](https://docs.rs/glob/latest/glob/)：用于 Zed 内部的路径匹配

简单模式跨环境通常兼容，但高级语法（字符集、排除、`**` 等）差异较大。本文介绍 Zed 中 `glob` crate 的行为。其他环境请参考[参考资料](#参考资料)。`glob` crate 完全由 Rust 实现，不依赖平台的 `glob/fnmatch`，因此跨平台行为一致。

## 基础

Glob 模式用于匹配文件名或完整路径。例如在“全局搜索” {#kb project_search::ToggleFocus} 中点击漏斗按钮（或 {#kb project_search::ToggleFilters}）即可在 Include/Exclude 文本框里输入通配符过滤路径。

可用特殊字符如下：

| 符号     | 含义                                       |
| -------- | ------------------------------------------ |
| `?`      | 匹配任意单个字符                          |
| `*`      | 匹配任意长度的字符序列（可为空）          |
| `**`     | 匹配当前目录及任意子目录                  |
| `[abc]`  | 匹配方括号内任一字符                      |
| `[a-z]`  | 匹配字符范围（按 Unicode 排序）           |
| `[!...]` | 匹配不在方括号中的字符                    |

注意：

1. 不支持 Shell 样式的 `{a,b,c}` 花括号展开。
2. 匹配字面量 `-` 时需放在首位 `[-abc]` 或末尾 `[abc-]`。
3. 匹配 `[` 可写 `[[]` 或放在字符集首位 `[[abc]`。
4. 匹配 `]` 可写 `[]]` 或放在末尾 `[abc]]`。

## 示例

### 匹配扩展名

若只搜索 Markdown 文件，可在 Include 中输入 `*.md`。

### 大小写敏感

Glob 在 Zed 中区分大小写，即使在大小写不敏感的文件系统上，`*.c` 也不会匹配 `main.C`。可以使用字符集 `*.[cC]` 同时匹配大小写。

### 匹配目录

若在 Zed 仓库中寻找配置语言服务器的示例，可在 Include 输入 `docs/**/*.md`，仅匹配 `docs` 目录及其子目录下的 `.md` 文件。若只需语言文档，可用更精确的 `docs/src/languages/*.md`。

### 隐式通配

项目搜索中的 Include/Exclude 会自动在两侧补上 `**`。例如在 Exclude 输入 `license`，实际匹配 `**license**`，因此 `license.*`、`*.license`、`license` 目录等都会被排除。这使得快速筛选 `*.ts` 无需每次输入 `**/*.ts`。

若在配置中使用 [`file_types`](./configuring-zed.md#file-types) 限定路径，则需显式写出通配符。例如将 `templates` 目录下的 `.html` 识别为 Jinja2 模板：

```json [settings]
{
  "file_types": {
    "C++": ["[cC]"],
    "Jinja2": ["**/templates/*.html"]
  }
}
```

## 参考资料

在其他环境使用通配符时，请查阅相应文档：

- [macOS fnmatch](https://developer.apple.com/library/archive/documentation/System/Conceptual/ManPages_iPhoneOS/man3/fnmatch.3.html)
- [Linux fnmatch](https://www.gnu.org/software/libc/manual/html_node/Wildcard-Matching.html)
- [POSIX fnmatch](https://pubs.opengroup.org/onlinepubs/9699919799/functions/fnmatch.html)
- [node-glob](https://github.com/isaacs/node-glob)
- [Python glob](https://docs.python.org/3/library/glob.html)
- [Go filepath.Match](https://pkg.go.dev/path/filepath#Match)
- [gitignore 模式](https://git-scm.com/docs/gitignore)
- [PowerShell 通配符](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_wildcards)
