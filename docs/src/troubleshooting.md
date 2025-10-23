# 故障排查

## 查看 Zed 日志

排查问题时，首先建议检查 Zed 日志，里面往往能找到线索。可通过命令面板执行 {#action zed::OpenLog}（macOS `cmd-shift-p`，Windows/Linux `ctrl-shift-p`）查看最近 1000 行日志。

若需完整文件，可在不同系统对应位置找到：

- macOS：`~/Library/Logs/Zed/Zed.log`
- Windows：`C:\Users\<用户名>\AppData\Local\Zed\logs\Zed.log`
- Linux：`~/.local/share/zed/logs/Zed.log` 或 `$XDG_DATA_HOME`

> 提示：在开发 Zed 扩展等场景，可实时跟踪日志，例如：
> ```sh
> tail -f ~/Library/Logs/Zed/Zed.log
> ```

日志通常能提供足够上下文，以便自助定位问题；若需反馈，可将相关错误附在 [GitHub Issue](https://github.com/zed-industries/zed/issues/new/choose)，或在 [Discord 社区](https://zed.dev/community-links#forums-and-discussions) 与开发团队交流。
