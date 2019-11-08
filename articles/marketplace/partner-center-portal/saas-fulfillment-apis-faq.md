---
title: SaaS 履单 Api-常见问题 |Azure Marketplace
description: Azure Marketplace 中 SaaS 产品/服务的客户发现和购买体验。
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 2f8aa41604ac176181252fb7b30cdeb2f039d10f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822876"
---
# <a name="saas-fulfillment-apis---faq"></a>SaaS 履行 API - 常见问题解答

列出了用于启用 Azure 客户订阅 SaaS 产品/服务的 Azure Marketplace 集成要求。

## <a name="discovery-experience"></a>发现体验

发布产品/服务后，Azure 用户可以在 Azure Marketplace 中发现 SaaS 产品/服务。 你的客户将能够根据产品类型（SaaS）筛选产品/服务，并发现他们感兴趣的 SaaS 服务。

## <a name="purchase-experience"></a>购买体验

用户对特定 SaaS 服务感兴趣后，便可从 Azure Marketplace 订阅。

### <a name="what-does-it-mean-for-an-azure-user-to-subscribe-to-a-saas-offer-in-azure-marketplace"></a>Azure 用户在 Azure Marketplace 中订阅 SaaS 产品/服务有何意义？

这意味着，用户可以查看与 SaaS 服务相关联的使用条款和隐私声明，并同意根据你在 Microsoft 发票上提供的 "SaaS 条款" 发布者所设置的计费条款为其付费。 用户可以在 Azure 中使用其现有的付款配置文件来支付 SaaS 服务消耗量。

由于许多原因，这一点很有用。 现在，客户可以使用 Microsoft 云平台作为受信任的源在一个位置发现和订阅，而不必来审查要使用的每个 ISV 软件。 此外，客户可以使用其现有的付款配置文件，而无需单独单独支付每个 ISV 软件。

### <a name="is-the-user-charged-automatically-when-the-offer-is-subscribed"></a>订阅产品/服务时，用户是否自动收费？

订阅 SaaS 产品/服务时，用户同意通过 Microsoft 平台支付 SaaS 服务的使用费用。 但是，仅当使用产品/服务时，才会收取费用。 用户必须先前往 SaaS 产品/服务并确认帐户创建，才能开始使用该产品/服务。 然后，将通知 Microsoft 开始为此客户 SaaS 订阅付费。

### <a name="how-are-you-notified-when-a-user-subscribes-to-your-saas-offer"></a>当用户订阅 SaaS 产品/服务时，如何通知你？

订阅产品/服务后，Azure 用户可以在 Azure 中发现和管理其所有产品/服务。 默认情况下，新订阅的 SaaS 产品/服务的状态为 **"预配，履单待定"** 。 在此状态下，会提示 Azure 用户使用 **"配置帐户"** 操作，以便浏览到 Azure 门户中的 SaaS 订阅管理体验。

当用户单击 **"配置帐户"** 时，他们将被重定向到 SaaS 服务网站。 发布产品/服务时，发布者会提供导航到的 URL。 此页面称为发布者登录页。 Azure 用户应该能够根据其在 Azure 中的现有 AAD 凭据登录到 SaaS 登陆页面。

将 Azure 用户重定向到登陆页面后，会将一个令牌添加到查询 URL。 此令牌生存期较短，且有效期为24小时。 然后，你可以检测该令牌是否存在，并调用 Microsoft 的 API 来获取与该令牌关联的更多上下文。

![客户订阅流](media/saas-metering-service-integration-flow-a.png)

请参阅[saas 履单 API](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2)文档，了解有关在 SaaS 产品生命周期中处理事务的 api 约定的详细信息。

### <a name="how-do-you-know-the-saas-offer-to-which-the-user-subscribes-in-azure"></a>如何知道用户在 Azure 中订阅的 SaaS 产品/服务？

对 `Resolve` API 的响应包括与 SaaS 订阅相关联的产品/服务和计划信息。

### <a name="how-can-the-azure-user-change-the-plan-associated-with-this-azure-subscription"></a>Azure 用户如何更改与此 Azure 订阅关联的计划？

* Azure 用户可以直接在 SaaS 体验或 Microsoft 平台中更改与 SaaS 订阅关联的计划。

* 在计费周期内随时可以执行转换。 您必须确认任何转换，这将在确认后生效。

* 预先支付的计划（**每月**或**每年**）费率按比例收费。 在下一张发票中，将按下一张发票对发出到转换时间的任何超额费用收费。 新的超额将根据新计划发出。

>[!Note]
>如果您不想支持特定的转换路径，则可以阻止降级。

以下序列会在 Azure 客户更改 SaaS 体验中的计划时捕获流：

![客户计划更改流](media/saas-metering-service-integration-flow-b.png)

以下序列会在 Azure 客户更改 Microsoft 店面的计划时捕获流

![客户店面计划更改流](media/saas-metering-service-integration-flow-c.png)

### <a name="how-can-the-azure-user-unsubscribe-from-the-plan-associated-with-azure-subscription"></a>Azure 用户如何取消订阅与 Azure 订阅关联的计划？

Azure 用户可以直接在 SaaS 体验或 Microsoft 平台上取消订阅购买的 SaaS 产品/服务。 用户取消订阅后，将不再从下一个计费周期向他们收费。

Azure 客户在 SaaS 体验中取消订阅 SaaS 产品/服务时，以下序列捕获流：

![在 SaaS 体验中取消订阅客户](media/saas-metering-service-integration-flow-d.png)

Azure 用户在 Microsoft 店面中取消订阅时，以下序列捕获流：

![Microsoft 店面的客户取消订阅](media/saas-metering-service-integration-flow-e.png)

## <a name="next-steps"></a>后续步骤

- 有关详细信息，请参阅[Marketplace 计量服务 api](./marketplace-metering-service-apis.md) 。
