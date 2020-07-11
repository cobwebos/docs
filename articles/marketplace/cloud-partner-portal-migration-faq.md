---
title: 有关从云合作伙伴门户过渡到合作伙伴中心的常见问题-Microsoft 商用 marketplace
description: 从云合作伙伴门户向合作伙伴中心转换产品/服务的常见问题的解答。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: anbene
ms.author: mingshen
ms.date: 06/29/2020
ms.openlocfilehash: e708d9a32d63c306a2d1ee8d06b044652f108dde
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2020
ms.locfileid: "86231873"
---
# <a name="frequently-asked-questions-about-transitioning-from-the-cloud-partner-portal-to-partner-center"></a>有关从云合作伙伴门户过渡到合作伙伴中心的常见问题

云合作伙伴门户已转换为合作伙伴中心。 合作伙伴中心为在[Microsoft AppSource](https://appsource.microsoft.com/)或[Azure Marketplace](https://azuremarketplace.microsoft.com/)上发布新产品/服务，以及用于管理从云合作伙伴门户迁移的现有产品/服务提供了一种简单、集成的体验。 云合作伙伴门户的所有功能都可在合作伙伴中心获得。 本文介绍有关此问题的常见问题解答。

## <a name="what-does-the-transition-to-partner-center-mean-for-me"></a>过渡到合作伙伴中心的含义是什么？

你可以在合作伙伴中心继续照常开展业务：

| 区域<img src="" width=200px> | 更改 |
| --- | --- |
| 帐户 | 无需创建新的合作伙伴中心帐户;你可以使用现有的云合作伙伴门户凭据登录到合作伙伴中心，现在你将在其中管理你的帐户、用户、权限和计费。 发布协议和公司配置文件信息将迁移到新的合作伙伴中心帐户，以及任何付出的配置文件信息、用户帐户和权限以及活动的产品/服务。 有关详细信息，请参阅[在合作伙伴中心管理商业 marketplace 帐户](partner-center-portal/manage-account.md)。 |
| 提供发布和提供管理体验 | 我们已将你的产品/服务数据从云合作伙伴门户移动到合作伙伴中心。 你现在可以在合作伙伴中心访问你的产品/服务，它提供改进的用户体验和直观的界面。 了解如何[在商业应用商店中更新现有产品/服务](partner-center-portal/update-existing-offer.md)。 |
| 你的产品/服务在商业应用商店中的可用性 | 无更改。 如果产品/服务在商业应用商店中生活，则它将继续生效。 |
| 新的购买和部署 | 无更改。 你的客户可以继续购买和部署你的产品/服务，而不会出现任何中断。 |
| 付款 | 任何购买和部署都将继续按常规方式支付给你。 详细了解如何[在商业市场中付费](partner-center-portal/get-paid.md)。 |
| API 与现有[云合作伙伴门户 API](cloud-partner-portal-api-overview.md) 的集成 | 仍支持现有云合作伙伴门户 Api，现有集成仍可正常工作。 要了解详细信息，[请参阅是否支持云合作伙伴门户 REST api？](#are-the-cloud-partner-portal-rest-apis-still-supported) |
| 分析 | 可以通过在合作伙伴中心查看分析来继续监视销售、评估性能和优化商业应用商店中的产品/服务。 有关详细信息，请参阅[合作伙伴中心的商业市场访问分析报表](partner-center-portal/analytics.md)。 |
|||

## <a name="do-i-need-to-create-a-new-account-to-manage-my-offers-in-partner-center"></a>是否需要创建新帐户来管理合作伙伴中心提供的产品/服务？

不会，将保留你的帐户。 这意味着，如果你是现有合作伙伴，则可以使用现有的云合作伙伴门户帐户凭据登录到合作伙伴中心。 请勿创建新帐户或在云合作伙伴门户中创建的任何产品/服务，并且不会将其关联到合作伙伴中心。

## <a name="what-pages-in-partner-center-correspond-to-pages-i-used-in-the-cloud-partner-portal"></a>合作伙伴中心中哪些页面与我在云合作伙伴门户中所用的页面相对应？

下面是云合作伙伴门户中常用页面的合作伙伴中心链接。 如果将云合作伙伴门户链接保存为书签，则需要对其进行更新。

| 云合作伙伴门户页面 <img src="" width=100px>| 云合作伙伴门户页面链接 | 合作伙伴中心页面链接 |
| --- | --- | --- |
| 全部产品/服务页 | [https://cloudpartner.azure.com/#alloffers](https://cloudpartner.azure.com/#alloffers) | [https://partner.microsoft.com/dashboard/commercial-marketplace/overview](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) |
| 所有发布者页面 | [https://cloudpartner.azure.com/#publishers](https://cloudpartner.azure.com/#publishers) | [https://partner.microsoft.com/dashboard/account/v3/publishers/list](https://partner.microsoft.com/dashboard/account/v3/publishers/list) |
| 发布者资料 | [https://cloudpartner.azure.com/#profile](https://cloudpartner.azure.com/#profile) | [https://partner.microsoft.com/dashboard/account/management](https://partner.microsoft.com/dashboard/account/management) |
| “用户”页 | [https://cloudpartner.azure.com/#users](https://cloudpartner.azure.com/#users) | [https://partner.microsoft.com/dashboard/account/usermanagement](https://partner.microsoft.com/dashboard/account/usermanagement) |
| “历史记录”页面 | [https://cloudpartner.azure.com/#history](https://cloudpartner.azure.com/#history) | 合作伙伴中心尚不支持“历史记录”功能。 |
| 见解仪表板 | [https://cloudpartner.azure.com/#insights](https://cloudpartner.azure.com/#insights) | [https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary) |
| 支出报表 | [https://cloudpartner.azure.com/#insights/payout](https://cloudpartner.azure.com/#insights/payout) | [https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments](https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments) |
|||

## <a name="what-about-offers-i-published-in-the-cloud-partner-portal"></a>在云合作伙伴门户中发布的产品/服务有哪些？

提供的产品/服务已移至合作伙伴中心，在你登录到合作伙伴中心后，你将可以访问这些产品/服务，但 Dynamics Nav 托管服务和 Cortana Intelligence 提供的除外。 如果你的产品/服务在商业应用商店中，它将继续保持活动，并且你的客户将能够继续在不中断的情况下进行购买和部署。 有关更多详细信息，请参阅下一个问题：**哪些产品/服务已移动到合作伙伴中心？**。

## <a name="what-offers-were-moved-to-partner-center"></a>哪些产品/服务已移动到合作伙伴中心？

除了 Dynamics Nav 托管服务和 Cortana Intelligence 产品/服务以外，云合作伙伴门户中的所有产品/服务类型都受合作伙伴中心支持。

对于合作伙伴中心支持的产品/服务类型，所有产品/服务都已移动，不管其状态如何;草稿、未列出和仅预览的产品/服务也被移动。

| 产品/服务类型 <img src="" width=150px>| 已移动到合作伙伴中心？ <img src="" width=100px>| 后续步骤 |
| --- | --- | --- |
| SaaS | 适合 | 登录到合作伙伴中心，创建新产品/服务，并管理在云合作伙伴门户中创建的产品/服务。 有关详细信息，请参阅在[商用 marketplace 中创建新的 SaaS 产品](partner-center-portal/create-new-saas-offer.md)。 |
| 虚拟机 | 适合 | 登录到合作伙伴中心，创建新产品/服务，并管理在云合作伙伴门户中创建的产品/服务。 有关详细信息，请参阅[在 Azure Marketplace 上创建 azure 虚拟机产品/服务](partner-center-portal/azure-vm-create-offer.md)。 |
| Azure 应用程序 | 适合 | 登录到合作伙伴中心，创建新产品/服务，并管理在云合作伙伴门户中创建的产品/服务。 有关详细信息，请参阅[创建 Azure 应用程序产品/服务](partner-center-portal/create-new-azure-apps-offer.md)。 |
| Dynamics 365 Business Central | 是 | 登录到合作伙伴中心，创建新产品/服务，并管理在云合作伙伴门户中创建的产品/服务。 有关详细信息，请参阅[创建 Dynamics 365 Business Central 产品](partner-center-portal/create-new-business-central-offer.md)。 |
| Dynamics 365 for Customer Engagement & PowerApps | 适合 | 登录到合作伙伴中心，创建新产品/服务，并管理在云合作伙伴门户中创建的产品/服务。 有关详细信息，请参阅[创建 Dynamics 365 For Customer Engagement & PowerApps 产品](partner-center-portal/create-new-customer-engagement-offer.md)。 |
| Dynamics 365 for Operations | 适合 | 登录到合作伙伴中心，创建新产品/服务，并管理在云合作伙伴门户中创建的产品/服务。 有关详细信息，请参阅[创建适用于操作的 Dynamics 365](partner-center-portal/create-new-operations-offer.md)。 |
| Power BI 应用 | 适合 | 登录到合作伙伴中心，创建新产品/服务，并管理在云合作伙伴门户中创建的产品/服务。 有关详细信息，请参阅[创建适用于 AppSource 的 Power BI 应用](partner-center-portal/create-power-bi-app-offer.md)。 |
| IoT Edge 模块 | 适合 | 登录到合作伙伴中心，创建新产品/服务，并管理在云合作伙伴门户中创建的产品/服务。 有关详细信息，请参阅在[Azure Marketplace 中创建、配置和发布 IoT Edge 模块产品](partner-center-portal/azure-iot-edge-module-creation.md)。 |
| 容器 | 适合 | 登录到合作伙伴中心，创建新产品/服务，并管理在云合作伙伴门户中创建的产品/服务。 有关详细信息，请参阅[创建 Azure 容器产品](partner-center-portal/create-azure-container-offer.md)。 |
| 咨询服务 | 适合 | 登录到合作伙伴中心，创建新产品/服务，并管理在云合作伙伴门户中创建的产品/服务。 请在[创建咨询服务产品](partner-center-portal/create-consulting-service-offer.md)上了解详细信息。 |
| 托管服务 | 适合 | 登录到合作伙伴中心，创建新产品/服务，并管理在云合作伙伴门户中创建的产品/服务。 请在[创建托管服务产品](partner-center-portal/create-new-managed-service-offer.md)中了解详细信息。 |
| Dynamics Nav 托管服务 | 不适合 | Microsoft 已将 Dynamics NAV 托管服务发展为[dynamics 365 Business Central](https://docs.microsoft.com/dynamics365/business-central/)，因此我们从 AppSource 中取消列出了 Dynamics Nav 托管服务实时优惠。 这些产品/服务不再被客户发现，也没有移动到合作伙伴中心。 若要在 AppSource 中提供产品/服务，请将其调整为 Dynamics 365 Business Central 产品/服务，并将其提交到[合作伙伴中心](https://partner.microsoft.com/)。 有关详细信息，请参阅[创建 Dynamics 365 Business Central 产品](partner-center-portal/create-new-business-central-offer.md)。 |
| Cortana Intelligence | 不适合 | Microsoft 为 Cortana Intelligence 发展了产品路线图，因此我们从 AppSource 中取消列出了 Cortana Intelligence live 产品。 这些产品/服务不再被客户发现，也没有移动到合作伙伴中心。 若要在商业应用商店中提供产品/服务，请将你的产品/服务作为服务 (SaaS) 提供，并将其提交到[合作伙伴中心](https://partner.microsoft.com/)。 有关详细信息，请参阅在[合作伙伴中心创建清单](partner-center-portal/offer-creation-checklist.md)。 |

## <a name="i-cant-find-my-cloud-partner-portal-offers-in-partner-center"></a>在合作伙伴中心找不到我的云合作伙伴门户产品/服务

在 "合作伙伴中心" 中看到的内容取决于你在中注册的程序、你所属的帐户，以及已分配的用户角色和权限。 有很多合作伙伴中心计划可用，你可以在多个程序中注册。 你还可以访问具有相同用户凭据的多个帐户。

在云合作伙伴门户中创建的产品/服务在 "**商业市场**" 计划下的 "合作伙伴中心" 下提供，并在用于创建产品/服务的帐户下提供。 若要确保您查看的是正确的程序和正确的帐户，请按照以下步骤进行操作。 有关其他疑难解答提示，请参阅[管理合作伙伴中心帐户](https://docs.microsoft.com/partner-center/partner-center-account-setup)。

### <a name="access-the-right-program-in-partner-center"></a>在合作伙伴中心访问正确的程序

1. 使用登录云合作伙伴门户时所用的相同凭据登录到[合作伙伴中心](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)。 左侧的导航窗格显示与注册的程序相关联的选项。

    示例：假设你有权访问三个程序： MPN 计划、推荐计划和商用 Marketplace 计划。 登录到合作伙伴中心时，会在导航窗格中看到这三个程序。

2. 选择 "**商业市场**  >  **概述**" 以访问你的产品/服务。

    如果左侧导航窗格中未显示 "商用 Marketplace 计划"，则可能是错误的帐户。 按照下一部分中的步骤访问正确的帐户。

    [![](media/cpp-pc-faq/overview-menu.png "Shows the Partner Center Overview menu")](media/cpp-pc-faq/overview-menu.png#lightbox)

### <a name="access-the-right-account-in-partner-center"></a>访问合作伙伴中心的正确帐户

如果你是多个帐户的一部分，请在合作伙伴中心中看到一个帐户选取器按钮，该按钮由左侧导航菜单中的两个箭头标记。 选择 "帐户选取器" 按钮以查看您所属的所有帐户的列表。 选择列表中的任何帐户以切换到该帐户，并查看与该帐户相关的所有程序和信息。 如果在导航菜单中看不到 "帐户选取器" 按钮，则您是单个帐户的成员。

[![](media/cpp-pc-faq/picker-button.png "Shows the Partner Center account picker button")](media/cpp-pc-faq/picker-button.png#lightbox)

## <a name="how-do-i-create-new-offers"></a>如何实现创建新产品/服务？

访问[合作伙伴中心](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)的 "商用 marketplace" 计划，以创建新产品/服务。 在 "概述" 页上，选择 " **+ 新产品/服务**"。

[![](media/cpp-pc-faq/new-offer.png "Shows the Partner Center Overview menu")](media/cpp-pc-faq/new-offer.png#lightbox)

## <a name="i-cant-sign-in-and-need-to-open-a-support-ticket"></a>我无法登录，需要打开支持票证

如果无法登录到帐户，可以在此处打开[支持票证](https://partner.microsoft.com/support/v2/?stage=1)。

## <a name="where-are-instructions-for-using-partner-center"></a>使用合作伙伴中心的说明位于何处？

请参阅[商业应用商店文档](index.yml)，然后展开**合作伙伴中心的 "商用 marketplace 门户**"。 若要查看有关在合作伙伴中心创建产品/服务的帮助文章，请展开 "**创建新产品/服务**"。

## <a name="what-are-the-publishing-and-offer-management-differences"></a>发布和产品/服务管理之间的区别是什么？

下面是云合作伙伴门户和合作伙伴中心之间的一些差异。

### <a name="modular-publishing-capabilities"></a>模块化发布功能

合作伙伴中心提供了一个模块化发布功能，你可用它来选择你想要发布的更改，而不是总是一次性发布所有更新。 例如，下面的屏幕显示，仅选择要发布的更改是对**属性**和**服务列表**的更改。 您在预览页中所做的更改将不会发布。

[![](media/cpp-pc-faq/review-page.png "Shows the Partner Center Review and publish page")](media/cpp-pc-faq/review-page.png#lightbox)

未发布的更新将保存为草稿。 在上线提供给公众之前，可继续使用产品/服务预览来验证你的产品/服务。

### <a name="enhanced-preview-options"></a>强化预览选项

合作伙伴中心包含一个[“比较”功能](partner-center-portal/update-existing-offer.md#compare-changes-to-marketplace-offers)，其中有改进后的筛选选项。 这使你能够根据产品/服务的预览版本和上线版本。

[![](media/cpp-pc-faq/compare.png "Shows the Partner Center compare feature")](media/cpp-pc-faq/compare.png#lightbox)

### <a name="branding-and-navigation-changes"></a>品牌和导航更改

你将注意到有一些品牌方面的更改。 例如， *sku*作为合作伙伴中心中的*计划*进行署名：

[![](media/cpp-pc-faq/plans.png "Shows the Partner Center Plans page")](media/cpp-pc-faq/plans.png#lightbox)

此外，你之前在**Marketplace**或**店面详细**信息中提供的信息 (咨询服务，云合作伙伴门户中的 Power BI 应用) 页现在将收集到合作伙伴中心的**产品/服务列表**页：

[![](media/cpp-pc-faq/offer-listing.png "Shows the Partner Center Offer listing page")](media/cpp-pc-faq/offer-listing.png#lightbox)

你之前在云合作伙伴门户中的一个页面中提供的 Sku 信息现在可以在合作伙伴中心的几个页面中收集：

- 计划设置页面
- 计划列表页面
- 计划可用性页面
- 规划技术配置页，如下所示： ![ ！ [] (media/cpp-pc-常见问题解答/technical-configuration.png "显示合作伙伴中心技术配置页" ) ](media/cpp-pc-faq/technical-configuration.png#lightbox)

产品/服务 ID 现在显示在 "产品/服务" 的左侧导航栏上：

![显示合作伙伴中心产品 ID 位置](media/cpp-pc-faq/offer-id.png)

### <a name="stop-selling-an-offer"></a>停止销售产品/服务

你可直接通过合作伙伴中心门户请求在市场上[停止销售某款产品/服务](partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan)。 可在产品/服务的“产品/服务概述”页面上找到该选项。

[![](media/cpp-pc-faq/stop-sell.png "Shows the Partner Center page to stop selling an offer")](media/cpp-pc-faq/stop-sell.png#lightbox)
<br><br>

## <a name="are-the-cloud-partner-portal-rest-apis-still-supported"></a>是否仍支持云合作伙伴门户 REST Api？

云合作伙伴门户 Api 与合作伙伴中心集成，并将继续工作。 过渡到合作伙伴中心会引入小更改。 请查看下表，以确保你的代码在合作伙伴中心中继续工作。

| API <img src="" width=100px>| 更改描述 | 影响 |
| --- | --- | --- |
| POST 发布、上线、取消 | 对于已迁移的产品/服务，响应标头将采用不同的格式，但将以相同的方式继续工作，这表示有一个相对路径用于检索操作状态。 | 发送产品/服务的任何相应 POST 请求时，位置标头将具有两种格式中的一种，具体取决于产品/服务的迁移状态： <ul><li>未迁移的产品/服务：`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>迁移的产品/服务：`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li></ul>|
| GET 操作 | 对于之前支持响应中的 "通知电子邮件" 字段的产品/服务，此字段将被弃用，并且不再为已迁移的产品/服务返回。 | 对于已迁移的产品/服务，我们将不再向请求中指定的一列电子邮件地址发送通知。 相反，API 服务将与 合作伙伴中心内的通知电子邮件进程保持一致来发送电子邮件。 具体而言，操作进度通知将发送到合作伙伴中心的 "帐户设置" 的 "卖方联系人信息" 部分中的电子邮件地址集。<br><br>确保在 "合作伙伴中心" 中的 "[帐户设置](https://partner.microsoft.com/dashboard/account/management)" 部分的 "销售人员联系信息" 部分中设置的电子邮件地址正确地接收通知。 |
|||