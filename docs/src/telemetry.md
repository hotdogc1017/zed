# Zed 的遥测

Zed 会收集匿名使用数据，帮助团队了解用户如何使用应用，以及他们遇到的问题类型。

## 配置遥测

你可以完全掌控 Zed 发送的数据。在命令面板执行 {#action zed::OpenSettings}（{#kb zed::OpenSettings}），打开 `settings.json`，加入并调整：

```json [settings]
"telemetry": {
  "diagnostics": false,
  "metrics": false
},
```

## 数据流向

遥测信息会从客户端发送到我们的服务器，并通过自建代理转发到分析平台，便于随时切换服务。目前我们使用：

- [Sentry](https://sentry.io)：崩溃监控，存储诊断事件
- [Snowflake](https://snowflake.com)：数据仓库，存储诊断与指标事件
- [Hex](https://www.hex.tech)：仪表盘与数据探索，读取 Snowflake 数据
- [Amplitude](https://www.amplitude.com)：仪表盘与分析，同样基于 Snowflake 数据

## 遥测类型

### 诊断信息

崩溃报告由 [minidump](https://learn.microsoft.com/en-us/windows/win32/debug/minidump-files) 及额外调试信息组成，会在崩溃后首次重新启动应用时上传。我们通过仪表盘监控用户遇到的问题频率与严重程度，自动收集报告可帮助我们在用户未提交 Issue 的情况下尽快修复。仪表盘曲线也能粗略反映 Zed 的稳定性。

附加数据的结构定义可在仓库 `crates/telemetry_events/src/telemetry_events.rs` 中的 `Panic` 结构体看到。更多内容请参阅[调试崩溃](./development/debugging-crashes.md)。

### 客户端使用数据 {#client-metrics}

为改进产品并了解实际使用情况，Zed 可选择性收集如下信息：

- 已打开文件的扩展名
- 编辑器内使用的功能与工具
- 项目统计（如文件数量）
- 项目中检测到的框架

这些数据不会包含你的代码内容或敏感细节。事件通过 HTTPS 上传，并进行速率限制以避免占用带宽。

使用数据会关联到一个安全的随机遥测 ID，并可能与邮箱对应，其目的在于：

1. 便于在保护隐私的前提下分析长期使用模式；
2. 便于针对特定用户群发出反馈请求。

你可以通过命令 {#action zed::OpenTelemetryLog}（或菜单 `Help > View Telemetry Log`）查看 Zed 上报的指标日志。要审查所有事件类型与字段，可浏览仓库中 `Event` 枚举及其结构体定义。

### 服务端使用数据 {#metrics}

使用 Zed 托管功能时，我们可能会收集、生成并处理数据，以支持用户并改进托管服务，例如限流元数据、计费/Token 消耗等。除非用户主动分享，Zed 不会持久化存储用户内容，也不会用其训练或改进 AI 功能，并与 Anthropic 签订了零数据保留协议。

关于数据采集的更多说明（以及 AI 相关内容完全 opt-in 的承诺）可参考 [AI Improvement](./ai/ai-improvement.md)。

## 联系方式

若对遥测有任何疑虑，欢迎[在 GitHub 上提交 Issue](https://github.com/zed-industries/zed/issues/new/choose)。
