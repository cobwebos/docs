---
title: "更改 Azure 订阅产品/服务 | Microsoft Docs"
description: "了解如何通过 Azure 帐户中心更改 Azure 订阅并切换为其他产品/服务"
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing,top-support-issue
ms.assetid: aae227b3-6d64-4550-a5b6-d359f53f0a59
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 08/30/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: a6bba6b3b924564fe7ae16fa1265dd4d93bd6b94
ms.openlocfilehash: 48f5f4db60c104778a6c391d254e0bc0c4c95cee
ms.contentlocale: zh-cn
ms.lasthandoff: 09/28/2017

---
# <a name="change-your-azure-pay-as-you-go-subscription-to-a-different-offer"></a>将 Azure 即用即付订阅更改为其他产品

[即用即付](https://azure.microsoft.com/offers/ms-azr-0003p/)客户可以在[帐户中心](https://account.windowsazure.com/Subscriptions)将 Azure 订阅转换为其他产品/服务。 例如，可以通过此功能充分利用 [Visual Studio 订户的每月信用额度](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)。 

只想从免费试用版升级？ 请参阅[升级到即用即付版](billing-upgrade-azure-subscription.md)。

## <a name="whats-supported"></a>支持的操作：

| 从 | 目标 |
| --- | --- |
| 即用即付 |[即用即付开发/测试](https://azure.microsoft.com/offers/ms-azr-0023p/) |
| 即用即付 |[Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/) |
| 即用即付 |[Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/) |
| 即用即付 |[MSDN 平台](https://azure.microsoft.com/offers/ms-azr-0062p/) |
| 即用即付 |[Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) |
| 即用即付 |[Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |

> [!NOTE]
> 有关其他产品/服务更改，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。
>
>

## <a name="switch-subscription-offer"></a>转换订阅产品/服务

> [!VIDEO https://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/Switch-to-a-different-Azure-offer/player]
>
>

1. 在 [Azure 帐户中心](https://account.windowsazure.com/Subscriptions)登录。
1. 选择“即用即付”订阅。
1. 单击“转换为其他产品/服务”。 仅当用户使用“即用即付”且已完成第一个计费周期的情况下，此按钮才可用。

   ![请注意页面右侧的“切换产品/服务”按钮](./media/billing-how-to-switch-azure-offer/switchbutton.png)
1. 从产品/服务列表中**选择所需产品/服务**，可以将订阅转换为这些产品/服务。 此列表因帐户的关联成员资格而异。 如果没有任何可用项目，请查看[可以转换为的可用产品/服务的列表](#whats-supported)，确保自己具有适当的成员身份。 

   ![选择要切换到的产品/服务](./media/billing-how-to-switch-azure-offer/selectoffer.png)
1. 根据要切换到的产品/服务，可能会看到一条有关切换所造成的影响的备注。 继续操作之前，请仔细查看此列表并遵循相关说明。

   ![查看说明](./media/billing-how-to-switch-azure-offer/thingstonote.png)
1. 可以重命名订阅。 默认情况下，我们会将它设置为新的产品/服务名称。 单击“转换产品/服务”完成该过程。

   ![单击绿色按钮](./media/billing-how-to-switch-azure-offer/confirmpage.png)
1. 成功！ 订阅现在已经切换到新的产品/服务。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="what-is-an-azure-offer"></a>什么是 Azure 产品/服务？

Azure 产品/服务是用户拥有的 Azure 订阅*类型*。 例如，[即用即付](https://azure.microsoft.com/offers/ms-azr-0003p/)、[Azure 开放许可](https://azure.microsoft.com/offers/ms-azr-0111p/)和 [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) 都是 Azure 产品/服务。 每个产品/服务附带不同的[条款](https://azure.microsoft.com/support/legal/offer-details/)，有些还附带特殊的权益。 可在帐户中心的订阅页中找到订阅的产品/服务。 单击产品/服务名称可获取更多详细信息。

   ![在帐户中心单击“产品/服务”链接可获取更多详细信息](./media/billing-how-to-switch-azure-offer/offerlink.png)

### <a name="why-dont-i-see-the-button"></a>为什么我看不到按钮？

如果出现以下情况，可能看不到“切换到其他产品/服务”按钮：

* 没有使用[即用即付](https://azure.microsoft.com/offers/ms-azr-0003p/)。 当前只能将即用即付订阅切换为其他产品/服务。
  * 如果使用的是[免费试用版](https://azure.microsoft.com/free/)，请了解如何[升级到即用即付](billing-upgrade-azure-subscription.md)。
  * 若要转换其他订阅的产品/服务，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。
* 用户仍处于第一个计费周期；必须等到第一个计费周期结束，才能转换产品/服务。

### <a name="why-do-i-see-there-are-no-offers-available-in-your-region-or-country-at-this-time"></a>为什么会出现“你所在的国家或地区目前没有可用的产品/服务”？

* 你可能没有进行任何产品/服务切换的资格。 请查看[可切换的可用产品/服务列表](#whats-supported)，确保已通过 Visual Studio 或 Bizspark 激活了正确的权益。
* 某些产品在部分国家/地区可能不适用。

### <a name="what-does-switching-azure-offers-do-to-my-service-and-billing"></a>转换 Azure 产品/服务对用户的服务和计费有何影响？

下面详述了在帐户中心切换 Azure 产品时所发生的情况。

#### <a name="no-service-downtime"></a>服务不会中断

与订阅关联的任何用户都不会出现服务无法使用的情况。 但是，切换到的产品/服务可能会有一些限制。 例如，某些产品/服务禁止生产性使用，因此，必须将生产资源移到另一个订阅。

#### <a name="quota-increases-are-reset"></a>配额增加被重置

切换产品时将重置任何[超过默认限制的限额或配额增加](../azure-supportability/resource-manager-core-quotas-request.md)。 即使更多资源超出默认限制也不会中断服务。 例如，如果订阅使用 200 个内核，那么切换产品会将内核配额重置回默认的 20 个内核。 使用 200 个内核的 VM 不受影响，将继续运行。 但如果不提出其他增加配额的请求，则将无法预配更多内核。

#### <a name="billing"></a>计费

在转换当天，将针对所有未付费用生成发票。 接下来，订阅将按新产品/服务的定价条款计费。 订阅计费周年会变为更改产品/服务的日期。 更改产品/服务之前的使用量与计费数据将不会保留，因此建议用户在转换之前先下载一个副本。

### <a name="can-i-migrate-from-pay-as-you-go-to-cloud-solution-providerhttpspartnermicrosoftcomsolutionscloud-reseller-overview-csp-or-enterprise-agreementhttpsazuremicrosoftcompricingenterprise-agreement-ea"></a>能否从即用即付转为[云解决方案提供商](https://partner.microsoft.com/Solutions/cloud-reseller-overview) (CSP) 或[企业协议](https://azure.microsoft.com/pricing/enterprise-agreement/) (EA)？

* 若要迁移到 CSP，请参阅[将 Azure 即用即付订阅迁移到 CSP](https://docs.microsoft.com/en-us/azure/cloud-solution-provider/migration/migration-from-payg-to-csp)。
* 要迁移到 EA，请让注册管理员将帐户加入 EA。 遵照邀请电子邮件中的说明，根据 EA 注册过程来移动订阅。 若要了解详细信息，请参阅 [Associate an Existing Account in the EA portal](https://ea.azure.com/helpdocs/associateExistingAccount)（在 EA 门户中关联现有帐户）。

### <a name="can-i-migrate-data-and-services-to-a-new-subscription"></a>是否可将数据和服务迁移到新订阅？

* 可以迁移直接将资源迁移到新订阅，具体请参阅[将资源移到新资源组或订阅中](../azure-resource-manager/resource-group-move-resources.md)。
* 要将 Azure 订阅的所有权及其包含的所有内容转让给其他某人，请参阅[转让 Azure 订阅的所有权](billing-subscription-transfer.md)

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员。

如果仍有疑问，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。

