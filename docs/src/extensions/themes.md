# 主题

扩展中的 `themes` 目录应包含一个或多个主题文件。

每个主题文件应遵守在 [`https://zed.dev/schema/themes/v0.2.0.json`](https://zed.dev/schema/themes/v0.2.0.json) 指定的 JSON 模式。

有关创建主题的更多详细信息，请参阅[此博客文章](https://zed.dev/blog/user-themes-now-in-preview)。

## 主题 JSON 结构

Zed 主题的结构在 [Zed 主题 JSON 模式](https://zed.dev/schema/themes/v0.2.0.json) 中定义。

Zed 主题由主题族对象组成，包括：

- `name`：主题族的名称
- `author`：主题族作者的名称
- `themes`：属于主题族的主题数组

主题对象的核心组件包括：

1. 主题元数据：

   - `name`：主题的名称
   - `appearance`："light" 或 "dark"

2. `style` 下的样式属性，例如：

   - `background`：主背景颜色
   - `foreground`：主文本颜色
   - `accent`：用于高亮和强调的重音颜色

3. 语法高亮：

   - `syntax`：包含各种语法元素（例如，关键字、字符串、注释）颜色定义的对象

4. UI 元素：

   - 各种 UI 组件的颜色，例如：
     - `element.background`：UI 元素的背景颜色
     - `border`：不同状态（正常、聚焦、选中）的边框颜色
     - `text`：不同状态（正常、静音、重音）的文本颜色

5. 编辑器特定颜色：

   - 与编辑器相关的元素的颜色，例如：
     - `editor.background`：编辑器背景颜色
     - `editor.gutter`：装订线颜色
     - `editor.line_number`：行号颜色

6. 终端颜色：
   - 集成终端的 ANSI 颜色定义

我们建议查看我们的[现有主题](https://github.com/zed-industries/zed/tree/main/assets/themes) 以更全面地了解可以设置样式的元素。
