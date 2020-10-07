---
title: 认知服务 SKU 和定价
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/01/2020
ms.author: pafarley
ms.openlocfilehash: cb066ecc1dc11985c0ccb0fa687a15d6b038b9fa
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "89321535"
---
请参阅下面的 SKU 和定价信息列表。 

#### <a name="multi-service"></a>多服务

| 服务                    | 种类                      |
|----------------------------|---------------------------|
| 多个服务。 有关详细信息，请参阅[定价](https://azure.microsoft.com/pricing/details/cognitive-services/)页。            | `CognitiveServices`     |


#### <a name="vision"></a>影像

| 服务                    | 种类                      |
|----------------------------|---------------------------|
| 计算机视觉            | `ComputerVision`          |
| 自定义视觉 - 预测 | `CustomVision.Prediction` |
| 自定义视觉 - 定型   | `CustomVision.Training`   |
| 人脸                       | `Face`                    |
| 表单识别器            | `FormRecognizer`          |
| 墨迹识别器             | `InkRecognizer`           |

#### <a name="search"></a>搜索

| 服务            | 种类                  |
|--------------------|-----------------------|
| 必应自动建议   | `Bing.Autosuggest.v7` |
| 必应自定义搜索 | `Bing.CustomSearch`   |
| 必应实体搜索 | `Bing.EntitySearch`   |
| 必应搜索        | `Bing.Search.v7`      |
| 必应拼写检查   | `Bing.SpellCheck.v7`  |

#### <a name="speech"></a>语音

| 服务            | 种类                 |
|--------------------|----------------------|
| 语音服务    | `SpeechServices`     |
| 语音识别 | `SpeakerRecognition` |

#### <a name="language"></a>语言

| 服务            | 种类                |
|--------------------|---------------------|
| 窗体理解 | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| 文本分析     | `TextAnalytics`     |
| 文本翻译   | `TextTranslation`   |

#### <a name="decision"></a>决策

| 服务           | 种类               |
|-------------------|--------------------|
| 异常检测器  | `AnomalyDetector`  |
| 内容审查器 | `ContentModerator` |
| 个性化体验创建服务      | `Personalizer`     |


#### <a name="pricing-tiers-and-billing"></a>定价层和计费

定价层（以及你收到的账单金额）基于你使用身份验证信息发送的事务数。 每个定价层指定：
* 每秒允许的最大事务数 (TPS)。
* 在定价层中启用的服务功能。
* 预定义事务数的成本。 根据[定价详细信息](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/)中为服务所指定的内容，超过此数字将导致额外费用。

> [!NOTE]
> 许多认知服务都有一个免费层，供你试用该服务。 若要使用免费层，请使用 `F0` 作为资源的 SKU。