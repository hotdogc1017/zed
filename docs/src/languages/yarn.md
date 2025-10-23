# Yarn

[Yarn](https://yarnpkg.com/) 是一款功能强大的包管理器，可为 JavaScript 等语言提供确定性的依赖树、离线支持与更高安全性，从而提升依赖管理效率。

## 安装步骤

1. 执行 `yarn dlx @yarnpkg/sdks base` 生成 `.yarn/sdks` 目录。
2. 在 [LSP 初始化选项](../configuring-zed.md#lsp) 中，将所用语言服务器（如 VTSLS）的 TypeScript SDK 设置为 `.yarn/sdks/typescript/lib`。具体字段取决于所用语言服务器，例如 VTSLS 需配置 [`typescript.tsdk`](https://github.com/yioneko/vtsls/blob/6adfb5d3889ad4b82c5e238446b27ae3ee1e3767/packages/service/configuration.schema.json#L5)。
3. 完成后，即可使用跳转定义、自动补全、悬停文档等语言服务器功能。
