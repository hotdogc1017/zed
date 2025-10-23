# Shell 脚本

Zed 原生支持 Shell 脚本（bash、zsh、dash、sh）。

- Tree-sitter：[tree-sitter/tree-sitter-bash](https://github.com/tree-sitter/tree-sitter-bash)

## 设置

可在用户设置（`~/.config/zed/settings.json`）或项目设置（`.zed/settings.json`）中配置 Shell Script：

```json [settings]
  "languages": {
    "Shell Script": {
      "tab_size": 2,
      "hard_tabs": false
    }
  }
```

### 格式化

Zed 支持使用外部工具（如 [`shfmt`](https://github.com/mvdan/sh)）对 Shell 脚本自动格式化。

1. 安装 `shfmt`：

```sh
brew install shfmt            # macOS（Homebrew）
sudo apt-get install shfmt    # Debian/Ubuntu
dnf install shfmt             # Fedora
yum install shfmt             # Red Hat
pacman -Sy shfmt              # Arch Linux
choco install shfmt           # Windows（Chocolatey）
```

2. 确认 `shfmt` 已加入 PATH 并检查版本：

```sh
which shfmt
shfmt --version
```

3. 在 Zed 中配置保存时自动使用 `shfmt`：

```json [settings]
  "languages": {
    "Shell Script": {
      "format_on_save": "on",
      "formatter": {
        "external": {
          "command": "shfmt",
          // 根据偏好调整缩进宽度
          "arguments": ["--filename", "{buffer_path}", "--indent", "2"]
        }
      }
    }
  }
```

## 另请参阅

- [Zed 文档：语言支持：Bash](./bash.md)
- [Zed 文档：语言支持：Fish](./fish.md)
