# 编辑预测

编辑预测是 Zed 通过 AI 预测您想要编写的代码的本机机制。
每个按键都会向我们的[开源、开放数据集 Zeta 模型](https://huggingface.co/zed-industries/zeta)发送一个新请求，它返回单个或多行建议，可以通过按 `tab` 快速接受。

## 配置 Zeta

Zed 的编辑预测最初是通过标题栏上的横幅引入的。
点击它会带您到一个带有按钮（"启用编辑预测"）的模态框，该按钮将 `zed` 设置为您的 `edit_prediction_provider`。

![入门横幅和模态框](https://zed.dev/img/edit-prediction/docs.webp)

但是，如果您没有遇到横幅，Zed 的编辑预测是默认的编辑预测提供程序，您应该立即在状态栏中看到它。

### 切换模式 {#switching-modes}

Zed 的编辑预测带有两种不同的显示模式：

1. `eager`（默认）：只要不与语言服务器补全冲突，预测就会内联显示
2. `subtle`：只有在按住修饰键（默认为 `alt`）时，预测才会内联出现

通过 `mode` 键在它们之间切换：

```json [settings]
"edit_predictions": {
  "mode": "eager" // 或 "subtle"
},
```

或直接通过状态栏菜单的 UI：

![编辑预测状态栏菜单，带有模式切换。](https://zed.dev/img/edit-prediction/status-bar-menu.webp)

### 与其他 `tab` 操作的冲突 {#edit-predictions-conflict}

默认情况下，当 `tab` 通常会执行不同操作时，Zed 需要修饰键来接受预测：

1. 当语言服务器补全菜单可见时。
2. 当您的光标不在正确的缩进级别时。

在这些情况下，使用 `alt-tab` 代替来接受预测。当语言服务器补全菜单打开时，首先按住 `alt` 将使其暂时消失，以便在缓冲区中预览预测。

在 Linux 上，`alt-tab` 通常被窗口管理器用于切换窗口，因此提供了 `alt-l` 作为接受预测的默认绑定。`tab` 和 `alt-tab` 也有效，但默认不显示。

{#action editor::AcceptPartialEditPrediction} ({#kb editor::AcceptPartialEditPrediction}) 可用于接受当前编辑预测直到下一个单词边界。

有关其他提供程序的配置，请参阅下面的[配置 GitHub Copilot](#github-copilot) 和[配置 Supermaven](#supermaven) 部分。这些提供程序仅支持在当前光标处插入文本，而 Zeta 模型提供包括删除在内的多种预测。

## 配置编辑预测键绑定 {#edit-predictions-keybinding}

默认情况下，`tab` 用于接受编辑预测。您可以通过在键映射中插入以下内容来使用其他键绑定：

```json [settings]
{
  "context": "Editor && edit_prediction",
  "bindings": {
    // 这里我们也允许 `alt-enter` 来接受预测
    "alt-enter": "editor::AcceptEditPrediction"
  }
}
```

当[与 `tab` 键冲突](#edit-predictions-conflict)时，Zed 使用不同的上下文来接受键绑定（`edit_prediction_conflict`）。如果您想使用不同的键绑定，可以在键映射中插入以下内容：

```json [settings]
{
  "context": "Editor && edit_prediction_conflict",
  "bindings": {
    "ctrl-enter": "editor::AcceptEditPrediction" // 修改后的键绑定示例
  }
}
```

如果您的键绑定包含修饰键（上例中的 `ctrl`），它也将用于预览编辑预测并暂时隐藏语言服务器补全菜单。

您也可以将此操作绑定到没有修饰键的键绑定。在这种情况下，Zed 将使用默认修饰键（`alt`）来预览编辑预测。

```json [settings]
{
  "context": "Editor && edit_prediction_conflict",
  "bindings": {
    // 这里我们将 tab 绑定为即使有语言服务器补全
    // 或光标不在正确的缩进级别时也接受预测
    "tab": "editor::AcceptEditPrediction"
  }
}
```

为了在有语言服务器补全菜单时保持使用修饰键接受预测，但允许 `tab` 无论光标位置如何都接受预测，您可以使用 `showing_completions` 进一步指定上下文：

```json [settings]
{
  "context": "Editor && edit_prediction_conflict && !showing_completions",
  "bindings": {
    // 这里除非有语言服务器补全，否则不需要修饰键
    "tab": "editor::AcceptEditPrediction"
  }
}
```

### 键绑定示例：始终使用 Alt-Tab

下面的键绑定示例导致始终使用 `alt-tab` 而不是有时使用 `tab`。您可能希望这样，以便只有一个键绑定用于接受编辑预测，因为 `tab` 的行为会根据上下文而变化。

```json [keymap]
  {
    "context": "Editor && edit_prediction",
    "bindings": {
      "alt-tab": "editor::AcceptEditPrediction"
    }
  },
  // 将 `tab` 绑定回其原始行为。
  {
    "context": "Editor",
    "bindings": {
      "tab": "editor::Tab"
    }
  },
  {
    "context": "Editor && showing_completions",
    "bindings": {
      "tab": "editor::ComposeCompletion"
    }
  },
```

如果在 `settings.json` 中设置了 `"vim_mode": true`，则需要在上述内容之后添加额外的绑定以将 `tab` 恢复为其原始行为：

```json [keymap]
  {
    "context": "(VimControl && !menu) || vim_mode == replace || vim_mode == waiting",
    "bindings": {
      "tab": "vim::Tab"
    }
  },
  {
    "context": "vim_mode == literal",
    "bindings": {
      "tab": ["vim::Literal", ["tab", "\u0009"]]
    }
  },
```

### 键绑定示例：在 Linux 上显示 Tab 和 Alt-Tab

虽然 `tab` 和 `alt-tab` 在 Linux 上受支持，但显示的是 `alt-l`。如果您的窗口管理器没有保留 `alt-tab`，并且您更愿意使用 `tab` 和 `alt-tab`，请在 `keymap.json` 中包含这些绑定：

```json [keymap]
  {
    "context": "Editor && edit_prediction",
    "bindings": {
      "tab": "editor::AcceptEditPrediction",
      // 可选：这使默认的 `alt-l` 绑定不执行任何操作。
      "alt-l": null
    }
  },
  {
    "context": "Editor && edit_prediction_conflict",
    "bindings": {
      "alt-tab": "editor::AcceptEditPrediction",
      // 可选：这使默认的 `alt-l` 绑定不执行任何操作。
      "alt-l": null
    }
  },
```

### 缺少键绑定 {#edit-predictions-missing-keybinding}

Zed 要求在 `Editor && edit_prediction` 和 `Editor && edit_prediction_conflict` 上下文中至少有一个用于 {#action editor::AcceptEditPrediction} 操作的键绑定（[在上面了解更多](#edit-predictions-keybinding)）。

如果您之前已将默认键绑定绑定到全局上下文中的不同操作，您将无法预览或接受编辑预测。例如：

```json [keymap]
[
  // 您的键映射
  {
    "bindings": {
      // 将 `alt-tab` 绑定到全局的不同操作
      "alt-tab": "menu::SelectNext"
    }
  }
]
```

要解决此问题，您可以指定自己的键绑定来接受编辑预测：

```json [keymap]
[
  // ...
  {
    "context": "Editor && edit_prediction_conflict",
    "bindings": {
      "alt-l": "editor::AcceptEditPrediction"
    }
  }
]
```

如果您想使用默认键绑定，可以通过将您的键绑定移动到更具体的上下文或将其更改为其他内容来释放它。

## 禁用自动编辑预测

您可以在不同级别禁用编辑预测的显示，包括完全不启用它。

或者，如果您已将 Zed 设置为提供程序，请考虑[使用微妙模式](#switching-modes)。

### 在缓冲区上

要在您输入时不自动显示预测，请在 `settings.json` 中设置：

```json [settings]
{
  "show_edit_predictions": false
}
```

这会隐藏所有指示有可用预测的迹象，无论您处于[哪种显示模式](#switching-modes)（仅在您将 Zed 作为提供程序时有效）。
尽管如此，您仍然可以通过执行 {#action editor::ShowEditPrediction} 或按 {#kb editor::ShowEditPrediction} 手动触发编辑预测。

### 对于特定语言

要在使用特定语言工作时不在输入时自动显示预测，请在 `settings.json` 中设置：

```json [settings]
{
  "language": {
    "python": {
      "show_edit_predictions": false
    }
  }
}
```

### 在特定目录中

要禁用特定目录或文件的编辑预测，请在 `settings.json` 中设置：

```json [settings]
{
  "edit_predictions": {
    "disabled_globs": ["~/.config/zed/settings.json"]
  }
}
```

### 完全关闭

要完全关闭所有提供程序的编辑预测，请明确将设置设置为 `none`，如下所示：

```json [settings]
"features": {
  "edit_prediction_provider": "none"
},
```

## 配置 GitHub Copilot {#github-copilot}

要使用 GitHub Copilot 作为您的提供程序，请在 `settings.json` 中设置：

```json [settings]
{
  "features": {
    "edit_prediction_provider": "copilot"
  }
}
```

您应该能够通过点击状态栏中的 Copilot 图标并按照设置说明登录 GitHub Copilot。

### 使用 GitHub Copilot Enterprise {#github-copilot-enterprise}

如果您的组织使用 GitHub Copilot Enterprise，您可以通过在 `settings.json` 中指定企业 URI 来配置 Zed 使用您的企业实例：

```json [settings]
{
  "edit_predictions": {
    "copilot": {
      "enterprise_uri": "https://your.enterprise.domain"
    }
  }
}
```

将 `"https://your.enterprise.domain"` 替换为您的 GitHub Enterprise 管理员提供的 URL（例如，`https://foo.ghe.com`）。

设置后，Zed 将通过您的企业端点路由 Copilot 请求。当您通过点击状态栏中的 Copilot 图标登录时，您将被重定向到您配置的企业 URL 以完成身份验证。所有其他 Copilot 功能和用法保持不变。

Copilot 可以提供多个补全替代方案，这些可以通过以下操作进行导航：

- {#action editor::NextEditPrediction} ({#kb editor::NextEditPrediction})：循环到下一个编辑预测
- {#action editor::PreviousEditPrediction} ({#kb editor::PreviousEditPrediction})：循环到上一个编辑预测

## 配置 Supermaven {#supermaven}

要使用 Supermaven 作为您的提供程序，请在 `settings.json` 中设置：

```json [settings]
{
  "features": {
    "edit_prediction_provider": "supermaven"
  }
}
```

您应该能够通过点击状态栏中的 Supermaven 图标并按照设置说明登录 Supermaven。

## 另请参阅

您也可以使用[代理面板](./agent-panel.md) 或[内联助手](./inline-assistant.md) 与语言模型交互，有关 Zed 中其他 AI 功能的更多信息，请参阅 [AI 文档](./overview.md)。
