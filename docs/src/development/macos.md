# 为 macOS 构建 Zed

## 存储库

克隆 [Zed 存储库](https://github.com/zed-industries/zed)。

## 依赖项

- 安装 [rustup](https://www.rust-lang.org/tools/install)

- 从 macOS App Store 或 [Apple Developer](https://developer.apple.com/download/all/) 网站安装 [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12)。请注意这需要开发者账户。

> 确保安装后启动 Xcode，并安装 macOS 组件，这是默认选项。

- 安装 [Xcode 命令行工具](https://developer.apple.com/xcode/resources/)

  ```sh
  xcode-select --install
  ```

- 确保 Xcode 命令行工具使用您新安装的 Xcode 副本：

  ```sh
  sudo xcode-select --switch /Applications/Xcode.app/Contents/Developer
  sudo xcodebuild -license accept
  ```

- 安装 `cmake`（由 [一个依赖项](https://docs.rs/wasmtime-c-api-impl/latest/wasmtime_c_api/) 需要）

  ```sh
  brew install cmake
  ```

### 后端依赖（可选） {#backend-dependencies}

如果您希望使用本地协作服务器开发 Zed 协作功能，请参阅：[本地协作](./local-collaboration.md) 文档。

## 从源代码构建 Zed

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

## 故障排除

### 编译 metal 着色器错误

```sh
error: failed to run custom build command for gpui v0.1.0 (/Users/path/to/zed)`**

xcrun: error: unable to find utility "metal", not a developer tool or in PATH
```

尝试 `sudo xcode-select --switch /Applications/Xcode.app/Contents/Developer`

如果您在 macOS 26 上，尝试 `xcodebuild -downloadComponent MetalToolchain`

### Cargo 错误声称依赖项使用不稳定功能

尝试 `cargo clean` 和 `cargo build`。

### 错误：找不到 'dispatch/dispatch.h' 文件

如果您遇到类似以下错误：

```sh
src/platform/mac/dispatch.h:1:10: fatal error: 'dispatch/dispatch.h' file not found

Caused by:
  process didn't exit successfully

  --- stdout
  cargo:rustc-link-lib=framework=System
  cargo:rerun-if-changed=src/platform/mac/dispatch.h
  cargo:rerun-if-env-changed=TARGET
  cargo:rerun-if-env-changed=BINDGEN_EXTRA_CLANG_ARGS_aarch64-apple-darwin
  cargo:rerun-if-env-changed=BINDGEN_EXTRA_CLANG_ARGS_aarch64_apple_darwin
  cargo:rerun-if-env-changed=BINDGEN_EXTRA_CLANG_ARGS
```

此文件是 Xcode 的一部分。确保您已安装 Xcode 命令行工具并设置了正确的路径：

```sh
xcode-select --install
sudo xcode-select --switch /Applications/Xcode.app/Contents/Developer
```

此外，设置 `BINDGEN_EXTRA_CLANG_ARGS` 环境变量：

```sh
export BINDGEN_EXTRA_CLANG_ARGS="--sysroot=$(xcrun --show-sdk-path)"
```

然后清理并重新构建项目：

```sh
cargo clean
cargo run
```

### 测试因 `Too many open files (os error 24)` 失败

此错误似乎是由操作系统资源限制引起的。安装并使用 `cargo-nextest` 运行测试应该可以解决此问题。

- `cargo install cargo-nextest --locked`
- `cargo nextest run --workspace --no-fail-fast`

## 提示与技巧

### 避免持续重新构建

如果您发现 Zed 持续重新构建根 crate，可能是因为您将开发 Zed 指向了代码库本身。

这会导致问题，因为 `cargo run` 导出了一堆环境变量，这些变量被在开发构建的 Zed 中运行的 `rust-analyzer` 拾取。这些环境变量又被传递给 `cargo check`，这会使我们依赖的一些 crate 的构建缓存失效。

您可以使用 `cargo run ~/path/to/other/project` 轻松避免在检出的 Zed 代码库上运行构建的二进制文件，以确保不会遇到此问题。

### 加速验证

如果您经常构建 Zed，您可能会发现 macOS 持续验证新的构建，这可能会在您的迭代周期中增加几秒钟。

要解决此问题，您可以：

- 运行 `sudo spctl developer-mode enable-terminal` 以在系统设置中启用开发者工具面板。
- 在系统设置中，搜索 "Developer Tools" 并将您的终端（例如 iTerm 或 Ghostty）添加到 "Allow applications to use developer tools" 下的列表中
- 重新启动您的终端。

感谢 nextest 开发者发布 [此内容](https://nexte.st/docs/installation/macos/#gatekeeper)。
