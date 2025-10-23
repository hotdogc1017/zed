# Bash

Zed 对 Bash 的语言支持由社区维护的 [Basher 扩展](https://github.com/d1y/bash.zed) 提供。
如需反馈问题，请访问：[https://github.com/d1y/bash.zed/issues](https://github.com/d1y/bash.zed/issues)

- Tree-sitter：[tree-sitter/tree-sitter-bash](https://github.com/tree-sitter/tree-sitter-bash)
- 语言服务器：[bash-lsp/bash-language-server](https://github.com/bash-lsp/bash-language-server)

## 配置

当系统中可用 `shellcheck` 时，`bash-language-server` 会内部调用它来提供诊断信息。

### 安装 `shellcheck`

```sh
brew install shellcheck             # macOS（Homebrew）
apt-get install shellcheck          # Ubuntu/Debian
pacman -S shellcheck                # Arch Linux
dnf install shellcheck              # Fedora
yum install shellcheck              # CentOS/RHEL
zypper install shellcheck           # openSUSE
choco install shellcheck            # Windows（Chocolatey）
```

安装后请确认它已添加到 PATH：

```sh
which shellcheck
shellcheck --version
```

如果想自定义需要忽略或关注的警告/错误，只需创建 `.shellcheckrc` 文件。你可以在项目根目录或用户主目录（`~/.shellcheckrc`）中创建。更多信息参见 [shellcheck 文档](https://github.com/koalaman/shellcheck/wiki/Ignore#ignoring-one-or-more-types-of-errors-forever)。

### 另见

- [Zed 文档：语言支持：Shell 脚本](./sh.md)
