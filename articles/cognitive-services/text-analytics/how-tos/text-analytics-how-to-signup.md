---
title: 注册文本分析 API（Azure 上的 Microsoft 认知服务）| Microsoft Docs
description: 说明如何在注册后使用文本分析以及如何在受限情况下运行。
services: cognitive-services
author: HeidiSteen
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: get-started-article
ms.date: 3/07/2018
ms.author: heidist
ms.openlocfilehash: dfa5ba138a2e0db75dfc097ca2430fe9c82e826f
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2018
ms.locfileid: "39623245"
---
# <a name="how-to-sign-up-for-text-analytics-api"></a>如何注册文本分析 API

文本分析资源在云中全天候提供。 在上传需分析的内容之前，必须通过注册获取访问密钥。 每次调用 API 都需要提供请求的访问密钥。

+ 从 Azure 订阅着手。 可以创建[免费帐户](https://azure.microsoft.com/free/)进行试验，无需支付任何费用。

+ 创建[认知服务 API 帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)，选择“文本分析 API”。 密钥在注册时生成。

就文本分析来说，我们提供供用户浏览和评估的“免费”层，以及适用于生产性工作负荷的计费层。 每个订阅都有多种注册选项：免费、付费，等等。 如果请求量增加，可以切换到提供更多事务的层。

预览版或免费层的服务没有服务级别协议。 有关详细信息，请参阅[认知服务的 SLA](https://azure.microsoft.com/support/legal/sla/cognitive-services/v1_1/)

## <a name="how-to-change-tiers"></a>如何更改层

先从“免费”层开始，然后过渡到适用于生产性工作负荷的计费层。 标准计费在提供时分不同的级别。 可以在切换层时仍保留相同的终结点和访问密钥。

1. 登录到 [Azure 门户](https://portal.azure.com)，[找到你的服务](text-analytics-how-to-access-key.md)。

2. 单击“价格层”。

   ![左侧导航菜单中的价格层命令](../media/portal-pricing-tier.png)

3. 选择一个层，然后单击“选择”。  所做的选择得到处理以后，新限制就会生效。 

   ![层选择页中的层和“选择”按钮](../media/portal-choose-tier.png)

## <a name="how-billing-works"></a>计费原理

计费取决于事务数。 你可以在特定层购买一批按月计费的事务，如果超出此限制，我们会按事务收取少量的超额费用。 如果经常性地超出最大限制，请考虑转到更高的层。

有关详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)。

### <a name="what-constitutes-a-transaction-in-the-text-analytics-api"></a>文本分析 API 中的事务由什么构成？
文档的任何注释都算作事务。 批处理评分调用也会考虑该事务中需评分的文档数。 因此，举例来说，如果通过一次 API 调用发送 1,000 份文档供情绪分析，系统会将其记为 1,000 个事务。

## <a name="see-also"></a>另请参阅 

 [文本分析概述](../overview.md)  
 [常见问题解答 (FAQ)](../text-analytics-resource-faq.md)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [获取访问密钥](text-analytics-how-to-access-key.md)
