# 为 FreeBSD 构建 Zed

注意，FreeBSD 目前不是受支持的平台，因此这是一个正在进行中的工作。

## 存储库

克隆 [Zed 存储库](https://github.com/zed-industries/zed)。

## 依赖项

- 安装必要的系统包和 rustup：

  ```sh
  script/freebsd
  ```

  如果愿意，您可以检查 [`script/freebsd`](https://github.com/zed-industries/zed/blob/main/script/freebsd) 并手动执行步骤。

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

### WebRTC 注意事项

目前，在 FreeBSD 上构建 `webrtc-sys` 失败，原因是缺少上游支持和不可用的预构建二进制文件。因此，一些依赖 WebRTC 的协作功能（音频通话和屏幕共享）暂时被禁用。

有关更多信息，请参阅 [Issue #15309: FreeBSD Support] 和 [Discussion #29550: Unofficial FreeBSD port for Zed]。

## 故障排除

### Cargo 错误声称依赖项使用了不稳定功能

尝试 `cargo clean` 和 `cargo build`。
