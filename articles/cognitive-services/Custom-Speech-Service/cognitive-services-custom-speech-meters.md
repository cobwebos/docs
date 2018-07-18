---
title: Azure 上的自定义语音服务计量和配额 | Microsoft Docs
description: 有关 Azure 上自定义语音服务计量和配额的信息。
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 07/08/2017
ms.author: panosper
ms.openlocfilehash: d2225dec818c600febfad2f9ebc42594f6ac09ac
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365424"
---
# <a name="custom-speech-service-meters-and-quotas"></a>自定义语音服务计量和配额

借助基于云的自定义语音服务，你可以自定义语音转文本的语音模型。

要开始使用自定义语音服务，请转到[自定义语音服务门户](https://cris.ai)。

对于当前的定价计量，请转到[自定义语音服务的认知服务定价](https://azure.microsoft.com/pricing/details/cognitive-services/custom-speech-service/)页面。

## <a name="tiers-explained"></a>层介绍
仅用于测试和原型，我们建议使用免费的 F0 层。 对于生产系统，我们建议使用 S2 层。 通过使用 S2 层，你可以将部署缩放到场景所需的缩放单元 (SU) 数量。

> [!NOTE]
> 无法在 F0 层和 S2 层之间进行迁移。
>

## <a name="meters-explained"></a>计量介绍

### <a name="scale-out"></a>Scale Out
Scale Out 是我们使用新定价模型发布的新功能。 通过使用 Scale Out，你可以控制模型可以处理的并发请求数。

可以使用创建模型部署视图中的 SU 度量来设置并发请求。 有关详细信息，请参阅[创建自定义语音转文本终结点](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-endpoint.md)。 根据设想的模型消耗的流量，你可以选择适当数量的 SU。 

> [!NOTE]
> 每个缩放单元保证 5 个并发请求。 你可以根据需要购买一个或多个 SU。 因为 SU 数量一次递增 1 个，所以并发请求数量保证一次递增 5 个。
>

### <a name="log-management"></a>日志管理
你可以选择关闭新部署模型的音频跟踪，但需要支付额外费用。 自定义语音服务不会记录该模型的音频请求或脚本。

## <a name="next-steps"></a>后续步骤
有关如何使用自定义语音服务的详细信息，请转到[自定义语音服务门户](https://cris.ai)。

* [入门](cognitive-services-custom-speech-get-started.md)
* [常见问题](cognitive-services-custom-speech-faq.md)
* [术语表](cognitive-services-custom-speech-glossary.md)
 