# 工作区持久化

Zed 使用本地 SQLite 数据库存储工作区与项目相关的信息。例如：每个项目当前打开的标签与分屏、各文件的滚动位置、近期打开的项目列表等。数据库位置如下：

- macOS：`~/Library/Application Support/Zed`
- Linux / FreeBSD：`~/.local/share/zed`（或 `XDG_DATA_HOME` / `FLATPAK_XDG_DATA_HOME`）
- Windows：`%LOCALAPPDATA%\Zed`

数据库命名规则为 `0-<zed_channel>`：

- 稳定版：`0-stable`
- 预览版：`0-preview`

**若遇到工作区持久化异常，删除数据库后重启 Zed 通常能解决问题，说明文件可能曾被破坏。** 若重建后问题仍在，请[提交 Issue](https://github.com/zed-industries/zed/issues/new?template=10_bug_report.yml)。

## 设置

可用以下配置调整恢复行为：

```json [settings]
{
  "restore_on_startup": "last_session", // last_session / last_workspace / none
  "restore_on_file_reopen": true,        // 重新打开文件时恢复选择、折叠、滚动等状态
  "close_on_file_delete": false          // 磁盘上删除文件时是否自动关闭标签
}
```
