---
title: 有关迁移到合作伙伴中心的常见问题 |Azure Marketplace
description: 本文介绍有关将产品/服务从云合作伙伴门户迁移到合作伙伴中心的常见问题。
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

本文介绍了有关从云合作伙伴门户迁移到合作伙伴中心的常见问题。

## <a name="what-does-offer-migration-mean"></a>提供迁移的意思是什么？

我们正在将你的产品/服务数据从 "云合作伙伴门户" 移动到 "合作伙伴中心"，其中包含产品/服务发布和管理体验的更改。

| 区域  | 更改  |
|-------|----------|
| **发布和提供管理体验** | 使用合作伙伴中心的直观界面，你将获得改进的用户体验。 有关更多详细信息，请参阅[合作伙伴中心与云合作伙伴门户之间有何区别？](#what-are-the-differences-between-partner-center-and-the-cloud-partner-portal) |
| **你的产品/服务在 marketplace 中的可用性** | 无更改。 如果产品/服务在 marketplace 中推出，则在迁移完成后和完成后，该产品/服务会继续保持活动。 |
| **新的购买和部署** | 无更改。 你的客户将能够继续购买并部署你的产品/服务，而不会出现任何中断。 |
| **付款** | 迁移过程中或迁移之后发生的任何采购和部署都将继续按正常方式支付给你。 |
|**与现有[云合作伙伴门户 api](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)的 api 集成** | 在迁移后将继续支持现有云合作伙伴门户 Api，你的现有集成将继续工作。 有关更多详细信息，请参阅[迁移后是否支持云合作伙伴门户 REST api？](#will-the-cloud-partner-portal-rest-apis-be-supported-post-migration) |
| | |

## <a name="can-i-still-access-the-cloud-partner-portal-and-manage-my-offers-during-migration"></a>是否仍然可以在迁移过程中访问云合作伙伴门户和管理我的产品/服务？

产品/服务将于5月13日开始迁移到合作伙伴中心。 在此期间，你可以像往常一样访问云合作伙伴门户，而你提供的时间只是计划迁移。
当你的产品/服务正在迁移时，他们将具有状态 "锁定–移动到合作伙伴中心"，如以下屏幕截图所示。 此迁移时间应小于24小时。 在此期间，你将无法对产品/服务进行任何更新。 完成产品/服务的迁移后，你会收到电子邮件通知。

:::image type="content" source="media/migration-faq/all-offers-1.png" alt-text="说明已迁移产品的状态。":::

迁移你的产品/服务后，它们将处于 "只读" 模式，在云合作伙伴门户中的**有限时间段**内。 其状态将显示为 "已移动到合作伙伴中心"，并在合作伙伴中心包含指向您的产品/服务的链接，如以下屏幕截图所示。 此时，你将通过合作伙伴中心来管理你的所有产品/服务的更新或创建新产品/服务，

:::image type="content" source="media/migration-faq/all-offers-2.png" alt-text="说明为已迁移到合作伙伴中心的产品/服务提供的消息":::

:::image type="content" source="media/migration-faq/offer-has-moved.png" alt-text="说明已迁移产品的云合作伙伴门户页面。":::

## <a name="how-will-i-create-new-offers"></a>如何创建新产品/服务？

在云合作伙伴门户中，你将被定向到在合作伙伴中心创建新产品/服务

:::image type="content" source="media/migration-faq/create-new-offer-1.png" alt-text="说明用于在云合作伙伴门户中创建新产品/服务的菜单":::

选择新的产品/服务后，你将看到一条消息，如下所示。

:::image type="content" source="media/migration-faq/create-new-offer-2.png" alt-text="说明在 CPP 中创建新产品/服务时收到的消息":::

## <a name="do-i-need-to-create-a-new-account-to-manage-offers-in-partner-center"></a>是否需要创建新帐户来管理合作伙伴中心提供的产品/服务？

不能。 你的基础帐户将会保留，你应该已在合作伙伴中心进行管理。 这意味着，如果你是现有合作伙伴，则可以使用现有的云合作伙伴门户帐户凭据登录到合作伙伴中心后期迁移。 仅提供和关联的管理体验从云合作伙伴门户转移到合作伙伴中心。 我们要求您不要创建任何新帐户，因为您的产品/服务不会与新帐户关联。

## <a name="i-see-a-message-in-the-cloud-partner-portal-to-activate-my-account-what-does-this-mean"></a>我在云合作伙伴门户中看到一条消息来激活我的帐户，这是什么意思？

我们需要更多详细信息，以便将你的帐户正确迁移到合作伙伴中心，并使你能够在完成产品/服务迁移后管理合作伙伴中心提供的产品/服务。 有关帐户激活的详细信息，请参阅[从云合作伙伴门户到合作伙伴中心的帐户迁移](https://docs.microsoft.com/azure/marketplace/partner-center-portal/account-migration-from-cpp-to-pc)。

完成帐户激活所需的步骤取决于你的帐户角色。

| 帐户角色 | 激活步骤 |
|--------------|----------------|
|“所有者” | 中转到云合作伙伴门户中的 "[发布者配置文件](https://cloudpartner.azure.com/#profile)" 页，然后单击横幅中的链接以激活。 你将重定向到合作伙伴中心来完成帐户激活。 |
| 参与者 | 只有具有所有者角色的帐户中的用户可以激活该帐户。 请与帐户所有者联系以完成帐户激活。 帐户所有者应在横幅消息中列出。 |
| | |

## <a name="im-having-trouble-logging-in-to-my-account-and-opening-a-support-ticket"></a>登录我的帐户并打开支持票证时遇到问题

如果无法登录到帐户，可以打开[支持票证](https://partner.microsoft.com/support/v2/?stage=1)。

## <a name="where-can-i-find-documentation-on-the-new-partner-center-publishing-experience"></a>在哪里可以找到有关新合作伙伴中心发布体验的文档？

请参阅[商业应用商店文档](https://docs.microsoft.com/azure/marketplace/)。 然后  > 展开 **"合作伙伴中心" 中的 "商用 Marketplace 门户**"**创建新产品/服务**，以查看创建每种类型的产品/服务的帮助主题。

:::image type="content" source="media/migration-faq/marketplace-help-topics.png" alt-text="说明合作伙伴中心的帮助主题":::

### <a name="what-are-the-differences-between-partner-center-and-the-cloud-partner-portal"></a>合作伙伴中心与云合作伙伴门户之间有何区别？

你可能会注意到云合作伙伴门户和合作伙伴中心之间存在以下差异。

### <a name="modular-publishing-capabilities"></a>模块化发布功能

合作伙伴中心提供了模块化发布选项，可让你选择要发布的更改，而不是始终同时发布所有更新。 例如，以下屏幕截图显示，仅选择要发布的更改是对**属性**和**服务列表**的更改。

:::image type="content" source="media/migration-faq/review-and-publish-migration.png" alt-text="说明预览和发布页":::

未发布的更新保存为草稿。 继续使用产品/服务预览版来验证你的产品/服务，使其成为公众。

### <a name="rich-text-format"></a>Rtf 格式

使用产品/服务列表和计划列表页上的富文本编辑器增强你的产品/服务和计划说明。

:::image type="content" source="media/migration-faq/rich-text-editor-migration.png" alt-text="阐释 rtf 编辑器":::

### <a name="enhanced-preview-options"></a>增强的预览选项

合作伙伴中心包含一[项比较功能](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#compare-changes-to-marketplace-offers)，其中包含改进的筛选选项。 这使你能够根据产品的预览和 live 版本进行比较。

:::image type="content" source="media/migration-faq/enhanced-preview.png" alt-text="阐释比较功能":::

### <a name="branding-and-navigation-changes"></a>品牌和导航更改

你会注意到一些品牌更改。 例如，"Sku" 的品牌为合作伙伴中心的 "计划"。

:::image type="content" source="media/migration-faq/plan-overview-migration.png" alt-text="说明计划概述。":::

此外，在 "合作伙伴中心" 的 "**产品**/服务" 页中收集了你在**Marketplace**或**Torefront 详细信息**（咨询服务，Power BI 应用）云合作伙伴门户页面中提供的信息。

:::image type="content" source="media/migration-faq/offer-listing-migration.png" alt-text="说明产品/服务列表页。":::

现在可以在合作伙伴中心的几个页面内收集用于在云合作伙伴门户中的单个页面中提供 Sku 的信息：

* "计划设置" 页
* 计划列表页
* 计划可用性页
* 规划技术配置页，如以下屏幕截图所示。

:::image type="content" source="media/migration-faq/tech-config-migration.png" alt-text="说明 "计划技术配置" 页。":::

产品/服务 ID 现在显示在 "产品/服务" 的左侧导航栏中。

:::image type="content" source="media/migration-faq/offer-id-offer-overview.png" alt-text="阐释带有产品 ID 的左侧导航菜单。":::

### <a name="stop-selling-an-offer"></a>停止销售产品/服务

你可以请求直接从合作伙伴中心门户停止在 marketplace 上[销售产品/服务](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)。 你的产品/**服务概述**页上提供了此选项。

:::image type="content" source="media/migration-faq/stop-selling-migration.png" alt-text="说明了 "产品/服务概述" 页和 "停止销售" 选项。":::

## <a name="what-pages-in-partner-center-correspond-to-pages-i-used-in-the-cloud-partner-portal"></a>合作伙伴中心中的哪些页面对应于在云合作伙伴门户中使用的页面？

下表显示了这两个门户之间的相应链接。

| 页 | 云合作伙伴门户链接 | 合作伙伴中心链接 |
|------|---------------------------|---------------------|
| **全部产品/服务页** | https://cloudpartner.azure.com/#alloffers | https://partner.microsoft.com/dashboard/commercial-marketplace/overview |
| **所有发布者页面** | https://cloudpartner.azure.com/#publishers | https://partner.microsoft.com/dashboard/account/v3/publishers/list |
| **发布者个人资料** | https://cloudpartner.azure.com/#profile | https://partner.microsoft.com/dashboard/account/management |
| **“用户”页** | https://cloudpartner.azure.com/#users | https://partner.microsoft.com/dashboard/account/usermanagement |
| **历史记录页** | https://cloudpartner.azure.com/#history | 伙伴中心目前尚不支持历史记录功能。 |
| **见解仪表板** | https://cloudpartner.azure.com/#insights | https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary |
| **支出报表** | https://cloudpartner.azure.com/#insights/payout | https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| | |

## <a name="will-the-cloud-partner-portal-rest-apis-be-supported-post-migration"></a>迁移后是否支持云合作伙伴门户 REST Api？

云合作伙伴门户 Api 与合作伙伴中心集成，并在将产品/服务迁移到合作伙伴中心后继续工作。 集成引入了少量更改。 查看下表中的更改，确保你的代码在迁移到合作伙伴中心后仍能正常工作。

| **API** | **更改描述** | **影响** |
| ------- | ---------------------- | ---------- |
| POST 发布，GoLive，取消 | 对于已迁移的产品/服务，响应标头将具有不同的格式，但会以相同的方式继续工作，这表示检索操作状态的相对路径。 | 发送产品/服务的任何相应 POST 请求时，位置标头将具有两种格式中的一种，具体取决于产品/服务的迁移状态：<ul><li>未迁移的产品<br>`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>已迁移产品<br>`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li> |
| 获取操作 | 对于先前在响应中受支持的 "通知电子邮件" 字段的产品/服务，将不再推荐使用此字段，并且不再为已迁移的产品返回此字段。 | 对于已迁移的产品/服务，我们将不再向请求中指定的电子邮件列表发送通知。 相反，API 服务将与合作伙伴中心发送电子邮件的通知电子邮件进程一致。 具体而言，通知将发送到合作伙伴中心的 "帐户设置" 的 "卖方联系人信息" 部分中的 "电子邮件地址" 设置，通知您操作进度。<br><br>查看合作伙伴中心的[帐户设置](https://partner.microsoft.com/dashboard/account/management)中的 "卖方联系人信息" 部分中的 "电子邮件地址" 设置，以确保为通知提供了正确的电子邮件。  |
| | | |
