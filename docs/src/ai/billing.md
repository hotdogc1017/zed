# 计费

我们使用 Stripe 作为支付提供商，使用 Orb 进行发票和计量。所有 Pro 计划都需要通过信用卡或其他支持的支付方式进行支付。
对于基于发票的计费，需要商业计划。请联系 [sales@zed.dev](mailto:sales@zed.dev) 获取更多信息。

## 计费信息 {#settings}

您可以在 [zed.dev/account](https://zed.dev/account) 访问计费信息和设置。
该页面的大部分内容嵌入了来自我们的发票/计量合作伙伴 Orb 的信息（我们计划很快提供更原生的体验！）。

## 计费周期 {#billing-cycles}

Zed 根据您最初订阅的日期按月计费。您订阅 Zed Pro 的每个月将_至少_收到一张来自 Zed 的发票，如果您在一个月内使用超过 $10 的增量令牌支出，则会收到多张发票。

## 阈值计费 {#threshold-billing}

Zed 使用阈值计费来确保及时收取应付款项并防止滥用。每次您使用 Zed 托管模型的支出超过 $10 的阈值时，就会生成一张新发票，并且阈值重置为 $0。

例如：

- 您在 2 月 1 日订阅。您的第一张发票是 $10。
- 您在 2 月份使用了 $12 的增量令牌，其中前 $10 在 2 月 15 日花费。您将在 2 月 15 日收到 $10 的发票
- 在 3 月 1 日，您收到 $12 的发票：$10（3 月 Pro 订阅）和 $2 的剩余令牌支出，因为您的使用没有超过 $10 的阈值。

## 支付失败 {#payment-failures}

如果发票支付失败，Zed 将阻止使用我们托管的模型，直到支付完成。请发送邮件至 [billing-support@zed.dev](mailto:billing-support@zed.dev) 寻求帮助。

## 发票历史 {#invoice-history}

您可以通过导航到 [zed.dev/account](https://zed.dev/account) 并在嵌入的 Orb 门户中点击 `Invoice history` 来访问您的发票历史。

如果您需要历史 Stripe 发票，请发送邮件至 [billing-support@zed.dev](mailto:billing-support@zed.dev)

## 更新计费信息 {#updating-billing-info}

请发送邮件至 [billing-support@zed.dev](mailto:billing-support@zed.dev) 寻求帮助更新支付方式、姓名、地址和税务信息。

> 我们将很快更新我们的账户页面以允许自助更新。敬请期待！

请注意，计费信息的更改**仅**影响未来的发票——**我们无法修改历史发票**。

## 销售税 {#sales-tax}

Zed 与 [Sphere](https://www.getsphere.com/) 合作，根据客户位置和销售的产品计算发票的间接税率。税款在发票上列为单独的项目，优先基于您的账单地址，其次是 Stripe 已知的卡发行国家。

如果您有 VAT/GST ID，可以在结账时添加。勾选表示您是企业的复选框。

请注意，VAT/GST ID 和地址的更改**仅**影响未来的发票——**我们无法修改历史发票**。
问题或疑问可以发送至 [billing-support@zed.dev](mailto:billing-support@zed.dev)。
