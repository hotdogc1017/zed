# 账户

登录 Zed 不是必需的。您可以在不登录的情况下使用代码编辑器中期望的大多数功能。我们将在此处概述需要登录的少数功能以及如何登录。

## 哪些功能需要登录？

1. 所有实时[协作功能](./collaboration.md)。
2. [LLM 驱动的功能](./ai/overview.md)，如果您使用 Zed 作为 LLM 模型的提供商。或者，如果您愿意，可以[自带并配置您自己的 API 密钥](./ai/llm-providers.md#use-your-own-keys)，从而避免登录。

## 登录

Zed 使用 GitHub 的 OAuth 流程来验证用户，仅需要 `read:user` GitHub 范围，该范围授予对您的 GitHub 个人资料信息的只读访问权限。

1. 打开 Zed 并点击窗口右上角的 `登录` 按钮，或从命令面板运行 `client: sign in` 命令（在 macOS 上为 `cmd-shift-p`，在 Windows/Linux 上为 `ctrl-shift-p`）。
2. 您的默认网页浏览器将打开 Zed 登录页面。
3. 在提示时使用您的 GitHub 账户进行身份验证。
4. 成功验证后，您的浏览器将显示确认信息，并且您将自动登录到 Zed。

**注意**：如果您位于企业防火墙后面，请确保允许连接到 `zed.dev` 和 `collab.zed.dev`。

## 登出

要登出 Zed，您可以使用以下任一方法：

- 点击右上角的个人资料图标，然后从下拉菜单中选择 `登出`。
- 打开命令面板并运行 `client: sign out` 命令。

## 电子邮件地址 {#email}

您的 Zed 账户的电子邮件地址是由 GitHub OAuth 提供的地址。如果您有公开的电子邮件地址，则将使用该地址，否则将使用您的主要 GitHub 电子邮件地址。通过在 [zed.dev 上登录](https://zed.dev/sign_in)，可以将 GitHub 上对电子邮件地址的更改同步到您的 Zed 账户。

Stripe 用于计费，并在开始订阅时将使用您的 Zed 账户的电子邮件地址。目前，对 Zed 账户电子邮件地址的更改不会更新 Stripe 中使用的电子邮件地址。请参阅[更新账单信息](./ai/billing.md#updating-billing-info)了解如何更改此电子邮件地址。

## 从界面隐藏登录按钮

如果未使用登录功能，可以通过使用 `show_sign_in` 设置属性将其从界面中隐藏。
有关更多详细信息，请参阅[视觉自定义页面](./visual-customization.md)。
