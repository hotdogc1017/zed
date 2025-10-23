# 为 Windows 构建 Zed

> 以下命令可以在任何 shell 中执行。

## 存储库

克隆 [Zed 存储库](https://github.com/zed-industries/zed)。

## 依赖项

- 安装 [rustup](https://www.rust-lang.org/tools/install)

- 安装 [Visual Studio](https://visualstudio.microsoft.com/downloads/) 并选择可选组件 `MSVC v*** - VS YYYY C++ x64/x86 build tools` 和 `MSVC v*** - VS YYYY C++ x64/x86 Spectre-mitigated libs (latest)`（`v***` 是您的 VS 版本，`YYYY` 是您的 VS 发布年份。注意架构并根据需要更改为您的架构。）
- 或者，如果您更喜欢仅包含 MSVC 编译器工具的较精简安装程序，可以安装 [build tools](https://visualstudio.microsoft.com/visual-cpp-build-tools/)（+上述库）和 "Desktop development with C++" 工作负载。
  但请注意此安装不会被 rustup 自动识别。您必须首先启动此安装放置在开始菜单或 Windows Terminal 中的 "developer" shell（cmd/powershell）来初始化环境变量，然后编译。
- 根据您的系统安装 Windows 11 或 10 SDK，但确保您的机器上至少安装了 `Windows 10 SDK version 2104 (10.0.20348.0)`。您可以从 [Windows SDK Archive](https://developer.microsoft.com/windows/downloads/windows-sdk/) 下载它
- 安装 [CMake](https://cmake.org/download)（由 [一个依赖项](https://docs.rs/wasmtime-c-api-impl/latest/wasmtime_c_api/) 需要）。或者您可以通过 Visual Studio Installer 安装它，然后手动将 `bin` 目录添加到您的 `PATH` 中，例如：`C:\Program Files\Microsoft Visual Studio\2022\Community\Common7\IDE\CommonExtensions\Microsoft\CMake\CMake\bin`。

如果您无法编译 Zed，请确保在 Visual Studio 安装的情况下至少安装了以下组件：

```json [settings]
{
  "version": "1.0",
  "components": [
    "Microsoft.VisualStudio.Component.CoreEditor",
    "Microsoft.VisualStudio.Workload.CoreEditor",
    "Microsoft.VisualStudio.Component.VC.Tools.x86.x64",
    "Microsoft.VisualStudio.ComponentGroup.WebToolsExtensions.CMake",
    "Microsoft.VisualStudio.Component.VC.CMake.Project",
    "Microsoft.VisualStudio.Component.Windows11SDK.26100",
    "Microsoft.VisualStudio.Component.VC.Runtimes.x86.x64.Spectre"
  ],
  "extensions": []
}
```

或者如果只是 Build Tools，则安装以下组件：

```json [settings]
{
  "version": "1.0",
  "components": [
    "Microsoft.VisualStudio.Component.Roslyn.Compiler",
    "Microsoft.Component.MSBuild",
    "Microsoft.VisualStudio.Component.CoreBuildTools",
    "Microsoft.VisualStudio.Workload.MSBuildTools",
    "Microsoft.VisualStudio.Component.Windows10SDK",
    "Microsoft.VisualStudio.Component.VC.CoreBuildTools",
    "Microsoft.VisualStudio.Component.VC.Tools.x86.x64",
    "Microsoft.VisualStudio.Component.VC.Redist.14.Latest",
    "Microsoft.VisualStudio.Component.Windows11SDK.26100",
    "Microsoft.VisualStudio.Component.VC.CMake.Project",
    "Microsoft.VisualStudio.Component.TextTemplating",
    "Microsoft.VisualStudio.Component.VC.CoreIde",
    "Microsoft.VisualStudio.ComponentGroup.NativeDesktop.Core",
    "Microsoft.VisualStudio.Workload.VCTools",
    "Microsoft.VisualStudio.Component.VC.Runtimes.x86.x64.Spectre"
  ],
  "extensions": []
}
```

可以按以下方式获取列表：

- 打开 Visual Studio Installer
- 在 `Installed` 选项卡中单击 `More`
- 单击 `Export configuration`

### 后端依赖（可选） {#backend-dependencies}

如果您希望使用本地协作服务器开发 Zed 协作功能，请参阅：[本地协作](./local-collaboration.md) 文档。

### 注意事项

您应该修改 `data` 目录中的 `pg_hba.conf` 文件，将 `host` 方法使用 `trust` 而不是 `scram-sha-256`。否则，连接将失败并出现错误 `password authentication failed`。`pg_hba.conf` 文件通常位于 `C:\Program Files\PostgreSQL\17\data\pg_hba.conf`。修改后，文件应如下所示：

```conf
# IPv4 local connections:
host    all             all             127.0.0.1/32            trust
# IPv6 local connections:
host    all             all             ::1/128                 trust
```

此外，如果您使用的是非拉丁语 Windows 版本，必须将 `data` 目录中 `postgresql.conf` 文件中的 `lc_messages` 参数修改为 `English_United States.1252`（或您拥有的任何 UTF8 兼容编码）。否则，数据库将崩溃。`postgresql.conf` 文件应如下所示：

```conf
# lc_messages = 'Chinese (Simplified)_China.936' # locale for system error message strings
lc_messages = 'English_United States.1252'
```

之后，您应该重新启动 `postgresql` 服务。按 `win` 键 + `R` 启动 `Run` 窗口。输入 `services.msc` 并按 `OK` 按钮打开服务管理器。然后，找到 `postgresql-x64-XX` 服务，右键单击它，然后选择 `Restart`。

## 从源代码构建

一旦安装了依赖项，您可以使用 [Cargo](https://doc.rust-lang.org/cargo/) 构建 Zed。

对于调试构建：

```sh
cargo run
```

对于发布构建：

```sh
cargo run --release
```

要运行测试：

```sh
cargo test --workspace
```

## 从 msys2 安装

Zed 不支持为 Mingw-w64 构建的非官方 MSYS2 Zed 包。请将您可能遇到的任何与 [mingw-w64-zed](https://packages.msys2.org/base/mingw-w64-zed) 相关的问题报告给 [msys2/MINGW-packages/issues](https://github.com/msys2/MINGW-packages/issues?q=is%3Aissue+is%3Aopen+zed)。

请首先参考 [MSYS2 文档](https://www.msys2.org/docs/ides-editors/#zed)。

## 故障排除

### 设置 `RUSTFLAGS` 环境变量会破坏构建

如果您设置了 `RUSTFLAGS` 环境变量，它将覆盖 `.cargo/config.toml` 中的 `rustflags` 设置，这些设置是正确构建 Zed 所必需的。

由于这些设置可能随时变化，您收到的构建错误可能从链接器错误到其他奇怪的错误不等。

如果您想添加额外的 rust 标志，可以在 `.cargo/config.toml` 中执行以下操作之一：

在构建部分添加您的标志

```toml
[build]
rustflags = ["-C", "symbol-mangling-version=v0", "--cfg", "tokio_unstable"]
```

在 windows 目标部分添加您的标志

```toml
[target.'cfg(target_os = "windows")']
rustflags = [
    "--cfg",
    "windows_slim_errors",
    "-C",
    "target-feature=+crt-static",
]
```

或者，您可以在与 Zed 存储库相同的文件夹中创建一个新的 `.cargo/config.toml`（见下文）。如果您正在进行 CI 构建，这特别有用，因为您不必编辑原始的 `.cargo/config.toml`。

```
upper_dir
├── .cargo          // <-- 创建此文件夹
│   └── config.toml // <-- 创建此文件
└── zed
    ├── .cargo
    │   └── config.toml
    └── crates
        ├── assistant
        └── ...
```

在新的（上面的）`.cargo/config.toml` 中，如果我们想将 `--cfg gles` 添加到我们的 rustflags 中，它将如下所示

```toml
[target.'cfg(all())']
rustflags = ["--cfg", "gles"]
```

### Cargo 错误声称依赖项使用不稳定功能

尝试 `cargo clean` 和 `cargo build`。

### `STATUS_ACCESS_VIOLATION`

如果您使用 "rust-lld.exe" 链接器，可能会发生此错误。考虑尝试不同的链接器。

如果您使用全局配置，考虑将 Zed 存储库移动到嵌套目录，并在父目录中添加带有自定义链接器配置的 `.cargo/config.toml`。

有关更多信息，请参阅此问题 [#12041](https://github.com/zed-industries/zed/issues/12041)

### 选择了无效的 RC 路径

有时，根据应用于您的笔记本电脑的安全规则，您可能在编译 Zed 时收到以下错误：

```
error: failed to run custom build command for `zed(C:\Users\USER\src\zed\crates\zed)`

Caused by:
  process didn't exit successfully: `C:\Users\USER\src\zed\target\debug\build\zed-b24f1e9300107efc\build-script-build` (exit code: 1)
  --- stdout
  cargo:rerun-if-changed=../../.git/logs/HEAD
  cargo:rustc-env=ZED_COMMIT_SHA=25e2e9c6727ba9b77415588cfa11fd969612adb7
  cargo:rustc-link-arg=/stack:8388608
  cargo:rerun-if-changed=resources/windows/app-icon.ico
  package.metadata.winresource does not exist
  Selected RC path: 'bin\x64\rc.exe'

  --- stderr
  The system cannot find the path specified. (os error 3)
warning: build failed, waiting for other jobs to finish...
```

要解决此问题，您可以手动将 `ZED_RC_TOOLKIT_PATH` 环境变量设置为 RC 工具包路径。通常，您可以将其设置为：
`C:\Program Files (x86)\Windows Kits\10\bin\<SDK_version>\x64`。

有关更多信息，请参阅此 [issue](https://github.com/zed-industries/zed/issues/18393)。

### 构建失败：路径太长

构建时您可能会收到类似以下的错误

```
error: failed to get `pet` as a dependency of package `languages v0.1.0 (D:\a\zed-windows-builds\zed-windows-builds\crates\languages)`

Caused by:
  failed to load source for dependency `pet`

Caused by:
  Unable to update https://github.com/microsoft/python-environment-tools.git?rev=ffcbf3f28c46633abd5448a52b1f396c322e0d6c#ffcbf3f2

Caused by:
  path too long: 'C:/Users/runneradmin/.cargo/git/checkouts/python-environment-tools-903993894b37a7d2/ffcbf3f/crates/pet-conda/tests/unix/conda_env_without_manager_but_found_in_history/some_other_location/conda_install/conda-meta/python-fastjsonschema-2.16.2-py310hca03da5_0.json'; class=Filesystem (30)
```

要解决此问题，您可以为 git 和 Windows 启用长路径支持。

对于 git：`git config --system core.longpaths true`

对于 Windows，使用此 PS 命令：

```powershell
New-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\FileSystem" -Name "LongPathsEnabled" -Value 1 -PropertyType DWORD -Force
```

有关此问题的更多信息，请参阅 [win32 文档](https://learn.microsoft.com/en-us/windows/win32/fileio/maximum-file-path-limitation?tabs=powershell)

（请注意，启用长路径支持后需要重新启动系统）

### 图形问题

#### Zed 无法启动

目前，Zed 在 Windows 上使用 Vulkan 作为其图形 API。然而，Vulkan 在 Windows 上并不总是最可靠的，因此如果 Zed 无法启动，很可能是与 Vulkan 相关的问题。

您可以在以下位置检查 Zed 日志：
`C:\Users\YOU\AppData\Local\Zed\logs\Zed.log`

如果您看到类似以下的消息：

- `Zed failed to open a window: NoSupportedDeviceFound`
- `ERROR_INITIALIZATION_FAILED`
- `GPU Crashed`
- `ERROR_SURFACE_LOST_KHR`

那么 Vulkan 可能无法在您的系统上正常工作。在大多数情况下，更新您的 GPU 驱动程序可能有助于解决此问题。

如果日志中没有与 Vulkan 相关的内容，而您恰好安装了 Bandicam，请尝试卸载它。Zed 目前与 Bandicam 不兼容。
