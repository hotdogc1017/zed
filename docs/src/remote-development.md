# 远程开发

远程开发让你即使在代码不位于本地机器时，也能保持“所想即所得”的开发体验。Zed 的界面在本地运行，保持即时反馈；计算密集型任务则在开发服务器上执行。

## 基本概念

远程开发需要两台机器：

1. 本地计算机：运行 Zed UI、与语言模型交互、使用 Tree-sitter 解析语法、保存未提交改动与历史项目。
2. 远程服务器：运行 Zed 无头服务（headless server），承载源代码、语言服务器、任务与终端。

两端通过 SSH 建立连接，因此需要在本地可直接 SSH 登录远程主机。

![远程开发架构](https://zed.dev/img/remote-development/diagram.png)

> **提示**：自 Zed v0.157 起，远程开发仅通过 SSH 直连，不再经过 Zed 中转服务器。

## 环境准备

1. 安装最新版 [Zed](https://zed.dev/releases)，至少需要 v0.159。
2. 执行 {#kb projects::OpenRemote} 打开 “Remote Projects” 面板。
3. 点击 “Connect New Server”，填写你平时用于 SSH 的命令（下文列出可用选项）。
4. Zed 会调用系统中的 `ssh` 命令发起连接。首次连接时会自动在远端下载并启动 Zed Server。
5. Server 启动后会提示选择远程路径。建议直接选取项目目录或大型仓库中的子目录，避免一次性加载 `/` 或 `~` 等包含十万级文件的路径。

若无需额外参数，也可直接在终端运行：

```sh
zed ssh://[<user>@]<host>[:<port>]/<path>
```

并可通过 `zed://ssh/...` 这种链接格式在外部跳转到远程项目。

## 支持平台

远程主机需能运行 Zed Server，目前支持：

- macOS Catalina 或更新版本（Intel / Apple Silicon）
- Linux（x86_64 / arm64）
- Windows 暂不支持

## 配置连接

远程连接信息保存在 `settings.json`（{#kb zed::OpenSettings}）。可通过 Remote Projects 面板维护，也可手动编辑：

```json [settings]
{
  "ssh_connections": [
    {
      "host": "192.168.1.10",
      "projects": [{ "paths": ["~/code/zed/zed"] }]
    }
  ]
}
```

Zed 默认读取 `ssh_config` 配置，如需覆盖可在连接项中指定：

```json [settings]
{
  "ssh_connections": [
    {
      "host": "192.168.1.10",
      "projects": [{ "paths": ["~/code/zed/zed"] }],
      "args": ["-i", "~/.ssh/work_id_file"],
      "port": 22,
      "username": "me"
    }
  ]
}
```

额外选项：

```json [settings]
{
  "ssh_connections": [
    {
      "host": "192.168.1.10",
      "projects": [{ "paths": ["~/code/zed/zed"] }],
      "upload_binary_over_ssh": true,
      "nickname": "lil-linux"
    }
  ]
}
```

- `upload_binary_over_ssh`: 默认远端直接下载 server，可改为先在本地下载再上传（适合远端无法访问互联网的情况）。
- `nickname`: 在 UI 中显示，便于区分多台主机。

命令行打开远程文件时，会查找设置中匹配 `host/username/port` 的配置，并复用其中参数。虽然可以在 URL 中写密码（`user:password@host`），但不建议；推荐使用 SSH 公钥认证。

## 端口转发

需要本地访问远端端口时，可添加 `port_forwards`：

```json [settings]
{
  "ssh_connections": [
    {
      "host": "192.168.1.10",
      "port_forwards": [{ "local_port": 8080, "remote_port": 80 }]
    }
  ]
}
```

这会将本地 `localhost:8080` 转发到远端 `80`。可设置 `local_host`、`remote_host` 调整绑定地址：

```json [settings]
{
  "ssh_connections": [
    {
      "host": "192.168.1.10",
      "port_forwards": [
        {
          "local_port": 8080,
          "remote_port": 80,
          "local_host": "0.0.0.0",
          "remote_host": "docker-host"
        }
      ]
    }
  ]
}
```

## 设置作用域

远程项目生效的设置来源：

1. 本地 Zed 设置（如字体、界面偏好）
2. 远端 Zed 设置（取决于语言服务器路径、工具链，保存在远端 `~/.config/zed/settings.json`）
3. 项目设置（仓库内 `.zed/settings.json` 或 `.editorconfig`）

项目设置会被本地和远端同时读取；本地与远端的主配置互不共享。

- 项目设置：格式化、缩进、语言服务器等
- 远端设置：语言服务器路径、任务工具等
- 本地设置：UI、键位等

本地安装的扩展会自动同步至远端，确保语言服务器正常运行。

## Server 初始化流程

Zed 使用 SSH ControlMaster 建立连接，过程中会在 UI 显示提示：

1. 提示确认主机指纹或输入密钥密码
2. 建立主连接后，检查远端 `~/.zed_server` 是否存在匹配版本的 server 二进制
3. 若不存在或版本不符，默认从 `https://zed.dev` 下载；如设置 `upload_binary_over_ssh`，则先下载到本地再上传
4. 也可以自行下载或编译（`cargo build -p remote_server --release`），并放置在 `~/.zed_server/zed-remote-server-<channel>-<version>`，版本需与本地 Zed 完全一致

## 连接维护

Server 初始化后，Zed 会复用 ControlMaster 派生新的 SSH 连接运行远程守护进程。守护进程具备自动重连能力：

- 连接断开后自动尝试重连
- 若重连失败，必要时重新启动守护进程
- 未保存的改动默认缓存在本地，不会丢失

若遭遇连接问题，可通过 `cmd-shift-p` → `Open Log` 查看 `Zed.log` 日志，或在 GitHub / Discord（#remoting-feedback）寻求帮助。

## 支持的 SSH 参数

Zed 实际调用系统 `ssh`，支持常见选项：

- `-p`、`-l`：指定端口、用户名
- `-L`、`-R`：端口转发
- `-i`：指定私钥
- `-o`：自定义 SSH 参数
- `-J`、`-w`：跳板/代理
- `-F`：指定配置文件
- 其他常见开关如 `-4/-6/-A/-C/-X/-Y/-a/-b` 等

部分选项（如 `-t/-T`）由 Zed 自动处理，为保证稳定会禁止用户覆盖。

在 “Connect New Server” 对话框中可使用 Shell 风格引用，将参数写成一条命令。保存后会写入 `settings.json` 的 `ssh_connections` 数组，可直接编辑。

## 已知限制

- 远端终端中运行 `zed` 命令无法在本地打开文件（当前版本尚未支持）。

## 反馈

欢迎加入 [Zed Discord](https://zed.dev/community-links) 的 `#remoting-feedback` 频道交流体验，也可在 GitHub 提交 Issue。
