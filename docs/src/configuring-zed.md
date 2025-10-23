# 配置 Zed

Zed 的核心理念是“随你所欲”。我们提供的默认体验希望能让大多数人开箱即用，但真正的力量来自于根据个人或团队的习惯进行调校。本页汇总了所有可用的设置；除此之外，你也可以继续阅读其他文档来个性化你的 [主题](./themes.md)、[快捷键](./key-bindings.md)、[任务](./tasks.md) 或安装新的 [扩展](https://github.com/zed-industries/extensions)。

## 设置文件

> **提示**
> - {#kb zed::OpenSettings}：打开全局设置。
> - {#action zed::OpenProjectSettings}：在当前项目创建并打开 `.zed/settings.json`。

全局设置默认为 `~/.config/zed/settings.json`，如果你在 Linux 上设置了 `XDG_CONFIG_HOME`，则会位于 `$XDG_CONFIG_HOME/zed/settings.json`。项目级设置存放在仓库根目录的 `.zed/settings.json` 中。如果需要，你还可以为子目录继续添加更局部的设置文件。

设置文件的解析规则是“全局 → 项目 → 子目录”依次覆盖。标量会被覆盖，映射类型会合并，数组会整体替换，因此若想在数组中追加新值，需要把默认值也带上。并非所有选项都允许本地覆写——只有会影响编辑器/语言工具行为的项目才可以，例如 `tab_size`、`formatter` 等；`theme`、`vim_mode` 这类全局 UI 行为只能放在用户设置里。

所有设置文件都是“带注释的 JSON”（JSONC），允许使用 `//` 添加行内注释。

## 默认设置

你可以通过命令 {#action zed::OpenDefaultSettings} 查看当前版本的完整默认配置。语言扩展通常也会为自己的语言服务器附带一份默认设置。

---
# 设置项清单

以下条目按字母顺序排列。每一小节都会给出：描述、键名、默认值以及可选项。

## Active Pane Modifiers（激活面板样式）

- 描述：控制当前激活面板的视觉样式。
- 键名：`active_pane_modifiers`
- 默认值：

```json [settings]
{
  "active_pane_modifiers": {
    "border_size": 0.0,
    "inactive_opacity": 1.0
  }
}
```

### Border Size

- 描述：激活面板的边框宽度（向内绘制）。0 表示不显示边框。
- 键名：`border_size`
- 默认值：`0.0`
- 取值：大于等于 0 的浮点数。

### Inactive Opacity

- 描述：非激活面板的透明度。0 完全不可见，1 与激活面板一致，超出区间会被限制在 `[0.0, 1.0]`。
- 键名：`inactive_opacity`
- 默认值：`1.0`
- 取值：浮点数。

## Agent Font Size（AI 面板字体大小）

- 描述：AI 面板的字体大小，未设置时继承 UI 字体。
- 键名：`agent_font_size`
- 默认值：`null`
- 取值：`6`~`100` 间的整数，单位为像素。

## Allow Rewrap（换行命令适用范围）

- 描述：控制 {#action editor::Rewrap} 在当前语言作用域内的行为。
- 键名：`allow_rewrap`
- 默认值：`"in_comments"`
- 取值：
  1. `"in_comments"`：仅在注释中重新换行。
  2. `"in_selections"`：仅在选区内可用。
  3. `"anywhere"`：不做限制。

> 注：在 Vim 模式下该设置无效，因为默认已允许任意位置换行。

## Auto Indent（自动缩进）

- 描述：输入时是否根据上下文自动调整缩进，可以按语言分别设置。
- 键名：`auto_indent`
- 默认值：`true`
- 取值：布尔值。

## Auto Indent On Paste（粘贴时自动缩进）

- 描述：粘贴内容时是否根据上下文调整缩进。
- 键名：`auto_indent_on_paste`
- 默认值：`true`
- 取值：布尔值。

## Auto Install Extensions（自动安装扩展）

- 描述：声明哪些扩展应自动安装或永不安装。
- 键名：`auto_install_extensions`
- 默认值：`{ "html": true }`
- 取值：键为扩展名，值为 `true`/`false`。

示例：

```json [settings]
{
  "auto_install_extensions": {
    "html": true,
    "dockerfile": true,
    "docker-compose": false
  }
}
```

你可以在扩展安装目录（见 [安装扩展](./extensions/installing-extensions.md#installation-location)）中查看现有扩展的名称。

## Autosave（自动保存）

- 描述：自动保存缓冲区的时机。
- 键名：`autosave`
- 默认值：`"off"`
- 取值：
  1. `"off"`：关闭自动保存。
  2. `"on_focus_change"`：焦点切换时保存。
  3. `"on_window_change"`：激活窗口变化时保存。
  4. `"after_delay"`：在指定的空闲时间后保存：

```json [settings]
{
  "autosave": {
    "after_delay": {
      "milliseconds": 1000
    }
  }
}
```

> 即便设置了延迟，关闭未保存的标签页时仍会触发保存。

## Autoscroll On Clicks（点击边缘自动滚动）

- 描述：点击可视区域边缘附近时是否自动滚动。
- 键名：`autoscroll_on_clicks`
- 默认值：`false`
- 取值：布尔值。

## Auto Signature Help（自动显示函数签名）

- 描述：光标位于括号内时是否自动显示函数签名。
- 键名：`auto_signature_help`
- 默认值：`false`
- 取值：布尔值。

### Show Signature Help After Edits

- 描述：补全或自动补全括号后是否展示签名帮助；若启用 `auto_signature_help`，该选项视作启用。
- 键名：`show_signature_help_after_edits`
- 默认值：`false`
- 取值：布尔值。

## Auto Update（自动检查更新）

- 描述：是否自动检查更新。
- 键名：`auto_update`
- 默认值：`true`
- 取值：布尔值。

## Base Keymap（基础键位方案）

- 描述：选择内置基础键位方案，可在其上叠加用户自定义绑定。
- 键名：`base_keymap`
- 默认值：`"VSCode"`
- 可选值：`VSCode`、`Atom`、`JetBrains`、`None`、`SublimeText`、`TextMate`。

示例：

```json [settings]
{
  "base_keymap": "Atom"
}
```

## Bottom Dock Layout（底部停靠布局）

- 描述：底部停靠栏与左右侧栏之间的布局关系。
- 键名：`bottom_dock_layout`
- 默认值：`"contained"`
- 可选值：
  1. `"contained"`：底栏被左右栏包裹，左右栏占满窗口高度。
  2. `"full"`：底栏占满宽度，左右栏纵向被截断。
  3. `"left_aligned"`：底栏贴左，压缩左侧栏。
  4. `"right_aligned"`：底栏贴右，压缩右侧栏。

## Buffer Font Family / Features / Fallbacks / Size / Weight / Line Height（编辑器字体设置）

这些键分别控制编辑器正文的字体族、OpenType 特性、备用字体、字号、字重与行高：

- `buffer_font_family`：默认为 `.ZedMono`（当前指向 Lilex）。可指定系统已安装的字体名称。
- `buffer_font_features`（仅 macOS/Windows）：如需禁用连字：

```json [settings]
{
  "buffer_font_features": {
    "calt": false
  }
}
```

- `buffer_font_fallbacks`（仅 macOS/Windows）：

```json [settings]
{
  "buffer_font_fallbacks": ["Nerd Font"]
}
```

- `buffer_font_size`：默认 `15`，取值 6~100（像素）。
- `buffer_font_weight`：默认 `400`，取值 100~900。
- `buffer_line_height`：`"standard"`、`"comfortable"` 或 `{ "custom": 浮点数 }`。

## Centered Layout（居中布局）

- 描述：聚焦布局的边距设置。
- 键名：`centered_layout`
- 默认值：

```json [settings]
"centered_layout": {
  "left_padding": 0.2,
  "right_padding": 0.2
}
```

取值范围 `0`~`0.4`，表示居中面板左右留白的比例。

## Close On File Delete（文件删除时关闭标签）

- 描述：文件在磁盘上被删除时是否自动关闭对应标签页。
- 键名：`close_on_file_delete`
- 默认值：`false`
- 取值：布尔值。未保存的缓冲区不会被自动关闭。

## Completions（补全设置）

- 描述：补全系统的行为。
- 键名：`completions`
- 默认值：

```json [settings]
{
  "completions": {
    "words": "fallback",
    "words_min_length": 3,
    "lsp": true,
    "lsp_fetch_timeout_ms": 0,
    "lsp_insert_mode": "replace_suffix"
  }
}
```

详细选项：

- `words`：`enabled`（总是查询文档词汇）、`fallback`（默认，仅在 LSP 失败时使用）、`disabled`（禁用）。
- `words_min_length`：自动触发词补全所需的最小字符数。
- `lsp`：是否请求语言服务器补全。
- `lsp_fetch_timeout_ms`：LSP 等待超时时间，0 表示无限等待。
- `lsp_insert_mode`：插入模式，`insert`、`replace`、`replace_subsequence`、`replace_suffix`。

## Confirm Quit（退出确认）

- 描述：退出应用前是否弹出确认。
- 键名：`confirm_quit`
- 默认值：`false`
- 取值：布尔值。

## Current Line Highlight / Selection Highlight / Rounded Selection（高亮与选区）

- `current_line_highlight`：当前行高亮方式，`"none"`、`"gutter"`、`"line"`、`"all"`（默认）。
- `selection_highlight`：是否高亮所有匹配选中文本，默认 `true`。
- `rounded_selection`：选区是否使用圆角，默认 `true`。

## Cursor Blink / Shape（光标闪烁与形状）

- `cursor_blink`：光标是否闪烁，默认 `true`。
- `cursor_shape`：`"bar"`（默认）、`"block"`、`"underline"`、`"hollow"`。

## Diagnostics / Diagnostics Max Severity（诊断配置）

- `diagnostics`：诊断总配置，默认：

```json [settings]
{
  "diagnostics": {
    "include_warnings": true,
    "inline": {
      "enabled": false
    },
    "update_with_cursor": false,
    "primary_only": false,
    "use_rendered": false
  }
}
```

  - `inline.enabled`：启用内联诊断。
  - `inline.update_debounce_ms`：刷新防抖，默认 150。
  - `inline.padding`：行尾到诊断文本的空隙。
  - `inline.min_column`：强制对齐的起始列。
  - `inline.max_severity`：限制最高严重等级。

- `diagnostics_max_severity`：过滤显示的诊断级别，取值 `"all"`、`"error"`、`"warning"`、`"info"`、`"hint"`。

## Direnv Integration (`load_direnv`)（Direnv 集成）

- 描述：通过 [direnv](https://direnv.net) 注入环境变量，用于发现语言服务器、运行任务。
- 默认值：`"direct"`
- 可选：
  1. `"shell_hook"`：依赖 shell hook（支持 POSIX shell、fish）。
  2. `"direct"`：直接执行 `direnv export json`。

## Double Click In Multibuffer（多缓冲双击行为）

- 描述：多缓冲视图中双击片段时的行为。
- 可选 `"select"`（默认，选中文本）或 `"open"`（在新标签打开片段，按 `Alt`+双击可以临时选中）。

## Drop Target Size（拖放触发区域）

- 描述：拖放打开分屏时的触发区域大小，范围 `0`~`0.5`，默认 `0.2`。

## Edit Predictions（编辑预测）

- 描述：编辑预测的禁用规则。
- 默认值：

```json [settings]
{
  "edit_predictions": {
    "disabled_globs": [
      "**/.env*",
      "**/*.pem",
      "**/*.key",
      "**/*.cert",
      "**/*.crt",
      "**/.dev.vars",
      "**/secrets.yml"
    ]
  }
}
```

- `edit_predictions_disabled_in`：按 scope 禁用，例如 `"comment"`、`"string"`。

## Enable Language Server（启用语言服务器）

- 描述：是否启用语言服务器。
- 默认值：`true`
- 可按语言单独配置。

## Ensure Final Newline On Save / Remove Trailing Whitespace On Save（保存时格式整理）

- 描述：保存时是否确保文件结尾仅保留一个换行、是否去除行尾空格。
- 默认值：均为 `true`。

## Expand Excerpt Lines / Excerpt Context Lines（多缓冲展开与上下文）

- 描述：多缓冲片段展开的额外行数、上下文行数。默认分别为 `5`、`2`。

## Extend Comment On Newline（换行续写注释）

- 描述：在注释后换行时是否自动续写注释前缀，默认 `true`。

## Features（全局特性）

```json [settings]
{
  "features": {
    "edit_prediction_provider": "zed"
  }
}
```

可选值：`zed`、`copilot`、`supermaven`、`none`。

## File Finder（文件搜索器）

- `file_icons`：是否在搜索结果中显示图标，默认 `true`。
- `modal_max_width`：弹窗宽度，`small`（默认）/`medium`/`large`/`xlarge`/`full`。
- `skip_focus_for_active_in_search`：是否跳过聚焦当前文件，默认 `true`。

## File Scan Exclusions / Inclusions（文件扫描排除与包含）

- `file_scan_exclusions`：默认排除 `.git`、`.DS_Store` 等；自定义时需连同默认值一并写入。
- `file_scan_inclusions`：强制包含（即便被 git 忽略），默认 `".env*"`。

## File Types（文件类型关联）

- 描述：将文件名/后缀与语言关联，支持 glob。
- 默认：

```json [settings]
{
  "file_types": {
    "JSONC": ["**/.zed/**/*.json", "**/zed/**/*.json", "**/Zed/**/*.json", "**/.vscode/**/*.json"],
    "Shell Script": [".env.*"]
  }
}
```

示例：

```json [settings]
{
  "file_types": {
    "C++": ["c"],
    "TOML": ["MyLockFile"],
    "Dockerfile": ["Dockerfile*"]
  }
}
```

## Format On Save / Formatter（保存格式化与格式化器）

- `format_on_save`：`"on"`（默认）、`"off"`。
- `formatter`：`"auto"`（默认）、`"language_server"`、`{"external":{...}}`、`"code_actions"` 或由多个格式化器组成的数组。例如：

```json [settings]
{
  "formatter": {
    "external": {
      "command": "prettier",
      "arguments": ["--stdin-filepath", "{buffer_path}"]
    }
  }
}
```

> `{buffer_path}` 用于传递文件路径，格式化器必须仍然从标准输入读取内容。

## Git（Git 集成）

```json [settings]
{
  "git": {
    "git_gutter": "tracked_files",
    "inline_blame": {
      "enabled": true
    },
    "branch_picker": {
      "show_author_name": true
    },
    "hunk_style": "staged_hollow"
  }
}
```

- `git_gutter`：`tracked_files`（默认）或 `hide`。
- `gutter_debounce`：在 Git gutter 中更新变更标记的延时。
- `inline_blame`：可设置 `delay_ms`、`show_commit_summary`、`min_column`、`padding`。
- `branch_picker.show_author_name`：分支切换菜单是否显示作者。
- `hunk_style`：`staged_hollow`/`unstaged_hollow`。

## Go To Definition Fallback（定义跳转回退）

- 描述：`editor::GoToDefinition` 失败时的回退行为。
- 默认 `"find_all_references"`，可改为 `"none"`。

## Gutter（侧边栏）

```json [settings]
{
  "gutter": {
    "line_numbers": true,
    "runnables": true,
    "breakpoints": true,
    "folds": true,
    "min_line_number_digits": 4
  }
}
```

各项分别控制行号、可运行按钮、断点、折叠指示，以及最小行号宽度。

## Hide Mouse（隐藏鼠标）

- 描述：是否在输入/移动时隐藏鼠标。
- 取值：`"never"`、`"on_typing"`、`"on_typing_and_movement"`（默认）。

## Hover Popover（悬停提示）

- `hover_popover_enabled`：是否启用鼠标悬停信息，默认 `true`。
- `hover_popover_delay`：延迟毫秒数，默认 `300`。

## Icon Theme / Theme（图标与主题）

- 可设置为主题名称或如下对象：

```json [settings]
{
  "icon_theme": {
    "mode": "system",
    "dark": "Zed (Default)",
    "light": "Zed (Default)"
  }
}
```

`mode` 可选 `dark`/`light`/`system`。UI 主题 `theme` 同理，默认 `One Dark` / `One Light`。

## Image Viewer（图片查看器）

- `image_viewer.unit`：文件大小单位，`"binary"`（KiB/MiB，默认）或 `"decimal"`。

## Inlay Hints（内联提示）

- 描述：控制编辑器内“内联提示”的展示。
- 默认：

```json [settings]
{
  "inlay_hints": {
    "enabled": false,
    "show_type_hints": true,
    "show_parameter_hints": true,
    "show_other_hints": true,
    "show_background": false,
    "edit_debounce_ms": 700,
    "scroll_debounce_ms": 50,
    "toggle_on_modifiers_press": null
  }
}
```

开启后还需语言服务器支持。`toggle_on_modifiers_press` 可指定组合键（`control`、`shift`、`alt`、`platform`、`function`）。

Zed 预置了 Go、Rust、Svelte、TypeScript 的相关配置，更多示例请参考各语言文档。

## Journal（日记）

- 描述：日记功能配置。
- 默认：

```json [settings]
{
  "journal": {
    "path": "~",
    "hour_format": "hour12"
  }
}
```

- `path`：存储目录。
- `hour_format`：`"hour12"`（12 小时制）或 `"hour24"`。

## JSX Tag Auto Close（自动闭合 JSX 标签）

- 描述：是否自动补全 JSX 标签。
- 默认：

```json [settings]
{
  "jsx_tag_auto_close": {
    "enabled": true
  }
}
```

## Languages（按语言覆写）

- 描述：按语言覆盖设置。
- 结构：

```json [settings]
{
  "languages": {
    "C": {
      "format_on_save": "off",
      "preferred_line_length": 64,
      "soft_wrap": "preferred_line_length"
    },
    "JSON": {
      "tab_size": 4
    }
  }
}
```

可覆盖的键包括：`enable_language_server`、`ensure_final_newline_on_save`、`format_on_save`、`formatter`、`hard_tabs`、`preferred_line_length`、`remove_trailing_whitespace_on_save`、`show_edit_predictions`、`show_whitespaces`、`whitespace_map`、`soft_wrap`、`tab_size`、`use_autoclose`、`always_treat_brackets_as_autoclosed` 等。

## Language Models（语言模型服务）

- 描述：配置各 AI 模型服务端点。
- 默认：

```json [settings]
{
  "language_models": {
    "anthropic": {
      "api_url": "https://api.anthropic.com"
    },
    "google": {
      "api_url": "https://generativelanguage.googleapis.com"
    },
    "ollama": {
      "api_url": "http://localhost:11434"
    },
    "openai": {
      "api_url": "https://api.openai.com/v1"
    }
  }
}
```

可进一步补充鉴权设置等。

## Line Indicator Format（行列指示格式）

- 描述：状态栏行列显示格式。
- 键名：`line_indicator_format`
- 默认：`"short"`；可选 `"long"`。

## Linked Edits（联动编辑）

- 描述：若 LSP 支持联动编辑（如 `<div>` ↔ `</div>`），是否启用。
- 默认：`true`
- 取值：布尔值。

## LSP / Global LSP Settings / Highlight Debounce（语言服务器设置）

- `lsp`：按语言服务器覆写 `initialization_options`、`settings`。
- `global_lsp_settings.button`：是否在状态栏显示 LSP 状态按钮。
- `lsp_highlight_debounce`：光标移动后请求高亮的防抖时间，默认 75ms。

## Max Tabs（标签数量）

- 描述：标签栏最大显示数量。
- 默认：`null`（无限制），可设为正整数。

## Middle Click Paste（仅 Linux，中键粘贴）

- 描述：是否启用中键粘贴。
- 默认：`true`
- 取值：布尔值。

## Minimap（迷你图）

```json [settings]
{
  "minimap": {
    "show": "never",
    "thumb": "always",
    "thumb_border": "left_open",
    "current_line_highlight": null
  }
}
```

- `show`：`"always"`/`"auto"`/`"never"`。
- `thumb`：`"always"`/`"hover"`。
- `thumb_border`：`"full"`/`"left_open"`/`"right_open"`/`"left_only"`/`"none"`。
- `current_line_highlight`：继承编辑器（`null`）或单独设置。

## Multi Cursor Modifier（多光标修饰键）

- 描述：鼠标添加多光标时使用的修饰键，同时会调整悬浮链接手势避免冲突。
- 默认：`"alt"`（Linux/Windows 为 Alt，macOS 为 Option）。
- 另有 `"cmd_or_ctrl"` 选项（Linux/Windows 为 Ctrl，macOS 为 Command）。

## Network Proxy（网络代理）

- 描述：代理服务器地址，可用协议：`http`、`https`、`socks4`、`socks4a`、`socks5`、`socks5h`。
- 默认：`null`。若未配置，将读取环境变量 `http_proxy`、`NO_PROXY` 等。
- 示例：

```json [settings]
{
  "proxy": "socks5h://localhost:10808"
}
```

## Node（Node.js 集成）

- 描述：Node.js 集成的手动配置。
- 默认：

```json [settings]
{
  "node": {
    "ignore_system_version": false,
    "path": null,
    "npm_path": null
  }
}
```

## On Last Window Closed / When Closing With No Tabs（关闭窗口行为）

- 描述：最后一个窗口关闭时/关闭空窗口时的行为。
- 默认：`"platform_default"`。
- 可选：`"quit_app"`、`"close_window"`、`"keep_window_open"` 等。

## Pane Split Direction Horizontal / Vertical（分屏方向）

- 描述：默认水平/垂直分屏方向。
- 默认：水平 `"up"`，垂直 `"left"`。

## Preferred Line Length（推荐行宽）

- 描述：软换行参考列数。
- 默认：`80`，取值为整数。

## Preview Tabs（预览标签页）

- 描述：控制预览标签页（斜体显示）行为。默认：

```json [settings]
{
  "preview_tabs": {
    "enabled": true,
    "enable_preview_from_file_finder": false,
    "enable_preview_from_code_navigation": false
  }
}
```

- `enable_preview_from_file_finder`：从文件搜索打开时是否进入预览。
- `enable_preview_from_code_navigation`：代码导航是否复用预览标签。

## Private Files / Redact Private Values（隐私文件与脱敏）

- `private_files`：敏感文件匹配模式，默认如 `.env*`、`*.pem` 等。
- `redact_private_values`：是否在 UI 中隐藏敏感值，默认 `false`。

## Profiles（配置档案）

- 描述：定义一组可一键套用的配置档案。
- 示例：

```json [settings]
{
  "profiles": {
    "presentation": {
      "buffer_font_size": 20,
      "ui_font_size": 18,
      "theme": "One Light"
    }
  }
}
```

## Project Panel（项目面板）

```json [settings]
{
  "project_panel": {
    "button": true,
    "default_width": 240,
    "dock": "left",
    "entry_spacing": "comfortable",
    "file_icons": true,
    "folder_icons": true,
    "git_status": true,
    "indent_size": 20,
    "auto_reveal_entries": true,
    "auto_fold_dirs": true,
    "drag_and_drop": true,
    "scrollbar": {
      "show": null
    },
    "sticky_scroll": true,
    "show_diagnostics": "all",
    "indent_guides": {
      "show": "always"
    },
    "hide_root": false,
    "hide_hidden": false,
    "starts_open": true,
    "open_file_on_paste": true
  }
}
```

关键选项说明：
- `dock`：`left`/`right`。
- `entry_spacing`：`comfortable` 或 `standard`。
- `auto_reveal_entries`：当前文件激活时自动展开。
- `auto_fold_dirs`：单链目录是否自动折叠。
- `indent_guides.show`：`always`/`never`。
- `scrollbar.show`：`null`（跟随编辑器）或 `auto/system/always/never`。

## Projects Online By Default（默认显示在线项目）

- 描述：项目面板默认是否显示在线项目。
- 默认：`true`，取值布尔。

## REPL（REPL 配置）

```json [settings]
{
  "repl": {
    "max_columns": 128,
    "max_lines": 32
  }
}
```

用于控制 REPL 滚动缓存大小。

## Relative Line Numbers（相对行号）

- 描述：是否显示相对行号。
- 默认：`false`
- 取值：布尔值。

## Resize All Panels In Dock（同步调整停靠面板）

- 描述：调整停靠栏尺寸时是否联动全部面板。
- 键名：`resize_all_panels_in_dock`
- 默认值：`["left"]`
- 取值：包含 `"left"`、`"right"`、`"bottom"` 的字符串数组，表示在调整对应停靠栏时是否同时拉伸内部所有面板。

## Restore on File Reopen（重新打开时还原状态）

- 描述：再次打开文件时是否尝试还原该文件在当前面板中的状态（光标、折叠等）。
- 键名：`restore_on_file_reopen`
- 默认值：`true`
- 取值：布尔值。

## Restore on Startup（启动时恢复会话）

- 描述：启动 Zed 时是否恢复上一会话。
- 键名：`restore_on_startup`
- 默认值：`"last_session"`
- 取值：
  1. `"last_session"`：恢复退出时打开的全部工作区。
  2. `"last_workspace"`：仅恢复最近关闭的那个工作区。
  3. `"none"`：总是空白启动。

## Scroll Beyond Last Line（滚动越过最后一行）

- 描述：滚动是否允许超出文件最后一行。
- 键名：`scroll_beyond_last_line`
- 默认值：`"one_page"`
- 取值：
  1. `"one_page"`：额外留出一屏的空间。
  2. `"vertical_scroll_margin"`：根据 `vertical_scroll_margin` 的值留白。
  3. `"off"`：严格停在最后一行。

## Scroll Sensitivity / Fast Scroll Sensitivity / Scroll Margins（滚动灵敏度与边距）

- `scroll_sensitivity`：滚动灵敏度倍数，默认 `1.0`。
- `fast_scroll_sensitivity`：按住 Alt/Option 快速滚动时的倍数，默认 `4.0`。
- `horizontal_scroll_margin`：鼠标水平滚动的缓冲字符数，默认 `5`。
- `vertical_scroll_margin`：键盘滚动时上下留白行数，默认 `3`。

## Search（搜索默认选项）

```json [settings]
{
  "search": {
    "whole_word": false,
    "case_sensitive": false,
    "include_ignored": false,
    "regex": false
  }
}
```

- 描述：新建项目/缓冲区搜索时的默认开关。

## Search Wrap（搜索回绕）

- 描述：是否在到达文件末尾后回绕搜索结果。
- 键名：`search_wrap`
- 默认值：`true`
- 取值：布尔值。

## Seed Search Query From Cursor（用光标文本初始化搜索）

- 描述：打开查找面板时是否使用光标下的文本初始化搜索关键字。
- 键名：`seed_search_query_from_cursor`
- 默认值：`"always"`
- 取值：`"always"`、`"selection"`、`"never"`。

## Use Smartcase Search（智能大小写搜索）

- 描述：智能大小写搜索。如果查询包含大写字母则启用区分大小写，否则不区分。
- 键名：`use_smartcase_search`
- 默认值：`false`
- 取值：布尔值。

示例：
- 搜索 `function` 会匹配 `function`/`Function` 等全部大小写。
- 搜索 `Function` 仅匹配大小写完全一致的单词。

## Show Call Status Icon（通话状态图标）

- 描述：是否在状态栏显示通话状态图标。
- 键名：`show_call_status_icon`
- 默认值：`true`
- 取值：布尔值。

## Show Completions On Input / Show Completion Documentation（补全显示与文档）

- 是否随输入实时弹出补全；是否显示补全项的文档描述。两个键默认均为 `true`。

## Show Edit Predictions（显示编辑预测）

- 描述：自动或手动触发编辑预测时是否显示结果。
- 键名：`show_edit_predictions`
- 默认值：`true`
- 取值：布尔值。

## Show Whitespaces / Whitespace Map（显示空白符）

- `show_whitespaces`：默认 `"selection"`，可选 `"all"`、`"none"`、`"boundary"`。
- `whitespace_map`：自定义显示字符，默认：

```json [settings]
{
  "whitespace_map": {
    "space": "•",
    "tab": "→"
  }
}
```

## Soft Wrap / Show Wrap Guides / Wrap Guides（软换行与参考线）

- `soft_wrap`：`"none"`（默认）、`"editor_width"`、`"preferred_line_length"`、`"bounded"`。
- `show_wrap_guides`：是否显示换行参考线，默认 `true`。
- `wrap_guides`：需要额外绘制换行线的列号列表。

## Tab Size / Hard Tabs（Tab 设置）

- `tab_size`：一个 Tab 对应的空格数，默认 `4`。
- `hard_tabs`：是否使用 `\t` 缩进，默认 `false`。

## Tasks（任务配置）

```json [settings]
{
  "tasks": {
    "variables": {},
    "enabled": true,
    "prefer_lsp": false
  }
}
```

- 描述：配置任务是否启用、是否优先使用 LSP 提供的任务，以及自定义变量。

## Telemetry（遥测）

```json [settings]
{
  "telemetry": {
    "diagnostics": true,
    "metrics": true
  }
}
```

- `diagnostics`：发送崩溃报告等调试信息。
- `metrics`：发送匿名使用统计（例如你正在编辑的语言）。

## Terminal（终端设置）

```json [settings]
{
  "terminal": {
    "alternate_scroll": "off",
    "blinking": "terminal_controlled",
    "copy_on_select": false,
    "keep_selection_on_copy": true,
    "dock": "bottom",
    "default_width": 640,
    "default_height": 320,
    "detect_venv": {
      "on": {
        "directories": [".env", "env", ".venv", "venv"],
        "activate_script": "default"
      }
    },
    "env": {},
    "font_family": null,
    "font_features": null,
    "font_size": null,
    "line_height": "comfortable",
    "minimum_contrast": 45,
    "option_as_meta": false,
    "button": true,
    "shell": "system",
    "toolbar": {
      "breadcrumbs": false
    },
    "working_directory": "current_project_directory",
    "scrollbar": {
      "show": null
    }
  }
}
```

重点选项：

- `dock`：停靠位置，`bottom`（默认）、`left`、`right`。
- `alternate_scroll`：处理备用滚动模式，`off`/`on`。
- `blinking`：光标闪烁模式，`off`、`terminal_controlled`（默认）、`on`。
- `copy_on_select`：选中即复制。
- `keep_selection_on_copy`：复制后是否保持选区。
- `cursor_shape`：终端光标形状，`block`/`bar`/`underline`/`hollow`。
- `env`：额外的环境变量。
- `font_family`、`font_features`、`font_size`、`line_height`：终端字体设置。
- `minimum_contrast`：根据 APCA 算法调整对比度（0 禁用，45 为默认）。
- `option_as_meta`：macOS 上将 Option 视作 Meta 键。
- `shell`：终端启动的 Shell，可指定程序及参数。
- `detect_venv`：自动激活虚拟环境，设置为 `"off"` 可关闭。
- `button`：是否在状态栏显示终端按钮。
- `toolbar.breadcrumbs`：是否显示终端标题（需 Shell 输出标题序列）。
- `working_directory`：`"current_project_directory"`、`"first_project_directory"`、`"always_home"`，或指定固定路径。

## REPL（终端部分后续）

- `repl.max_columns`：窗口横向缓存列数（20~512）。
- `repl.max_lines`：滚动缓冲行数（4~256）。

## Theme / Title Bar / Vim（主题、标题栏与 Vim）

- `theme`：与 icon theme 类似，可指定暗/亮主题。
- `title_bar`：控制标题栏元素是否显示（分支图标、项目名称、菜单、用户头像等）。
- `vim_mode`：是否启用 Vim 模式，默认 `false`。
- `helix_mode`：Helix 模式（自动启用 Vim 模式），默认 `false`。

## Unnecessary Code Fade（未使用代码淡化）

- 描述：未使用代码的淡化程度（0~0.9），默认 `0.3`。

## UI Font 家族/特性/后备/大小/字重（界面字体）

- 与 `buffer_font_*` 类似：
  - `ui_font_family` 默认 `.ZedSans`（IBM Plex）。
  - `ui_font_features` 默认禁用 `calt`。
  - `ui_font_fallbacks`：设置 UI 字体后备列表。
  - `ui_font_size` 默认 `16`，取值 6~100。
  - `ui_font_weight` 默认 `400`，范围 100~900。

## Use Auto Surround / Use Autoclose / Always Treat Brackets As Autoclosed（自动包裹与括号行为）

- `use_auto_surround`：选中文本后输入括号/引号时是否自动包裹，默认 `true`。
- `use_autoclose`：输入左括号/引号时自动补齐，默认 `true`。
- `always_treat_brackets_as_autoclosed`：总是把括号视为自动补齐状态，默认 `false`。

## Use On Type Format（实时格式化）

- 描述：输入触发符号后是否请求 LSP 做增量格式化。
- 默认：`true`

## Use System Path Prompts / Use System Prompts / Use System Window Tabs（macOS）（系统对话框与窗口标签）

- `use_system_path_prompts`：打开/另存为是否调用系统对话框，默认 `true`。
- `use_system_prompts`：确认类弹窗是否使用系统对话框（Linux 固定为内置），默认 `true`。
- `use_system_window_tabs`（macOS）：是否让窗口参与系统标签页机制，默认 `false`。

## Window / Workspace 相关（窗口与工作区）

- `projects_online_by_default`、`call`、`collaboration_panel`、`debugger`、`git_panel`、`outline_panel`、`calls` 等均以相同结构控制对应侧边栏的按钮、停靠位置、默认宽度等。

## 语言服务器、AI 与协作

- `agent`：AI Agent 相关设置详见 [AI 配置](./ai/configuration.md)。
- `collaboration_panel.button` 等：是否显示协作面板按钮、停靠方位。
- `debugger.stepping_granularity`：调试步进粒度（默认 `line`），`save_breakpoints` 是否保存断点。
- `git_panel.status_style`：`icon` 或 `label_color`。
- `calls.mute_on_join`、`share_on_join`：加入通话时是否静音、是否自动共享项目。

## 日志、代理与 SSH

- `journal`：日志目录与时间格式。
- `proxy`：网络代理地址。
- `read_ssh_config`：是否读取 SSH 配置文件，默认 `true`。

## 终端滚动条 / 项目面板滚动条

- `scrollbar.show`：四种模式 `auto/system/always/never`。
- `scrollbar.axes.horizontal/vertical`：强制开启/关闭对应方向滚动条。

## Call / Collaboration / Debugger / Git / Outline Panels（面板总览）

这些面板遵循统一模式：
- `button`：状态栏是否显示按钮。
- `dock`：停靠在 `left`/`right`/`bottom`。
- `default_width`：默认宽度。
- 各自的额外设置（如 git 面板的 `status_style`）。

## 其余设置速览

- `enable_language_server`、`edit_predictions_disabled_in` 等，可按语言覆写。
- `linked_edits`、`lsp_document_colors`：默认 `true`。
- `max_tabs`：限制标签数；`middle_click_paste`（Linux）默认开启。
- `preview_tabs.enable_preview_from_code_navigation`：是否在代码导航中复用预览标签。
- `private_files`/`redact_private_values`：敏感文件及其遮罩策略。
- `relative_line_numbers`：相对行号。
- `unnecessary_code_fade`：未使用代码淡化强度。

## 示例配置

```json [settings]
// ~/.config/zed/settings.json
{
  "theme": "cave-light",
  "tab_size": 2,
  "preferred_line_length": 80,
  "soft_wrap": "none",

  "buffer_font_size": 18,
  "buffer_font_family": ".ZedMono",

  "autosave": "on_focus_change",
  "format_on_save": "off",
  "vim_mode": false,
  "projects_online_by_default": true,
  "terminal": {
    "font_family": "FiraCode Nerd Font Mono",
    "blinking": "off"
  },
  "languages": {
    "C": {
      "format_on_save": "on",
      "formatter": "language_server",
      "preferred_line_length": 64,
      "soft_wrap": "preferred_line_length"
    }
  }
}
```
