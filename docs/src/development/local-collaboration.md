# 本地协作

1. 确保您有权访问我们的云基础设施。如果您没有访问权限，目前无法进行本地协作。

2. 确保您已为您的平台安装了 Zed 的依赖项：

- [macOS](#macos)
- [Linux](#linux)
- [Windows](#backend-windows)

请注意，在 Windows 上 `collab` 只能使用 MSVC 工具链编译

3. 克隆我们的云存储库并按照云 README 中的说明操作

4. 为您的平台设置本地数据库：

- [macOS & Linux](#database-unix)
- [Windows](#database-windows)

5. 运行 collab：

- [macOS & Linux](#run-collab-unix)
- [Windows](#run-collab-windows)

## 后端依赖

如果您正在开发 Zed 的协作功能，您需要安装 zed 的 `collab` 服务器的依赖项：

- PostgreSQL
- LiveKit
- Foreman

您可以本地安装这些依赖项，或在 Docker 下运行它们。

### macOS

1. 安装 [Postgres.app](https://postgresapp.com) 或通过 homebrew 安装 [postgresql](https://formulae.brew.sh/formula/postgresql@15)：

   ```sh
   brew install postgresql@15
   ```

2. 安装 [Livekit](https://formulae.brew.sh/formula/livekit) 和 [Foreman](https://formulae.brew.sh/formula/foreman)

   ```sh
   brew install livekit foreman
   ```

- 按照 [collab README](https://github.com/zed-industries/zed/blob/main/crates/collab/README.md) 中的步骤配置用于集成测试的 Postgres 数据库

或者，如果您安装了 [Docker](https://www.docker.com/)，可以使用 Docker Compose 启动所有 `collab` 依赖项。

### Linux

1. 安装 [Postgres](https://www.postgresql.org/download/linux/)

   ```sh
   sudo apt-get install postgresql                    # Ubuntu/Debian
   sudo pacman -S postgresql                          # Arch Linux
   sudo dnf install postgresql postgresql-server      # RHEL/Fedora
   sudo zypper install postgresql postgresql-server   # OpenSUSE
   ```

2. 安装 [Livekit](https://github.com/livekit/livekit-cli)

   ```sh
   curl -sSL https://get.livekit.io/cli | bash
   ```

3. 安装 [Foreman](https://theforeman.org/manuals/3.15/quickstart_guide.html)

### Windows {#backend-windows}

> 此部分仍在开发中。说明尚未完成。

- 安装 [Postgres](https://www.postgresql.org/download/windows/)
- 安装 [Livekit](https://github.com/livekit/livekit)，可选地您可以将 `livekit-server` 二进制文件添加到您的 `PATH` 中。

或者，如果您安装了 [Docker](https://www.docker.com/)，可以使用 Docker Compose 启动所有 `collab` 依赖项。

### Docker {#Docker}

如果您有 docker 或 podman 可用，可以使用 Docker Compose 在容器内运行后端依赖项：

```sh
docker compose up -d
```

## 数据库设置

在本地运行 `collab` 服务器之前，您需要设置一个 `zed` Postgres 数据库。

### 在 macOS 和 Linux 上 {#database-unix}

```sh
script/bootstrap
```

此脚本将设置 `zed` Postgres 数据库，并用一些用户填充它。它需要互联网访问，因为它从 GitHub API 获取一些用户。

该脚本将使用以下定义的各种内容填充数据库：

```sh
cat crates/collab/seed.default.json
```

要使用不同的管理员用户集，您可以创建该 json 文件的自己的版本并导出 `SEED_PATH` 环境变量。请注意，管理员列表中列出的用户名当前必须对应于有效的 GitHub 用户。

```json [settings]
{
  "admins": ["admin1", "admin2"],
  "channels": ["zed"]
}
```

### 在 Windows 上 {#database-windows}

```powershell
.\script\bootstrap.ps1
```

## 本地测试协作功能

### 在 macOS 和 Linux 上 {#run-collab-unix}

确保 Postgres 已配置并运行，然后运行 Zed 的协作服务器和 `livekit` 开发服务器：

```sh
foreman start
# 或
docker compose up
```

或者，如果您不测试语音和屏幕共享，可以只运行 `collab` 和 `cloud`，而不运行 `livekit` 开发服务器：

```sh
cargo run -p collab -- serve all
```

```sh
cd ../cloud; cargo make dev
```

在新的终端中，运行两个或更多 Zed 实例。

```sh
script/zed-local -3
```

此脚本启动一到四个 Zed 实例，具体取决于 `-2`、`-3` 或 `-4` 标志。每个实例都将连接到本地 `collab` 服务器，并以 `.admins.json` 或 `.admins.default.json` 中的不同用户身份登录。

### 在 Windows 上 {#run-collab-windows}

由于 `foreman` 在 Windows 上不可用，您可以在单独的终端中运行以下命令：

```powershell
cargo run --package=collab -- serve all
```

如果您已将 `livekit-server` 二进制文件添加到您的 `PATH` 中，可以运行：

```powershell
livekit-server --dev
```

否则，

```powershell
.\path\to\livekit-serve.exe --dev
```

您还需要启动云服务器：

```powershell
cd ..\cloud; cargo make dev
```

在新的终端中，运行两个或更多 Zed 实例。

```powershell
node .\script\zed-local -2
```

请注意，这需要 `node.exe` 在您的 `PATH` 中。

## 运行本地 collab 服务器

> [!NOTE]
> 由于我们认证系统的最新更改，Zed 将无法与本地 collab 服务器进行身份验证，因此无法使用它。

如果您想运行自己的 zed 协作服务版本，可以这样做，但请注意这仍在开发中，并且不支持身份验证或扩展。

配置通过环境变量完成。默认情况下，它将从 [`.env.toml`](https://github.com/zed-industries/zed/blob/main/crates/collab/.env.toml) 读取配置，您应该将其作为设置指南。

默认情况下，Zed 假设 DATABASE_URL 是 Postgres 数据库，但您可以通过使用 `--features sqlite` 编译并使用带有 `?mode=rwc` 的 sqlite DATABASE_URL 来使其使用 Sqlite。

要进行身份验证，您必须首先通过创建一个至少包含您的 GitHub 用户名的 seed.json 文件来配置服务器。这将用于按需创建用户。

```json [settings]
{
  "admins": ["nathansobo"]
}
```

默认情况下，collab 服务器在首次创建数据库时会填充它，但如果您想添加更多用户，可以使用 `SEED_PATH=./seed.json cargo run -p collab seed` 显式重新填充它们

然后在运行 zed 客户端时，您必须指定两个环境变量：`ZED_ADMIN_API_TOKEN`（应与 .env.toml 中的 `API_TOKEN` 值匹配）和 `ZED_IMPERSONATE`（应与您的 seed.json 中的一个用户匹配）
