# GDScript

Godot [GDScript](https://gdscript.com/) 的语言支持由社区维护的 [GDScript 扩展](https://github.com/grndctrl/zed-gdscript) 提供。
如需反馈问题，请访问：[https://github.com/grndctrl/zed-gdscript/issues](https://github.com/grndctrl/zed-gdscript/issues)

- Tree-sitter：[PrestonKnopp/tree-sitter-gdscript](https://github.com/PrestonKnopp/tree-sitter-gdscript) 与 [PrestonKnopp/tree-sitter-godot-resource](https://github.com/PrestonKnopp/tree-sitter-godot-resource)
- 语言服务器：[gdscript-language-server](https://github.com/godotengine/godot)

## 安装步骤

1. 下载并安装 [macOS 版 Godot](https://godotengine.org/download/macos/)。
2. 解压 Godot.app 并拖入 /Applications 目录。
3. 启动 Godot.app 并打开你的项目（示例项目亦可）。
4. 在 Godot 中打开 Editor 菜单 -> Editor Settings，左侧导航滚动到 `Text Editor -> External`：
   1. Use External Editor：勾选“✅ On”
   2. Exec path：`/Applications/Zed.app/Contents/MacOS/zed`
   3. Exec flags：`{project} {file}:{line}:{col}`
   4. 关闭设置以保存。
5. 在 Godot 中双击任意 `.gd` 脚本即可启动 Zed。

<!--
待补充：GDScript 在 Linux 上的安装说明
-->

## 使用说明

当 Godot 正在运行时，GDScript 扩展会连接到 Godot 运行时提供的语言服务器，从而支持跳转定义、按住 Cmd 查看悬停信息等语言服务器功能。

> 注意：如果 Zed 已经在其他工作区中运行，从 Godot 启动可能会失败。请先退出 Zed，再重新尝试。
