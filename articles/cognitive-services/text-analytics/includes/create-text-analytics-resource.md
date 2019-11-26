---
title: 创建认知服务文本分析资源
titleSuffix: Azure Cognitive Services
description: 了解如何创建认知服务文本分析资源。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: d3b65174f3d161e0b8780a890f297d9d1c812b44
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383445"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>创建认知服务文本分析资源

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 选择“创建资源”，然后转到“AI + 机器学习” > “文本分析”。
   或者，转到 [创建文本分析](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)。
1. 输入所有必需的设置：

    |设置|值|
    |--|--|
    |名称|输入名称（2-64 个字符）。|
    |订阅|选择相应的订阅。|
    |位置|选择附近的位置。|
    |定价层| 输入 **"标准" 定价**层。|
    |资源组|选择可用的资源组。|

1. 选择 "**创建**"，并等待创建资源。 浏览器会自动重定向到新创建的资源页。
1. 收集配置的 `endpoint` 和 API 密钥：

    |门户中的“资源”选项卡|设置|值|
    |--|--|--|
    |**概述**|终结点|复制终结点。 它看上去类似于 `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`。|
    |密钥|API 密钥|复制两个密钥中的一个。 它是一个 32 个字符的字母数字字符串（不包含空格或短划线）：<`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>。|
