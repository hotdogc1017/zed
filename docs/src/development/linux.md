# 为 Linux 构建 Zed

## 存储库

克隆 [Zed 存储库](https://github.com/zed-industries/zed)。

## 依赖项

- 安装 [rustup](https://www.rust-lang.org/tools/install)

- 安装必要的系统库：

  ```sh
  script/linux
  ```

  如果您更喜欢手动安装系统库，可以在 `script/linux` 文件中找到所需的包列表。

### 后端依赖（可选） {#backend-dependencies}

如果您希望使用本地协作服务器开发 Zed 协作功能，请参阅：[本地协作](./local-collaboration.md) 文档。

### 链接器 {#linker}

在 Linux 上，Rust 的默认链接器是 [LLVM 的 `lld`](https://blog.rust-lang.org/2025/09/18/Rust-1.90.0/)。替代链接器，特别是 [Wild](https://github.com/davidlattimore/wild) 和 [Mold](https://github.com/rui314/mold) 可以显著改善干净和增量构建时间。

目前 Zed 在 CI 中使用 Mold，因为它更成熟。对于本地开发，推荐使用 Wild，因为它比 Mold 快 5-20%。

这些链接器可以使用 `script/install-mold` 和 `script/install-wild` 安装。

要将 Wild 用作默认链接器，请将这些行添加到您的 `~/.cargo/config.toml` 中：

```toml
[target.x86_64-unknown-linux-gnu]
linker = "clang"
rustflags = ["-C", "link-arg=--ld-path=wild"]

[target.aarch64-unknown-linux-gnu]
linker = "clang"
rustflags = ["-C", "link-arg=--ld-path=wild"]
```

要将 Mold 用作默认链接器：

```toml
[target.'cfg(target_os = "linux")']
rustflags = ["-C", "link-arg=-fuse-ld=mold"]
```

## 从源代码构建

一旦安装了依赖项，您可以使用 [Cargo](https://doc.rust-lang.org/cargo/) 构建 Zed。

对于编辑器的调试构建：

```sh
cargo run
```

要运行测试：

```sh
cargo test --workspace
```

在发布模式下，主要用户界面是 `cli` crate。您可以在开发中使用以下命令运行它：

```sh
cargo run -p cli
```

## 安装开发构建

您可以使用以下命令在您的机器上安装本地构建：

```sh
./script/install-linux
```

这将在发布模式下构建 zed 和 cli，并使它们在 `~/.local/bin/zed` 可用，将 .desktop 文件安装到 `~/.local/share`。

> **_注意_**: 如果您遇到类似以下的链接器错误：
>
> ```bash
> error: linking with `cc` failed: exit status: 1 ...
> = note: /usr/bin/ld: /tmp/rustcISMaod/libaws_lc_sys-79f08eb6d32e546e.rlib(f8e4fd781484bd36-bcm.o): in function `aws_lc_0_25_0_handle_cpu_env':
>           /aws-lc/crypto/fipsmodule/cpucap/cpu_intel.c:(.text.aws_lc_0_25_0_handle_cpu_env+0x63): undefined reference to `__isoc23_sscanf'
>           /usr/bin/ld: /tmp/rustcISMaod/libaws_lc_sys-79f08eb6d32e546e.rlib(f8e4fd781484bd36-bcm.o): in function `pkey_rsa_ctrl_str':
>           /aws-lc/crypto/fipsmodule/evp/p_rsa.c:741:(.text.pkey_rsa_ctrl_str+0x20d): undefined reference to `__isoc23_strtol'
>           /usr/bin/ld: /aws-lc/crypto/fipsmodule/evp/p_rsa.c:752:(.text.pkey_rsa_ctrl_str+0x258): undefined reference to `__isoc23_strtol'
>           collect2: error: ld returned 1 exit status
>   = note: some `extern` functions couldn't be found; some native libraries may need to be installed or have their path specified
>   = note: use the `-l` flag to specify native libraries to link
>   = note: use the `cargo:rustc-link-lib` directive to specify the native libraries to link with Cargo (see https://doc.rust-lang.org/cargo/reference/build-scripts.html#rustc-link-lib)
> error: could not compile `remote_server` (bin "remote_server") due to 1 previous error
> ```
>
> **原因**:
> 这是由 aws-lc-rs 中的已知错误引起的（不支持 GCC >= 14）：[FIPS fails to build with GCC >= 14](https://github.com/aws/aws-lc-rs/issues/569)
> & [GCC-14 - build failure for FIPS module](https://github.com/aws/aws-lc/issues/2010)
>
> 您可以参考 [linux: Linker error for remote_server when using script/install-linux](https://github.com/zed-industries/zed/issues/24880) 获取更多信息。
>
> **解决方法**:
> 将远程服务器目标设置为 `x86_64-unknown-linux-gnu`，如下所示：`export REMOTE_SERVER_TARGET=x86_64-unknown-linux-gnu; script/install-linux`

## Wayland & X11

Zed 支持 X11 和 Wayland。默认情况下，我们在运行时选择能找到的任何一种。如果您在 Wayland 上并希望在 X11 模式下运行，请使用环境变量 `WAYLAND_DISPLAY=''`。

## 打包 Zed 的注意事项

感谢您承担打包 Zed 的任务！

### 技术要求

Zed 有两个主要二进制文件：

- 您需要构建 `crates/cli` 并使其二进制文件在 `$PATH` 中以名称 `zed` 可用。
- 您需要构建 `crates/zed` 并将其放在 `$PATH/to/cli/../../libexec/zed-editor`。例如，如果您要将 cli 放在 `~/.local/bin/zed`，则将 zed 放在 `~/.local/libexec/zed-editor`。由于某些 Linux 发行版（特别是 Arch）不鼓励使用 `libexec`，您也可以将此二进制文件放在 `$PATH/to/cli/../../lib/zed/zed-editor`（例如 `~/.local/lib/zed/zed-editor`）。
- 如果您要提供 `.desktop` 文件，可以在 `crates/zed/resources/zed.desktop.in` 中找到模板，并使用 `envsubst` 填充所需的值。此文件也应重命名为 `$APP_ID.desktop`，以便文件[遵循 FreeDesktop 标准](https://github.com/zed-industries/zed/issues/12707#issuecomment-2168742761)。您还应使此桌面文件可执行（`chmod 755`）。
- 您需要确保安装了必要的库。您可以通过[检查系统上构建的二进制文件](https://github.com/zed-industries/zed/blob/935cf542aebf55122ce6ed1c91d0fe8711970c82/script/bundle-linux#L65-L67)来获取当前列表。
- 有关完整构建脚本的示例，请参阅 [script/bundle-linux](https://github.com/zed-industries/zed/blob/935cf542aebf55122ce6ed1c91d0fe8711970c82/script/bundle-linux)。
- 您可以通过使用环境变量 `ZED_UPDATE_EXPLANATION` 构建（或运行）Zed 来禁用 Zed 的自动更新，并为尝试手动更新 Zed 的用户提供说明。例如：`ZED_UPDATE_EXPLANATION="Please use flatpak to update zed."`。
- 确保将 `crates/zed/RELEASE_CHANNEL` 文件的内容更新为 'nightly'、'preview' 或 'stable'，不带换行符。这将导致 Zed 使用凭据管理器记住用户的登录。

### 其他注意事项

在 Zed，我们的首要任务是快速行动并为用户带来最新技术。长期以来，我们对软件运行缓慢、过时或难以配置感到沮丧，因此我们按照这些品味构建了我们的编辑器。

然而，我们意识到许多发行版有其他优先事项。我们希望与所有人合作，将 Zed 带到他们喜欢的平台上。但还有很长的路要走：

- Zed 是一个快速发展的早期项目。我们通常每周发布 2-3 个构建版本来修复用户报告的问题并发布主要功能。
- 在 Linux 系统上可能存在其他几个 `zed` 二进制文件（[1](https://openzfs.github.io/openzfs-docs/man/v2.2/8/zed.8.html)、[2](https://zed.brimdata.io/docs/commands/zed)）。如果您想因为这些问题重命名我们的 CLI 二进制文件，我们建议使用 `zedit`、`zeditor` 或 `zed-cli`。
- Zed 会自动安装正确版本的常用开发工具，方式与 rustup/rbenv/pyenv 等类似。我们理解这是有争议的，[请参阅此处](https://github.com/zed-industries/zed/issues/12589)。
- 我们允许用户从本地和 [zed-industries/extensions](https://github.com/zed-industries/extensions) 安装扩展。这些扩展可能会根据需要安装更多工具，例如语言服务器。从长远来看，我们希望使这更安全，[请参阅此处](https://github.com/zed-industries/zed/issues/12358)。
- Zed 默认连接到多个在线服务（AI、遥测、协作）。您的用户可以通过他们的 zed 设置或修补我们的[默认设置文件](https://github.com/zed-industries/zed/blob/main/assets/settings/default.json)来禁用 AI 和我们的遥测功能。
- 由于上述问题，zed 目前与沙盒不兼容，[请参阅此处](https://github.com/zed-industries/zed/pull/12006#issuecomment-2130421220)

## Flatpak

> Zed 当前的 Flatpak 集成在启动时会退出沙盒。依赖 Flatpak 沙盒功能的工作流程可能无法按预期工作。

要在本地构建和安装 Flatpak 包，请按照以下步骤操作：

1. 按照[此处](https://flathub.org/setup)的说明为您的发行版安装 Flatpak。
2. 运行 `script/flatpak/deps` 脚本来安装所需的依赖项。
3. 运行 `script/flatpak/bundle-flatpak`。
4. 现在包已安装，并在 `target/release/{app-id}.flatpak` 处有一个可用的包。

## 内存分析

[`heaptrack`](https://github.com/KDE/heaptrack) 对于诊断内存泄漏非常有用。要安装它：

```sh
$ sudo apt install heaptrack heaptrack-gui
$ cargo install cargo-heaptrack
```

然后，要构建并运行带有分析器附加的 Zed：

```sh
$ cargo heaptrack -b zed
```

当这个 zed 实例退出时，终端输出将包含一个运行 `heaptrack_interpret` 的命令，用于将 `*.raw.zst` 配置文件转换为 `*.zst` 文件，该文件可以传递给 `heaptrack_gui` 进行查看。

## 故障排除

### Cargo 错误声称依赖项使用了不稳定功能

尝试 `cargo clean` 和 `cargo build`。
