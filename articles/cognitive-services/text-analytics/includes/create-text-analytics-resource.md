---
title: 容器支持
titleSuffix: Azure Cognitive Services
description: 了解如何创建认知服务文本分析资源。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 022ffcd806d4d4f89f8a8cf256a541518ea12602
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455098"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>创建认知服务文本分析资源

1. 登录到 [Azure 门户](https://portal.azure.com)
1. 选择 **+ 创建资源**，导航到**AI + 机器学习 > 文本分析**，或单击[创建**文本分析**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)
1. 输入所有所需的设置：

    |设置|值|
    |--|--|
    |名称|所需的名称 （2-64 个字符）|
    |订阅|选择相应的订阅|
    |Location|选择附近的位置|
    |定价层|`S` -标准定价层|
    |资源组|选择可用的资源组|

1. 单击**创建**并等待要创建的资源。 创建后，你的浏览器会自动重定向到新创建的资源页
1. 收集配置`endpoint`和 API 密钥：

    |在门户中的资源选项卡|设置|值|
    |--|--|--|
    |**概述**|终结点|复制的终结点。 它看起来类似于 `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`|
    |密钥 |API 密钥|将复制两个密钥的 1。 它是不包含空格或短划线的 32 字母数字字符字符串`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`。|