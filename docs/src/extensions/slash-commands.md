# 斜杠命令

扩展可以提供斜杠命令，供助手使用。

## 示例扩展

要查看提供斜杠命令的扩展的工作示例，请查看 [`slash-commands-example` 扩展](https://github.com/zed-industries/zed/tree/main/extensions/slash-commands-example)。

如果您想亲自试用，可以将此扩展[安装为开发扩展](./developing-extensions.md#developing-an-extension-locally)。

## 定义斜杠命令

给定的扩展可以提供一个或多个斜杠命令。每个斜杠命令必须在 `extension.toml` 中注册。

例如，这是一个提供两个斜杠命令的扩展：`/echo` 和 `/pick-one`：

```toml
[slash_commands.echo]
description = "回显提供的输入"
requires_argument = true

[slash_commands.pick-one]
description = "从三个选项中选择一个"
requires_argument = true
```

每个斜杠命令可以定义以下属性：

- `description`：斜杠命令的描述，将在完成可用命令时显示。
- `requires_argument`：指示斜杠命令是否需要至少一个参数才能运行。

## 实现斜杠命令行为

要为您的斜杠命令实现行为，请为您的扩展实现 `run_slash_command`。

此方法接受将要运行的斜杠命令、传递给它的参数列表以及可选的 `Worktree`。

此方法返回 `SlashCommandOutput`，其中包含命令的文本输出在 `text` 字段中。输出还可以定义包含输出范围的 `SlashCommandOutputSection`。这些部分随后在助手的上下文编辑器中渲染为折叠区域。

您的扩展应该 `match` 命令名称（不带前导 `/`），然后相应地执行行为：

```rs
impl zed::Extension for MyExtension {
    fn run_slash_command(
        &self,
        command: SlashCommand,
        args: Vec<String>,
        _worktree: Option<&Worktree>,
    ) -> Result<SlashCommandOutput, String> {
        match command.name.as_str() {
            "echo" => {
                if args.is_empty() {
                    return Err("没有要回显的内容".to_string());
                }

                let text = args.join(" ");

                Ok(SlashCommandOutput {
                    sections: vec![SlashCommandOutputSection {
                        range: (0..text.len()).into(),
                        label: "回显".to_string(),
                    }],
                    text,
                })
            }
            "pick-one" => {
                let Some(selection) = args.first() else {
                    return Err("未选择选项".to_string());
                };

                match selection.as_str() {
                    "option-1" | "option-2" | "option-3" => {}
                    invalid_option => {
                        return Err(format!("{invalid_option} 不是有效选项"));
                    }
                }

                let text = format!("您选择了 {selection}。");

                Ok(SlashCommandOutput {
                    sections: vec![SlashCommandOutputSection {
                        range: (0..text.len()).into(),
                        label: format!("选择一个: {selection}"),
                    }],
                    text,
                })
            }
            command => Err(format!("未知的斜杠命令: \"{command}\"")),
        }
    }
}
```

## 自动完成斜杠命令参数

对于有参数的斜杠命令，您还可以选择实现 `complete_slash_command_argument` 来为您的斜杠命令提供补全。

此方法接受将要运行的斜杠命令和传递给它的参数列表。它返回一个 `SlashCommandArgumentCompletion` 列表，这些将在补全菜单中显示。

`SlashCommandArgumentCompletion` 包含以下属性：

- `label`：将在补全菜单中显示的标签。
- `new_text`：接受补全时将插入的文本。
- `run_command`：接受补全时是否运行斜杠命令。

再次，您的扩展应该 `match` 命令名称（不带前导 `/`）并返回所需的参数补全：

```rs
impl zed::Extension for MyExtension {
    fn complete_slash_command_argument(
        &self,
        command: SlashCommand,
        _args: Vec<String>,
    ) -> Result<Vec<SlashCommandArgumentCompletion>, String> {
        match command.name.as_str() {
            "echo" => Ok(vec![]),
            "pick-one" => Ok(vec![
                SlashCommandArgumentCompletion {
                    label: "选项一".to_string(),
                    new_text: "option-1".to_string(),
                    run_command: true,
                },
                SlashCommandArgumentCompletion {
                    label: "选项二".to_string(),
                    new_text: "option-2".to_string(),
                    run_command: true,
                },
                SlashCommandArgumentCompletion {
                    label: "选项三".to_string(),
                    new_text: "option-3".to_string(),
                    run_command: true,
                },
            ]),
            command => Err(format!("未知的斜杠命令: \"{command}\"")),
        }
    }
}
```
