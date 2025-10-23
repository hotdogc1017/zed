# 系统要求

## Apple

### macOS

Zed 支持以下 macOS 版本：

| 版本         | 代号     | Apple 状态 | Zed 状态         |
| ------------ | -------- | ---------- | ---------------- |
| macOS 15.x   | Sequoia  | 官方支持   | 支持             |
| macOS 14.x   | Sonoma   | 官方支持   | 支持             |
| macOS 13.x   | Ventura  | 官方支持   | 支持             |
| macOS 12.x   | Monterey | 2024-09-16 停止更新 | 支持 |
| macOS 11.x   | Big Sur  | 2023-09-26 停止更新 | 部分支持 |
| macOS 10.15.x| Catalina | 2022-09-12 停止更新 | 部分支持 |

标记为“部分支持”的版本（Big Sur、Catalina）不支持通过 Zed 协作进行屏幕共享。该功能依赖 [LiveKit SDK](https://livekit.io)，而 LiveKit 又需要 macOS 12（Monterey）及以上才提供的 [ScreenCaptureKit.framework](https://developer.apple.com/documentation/screencapturekit/)。

### Mac 硬件

满足上述 macOS 要求的 Intel（x86_64）或 Apple 芯片（aarch64）机型均受支持，例如：

- MacBook Pro（2015 年初及更新机型）
- MacBook Air（2015 年初及更新机型）
- MacBook（2016 年初及更新机型）
- Mac Mini（2014 年末及更新机型）
- Mac Pro（2013 年末或更新机型）
- iMac（2015 年末及更新机型）
- iMac Pro（全部机型）
- Mac Studio（全部机型）

## Linux

Zed 支持 64 位 Intel/AMD（x86_64）与 64 位 ARM（aarch64）处理器。

需要满足以下条件：

- Vulkan 1.3 驱动
- 以下桌面 portal：
  - `org.freedesktop.portal.FileChooser`
  - `org.freedesktop.portal.OpenURI`
  - `org.freedesktop.portal.Secret` 或 `org.freedesktop.Secrets`

## Windows

Zed 支持下列 Windows 版本：

| 版本                | Microsoft 状态 | Zed 状态 |
| ------------------- | --------------- | -------- |
| Windows 11（全部版本） | 官方支持        | 支持     |
| Windows 10（64 位） | 官方支持        | 支持     |

### Windows 硬件

满足上述 Windows 要求的 64 位 Intel/AMD 处理器均可使用，具体包括：

- Windows 11（64 位）
- Windows 10（64 位）
- 显卡需支持 DirectX 11（大多数 2012 年后的 PC 都符合）
- 驱动需为 NVIDIA / AMD / Intel 官方驱动（非 Microsoft Basic Display Adapter）

## FreeBSD

暂未提供官方发行版，可[从源码构建](./development/freebsd.md)。

## Web

目前不支持 Web 版本，详情参见 [平台支持议题](https://github.com/zed-industries/zed/issues/5391)。
