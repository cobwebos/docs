---
title: 有关迁移到合作伙伴中心的常见问题 |Azure 应用商店
description: 本文解决了有关产品/服务从云合作伙伴门户迁移到合作伙伴中心的常见问题。
author: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: mingshen
ms.openlocfilehash: 672153eba4aa2b739b67694f939c4796b39ac4c6
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2020
ms.locfileid: "81274375"
---
# <a name="frequently-asked-questions-for-migrating-from-the-cloud-partner-portal-to-partner-center"></a>从云合作伙伴门户迁移到合作伙伴中心的常见问题

本文解决了有关产品/服务从云合作伙伴门户迁移到合作伙伴中心的常见问题。

## <a name="what-does-offer-migration-mean"></a>提供迁移意味着什么？

我们将您的产品/服务数据从云合作伙伴门户移动到合作伙伴中心，并更改产品/服务发布和管理经验。

| 区域  | 更改  |
|-------|----------|
| **发布并提供管理经验** | 合作伙伴中心中的直观界面将改善用户体验。 有关详细信息，请参阅[合作伙伴中心和云合作伙伴门户之间的区别？](#what-are-the-differences-between-partner-center-and-the-cloud-partner-portal) |
| **您的产品/服务在市场上的可用性** | 无更改。 如果您的优惠是活在市场中，它将在迁移完成期间和之后继续直播。 |
| **新的购买和部署** | 无更改。 您的客户将继续能够购买和部署您的产品/服务，而不会中断。 |
| **付款** | 迁移期间或之后发生的任何购买和部署将继续照常支付给您。 |
|**API 集成现有[云合作伙伴门户 API](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)** | 迁移后，将继续支持现有云合作伙伴门户 API，并且您的现有集成将继续工作。 有关详细信息，请参阅[迁移后云合作伙伴门户 REST API 是否受支持？](#will-the-cloud-partner-portal-rest-apis-be-supported-post-migration) |
| | |

## <a name="can-i-still-access-the-cloud-partner-portal-and-manage-my-offers-during-migration"></a>我能否在迁移期间访问云合作伙伴门户并管理我的优惠？

优惠将于 4 月 13 日起从 5 月 13 日起迁移到合作伙伴中心。 在此期间，您可以像往常一样访问云合作伙伴门户，但您提供的时间安排进行迁移的时间除外。
迁移优惠时，其状态为"锁定 - 移动到合作伙伴中心"，如下图所示。 此迁移期应小于 24 小时。 在此期间，您将无法对优惠进行任何更新。 在我们完成您的优惠迁移后，您将收到电子邮件通知。

:::image type="content" source="media/migration-faq/all-offers-1.png" alt-text="说明迁移的优惠状态。":::

迁移产品/服务后，将在云合作伙伴门户中处于只读模式，时间**有限**。 其状态将显示"移动到合作伙伴中心"，并在合作伙伴中心中包含指向您的优惠的链接，如以下屏幕截图所示。 从此时起，您将管理所有优惠的更新，或通过合作伙伴中心创建新的优惠，

:::image type="content" source="media/migration-faq/all-offers-2.png" alt-text="说明为已迁移到合作伙伴中心的报价提供的消息":::

:::image type="content" source="media/migration-faq/offer-has-moved.png" alt-text="演示迁移产品的云合作伙伴门户页面。":::

## <a name="how-will-i-create-new-offers"></a>如何创建新优惠？

从云合作伙伴门户，您将被引导在合作伙伴中心创建新优惠

:::image type="content" source="media/migration-faq/create-new-offer-1.png" alt-text="说明在云合作伙伴门户中创建新产品/服务菜单":::

选择新产品/服务后，您将看到一条消息，如下所示。

:::image type="content" source="media/migration-faq/create-new-offer-2.png" alt-text="说明在 CPP 中创建新产品/服务时收到的消息":::

## <a name="do-i-need-to-create-a-new-account-to-manage-offers-in-partner-center"></a>我需要创建新帐户来管理合作伙伴中心的报价吗？

不是。 您的基础帐户将被保留，您应该已经在合作伙伴中心管理它。 这意味着，如果您是现有合作伙伴，则可以使用现有的云合作伙伴门户帐户凭据在迁移后登录合作伙伴中心。 只有优惠和相关管理体验从云合作伙伴门户迁移到合作伙伴中心。 我们要求您不要创建任何新帐户，因为您的优惠不会与新帐户关联。

## <a name="i-see-a-message-in-the-cloud-partner-portal-to-activate-my-account-what-does-this-mean"></a>我在云合作伙伴门户中看到一条消息以激活我的帐户，这意味着什么？

我们需要您提供更多详细信息，以便将您的帐户正确迁移到合作伙伴中心，并使您能够在产品/服务迁移完成后在合作伙伴中心管理您的优惠。 有关帐户激活的更多详细信息，请参阅[从云合作伙伴门户向合作伙伴中心进行帐户迁移](https://docs.microsoft.com/azure/marketplace/partner-center-portal/account-migration-from-cpp-to-pc)。

完成帐户激活所需的步骤因您的帐户角色而异。

| 帐户角色 | 激活步骤 |
|--------------|----------------|
|“所有者” | 转到云合作伙伴门户中的[发布服务器配置文件](https://cloudpartner.azure.com/#profile)页面，然后单击横幅中的链接以激活。 您将被重定向到合作伙伴中心以完成帐户激活。 |
| 参与者 | 只有具有所有者角色的帐户中的用户可以激活该帐户。 与您的帐户所有者联系以完成帐户激活。 您的帐户所有者应列在横幅消息中。 |
| | |

## <a name="im-having-trouble-logging-in-to-my-account-and-opening-a-support-ticket"></a>登录我的帐户和打开支持票证时遇到问题

如果您无法登录到您的帐户，您可以打开[支持票证](https://partner.microsoft.com/support/v2/?stage=1)。

## <a name="where-can-i-find-documentation-on-the-new-partner-center-publishing-experience"></a>在哪里可以找到有关新合作伙伴中心发布体验的文档？

转到[商业市场文档](https://docs.microsoft.com/azure/marketplace/)。 然后  > 展开**合作伙伴中心中的商业市场门户****创建新产品/服务**以查看创建每种产品/服务的帮助主题。

:::image type="content" source="media/migration-faq/marketplace-help-topics.png" alt-text="演示合作伙伴中心的帮助主题":::

### <a name="what-are-the-differences-between-partner-center-and-the-cloud-partner-portal"></a>合作伙伴中心和云合作伙伴门户之间的区别是什么？

您可能会注意到云合作伙伴门户和合作伙伴中心之间的以下差异。

### <a name="modular-publishing-capabilities"></a>模块化发布功能

合作伙伴中心提供了一个模块化发布选项，允许您选择要发布的更改，而不是始终一次发布所有更新。 例如，以下屏幕截图显示，选择要发布的唯一更改**是属性和****要约列表的**更改。

:::image type="content" source="media/migration-faq/review-and-publish-migration.png" alt-text="说明预览和发布页面":::

不发布的更新将另存为草稿。 继续使用您的优惠预览，以验证您的报价，然后再向公众直播。

### <a name="rich-text-format"></a>富文本格式

使用"优惠列表"和"计划列表"页上的"丰富文本编辑器"增强您的优惠和计划说明。

:::image type="content" source="media/migration-faq/rich-text-editor-migration.png" alt-text="说明富文本编辑器":::

### <a name="enhanced-preview-options"></a>增强的预览选项

合作伙伴中心包括一个[比较功能](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#compare-changes-to-marketplace-offers)和改进的筛选选项。 这使您可以与优惠的预览版和实时版本进行比较。

:::image type="content" source="media/migration-faq/enhanced-preview.png" alt-text="说明比较功能":::

### <a name="branding-and-navigation-changes"></a>品牌和导航更改

您会注意到一些品牌更改。 例如，"SKU"在合作伙伴中心中称为"计划"。

:::image type="content" source="media/migration-faq/plan-overview-migration.png" alt-text="说明计划概述。":::

此外，您用于在云合作伙伴门户中的 **"应用商店**"或"S**翻页详细信息**"（咨询服务、Power BI 应用）页面提供的信息，在合作伙伴中心的产品 **/服务列表**页中收集。

:::image type="content" source="media/migration-faq/offer-listing-migration.png" alt-text="说明产品/服务列表页。":::

您用于在云合作伙伴门户中的单个页面中为 SKU 提供的信息现在可能会在合作伙伴中心中的多个页面中收集：

* 计划设置页面
* 计划列表页
* 计划可用性页面
* 规划技术配置页面，如此屏幕截图所示。

:::image type="content" source="media/migration-faq/tech-config-migration.png" alt-text="说明计划技术配置页面。":::

您的优惠 ID 现在显示在优惠的左导航栏上。

:::image type="content" source="media/migration-faq/offer-id-offer-overview.png" alt-text="使用产品/服务 ID 说明左侧导航菜单。":::

### <a name="stop-selling-an-offer"></a>停止销售要约

您可以直接从合作伙伴中心门户请求[停止在市场上销售产品/服务](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)。 该选项可在优惠**优惠概览**页面上使用。

:::image type="content" source="media/migration-faq/stop-selling-migration.png" alt-text="使用止损选项说明 产品/服务概述 页面。":::

## <a name="what-pages-in-partner-center-correspond-to-pages-i-used-in-the-cloud-partner-portal"></a>合作伙伴中心中的哪些页面对应于我在云合作伙伴门户中使用的页面？

下表显示了两个门户之间的相应链接。

| 页 | 云合作伙伴门户链接 | 合作伙伴中心链接 |
|------|---------------------------|---------------------|
| **全部产品/服务页** | https://cloudpartner.azure.com/#alloffers | https://partner.microsoft.com/dashboard/commercial-marketplace/overview |
| **所有发布者页面** | https://cloudpartner.azure.com/#publishers | https://partner.microsoft.com/dashboard/account/v3/publishers/list |
| **发布者个人资料** | https://cloudpartner.azure.com/#profile | https://partner.microsoft.com/dashboard/account/management |
| **“用户”页** | https://cloudpartner.azure.com/#users | https://partner.microsoft.com/dashboard/account/usermanagement |
| **历史记录页** | https://cloudpartner.azure.com/#history | 合作伙伴中心中尚不支持"历史记录"功能。 |
| **见解仪表板** | https://cloudpartner.azure.com/#insights | https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary |
| **付款报告** | https://cloudpartner.azure.com/#insights/payout | https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| | |

## <a name="will-the-cloud-partner-portal-rest-apis-be-supported-post-migration"></a>迁移后云合作伙伴门户 REST API 是否会得到支持？

云合作伙伴门户 API 与合作伙伴中心集成，在您的产品/服务迁移到合作伙伴中心后将继续工作。 集成引入了小更改。 查看下表中的更改，以确保代码在迁移到合作伙伴中心后继续工作。

| **API** | **更改描述** | **影响** |
| ------- | ---------------------- | ---------- |
| POST 发布、GoLive、取消 | 对于迁移的要约，响应标头将具有不同的格式，但将继续以相同的方式工作，表示检索操作状态的相对路径。 | 发送任何相应的 POST 请求为产品/服务时，位置标头将具有两种格式之一，具体取决于产品/服务的状态：<ul><li>非迁移优惠<br>`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>迁移优惠<br>`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li> |
| 获取操作 | 对于响应中以前支持"通知电子邮件"字段的优惠，此字段将被弃用，并且不再为迁移的优惠返回。 | 对于迁移的优惠，我们将不再向请求中指定的电子邮件列表发送通知。 相反，API 服务将与合作伙伴中心中的通知电子邮件进程一致发送电子邮件。 具体来说，通知将发送到合作伙伴中心中帐户设置的卖家联系信息部分中设置的电子邮件地址，以通知您操作进度。<br><br>查看合作伙伴中心["帐户"设置](https://partner.microsoft.com/dashboard/account/management)中的"卖家联系信息"部分中设置的电子邮件地址，以确保为通知提供正确的电子邮件。  |
| | | |
