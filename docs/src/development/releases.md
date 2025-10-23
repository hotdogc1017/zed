# Zed 发布

在此处阅读有关 Zed 的[发布渠道](https://zed.dev/faq#what-are-the-release-channels)。

## 周三发布流程

您需要具有 Zed 存储库的写入权限才能执行此操作。

此过程中使用的各种服务的凭据可以在 1Password 中找到。

使用 `releases` Slack 频道通知团队发布即将开始。
这在周三的小更新发布中主要是形式上的，但在进行补丁发布时可能有益，因为其他开发人员可能已经提交了他们想要挑选的修复。

### 开始构建

1. 检出 `main` 并确保您的工作副本是干净的。

1. 运行 `git fetch && git pull` 以确保您在本地拥有最新的提交。

1. 运行 `git fetch --tags --force` 以强制确保您的本地标签与远程同步。

1. 运行 `./script/get-stable-channel-release-notes` 并在本地存储输出。

1. 运行 `./script/bump-zed-minor-versions`。

   - 按照指示推送标签和分支。

1. 运行 `./script/get-preview-channel-changes` 并在本地存储输出。

> **注意：** 始终优先处理稳定版发布。
> 如果您已完成聚合稳定版发布说明，可以继续处理聚合预览版发布说明，但一旦稳定版构建完成，请完成其余的稳定版步骤以完全发布。
> 预览版可以在之后完成。

### 稳定版发布

1. 聚合稳定版发布说明。

   - 按照脚本末尾的说明，将发布说明聚合到一个结构中。

1. 一旦稳定版发布草稿在 [GitHub Releases](https://github.com/zed-industries/zed/releases) 上可用，将稳定版发布说明粘贴到其中并**保存**。

   - **不要发布草稿！**

1. 检查稳定版发布资源。

   - 确保稳定版发布作业已完成且没有错误。
   - 确保草稿具有正确数量的资源 — 当前每个发布有 11 个资源。
   - 下载稳定版发布草稿的工件并在本地测试是否可以运行它们。

1. 在 [GitHub Releases](https://github.com/zed-industries/zed/releases) 上发布稳定版草稿。

   - 使用 [Vercel](https://vercel.com/zed-industries/zed-dev) 检查网站重建的进度。
     重建完成后，发布将公开。

1. 将稳定版发布说明发布到社交媒体。

   - Bluesky 和 X 帖子将已经在 [Buffer](https://buffer.com) 中构建为草稿。
   - 仔细检查链接。
   - 逐个发布两者，确保两者都发布到各自平台。

1. 发送稳定版发布说明电子邮件。

   - 电子邮件广播将已经在 [Kit](https://kit.com) 中构建为草稿。
   - 仔细检查链接。
   - 发布电子邮件。

### 预览版发布

1. 聚合预览版发布说明。

   - 获取脚本的输出，并通过将每个发布说明行组织到一个类别中来构建发布说明。
   - 使用先前的发布作为初始大纲。
   - 确保如果存在 `Credit` 行，将其附加到发布说明行的末尾。

1. 一旦预览版发布草稿在 [GitHub Releases](https://github.com/zed-industries/zed/releases) 上可用，将预览版发布说明粘贴到其中并**保存**。

   - **不要发布草稿！**

1. 检查预览版发布资源。

   - 确保预览版发布作业已完成且没有错误。
   - 确保草稿具有正确数量的资源 — 当前每个发布有 11 个资源。
   - 下载预览版发布草稿的工件并在本地测试是否可以运行它们。

1. 在 [GitHub Releases](https://github.com/zed-industries/zed/releases) 上发布预览版草稿。
   - 使用 [Vercel](https://vercel.com/zed-industries/zed-dev) 检查网站重建的进度。
     重建完成后，发布将公开。

### 准备下周稳定版发布的内容

1. 基于预览版中的热门项目构建社交媒体帖子。

   - 在 [tweets](https://zed.dev/channel/tweets-23331) 频道中起草文案。
   - 创建预览媒体（视频、截图）。
     - 对于您拍摄视频的功能，尝试创建仅照片的替代版本用于电子邮件，因为视频和 GIF 不太适合电子邮件。
     - 将所有创建的媒体存储在我们的 Google Drive 中的 `Feature Media` 中。
   - 在 [Buffer](https://buffer.com) 中构建 X 和 Bluesky 帖子草稿（文案和媒体），以便在下周的稳定版发布时发送。

   **注意：这些是预览项目，您可能会发现错误。**
   **这是向团队报告这些发现的绝佳时机！**

1. 基于预览版中的热门项目构建电子邮件。

   - 您可以重用预览社交媒体帖子中的文案和照片媒体。
   - 在 [Kit](https://kit.com) 中创建电子邮件草稿，以便在下周的稳定版发布时发送。

## 补丁发布流程

如果您的 PR 修复了恐慌或崩溃，您应该将其挑选到当前的稳定版和预览版分支。
如果您的 PR 修复了最近发布代码中的回归问题，您应该将其挑选到预览版。

您需要具有 Zed 存储库的写入权限才能执行此操作：

---

1. 像往常一样，将包含您更改的 PR 发送到 `main`。

1. 一旦合并，在本地将提交挑选到任一发布分支（`v0.XXX.x`）。

   - 在某些情况下，您可能需要处理合并冲突。
     大多数情况下，这会在挑选到稳定版时发生，因为稳定版分支比预览版分支更"陈旧"。

1. 提交被挑选后，运行 `./script/trigger-release {preview|stable}`。
   这将增加版本号，创建一个新的发布标签，并启动发布构建。

   - 这也可以从 [GitHub Actions UI](https://github.com/zed-industries/zed/actions/workflows/bump_patch_version.yml) 运行：
     ![](https://github.com/zed-industries/zed/assets/1486634/9e31ae95-09e1-4c7f-9591-944f4f5b63ea)

1. 一旦发布草稿在 [GitHub Releases](https://github.com/zed-industries/zed/releases) 上可用，根据需要校对和编辑发布说明并**保存**。

   - **暂时不要发布草稿。**

1. 检查发布资源。

   - 确保稳定版/预览版发布作业已完成且没有错误。
   - 确保每个草稿具有正确数量的资源 — 当前每个发布有 10 个资源。
   - 下载每个发布草稿的工件并在本地测试是否可以运行它们。

1. 逐个发布稳定版/预览版草稿。
   - 使用 [Vercel](https://vercel.com/zed-industries/zed-dev) 检查网站重建的进度。
     重建完成后，发布将公开。

## 夜间构建流程

除了公共发布之外，我们还有一个夜间构建，我们鼓励员工使用。
夜间构建每天由 cron 发布一次，并且可以根据需要频繁发布。
没有发布说明或公告，因此您只需将更改合并到 main 并运行 `./script/trigger-release nightly`。
