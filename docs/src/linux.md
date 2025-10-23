# 在 Linux 上使用 Zed

## 标准安装

推荐使用下载页提供的脚本安装 Zed：

```sh
curl -f https://zed.dev/install.sh | sh
```

若想提前体验预览版（通常比稳定版快约一周更新），可执行：

```sh
curl -f https://zed.dev/install.sh | ZED_CHANNEL=preview sh
```

脚本安装的 Zed 在以下环境表现最佳：

- 搭载兼容 Vulkan 的 GPU（例如运行 Linux 的 Apple M 系列笔记本）
- 使用系统级 glibc（NixOS、Alpine 默认不包含）
  - x86_64：glibc ≥ 2.31（Ubuntu 20 及以上）
  - aarch64：glibc ≥ 2.35（Ubuntu 22 及以上）

Nix 与 Alpine 有第三方包可用（可能不是最新）。若希望使用官方构建，可安装 glibc 兼容层：NixOS 可尝试 [nix-ld](https://github.com/Mic92/nix-ld)，Alpine 可使用 [gcompat](https://wiki.alpinelinux.org/wiki/Running_glibc_programs)。

以下情况需自行编译：

- 除 x86_64 / aarch64 以外的架构（如 32 位或 RISC-V）
- RHEL 8 / Rocky 8 / Alma 8 / Amazon Linux 2（所有架构）
- RHEL 9.x / Rocky 9.3 / Alma 8 / Amazon Linux 2023 的 aarch64（x86_64 可直接使用）

## 其他安装方式

Zed 开源，可[从源码构建](./development/linux.md)。

### 软件源 / 包管理器

部分发行版或第三方仓库已提供打包版本（通常名为 `zed` 或 `zed-editor`）：

- Flathub：[`dev.zed.Zed`](https://flathub.org/apps/dev.zed.Zed)
- Arch：[`zed`](https://archlinux.org/packages/extra/x86_64/zed/)；AUR 上还有 [`zed-git`](https://aur.archlinux.org/packages/zed-git)、[`zed-preview`](https://aur.archlinux.org/packages/zed-preview)、[`zed-preview-bin`](https://aur.archlinux.org/packages/zed-preview-bin)
- Alpine：`zed`（[aarch64](https://pkgs.alpinelinux.org/package/edge/testing/aarch64/zed)、[x86_64](https://pkgs.alpinelinux.org/package/edge/testing/x86_64/zed)）
- Nix：`zed-editor`（[unstable](https://search.nixos.org/packages?channel=unstable&show=zed-editor)）
- Fedora/Ultramarine (Terra)：[`zed`](https://github.com/terrapkg/packages/tree/frawhide/anda/devs/zed/stable) 等
- Solus、Parabola、Manjaro、ALT Linux、AOSC 等均有社区维护包

更多仓库列表可查阅 [Repology](https://repology.org/project/zed-editor/versions)。

> 安装第三方包时请注意版本可能滞后，某些包会将可执行文件改名为 `zedit`/`zeditor` 以避免冲突。

若你愿意维护官方包，我们整理了[打包注意事项](./development/linux.md#notes-for-packaging-zed)供参考。

#### Debian 社区仓库

社区维护的 Debian 包见 <https://debian.griffo.io/>，具体版本的构建与使用说明请查阅仓库 README：<https://github.com/dariogriffo/zed-debian>。

### 手动下载

亦可下载 `.tar.gz` 手动安装（脚本内部使用同一包，可自定义安装目录）：

- [zed-linux-x86_64.tar.gz](https://zed.dev/api/releases/stable/latest/zed-linux-x86_64.tar.gz)（[预览版](https://zed.dev/api/releases/preview/latest/zed-linux-x86_64.tar.gz)）
- [zed-linux-aarch64.tar.gz](https://zed.dev/api/releases/stable/latest/zed-linux-aarch64.tar.gz)（[预览版](https://zed.dev/api/releases/preview/latest/zed-linux-aarch64.tar.gz)）

示例安装步骤：

```sh
mkdir -p ~/.local
# 解压到 ~/.local/zed.app/
tar -xvf <下载文件>.tar.gz -C ~/.local
# 将可执行文件链接到 PATH
ln -sf ~/.local/zed.app/bin/zed ~/.local/bin/zed
```

若需在 XDG 桌面环境中显示图标，可安装 `.desktop` 文件：

```sh
cp ~/.local/zed.app/share/applications/zed.desktop ~/.local/share/applications/dev.zed.Zed.desktop
sed -i "s|Icon=zed|Icon=$HOME/.local/zed.app/share/icons/hicolor/512x512/apps/zed.png|g" ~/.local/share/applications/dev.zed.Zed.desktop
sed -i "s|Exec=zed|Exec=$HOME/.local/zed.app/libexec/zed-editor|g" ~/.local/share/applications/dev.zed.Zed.desktop
```

## 卸载 Zed

### 脚本安装

若通过脚本安装，可使用 `--uninstall` 直接卸载：

```sh
zed --uninstall
```

按提示选择是否保留配置文件。若 `zed` 不在 PATH，可尝试：

```sh
$HOME/.local/bin/zed --uninstall
# 或
$HOME/.local/zed.app/bin/zed --uninstall
```

若安装在其他目录，请进入对应路径运行 `zed --uninstall`。

### 包管理器

若通过发行版包安装，请参阅该包管理器的卸载说明。

## 故障排查

我们主要在 Ubuntu 标准环境测试，但期望在各类发行版上正常运行。

### 无法启动

若出现 `/lib64/libc.so.6: version 'GLIBC_2.29' not found` 等错误，说明 glibc 版本过旧，可升级系统或[从源码编译](./development/linux.md)。

### 图形相关问题

#### 无法打开窗口

Zed 依赖 GPU，通过 [Vulkan](https://www.vulkan.org/) 与显卡通信。如提示 `NoSupportedDeviceFound`，表示未找到支持的 GPU。可尝试运行 `vkcube`（通常包含在 `vulkaninfo` 或 `vulkan-tools` 包中）：

```sh
vkcube
```

> 可用 `vkcube -m x11` 或 `vkcube -m wayland` 测试不同后端；某些发行版提供 `vkcube-wayland` 可直接运行。

若无法正常显示旋转方块，请安装匹配的 Vulkan 驱动。Zed 使用的 GPU 可在日志 `~/.local/share/zed/logs/Zed.log` 中查到（搜索 `Using GPU:`）。

如遇 `ERROR_INITIALIZATION_FAILED`、`ERROR_SURFACE_LOST_KHR` 等，可尝试更换驱动或指定不同 GPU。例如：

- 修改 `/etc/prime-discrete` 以切换独显/核显（取决于 PRIME 配置）
- 运行 Zed 前设置 `DRI_PRIME=1` 强制使用独显

更多案例参考 Issue [#14225](https://github.com/zed-industries/zed/issues/14225)。

## 求助与反馈

如仍遇问题，建议：

1. 查看日志 `~/.local/share/zed/logs/Zed.log`
2. 在 GitHub 或社区搜索、提问
3. 提供发行版、驱动、Vulkan 诊断信息，便于定位

欢迎贡献安装脚本或打包支持，让更多 Linux 用户受益。
