---
title: 创建认知服务文本分析资源
titleSuffix: Azure Cognitive Services
description: 了解如何创建认知服务文本分析资源。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: daafea59b96cc8da6b78f0733c9f54e0e4d3a8c2
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779446"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>创建认知服务文本分析资源

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 选择“创建资源”  ，然后转到  “AI + 机器学习” > “文本分析”  。
   或者，转到 [创建文本分析](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)。
1. 输入所有必需的设置：

    |设置|值|
    |--|--|
    |名称|输入名称（2-64 个字符）。|
    |订阅|选择适当的订阅。|
    |位置|选择附近的位置。|
    |定价层| 输入 **S**（即标准定价层）。|
    |资源组|选择可用的资源组。|

1. 选择“创建”  并等待创建资源。 浏览器会自动重定向到新创建的资源页。
1. 收集配置的 `endpoint` 和 API 密钥：

    |门户中的“资源”选项卡|设置|值|
    |--|--|--|
    |**概述**|端点|复制终结点。 它看起来类似于 `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0` 。|
    |**“键”**|API 密钥|复制两个密钥中的一个。 它是一个 32 个字符的字母数字字符串（不包含空格或短划线）：<`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>。|
