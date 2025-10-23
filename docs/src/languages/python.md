# 如何在 Zed 中设置 Python

Zed 原生支持 Python。

- Tree-sitter：[tree-sitter-python](https://github.com/zed-industries/tree-sitter-python)
- 语言服务器：
  - [DetachHead/basedpyright](https://github.com/DetachHead/basedpyright)
  - [astral-sh/ruff](https://github.com/astral-sh/ruff)
  - [astral-sh/ty](https://github.com/astral-sh/ty)
  - [microsoft/pyright](https://github.com/microsoft/pyright)
  - [python-lsp/python-lsp-server](https://github.com/python-lsp/python-lsp-server)（PyLSP）
- 调试适配器：[debugpy](https://github.com/microsoft/debugpy)

## 安装 Python

在开始之前，需要先安装 Zed 与 Python。

### 步骤 1：安装 Python

Zed 不随附 Python 运行时，需自行安装。可选择以下任一方式：

- uv（推荐）

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

了解更多请参阅 [Astral 安装指南](https://docs.astral.sh/uv/getting-started/installation/)。

- Homebrew：

```bash
brew install python
```

- 从 Python.org 下载最新安装包：[python.org/downloads](https://python.org/downloads)

### 步骤 2：验证 Python 是否安装成功

确认 Python 已在终端中可用：

```bash
python3 --version
```

若输出类似 `Python 3.x.x` 即表示安装成功。

## 在 Zed 中打开第一个 Python 项目

完成 Zed 与 Python 的安装后，打开任意包含 Python 代码的文件夹即可开始工作。

### 步骤 1：使用 Python 项目启动 Zed

启动 Zed，可通过菜单 File > Open Folder 打开工程，也可以在终端执行：

```bash
zed path/to/your/project
```

Zed 会自动识别 `.py` 文件，并使用内置的 tree-sitter 解析器，无需额外插件或手动配置。

### 步骤 2：使用集成终端（可选）

Zed 提供位于底部面板的集成终端。如果检测到项目正在使用[虚拟环境](#virtual-environments)，新建终端时会自动激活。可通过 [`detect_venv`](../configuring-zed.md#terminal-detect_venv) 设置调整这一行为。

## 在 Zed 中配置 Python 语言服务器

Zed 内置多种 Python 语言服务器。默认情况下，[basedpyright](https://github.com/DetachHead/basedpyright) 作为主语言服务器，[Ruff](https://github.com/astral-sh/ruff) 负责格式化与 Lint。

其他可选语言服务器包括：

- [Ty](https://docs.astral.sh/ty/)——来自 Astral 的高速语言服务器。
- [Pyright](https://github.com/microsoft/pyright)——basedpyright 的基础。
- [PyLSP](https://github.com/python-lsp/python-lsp-server)——基于插件的语言服务器，可整合 `pycodestyle`、`autopep8`、`yapf` 等工具。

这些服务器默认禁用，可在设置中启用。例如：

```json [settings]
{
  "languages": {
    "Python": {
      "language_servers": [
        // 禁用 basedpyright，启用 Ty，并保留其他默认设置
        "ty",
        "!basedpyright",
        "..."
      ]
    }
  }
}
```

更多启用/禁用语言服务器的方法请参阅[《使用语言服务器》](https://zed.dev/docs/configuring-languages#working-with-language-servers)。

### Basedpyright

[basedpyright](https://docs.basedpyright.com/latest/) 自 Zed v0.204.0 起成为默认 Python 语言服务器，提供跳转、类型检查等核心功能。与 Pyright 相比，它新增了内联提示、更多检查规则等特性。

默认情况下，basedpyright 以 `standard` 类型检查模式运行，与 Pyright 行为一致；单独使用 basedpyright 时的默认值为 `recommended`。可以在 `pyrightconfig.json` 或 `pyproject.toml` 中设置 `typeCheckingMode` 覆盖该默认值。

#### Basedpyright 配置

basedpyright 可从两类来源读取配置：

- 语言服务器设置（即每个编辑器独有的“工作区配置”，在 Zed 中对应 `settings.json`）
- 项目配置文件（`pyrightconfig.json`、`pyproject.toml`），与编辑器无关，但只影响所在项目

一般而言，只对编辑器使用场景生效的选项放在语言服务器设置中；即使在命令行运行也需要的选项则写入配置文件。内联提示相关选项属于第一类，而[诊断分类](https://docs.basedpyright.com/latest/configuration/config-files/#diagnostic-categories)等则属于第二类。

下面给出两类配置的示例，更多选项请参阅官方文档：[语言服务器设置](https://docs.basedpyright.com/latest/configuration/language-server-settings/)与[配置文件](https://docs.basedpyright.com/latest/configuration/config-files/)。

##### 语言服务器设置

在 `settings.json` 的 `lsp` 段中配置 basedpyright，例如：

- 对整个工作区进行诊断，而非仅限于打开的文件
- 禁用函数参数的内联提示

```json [settings]
{
  "lsp": {
    "basedpyright": {
      "settings": {
        "analysis": {
          "diagnosticMode": "workspace",
          "inlayHints.callArgumentNames": false
        }
      }
    }
  }
}
```

##### 配置文件

basedpyright 会读取 `pyrightconfig.json`，以及 `pyproject.toml` 中 `[tool.basedpyright]`、`[tool.pyright]` 段的内容。若两处同时存在配置，以 `pyrightconfig.json` 为准。

例如：

```json [settings]
{
  "typeCheckingMode": "strict",
  "ignore": ["**/__pycache__"]
}
```

### PyLSP

[python-lsp-server](https://github.com/python-lsp/python-lsp-server/)（简称 PyLSP）默认集成 `autopep8`、`mccabe`、`pycodestyle`、`yapf` 等工具，`flake8`、`pylint` 等需手动启用。更多信息请参阅 [Python Language Server Configuration](https://github.com/python-lsp/python-lsp-server/blob/develop/CONFIGURATION.md)。

## 虚拟环境

[虚拟环境](https://docs.python.org/3/library/venv.html) 可为项目提供独立的 Python 版本与依赖集合。Zed 对虚拟环境具有内建支持，并通过[工具链](../toolchains.md)这一通用概念来管理。

请注意，全局 Python 安装也会被视为一个工具链。

### 创建虚拟环境

如果项目尚未创建虚拟环境，可执行：

```bash
python3 -m venv .venv
```

若使用 `uv`，第一次运行 `uv sync` 时也会自动创建虚拟环境。

### Zed 如何使用 Python 工具链

Zed 会按如下方式使用所选工具链：

- 内置语言服务器会自动获取工具链 Python 解释器及虚拟环境路径，以便正确解析依赖。（当前社区扩展提供的语言服务器无法自动配置。）
- Python 任务（如 pytest）会使用该工具链的解释器执行。
- 若工具链为虚拟环境，在 Zed 集成终端中新建 shell 时会自动执行激活脚本，方便使用对应解释器与依赖。
- 如果虚拟环境中已安装内置语言服务器或 debugpy，将优先使用该版本，而非 Zed 自动下载的私有副本。

### 选择工具链

大部分情况下，Zed 会自动选择正确的工具链。若项目较复杂、存在多个虚拟环境，可通过[工具链选择器](../toolchains.md#selecting-toolchains)手动切换；若未出现在列表，可[手动添加工具链路径](../toolchains.md#adding-toolchains-manually)。

## 代码格式化与 Lint

Zed 使用 [Ruff](https://docs.astral.sh/ruff/) 同时完成 Python 格式化与 Lint（以 `ruff server` 子命令形式作为 LSP 运行），并默认启用保存时格式化。

### 配置格式化

可在 `settings.json` 中关闭保存时格式化：

```json [settings]
{
  "languages": {
    "Python": {
      "format_on_save": "off"
    }
  }
}
```

也可以保留 Ruff Lint，只将格式化器改为命令行工具 `black`：

```json [settings]
{
  "languages": {
    "Python": {
      "formatter": {
        "external": {
          "command": "black",
          "arguments": ["--stdin-filename", "{buffer_path}", "-"]
        }
      }
      // 或设置 "formatter": null 完全禁用格式化
    }
  }
}
```

### 配置 Ruff

与 basedpyright 类似，Ruff 在 Zed 中既可通过语言服务器设置配置，也可在 `ruff.toml` 中配置。不同之处在于：所有选项都可以在任一位置设定。若希望配置在各项目通用但仅对 Zed 生效，可写入语言服务器设置；若想让配置在项目中对所有 Ruff 调用统一生效，则写入 `ruff.toml`。

以下示例演示如何在 `settings.json` 中禁用 Ruff 的所有 lint（仍保留 Ruff 作为格式化器）：

```json [settings]
{
  "lsp": {
    "ruff": {
      "initialization_options": {
        "settings": {
          "exclude": ["*"]
        }
      }
    }
  }
}
```

`ruff.toml` 示例：

```toml
[lint]
# 忽略行长 (`E501`)
ignore = ["E501"]

[format]
# 格式化时优先使用单引号
quote-style = "single"
```

更多细节请参阅 Ruff 文档：[配置文件](https://docs.astral.sh/ruff/configuration/)、[语言服务器设置](https://docs.astral.sh/ruff/editors/settings/)、[配置项列表](https://docs.astral.sh/ruff/settings/)。

## 调试

Zed 通过 `debugpy` 适配器支持 Python 调试。既可以零配置直接使用，也可以在 `.zed/debug.json` 中定义自定义的启动配置。

### 无需配置直接开始调试

Zed 会自动识别可调试的 Python 入口点。按 F4（或通过命令面板执行 `debugger: start`）即可查看当前项目可用的选项，支持：

- Python 脚本
- 模块
- pytest 测试

Zed 底层使用 `debugpy`，但无需手动配置适配器。

### 定义自定义调试配置

若需复用特定调试方案，可在项目根目录创建 `.zed/debug.json`。例如：

- [debugpy 配置文档](https://github.com/microsoft/debugpy/wiki/Debug-configuration-settings#launchattach-settings)

#### 调试当前文件

```json [debug]
[
  {
    "label": "Python Active File",
    "adapter": "Debugpy",
    "program": "$ZED_FILE",
    "request": "launch"
  }
]
```

上述配置会运行当前编辑器打开的文件。

#### 调试 Flask 应用

假设项目结构如下：

```
.venv/
app/
  init.py
  main.py
  routes.py
templates/
  index.html
static/
  style.css
requirements.txt
```

可使用下列配置：

```json [debug]
[
  {
    "label": "Python: Flask",
    "adapter": "Debugpy",
    "request": "launch",
    "module": "app",
    "cwd": "$ZED_WORKTREE_ROOT",
    "env": {
      "FLASK_APP": "app",
      "FLASK_DEBUG": "1"
    },
    "args": [
      "run",
      "--reload", // 启用自动重载
      "--debugger" // 启用 Flask 调试器
    ],
    "autoReload": {
      "enable": true
    },
    "jinja": true,
    "justMyCode": true
  }
]
```

可以根据需要组合这些配置，用于调试 Web 服务、测试运行器或自定义脚本。

## 故障排查与维护

Zed 旨在减轻配置负担，但虚拟环境、语言服务器或工具链仍可能偶尔出现问题。以下建议可帮助你保持顺畅的 Python 开发体验。

### 语言服务器无法启动

如果语言服务器无响应，或诊断/补全等功能不可用：

- 通过 {#action zed::OpenLog} 查看 Zed 日志中相关错误。
- 使用 {#action dev::OpenLanguageServerLogs} 打开语言服务器日志，或点击状态栏中的闪电图标并选择相应语言服务器。重点关注：
  - “Server Logs”：语言服务器输出的错误信息
  - “Server Info”：语言服务器的启动参数
- 检查 `settings.json` 或 `pyrightconfig.json` 语法是否正确。
- 重启 Zed，或通过 {#action editor::RestartLanguageServer} 重启语言服务器。
- 如果你使用虚拟环境但语言服务器无法解析依赖，请确认已在工具链选择器中选择正确的环境。可在 “Server Info” 的 `* Configuration` 段确认 Zed 传递的虚拟环境路径。
