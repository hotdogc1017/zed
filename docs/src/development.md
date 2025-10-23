# 开发 Zed

查看特定平台的从源代码构建 Zed 的说明：

- [macOS](./development/macos.md)
- [Linux](./development/linux.md)
- [Windows](./development/windows.md)

如果您想开发协作功能，另外请参阅：

- [本地协作](./development/local-collaboration.md)

## 钥匙串访问

Zed 在系统钥匙串中存储密钥。

然而，在 macOS（可能还有其他平台）上运行开发版本的 Zed 时，尝试访问钥匙串会导致大量钥匙串提示，需要反复输入密码。

在 macOS 上，这是由于开发版本没有稳定的身份造成的。即使您选择"始终允许"选项，操作系统仍会在二进制文件发生变化时再次提示您输入密码。

这很快变得烦人并阻碍开发速度。

这就是为什么默认情况下，在运行 Zed 的开发版本时，使用替代的凭据提供程序来绕过系统钥匙串。

> 注意：这**仅**适用于开发版本。对于所有非开发发布渠道，始终使用系统钥匙串。

如果您需要在开发版本中使用真实的系统钥匙串测试某些内容，请设置以下环境变量运行 Zed：

```
ZED_DEVELOPMENT_USE_KEYCHAIN=1
```

## 性能测量

Zed 包含一个帧时间测量系统，可用于分析渲染每一帧所需的时间。这在比较不同版本之间的渲染性能或优化帧渲染代码时特别有用。

### 使用 ZED_MEASUREMENTS

要启用性能测量，请设置 `ZED_MEASUREMENTS` 环境变量：

```sh
export ZED_MEASUREMENTS=1
```

启用后，Zed 将向 stderr 打印帧渲染计时信息，显示渲染每一帧所需的时间。

### 性能比较工作流程

以下是比较不同版本之间帧渲染性能的典型工作流程：

1. **启用测量：**

   ```sh
   export ZED_MEASUREMENTS=1
   ```

2. **测试第一个版本：**

   - 检出您想要测量的提交
   - 在发布模式下运行 Zed 并使用 5-10 秒：`cargo run --release &> version-a`

3. **测试第二个版本：**

   - 检出您想要比较的另一个提交
   - 在发布模式下运行 Zed 并使用 5-10 秒：`cargo run --release &> version-b`

4. **生成比较：**

   ```sh
   script/histogram version-a version-b
   ```

`script/histogram` 工具可以接受任意数量的测量文件，并生成直方图可视化，比较提供的版本之间的帧渲染性能数据。

### 使用 `util_macros::perf`

对于基准测试单元测试，使用 `util_macros` crate 中的 `#[perf]` 属性注释它们。然后运行 `cargo perf-test -p $CRATE` 对它们进行基准测试。有关深入示例和解释，请参阅 `crates/util_macros` 和 `tooling/perf` 上的 rustdoc 文档。

## 贡献者链接

- [CONTRIBUTING.md](https://github.com/zed-industries/zed/blob/main/CONTRIBUTING.md)
- [发布](./development/releases.md)
- [调试崩溃](./development/debugging-crashes.md)
- [行为准则](https://zed.dev/code-of-conduct)
- [Zed 贡献者许可协议](https://zed.dev/cla)
