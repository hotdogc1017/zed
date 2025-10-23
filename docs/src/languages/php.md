# PHP

可以通过 [PHP 扩展](https://github.com/zed-extensions/php) 在 Zed 中使用 PHP。

- Tree-sitter：https://github.com/tree-sitter/tree-sitter-php
- 语言服务器：
  - [phpactor](https://github.com/phpactor/phpactor)
  - [intelephense](https://github.com/bmewburn/vscode-intelephense/)

## 选择语言服务器

PHP 扩展支持 `phpactor` 与 `intelephense` 两种语言服务器。

默认启用 `phpactor`。

### Phpactor

Zed 的 PHP 扩展可以自动安装 `phpactor`，但前提是系统已安装 `php` 且可在 PATH 中找到：

```sh
# brew install php            # macOS
# sudo apt-get install php    # Debian/Ubuntu
# yum install php             # CentOS/RHEL
# pacman -S php               # Arch Linux
which php
```

### Intelephense

[Intelephense](https://intelephense.com/) 是一款采用免费增值模式的[商业](https://github.com/bmewburn/vscode-intelephense/blob/master/LICENSE.txt#L29) PHP 语言服务器，部分功能需购买 [高级许可证](https://intelephense.com/)。

若要切换至 `intelephense`，在 `settings.json` 中添加：

```json [settings]
{
  "languages": {
    "PHP": {
      "language_servers": ["intelephense", "!phpactor", "..."]
    }
  }
}
```

如果需要启用高级功能，可将 [licence.txt 文件](https://intelephense.com/faq.html) 放在主目录下的 `~/intelephense/licence.txt`。也可以通过 `intelephense` 的初始化选项传入许可证密钥或密钥文件路径，在 `settings.json` 中添加：

```json [settings]
{
  "lsp": {
    "intelephense": {
      "initialization_options": {
        "licenceKey": "/path/to/licence.txt"
      }
    }
  }
}
```

## PHPDoc

Zed 支持 PHPDoc 注释的语法高亮。

- Tree-sitter：[claytonrcarter/tree-sitter-phpdoc](https://github.com/claytonrcarter/tree-sitter-phpdoc)

## 配置 Xdebug

Zed 的 PHP 扩展提供了针对 PHP 与 Xdebug 的调试适配器，名称为 `Xdebug`。以下是两种常见用法：

```json
[
  {
    "label": "PHP: Listen to Xdebug",
    "adapter": "Xdebug",
    "request": "launch",
    "initialize_args": {
      "port": 9003
    }
  },
  {
    "label": "PHP: Debug this test",
    "adapter": "Xdebug",
    "request": "launch",
    "program": "vendor/bin/phpunit",
    "args": ["--filter", "$ZED_SYMBOL"]
  }
]
```

若遇到问题，可依次检查：

- 确认当前 PHP 版本已安装 Xdebug
- 确认 Xdebug 以 `debug` 模式运行
- 确认 Xdebug 实际启动了调试会话
- 核对 Xdebug 与 Zed 的主机名与端口是否一致
- 在需要调试的页面调用 `xdebug_info()` 查看诊断信息
