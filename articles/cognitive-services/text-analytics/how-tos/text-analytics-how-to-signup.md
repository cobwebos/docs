---
title: 注册文本分析 API
titleSuffix: Azure Cognitive Services
description: 有关注册和使用文本分析服务的说明。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: 53532a19482a33f8727e71d44ae169225b5b1c98
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60829631"
---
# <a name="how-to-sign-up-for-the-text-analytics-api"></a>如何注册文本分析 API

文本分析资源在云中全天候提供。 在上传需分析的内容之前，必须通过注册获取访问密钥。 每次调用 API 都需要提供请求的访问密钥。

+ 从 Azure 订阅着手。 可以创建[免费帐户](https://azure.microsoft.com/free/)进行试验，无需支付任何费用。

+ 创建[认知服务 API 帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)，选择“文本分析 API”。 密钥在注册时生成。

就文本分析来说，我们提供供用户浏览和评估的“免费”层，以及适用于生产性工作负荷的计费层。 每个订阅中可以有多个注册： 一个免费、 付费，一个，等等。 如果请求量增加，可以切换到提供更多事务的层。

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
