---
title: 创建认知服务文本分析资源
titleSuffix: Azure Cognitive Services
description: 了解如何创建认知服务文本分析资源。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 7e8b4480911f00afa8524ef4b81d697bb8ee5bcc
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2019
ms.locfileid: "67877441"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>创建认知服务文本分析资源

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 选择**创建资源**，然后转到**AI + 机器学习** > **文本分析**。
   或者，转到[创建文本分析](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)。
1. 输入所有所需的设置：

    |设置|值|
    |--|--|
    |名称|输入名称 （2-64 个字符）|
    |订阅|选择相应的订阅|
    |Location|选择附近的位置|
    |定价层| 输入**S**、 标准定价层|
    |资源组|选择可用的资源组|

1. 选择**创建**并等待要创建的资源。 在浏览器会自动重定向到新创建的资源页。
1. 收集已配置`endpoint`和 API 密钥：

    |在门户中的资源选项卡|设置|值|
    |--|--|--|
    |**概述**|终结点|复制的终结点。 它看起来类似于 `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`|
    |密钥 |API 密钥|两个密钥中的一个副本。 它是不包含空格或短划线的 32 个字符字母数字字符串： <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>。|
