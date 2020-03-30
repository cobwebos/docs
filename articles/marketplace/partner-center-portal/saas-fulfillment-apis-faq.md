---
title: SaaS 履行 API - 常见问题 |Azure 应用商店
description: Azure 应用商店中 SaaS 产品的客户发现和购买体验。
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 6d3a84341d5221950da20f39456461dafc5d2e75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275690"
---
# <a name="saas-fulfillment-apis---faq"></a>SaaS 履行 API - 常见问题解答

列出了与 Azure 应用商店的集成要求，以使 Azure 客户能够订阅 SaaS 产品/服务。

## <a name="discovery-experience"></a>发现体验

发布产品/服务后，Azure 用户可以在 Azure 应用商店中发现 SaaS 产品/服务。 您的客户将能够根据产品类型 （SaaS） 筛选产品/服务，并了解他们感兴趣的 SaaS 服务。

## <a name="purchase-experience"></a>购买体验

用户对特定的 SaaS 服务感兴趣后，用户可以从 Azure 应用商店订阅该服务。

### <a name="what-does-it-mean-for-an-azure-user-to-subscribe-to-a-saas-offer-in-azure-marketplace"></a>Azure 用户订阅 Azure 应用商店中的 SaaS 产品/服务意味着什么？

这意味着用户可以查看与 SaaS 服务关联的使用条款和隐私声明，并同意根据您（SaaS 产品发布者）在 Microsoft 发票上设置的计费条款付款。 用户可以使用 Azure 中的现有付款配置文件来为 SaaS 服务消耗付费。

这有很多原因。 客户现在可以使用 Microsoft 云平台作为受信任的来源在一个地方发现和订阅，而无需审查它打算使用的每个 ISV 软件。 此外，客户可以使用其现有的付款配置文件，而无需单独显式支付每个 ISV 软件。

### <a name="is-the-user-charged-automatically-when-the-offer-is-subscribed"></a>订阅产品/服务时，用户是否自动收费？

在订阅 SaaS 优惠时，用户已同意通过 Microsoft 平台支付 SaaS 服务的使用费用。 但是，费用仅在消费产品/服务时开始。 用户必须转到您的 SaaS 产品/服务并确认帐户创建才能开始使用产品/服务。 然后，您将通知 Microsoft 开始为此客户 SaaS 订阅计费。

### <a name="how-are-you-notified-when-a-user-subscribes-to-your-saas-offer"></a>当用户订阅您的 SaaS 产品/服务时，您如何收到通知？

订阅产品/服务后，Azure 用户可以在 Azure 中发现和管理其所有产品/服务。 默认情况下，新订阅的 SaaS 产品/服务的状态为 **"预配，正在履行"。** 在此状态下，将提示 Azure 用户执行 **"配置帐户"** 的操作，以便浏览到 Azure 门户中的 SaaS 订阅管理体验。

当用户单击 **"配置帐户"** 时，他们将重定向到 SaaS 服务网站。 导航到它们的 URL 由发布者在发布产品/服务时提供。 此页称为发布者的着陆页。 Azure 用户应该能够基于 Azure 中现有的 AAD 凭据登录到 SaaS 登录页。

将 Azure 用户重定向到登录页时，将标记添加到查询 URL 中。 此令牌持续时间短，有效期为 24 小时。 然后，可以检测此令牌的存在，并调用 Microsoft 的 API 以获取有关令牌的更多上下文。

![客户订阅流](media/saas-metering-service-integration-flow-a.png)

有关在 SaaS 产品生命周期内处理交易方案的 API 合同的详细信息，请参阅[SaaS 履行 API](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2)文档。

### <a name="how-do-you-know-the-saas-offer-to-which-the-user-subscribes-in-azure"></a>您如何知道用户在 Azure 中订阅的 SaaS 产品/服务？

对 API`Resolve`的响应包括与 SaaS 订阅关联的产品/服务并计划信息。

### <a name="how-can-the-azure-user-change-the-plan-associated-with-this-azure-subscription"></a>Azure 用户如何更改与此 Azure 订阅关联的计划？

* Azure 用户可以直接在 SaaS 体验中或通过 Microsoft 平台更改与 SaaS 订阅关联的计划。

* 转换可以在计费周期的任何时间完成。 您必须确认任何转换，一旦确认，该转换将生效。

* 预付费计划（**每月**或**每年**）费率按比例计算。 在转换时间前发出的任何超额费用将在下一张发票中收取。 将根据新计划排放新的超额。

>[!Note]
>如果不想支持特定的转换路径，则可以阻止降级。

以下序列捕获 Azure 客户在 SaaS 体验中更改计划时的流：

![客户计划变更流程](media/saas-metering-service-integration-flow-b.png)

以下序列捕获 Azure 客户在 Microsoft 网店中更改计划时的流

![客户店面计划变更流程](media/saas-metering-service-integration-flow-c.png)

### <a name="how-can-the-azure-user-unsubscribe-from-the-plan-associated-with-azure-subscription"></a>Azure 用户如何取消订阅与 Azure 订阅关联的计划？

Azure 用户可以直接在 SaaS 体验中或通过 Microsoft 平台取消订阅已购买的 SaaS 产品。 用户取消订阅后，将不再从下一个计费周期中收取费用。

以下序列捕获 Azure 客户取消订阅 SaaS 体验中 SaaS 产品/服务的流：

![客户在 SaaS 体验中取消订阅](media/saas-metering-service-integration-flow-d.png)

以下序列捕获 Azure 用户在 Microsoft 网店中取消订阅时的流：

![客户在微软的网店取消订阅](media/saas-metering-service-integration-flow-e.png)

## <a name="next-steps"></a>后续步骤

- 有关详细信息[，请参阅应用商店计量服务 API。](./marketplace-metering-service-apis.md)
