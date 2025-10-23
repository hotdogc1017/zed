# 视觉自定义

可以通过 {#action zed::OpenSettings}（{#kb zed::OpenSettings}）打开 `settings.json`，调整 Zed 的界面布局与外观。更多非视觉设置请参阅[《配置 Zed》](./configuring-zed.md)。

## 主题

可使用 {#action zed::Extensions} 安装提供 [主题](./themes.md) 和 [图标主题](./icon-themes.md) 的扩展。安装后，可用 {#action theme_selector::Toggle}（{#kb theme_selector::Toggle}）或 {#action icon_theme_selector::Toggle} 预览并切换主题，系统会自动写入：

```json [settings]
{
  "theme": "One Dark",
  "icon_theme": "Zed (Default)"
}
```

若需要为浅色/深色模式分别设置主题，可写：

```json [settings]
{
  "theme": {
    "dark": "One Dark",
    "light": "One Light",
    "mode": "system" // dark / light / system
  },
  "icon_theme": {
    "dark": "Zed (Default)",
    "light": "Zed (Default)",
    "mode": "system"
  }
}
```

## 字体

```json [settings]
  // UI 字体，".SystemUIFont" 使用系统默认字体，".ZedSans" 为自带字体（IBM Plex）
  "ui_font_family": ".SystemUIFont",
  "ui_font_weight": 400,
  "ui_font_size": 16,

  // 编辑器字体（缓冲区），".ZedMono" 为自带等宽字体（Lilex）
  "buffer_font_family": "Berkeley Mono",
  "buffer_font_size": 15,
  "buffer_font_weight": 400,
  "buffer_line_height": "comfortable", // 1.618，可设 standard 或 { "custom": 2 }

  // 终端字体
  "terminal": {
    "font_family": "",
    "font_size": 15,
    "line_height": "standard"
  },

  // AI 面板字体大小
  "agent_font_size": 15
```

### 字体连字

默认开启字体连字，视觉上会将特定字符组合成符号，例如 `=>` 显示为 `→`、`!=` 显示为 `≠`。这仅影响显示，实际字符不会改变。若想关闭：

```json [settings]
{
  "buffer_font_features": {
    "calt": false
  }
}
```

### 状态栏

```json [settings]
{
  "line_indicator_format": "long" // "short" 为简写，“2 s, 15 l, 32 c”

  // 可逐项隐藏状态栏按钮：
  // "project_panel": {"button": false},
  // "outline_panel": {"button": false},
  // "collaboration_panel": {"button": false},
  // "git_panel": {"button": false},
  // "notification_panel": {"button": false},
  // "agent": {"button": false},
  // "debugger": {"button": false},
  // "diagnostics": {"button": false},
  // "search": {"button": false}
}
```

### 标题栏

```json [settings]
  "title_bar": {
    "show_branch_icon": false,
    "show_branch_name": true,
    "show_project_items": true,
    "show_onboarding_banner": true,
    "show_user_picture": true,
    "show_sign_in": true,
    "show_menus": false
  },
```

## 工作区

```json [settings]
{
  // 是否使用系统文件选择器（true）或内置选择器（false）
  "use_system_path_prompts": true,
  // 是否使用系统确认弹窗，Linux 始终使用内置
  "use_system_prompts": true,

  "active_pane_modifiers": {
    "border_size": 0.0,
    "inactive_opacity": 1.0
  },

  // 底部停靠布局：contained / full / left_aligned / right_aligned
  "bottom_dock_layout": "contained",

  // 调整停靠栏时是否联动所有面板，可包含 "left"、"right"、"bottom"
  "resize_all_panels_in_dock": ["left"]
}
```

## 编辑器

```json [settings]
  "cursor_blink": true,
  "cursor_shape": null,            // bar / block / underline / hollow
  "current_line_highlight": "all",// none / gutter / line / all
  "hide_mouse": "on_typing_and_movement",
  "selection_highlight": true,

  "show_whitespaces": "selection",// none / all / selection / boundary / trailing
  "whitespace_map": {
    "space": "•",
    "tab": "→"
  },

  "unnecessary_code_fade": 0.3,
  "redact_private_values": false,

  "soft_wrap": "none",
  "preferred_line_length": 80,
  "show_wrap_guides": true,
  "wrap_guides": [],

  "gutter": {
    "line_numbers": true,
    "runnables": true,
    "breakpoints": true,
    "folds": true,
    "min_line_number_digits": 4
  },
  "relative_line_numbers": false,

  "indent_guides": {
    "enabled": true,
    "line_width": 1,
    "active_line_width": 1,
    "coloring": "fixed",
    "background_coloring": "disabled"
  }
```

### Git Blame {#editor-blame}

```json [settings]
  "git": {
    "inline_blame": {
      "enabled": true,
      "delay_ms": 0,
      "min_column": 0,
      "padding": 7,
      "show_commit_summary": false
    },
    "hunk_style": "staged_hollow"
  }
```

### 编辑器工具栏

```json [settings]
  "toolbar": {
    "breadcrumbs": true,
    "quick_actions": true,
    "selections_menu": true,
    "agent_review": true,
    "code_actions": false
  }
```

### 滚动条与迷你图 {#editor-scrollbar}

```json [settings]
  "scrollbar": {
    "show": "auto",
    "cursors": true,
    "git_diff": true,
    "search_results": true,
    "selected_text": true,
    "selected_symbol": true,
    "diagnostics": "all",
    "axes": {
      "horizontal": true,
      "vertical": true
    }
  },

  "minimap": {
    "show": "never",
    "display_in": "active_editor",
    "thumb": "always",
    "thumb_border": "left_open",
    "max_width_columns": 80,
    "current_line_highlight": null
  },

  "scroll_beyond_last_line": "one_page",
  "vertical_scroll_margin": 3,
  "horizontal_scroll_margin": 5,
  "scroll_sensitivity": 1.0,
  "fast_scroll_sensitivity": 4.0,
```

### 标签与标签栏

```json [settings]
  "max_tabs": null,
  "tab_bar": {
    "show": true,
    "show_nav_history_buttons": true,
    "show_tab_bar_buttons": true
  },
  "tabs": {
    "git_status": false,
    "close_position": "right",
    "show_close_button": "hover",
    "file_icons": false,
    "show_diagnostics": "off"
  }
```

### 状态栏

```json [settings]
  "status_bar": {
    "active_language_button": true,
    "cursor_position_button": true,
    "line_endings_button": false
  },
  "global_lsp_settings": {
    "button": true
  }
```

### 多缓冲区

```json [settings]
{
  "expand_excerpt_lines": 5,
  "excerpt_context_lines": 2
}
```

### 补全 / 片段 / 操作 / 诊断 {#editor-lsp}

```json [settings]
  "snippet_sort_order": "inline",
  "show_completions_on_input": true,
  "show_completion_documentation": true,
  "auto_signature_help": false,
  "show_signature_help_after_edits": false,
  "inline_code_actions": true,
  "diagnostics_max_severity": null,
  "lsp_document_colors": "inlay"
```

### 编辑预测 {#editor-ai}

```json [settings]
  "edit_predictions": {
    "mode": "eager",
    "enabled_in_text_threads": true
  },
  "show_edit_predictions": true
```

### 内联提示

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
    "toggle_on_modifiers_press": {
      "control": false,
      "shift": false,
      "alt": false,
      "platform": false,
      "function": false
    }
  }
}
```

## 文件查找器

```json [settings]
  "file_finder": {
    "file_icons": true,
    "modal_max_width": "small",
    "git_status": true,
    "include_ignored": null
  },
```

## 项目面板

可通过 {#action project_panel::ToggleFocus}（{#kb project_panel::ToggleFocus}）或 {#action pane::RevealInProjectPanel}（{#kb pane::RevealInProjectPanel}）显示/隐藏：

```json [settings]
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
    "sticky_scroll": true,
    "drag_and_drop": true,
    "scrollbar": {
      "show": null
    },
    "show_diagnostics": "all",
    "indent_guides": {
      "show": "always"
    },
    "hide_root": false,
    "hide_hidden": false
  }
```

## AI 面板

```json [settings]
  "agent": {
    "version": "2",
    "enabled": true,
    "button": true,
    "dock": "right",
    "default_width": 640,
    "default_height": 320
  },
  "agent_font_size": 16
```

更多 AI 相关设置见 [Zed AI 概览](./ai/overview.md)。

## 终端面板

```json [settings]
  "terminal": {
    "dock": "bottom",
    "button": true,
    "default_width": 640,
    "default_height": 320,
    "blinking": "terminal_controlled",
    "cursor_shape": "block",
    "env": {},
    "scrollbar": {
      "show": null
    },
    "font_family": "Fira Code",
    "font_size": 15,
    "font_weight": 400,
    "line_height": "comfortable",
    "max_scroll_history_lines": 10000
  }
```

更多终端设置可参考[配置 Zed > 终端](./configuring-zed.md#terminal)。

### 其他面板

```json [settings]
  "git_panel": {
    "button": true,
    "dock": "left",
    "default_width": 360,
    "status_style": "icon",
    "sort_by_path": false,
    "scrollbar": { "show": null }
  },
  "debugger": {
    "dock": "bottom",
    "button": true
  },
  "outline_panel": {
    "button": true,
    "default_width": 300,
    "dock": "left",
    "file_icons": true,
    "folder_icons": true,
    "git_status": true,
    "indent_size": 20,
    "indent_guides": { "show": "always" },
    "auto_reveal_entries": true,
    "auto_fold_dirs": true,
    "scrollbar": { "show": null }
  }
```

## 协作与通知面板

```json [settings]
{
  "collaboration_panel": {
    "button": true,
    "dock": "left",
    "default_width": 240
  },
  "show_call_status_icon": true,

  "notification_panel": {
    "button": true,
    "dock": "right",
    "default_width": 380
  }
}
```
