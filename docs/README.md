# Zed 文档

欢迎来到 Zed 的文档。

这些文档在推送到 `main` 分支时会自动构建并发布到 [https://zed.dev/docs](https://zed.dev/docs)。

要在本地预览文档，您需要安装 [mdBook](https://rust-lang.github.io/mdBook/) (`cargo install mdbook@0.4.40`)，然后运行：

```sh
mdbook serve docs
```

请注意上面的版本号。由于未知原因，截至 2025-04-23，运行 0.4.48 版本会导致奇怪的 URL 行为，从而破坏文档。

在提交之前，请验证文档是否按照 Prettier 期望的格式进行格式化：

```
cd docs && pnpm dlx prettier@3.5.0 . --write && cd ..
```

## 预处理器

我们有一个自定义的 mdbook 预处理器，用于与我们的 crate 交互 (`crates/docs_preprocessor`)。

如果出于某种原因需要绕过文档预处理器，您可以从 `book.toml` 中注释掉 `[preprocessor.zed_docs_preprocessor]`。

## 图片和视频

要向文档添加图片或视频，请将它们上传到其他位置（例如 zed.dev、GitHub 的资源存储），然后从文档中链接到它们。

将二进制资源（如图片）放入 Git 仓库会随着时间的推移使仓库大小膨胀。

## 内部说明：

- 我们有一个名为 `docs-proxy` 的 Cloudflare 路由器，它拦截对 `zed.dev/docs` 的请求并将其转发到 "docs" Cloudflare Pages 项目。
- CI 在每次推送到 `main` 时从 `.github/workflows/deploy_docs.yml` 上传新版本到 Pages 项目。

### 目录

目录文件 (`theme/page-toc.js` 和 `theme/page-doc.css`) 最初由 [`mdbook-pagetoc`](https://crates.io/crates/mdbook-pagetoc) 生成。

由于这个预处理器所做的只是生成静态资源，一旦它们生成后，我们就不需要保留它。

## 引用快捷键和操作

引用快捷键或操作时，请使用以下格式：

### 快捷键：

`{#kb scope::Action}` - 例如，`{#kb zed::OpenSettings}`。

这将输出一个代码元素，如：`<code>Cmd+,|Ctrl+,</code>`。然后我们使用客户端插件根据用户平台显示实际的快捷键。

通过使用操作名称，我们可以确保快捷键始终是最新的，而不是硬编码快捷键。

### 操作：

`{#action scope::Action}` - 例如，`{#action zed::OpenSettings}`。

这将渲染操作名称的人类可读版本，例如 "zed: 打开设置"，并允许我们实现诸如悬停时显示额外上下文等功能。

### 创建新模板

模板只是修改文档页面源文件的函数（通常通过正则表达式匹配和替换）。您可以参考 `crates/docs_preprocessor/src/main.rs` 中快捷键和操作的模板化方式，了解如何创建新模板。

### 参考

- 模板特性：crates/docs_preprocessor/src/templates.rs
- 示例模板：crates/docs_preprocessor/src/templates/keybinding.rs
- 客户端插件：docs/theme/plugins.js

## 后处理器

后处理器作为 `docs_preprocessor` 的子命令实现，它包装内置的 `html` 渲染器并对 `html` 文件应用后处理，以支持页面特定的标题和元描述值。

语法示例可以在 `git.md` 中找到，如下所示：

```md
---
title: 此页面的更详细标题
description: 页面特定描述
---

# 编辑器
```

上述内容将被转换为（删除不相关标签）：

```html
<head>
  <title>编辑器 | 此页面的更详细标题</title>
  <meta name="description" contents="页面特定描述" />
</head>
<body>
  <h1>编辑器</h1>
</body>
```

如果未提供前置元数据，或者未提供一个或两个键，标题和描述将分别基于 `book.toml` 中的 `default-title` 和 `default-description` 键设置。

### 实现细节

不幸的是，`mdbook` 不支持像预处理器那样的后处理，并且每本书只能定义一个要放入元标签的描述。因此，为了应用后处理（需要修改 html head 标签），全局书籍描述被设置为标记值 `#description#`，并且 html 渲染器被替换为 `docs_preprocessor` 的子命令，该子命令包装内置的 `html` 渲染器并对 `html` 文件应用后处理，如果有前置元数据，则替换标记值和 `<title>(.*)</title>`。

### 已知限制

前置元数据解析极其简单，这避免了需要引入额外依赖项或实现完整的 yaml 解析。

- 不支持双引号和多行值，即键和值必须完全在同一行上，值周围没有双引号。

以下内容将不起作用：

```md
---
title: 某些
  多行
  标题
---
```

以下内容也不起作用：

```md
---
title: "某些标题"
---
```

- 前置元数据必须在文件顶部，前面只有空白字符

- 标题和描述的内容不会被 html 转义。它们应该是简单的 ascii 文本，没有 unicode 或表情符号字符
