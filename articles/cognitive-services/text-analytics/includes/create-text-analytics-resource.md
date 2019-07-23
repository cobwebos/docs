---
title: 创建认知服务文本分析资源
titleSuffix: Azure Cognitive Services
description: 了解如何文本分析资源创建认知服务。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 6a9363472e1d5770232ca9a0b151fb309d9c4c75
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377431"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>创建认知服务文本分析资源

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 选择 "**创建资源**", 然后中转到 " **AI + 机器学习** > "**文本分析**"。
   或者, 请参阅[创建文本分析](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)。
1. 输入所有必需的设置:

    |设置|值|
    |--|--|
    |名称|输入名称 (2-64 个字符)。|
    |订阅|选择相应的订阅。|
    |Location|选择附近的位置。|
    |定价层| 输入 **"** 标准" 定价层。|
    |资源组|选择可用的资源组。|

1. 选择 "**创建**", 并等待创建资源。 你的浏览器将自动重定向到新创建的资源页。
1. 收集配置`endpoint`的和 API 密钥:

    |门户中的 "资源" 选项卡|设置|ReplTest1|
    |--|--|--|
    |**概述**|终结点|复制终结点。 它看起来类似`https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`于。|
    |密钥 |API 密钥|复制这两个密钥中的一个。 它是一个32字符的字母数字字符串, 不含空格或短`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`划线: < >。|
