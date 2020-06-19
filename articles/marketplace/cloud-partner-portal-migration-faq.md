---
title: 有关迁移到合作伙伴中心的常见问题 - Microsoft 商业市场
description: 解答有关将产品/服务从云合作伙伴门户迁移到合作伙伴中心的常见问题。
author: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mingshen
ms.openlocfilehash: c4fdde94ea703d194e2de27a8df429f62ea374f4
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83727475"
---
# <a name="common-questions-about-migrating-from-the-cloud-partner-portal-to-partner-center"></a>有关从云合作伙伴门户迁移到合作伙伴中心的常见问题

本文解答了有关将产品/服务从云合作伙伴门户迁移到合作伙伴中心的常见问题。

## <a name="what-does-offer-migration-mean"></a>产品/服务迁移是指什么？

随着产品/服务发布和管理体验的变动，我们正在将你的产品/服务数据从云合作伙伴门户迁移到合作伙伴中心。

| 区域  | 更改  |
|-------|----------|
| **发布和产品/服务管理体验** | 通过合作伙伴中心内的直观界面，你将获得更卓越的用户体验。 有关更多详细信息，请参阅[合作伙伴中心与云合作伙伴门户之间有何区别？](#what-are-the-differences-between-partner-center-and-the-cloud-partner-portal) |
| **你的产品/服务在市场的供应情况** | 无更改。 如果你的产品/服务已在市场中上线，则在迁移期间和迁移完成后，该产品/服务都将保持上线状态。 |
| **新的购买和部署** | 无更改。 你的客户将继续能够购买和部署你的产品/服务，不会出现任何中断。 |
| **付款** | 在迁移期间或迁移后发生的任何购买和部署将继续如往常一样付款给你。 |
|**API 与现有[云合作伙伴门户 API](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) 的集成** | 现有云合作伙伴门户 API 在迁移后继续受到支持，你的现有集成也将继续工作。 有关更多详细信息，请参阅[迁移后还将支持云合作伙伴门户 REST API 吗？](#will-the-cloud-partner-portal-rest-apis-be-supported-post-migration) |
| | |

## <a name="can-i-still-access-the-cloud-partner-portal-and-manage-my-offers-during-migration"></a>在迁移期间，我是否仍能访问云合作伙伴门户并管理我的产品/服务？

所有产品/服务都已迁移到合作伙伴中心。 每项产品/服务的迁移时间不超过 24 小时。 在产品/服务迁移完成后，你会收到一封电子邮件通知。

产品/服务迁移后，将在一段有限的时间内在云合作伙伴门户中处于只读模式下。 它们的状态将显示“已移到合作伙伴中心”，并且包含一个指向合作伙伴中心内相应内容的链接，如以下屏幕截图所示。 从此之后，你将只能通过合作伙伴中心管理你所有产品/服务的更新或创建新产品/服务。

:::image type="content" source="media/migration-faq/all-offers-2.png" alt-text="显示为已迁移到合作伙伴中心的产品/服务提供的消息":::

:::image type="content" source="media/migration-faq/offer-has-moved.png" alt-text="显示云合作伙伴门户中已迁移的产品/服务的页面。":::

## <a name="how-will-i-create-new-offers"></a>如何创建新产品/服务？

在云合作伙伴门户中，你将被定向到合作伙伴中心来创建新产品/服务

:::image type="content" source="media/migration-faq/create-new-offer-1.png" alt-text="显示云合作伙伴门户中用于创建新产品/服务的菜单":::

选择新的产品/服务后，将看到一条消息，如下所示。

:::image type="content" source="media/migration-faq/create-new-offer-2.png" alt-text="显示在云合作伙伴门户中创建新产品/服务时收到的消息":::

## <a name="do-i-need-to-create-a-new-account-to-manage-offers-in-partner-center"></a>是否需要在合作伙伴中心创建新帐户来管理产品/服务？

不需要。 系统将保留你的基本账户，你应该已经在合作伙伴中心管理它了。 这意味着如果你当前有合作伙伴，则在迁移后，你可使用现有的云合作伙伴门户帐户凭据登录合作伙伴中心。 只有产品/服务及相关管理体验将从云合作伙伴门户移至合作伙伴中心。 请不要创建任何新帐户，因为你的产品/服务将不与新帐户关联。

## <a name="i-see-a-message-in-the-cloud-partner-portal-to-activate-my-account-what-does-this-mean"></a>我在云合作伙伴门户中看到要求我激活帐户的消息，这是什么意思？

我们需要你再提供一些详细信息，这样才能正确地将你的帐户迁移到合作伙伴中心，并让你能够在产品/服务迁移完成后在合作伙伴中心管理你的产品/服务。 有关帐户激活的更多详细信息，请参阅[将帐户从云合作伙伴门户迁移到合作伙伴中心](https://docs.microsoft.com/azure/marketplace/partner-center-portal/account-migration-from-cpp-to-pc)。

完成帐户激活所需的步骤由你的帐户角色而定。

| 帐户角色 | 激活步骤 |
|--------------|----------------|
|所有者 | 在云合作伙伴门户中转到[发布者简介](https://cloudpartner.azure.com/#profile)页面，然后单击横幅中的链接进行激活。 你将被重定向到合作伙伴中心来完成帐户激活。 |
| 参与者 | 只有帐户中具有所有者角色的用户可激活帐户。 请联系帐户所有者来完成帐户激活。 帐户所有者会在横幅消息中列出。 |
| | |

## <a name="im-having-trouble-logging-in-to-my-account-and-opening-a-support-ticket"></a>我在登录帐户和创建支持票证时遇到问题

如果你无法登录帐户，可创建[支持票证](https://partner.microsoft.com/support/v2/?stage=1)。

## <a name="where-can-i-find-documentation-on-the-new-partner-center-publishing-experience"></a>可在哪里找到有关新的合作伙伴中心发布体验的文档？

请转到[商业市场文档](https://docs.microsoft.com/azure/marketplace/)。 然后，展开“合作伙伴中心内的商业市场门户”  > “创建新产品/服务”，查看有关创建每种产品/服务类型的帮助主题 。

:::image type="content" source="media/migration-faq/marketplace-help-topics.png" alt-text="显示合作伙伴中心的帮助主题":::

### <a name="what-are-the-differences-between-partner-center-and-the-cloud-partner-portal"></a>合作伙伴中心与云合作伙伴门户之间有何区别？

你可能已注意到云合作伙伴门户与合作伙伴中心之间存在以下差异。

### <a name="modular-publishing-capabilities"></a>模块化发布功能

合作伙伴中心提供了一个模块化发布功能，你可用它来选择你想要发布的更改，而不是总是一次性发布所有更新。 例如，以下屏幕截图显示了只选择发布对“属性”和“产品/服务列表”的更改 。

:::image type="content" source="media/migration-faq/review-and-publish-migration.png" alt-text="显示“预览和发布”页面":::

未发布的更新将保存为草稿。 在上线提供给公众之前，可继续使用产品/服务预览来验证你的产品/服务。

### <a name="rich-text-format"></a>RTF 格式

使用“产品/服务列表和计划列表”页面上的 RTF 编辑器完善你对产品/服务和计划的描述。

:::image type="content" source="media/migration-faq/rich-text-editor-migration.png" alt-text="显示 RTF 编辑器":::

### <a name="enhanced-preview-options"></a>强化预览选项

合作伙伴中心包含一个[“比较”功能](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#compare-changes-to-marketplace-offers)，其中有改进后的筛选选项。 这使你能够根据产品/服务的预览版本和上线版本。

:::image type="content" source="media/migration-faq/enhanced-preview.png" alt-text="显示“比较”功能":::

### <a name="branding-and-navigation-changes"></a>品牌和导航更改

你将注意到有一些品牌方面的更改。 例如，“SKU”在合作伙伴中心的品牌显示为“计划”。

:::image type="content" source="media/migration-faq/plan-overview-migration.png" alt-text="显示计划概述。":::

此外，你过去在云合作伙伴门户的“市场”和“店面详细信息”（咨询服务、Power BI 应用）页面中提供的信息现收集到合作伙伴中心的“产品/服务列表”页面  。

:::image type="content" source="media/migration-faq/offer-listing-migration.png" alt-text="显示“产品/服务列表”页面。":::

你过去在云合作伙伴门户的单个页面中为 SKU 提供的信息现在可能会在合作伙伴中心通过多个页面进行收集：

* 计划设置页面
* 计划列表页面
* 计划可用性页面
* 计划技术配置页面，如以下屏幕截图所示。

:::image type="content" source="media/migration-faq/tech-config-migration.png" alt-text="显示计划技术配置页面。":::

产品/服务 ID 现显示在产品/服务的左侧导航栏中。

:::image type="content" source="media/migration-faq/offer-id-offer-overview.png" alt-text="显示具有产品/服务 ID 的左侧导航菜单。":::

### <a name="stop-selling-an-offer"></a>停止销售产品/服务

你可直接通过合作伙伴中心门户请求在市场上[停止销售某款产品/服务](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)。 可在产品/服务的“产品/服务概述”页面上找到该选项。

:::image type="content" source="media/migration-faq/stop-selling-migration.png" alt-text="显示具有“停止销售”选项的“产品/服务概述”页面。":::

## <a name="what-pages-in-partner-center-correspond-to-pages-i-used-in-the-cloud-partner-portal"></a>合作伙伴中心中哪些页面与我在云合作伙伴门户中所用的页面相对应？

下表显示了这两个门户之间的对应链接。

| 页面 | 云合作伙伴门户链接 | 合作伙伴中心链接 |
|------|---------------------------|---------------------|
| **“所有产品/服务”页面** | https://cloudpartner.azure.com/#alloffers | https://partner.microsoft.com/dashboard/commercial-marketplace/overview |
| **“所有发布者”页面** | https://cloudpartner.azure.com/#publishers | https://partner.microsoft.com/dashboard/account/v3/publishers/list |
| **发布者资料** | https://cloudpartner.azure.com/#profile | https://partner.microsoft.com/dashboard/account/management |
| **“用户”页面** | https://cloudpartner.azure.com/#users | https://partner.microsoft.com/dashboard/account/usermanagement |
| **“历史记录”页面** | https://cloudpartner.azure.com/#history | 合作伙伴中心尚不支持“历史记录”功能。 |
| **见解仪表板** | https://cloudpartner.azure.com/#insights | https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary |
| **支出报表** | https://cloudpartner.azure.com/#insights/payout | https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| | |

## <a name="will-the-cloud-partner-portal-rest-apis-be-supported-post-migration"></a>迁移后还将支持云合作伙伴门户 REST API 吗？

云合作伙伴门户 API 已与合作伙伴中心集成，在你的产品/服务迁移到合作伙伴中心后，它们仍可运行。 集成造成了少量的更改。 请查看下表中的更改，确保在迁移到合作伙伴中心后你的代码继续正常工作。

| **API** | **更改描述** | **影响** |
| ------- | ---------------------- | ---------- |
| POST 发布、上线、取消 | 对于已迁移的产品/服务，响应标头将采用不同的格式，但将以相同的方式继续工作，这表示有一个相对路径用于检索操作状态。 | 在发送某产品/服务的任何相应 POST 请求时，位置标头将采用以下两种格式之一，具体取决于产品/服务的迁移状态：<ul><li>未迁移的产品/服务<br>`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>已迁移的产品/服务<br>`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li> |
| GET 操作 | 对于之前支持在响应中使用“notification-email”字段的产品/服务，此字段将被弃用，且将不对已迁移的产品/服务返回此字段。 | 对于已迁移的产品/服务，我们将不再向请求中指定的一列电子邮件地址发送通知。 相反，API 服务将与 合作伙伴中心内的通知电子邮件进程保持一致来发送电子邮件。 具体而言，通知将发送至合作伙伴中心内在你的帐户设置的卖家联系人信息部分中设定的电子邮件地址，告知你操作进度。<br><br>请在合作伙伴中心的[帐户设置](https://partner.microsoft.com/dashboard/account/management)中查看卖家联系人信息中设定的电子邮件地址，确保提供了正确的电子邮件地址来接收通知。  |
| | | |
