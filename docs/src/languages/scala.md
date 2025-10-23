# Scala

Zed 对 Scala 的语言支持由社区维护的 [Scala 扩展](https://github.com/scalameta/metals-zed) 提供。
如需反馈问题，请访问：[https://github.com/scalameta/metals-zed/issues](https://github.com/scalameta/metals-zed/issues)

- Tree-sitter：[tree-sitter/tree-sitter-scala](https://github.com/tree-sitter/tree-sitter-scala)
- 语言服务器：[scalameta/metals](https://github.com/scalameta/metals)

## 安装

- 使用 Coursier 安装 Scala：`cs setup`（https://www.scala-lang.org/download/）
  - 例如：`brew install coursier/formulas/coursier && cs setup`
- REPL（Almond）安装指南：https://almond.sh/docs/quick-start-install
  - `brew install --cask temurin`（Eclipse 基金会官方 OpenJDK）
  - `brew install coursier/formulas/coursier && cs setup`
  - `coursier launch --use-bootstrap almond -- --install`

## 配置

可以通过以下文件控制 Metals 语言服务器的行为：

- `.scalafix.conf`——参阅 [Scalafix 配置](https://scalacenter.github.io/scalafix/docs/users/configuration.html)
- `.scalafmt.conf`——参阅 [Scalafmt 配置](https://scalameta.org/scalafmt/docs/configuration.html)

可将这些文件放在项目根目录，或在 Metals 配置中指定路径。更多内容请参阅 [Metals 用户配置文档](https://scalameta.org/metals/docs/editors/user-configuration)。

<!--
待补充：Metals 在 Zed settings.json 中的配置示例，如 metals.{javaHome,excludedPackages,customProjectRoot} 等。
-->
