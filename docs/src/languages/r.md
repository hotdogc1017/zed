# R

Zed 上有多个 R 相关扩展可供选择：

- [ocsmit/zed-r](https://github.com/ocsmit/zed-r)
  - Tree-sitter：[r-lib/tree-sitter-r](https://github.com/r-lib/tree-sitter-r)
  - 语言服务器：[REditorSupport/languageserver](https://github.com/REditorSupport/languageserver)
- [posit-dev/air](https://github.com/posit-dev/air/tree/main/editors/zed)
  - 语言服务器：[posit-dev/air](https://github.com/posit-dev/air)

## 安装

1. [下载安装 R](https://cloud.r-project.org/)。
2. 安装 `languageserver` 与 `lintr` 两个 R 包：

```R
install.packages("languageserver")
install.packages("lintr")
```

3. 通过 Zed 扩展管理器安装 [ocsmit/zed-r](https://github.com/ocsmit/zed-r)。

以 macOS 为例：

```sh
brew install --cask r
Rscript --version
Rscript -e 'options(repos = "https://cran.rstudio.com/"); install.packages("languageserver")'
Rscript -e 'options(repos = "https://cran.rstudio.com/"); install.packages("lintr")'
Rscript -e 'packageVersion("languageserver")'
Rscript -e 'packageVersion("lintr")'
```

## 配置

### Lint

`REditorSupport/languageserver` 内置 [r-lib/lintr](https://github.com/r-lib/lintr)。可在项目内（或主目录）创建 `.lintr` 文件自定义规则：

```r
linters: linters_with_defaults(
    line_length_linter(120),
    commented_code_linter = NULL
  )
exclusions: list(
    "inst/doc/creating_linters.R" = 1,
    "inst/example/bad.R",
    "tests/testthat/exclusions-test"
  )
```

若想完全禁用 lint，可使用：

```r
exclusions: list(".")
```

完整配置请参阅 [Using lintr](https://lintr.r-lib.org/articles/lintr.html)。

### 格式化

`REditorSupport/languageserver` 同样集成 [r-lib/styler](https://github.com/r-lib/styler) 作为 formatter。关于定制化可查阅 [Customizing Styler](https://cran.r-project.org/web/packages/styler/vignettes/customizing_styler.html)。

<!--
待补充：语言服务器设置示例

待补充：R REPL 相关文档
-->
