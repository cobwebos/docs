---
title: "更改 Azure 订阅产品/服务 | Microsoft Docs"
description: "了解如何通过订阅管理门户更改 Azure 订阅并转换为其他产品/服务"
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
ms.topic: article
ms.date: 02/03/2017
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 09c18ca6a967c2930ddd2b16d37f0bc606712ea1
ms.openlocfilehash: 67657ac7135f55cc4560a53c5ce76d7a62426928


---
# <a name="switch-your-azure-subscription-to-another-offer"></a>将 Azure 订阅切换到其他产品/服务
作为[即用即付](https://azure.microsoft.com/offers/ms-azr-0003p/)客户，用户可以在[帐户中心](https://account.windowsazure.com/Subscriptions)将 Azure 订阅转换为其他产品/服务。 例如，可以通过此功能充分利用 [Visual Studio 订户的每月信用额度](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)。 如果使用的是[免费试用版](https://azure.microsoft.com/free/)，请了解如何[升级到即用即付](billing-upgrade-azure-subscription.md)。

#### <a name="whats-supported"></a>支持的操作：
| 从 | 如果 |
| --- | --- |
| [即用即付](https://azure.microsoft.com/offers/ms-azr-0003p/) |[即用即付开发/测试](https://azure.microsoft.com/offers/ms-azr-0023p/) |
| [即用即付](https://azure.microsoft.com/offers/ms-azr-0003p/) |[Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/) |
| [即用即付](https://azure.microsoft.com/offers/ms-azr-0003p/) |[Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/) |
| [即用即付](https://azure.microsoft.com/offers/ms-azr-0003p/) |[MSDN 平台](https://azure.microsoft.com/offers/ms-azr-0062p/) |
| [即用即付](https://azure.microsoft.com/offers/ms-azr-0003p/) |[Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) |
| [即用即付](https://azure.microsoft.com/offers/ms-azr-0003p/) |[Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |

> [!NOTE]
> 有关其他产品/服务更改，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。
> 
> 

## <a name="switch-subscription-offer"></a>转换订阅产品/服务
> [!VIDEO https://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/Switch-to-a-different-Azure-offer/player]
> 
> 

1. 在 [Azure 帐户中心](https://account.windowsazure.com/Subscriptions)登录。
2. 选择“即用即付”订阅。
3. 单击“转换为其他产品/服务”。 仅当用户使用“即用即付”且已完成第一个计费周期的情况下，此按钮才可用。
   
   ![请注意页面右侧的“切换产品/服务”按钮](./media/billing-how-to-switch-azure-offer/switchbutton.png)
4. 从产品/服务列表中**选择所需产品/服务**，可以将订阅转换为这些产品/服务。 此列表因帐户的关联成员资格而异。 如果没有任何可用项目，请查看[可以转换为的可用产品/服务的列表](#whats-supported)，确保自己具有适当的成员身份。 
   
   ![选择要切换到的产品/服务](./media/billing-how-to-switch-azure-offer/selectoffer.png)
5. 根据要切换到的产品/服务，你可能会看到一条有关切换所造成的影响的备注。 继续操作之前，请仔细查看此列表并遵循相关说明。
   
   ![查看说明](./media/billing-how-to-switch-azure-offer/thingstonote.png)
6. 可以重命名订阅。 默认情况下，我们会将它设置为新的产品/服务名称。 单击“转换产品/服务”完成该过程。
   
   ![单击绿色按钮](./media/billing-how-to-switch-azure-offer/confirmpage.png)
7. 成功！ 订阅现在已经切换到新的产品/服务。

## <a name="why-cant-i-switch-offers"></a>为何不能转换产品/服务？
如果出现以下情况，则可能看不到“转换为其他产品/服务”：

* 没有使用[即用即付](https://azure.microsoft.com/offers/ms-azr-0003p/)。 当前只能将即用即付订阅转换为其他产品/服务。
  
  * 如果使用的是[免费试用版](https://azure.microsoft.com/free/)，请了解如何[升级到即用即付](billing-upgrade-azure-subscription.md)。
  * 若要转换其他订阅的产品/服务，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。
* 用户仍处于第一个计费周期；必须等到第一个计费周期结束，才能转换产品/服务。

如果出现以下情况，则可能会显示“你所在的国家或地区目前没有可用的产品/服务”：

* 用户没有资格进行任何产品/服务转换。 请查看[可以转换为的可用产品/服务的列表](#whats-supported)。

## <a name="what-does-switching-azure-offers-do-to-my-service-and-billing"></a>转换 Azure 产品/服务对用户的服务和计费有何影响？
下面详述了在帐户中心转换 Azure 计划时所发生的情况。

### <a name="access-to-services"></a>访问服务
与订阅关联的任何用户都不会出现服务无法使用的情况。 但是，你切换到的产品/服务可能会有一些限制。 例如，某些产品/服务禁止生产性使用，因此，必须将生产资源移到另一个订阅。

### <a name="billing"></a>计费
在转换当天，将针对所有未付费用生成发票。 接下来，订阅将按新产品/服务的定价条款计费。 订阅计费周年会变为更改产品/服务的日期。 更改产品/服务之前的使用量与计费数据将不会保留，因此建议用户在转换之前先下载一个副本。

> [!NOTE]
> 因为计费相关约束，产品/服务无法在创建订阅之后的第一个计费周期内转换。
> 
> 

## <a name="can-i-migrate-from-pay-as-you-go-to-cloud-solution-providerhttpspartnermicrosoftcomsolutionscloud-reseller-overview-csp-or-enterprise-agreementhttpsazuremicrosoftcompricingenterprise-agreement-ea"></a>能否从即用即付转为[云解决方案提供商](https://partner.microsoft.com/Solutions/cloud-reseller-overview) (CSP) 或[企业协议](https://azure.microsoft.com/pricing/enterprise-agreement/) (EA)？
目前不支持在帐户中心将产品/服务转换为 CSP 或 EA。 若要将现有订阅移至 EA，请让注册管理员将帐户添加到 EA。 接着，用户会收到一封邀请电子邮件。 在按照说明接受邀请后，订阅会自动移到企业协议下。 若要迁移到 CSP，请参阅 [Azure Subscription Migration to CSP](https://blogs.technet.microsoft.com/hybridcloudbp/2016/08/26/azure-subscription-migration-to-csp/)（Azure 订阅迁移到 CSP）。

## <a name="can-i-migrate-data-and-services-for-my-azure-subscription-to-a-new-subscription"></a>能否将 Azure 订阅的数据和服务迁移到新的订阅？
是的。 有关详细信息，请参阅[将资源移到新资源组或订阅](../azure-resource-manager/resource-group-move-resources.md)。

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员。
如果仍有疑问，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。




<!--HONumber=Feb17_HO2-->


