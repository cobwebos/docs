---
title: 容器支持
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 8f93e0ffe74ade79059fc845788faf36ab5b4f13
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91025036"
---
## <a name="create-an-anomaly-detector-resource"></a>创建异常检测器资源

1. 登录到 [Azure 门户](https://portal.azure.com)
1. 单击 " [创建 **异常检测**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) 资源"
1. 输入所有必需的设置：

    |设置|值|
    |--|--|
    |名称|所需名称（2-64 个字符）|
    |订阅|选择相应的订阅|
    |位置|选择附近任何可用的位置|
    |定价层|`F0` - 最小定价层|
    |资源组|选择可用的资源组|

1. 单击“创建”**** 并等待创建资源。 创建资源后，导航到“资源”页
1. 收集配置的 `endpoint` 和 API 密钥：

    |门户中的“资源”选项卡|设置|值|
    |--|--|--|
    |**概述**|端点|复制终结点。 它看起来类似于 `https://westus2.api.cognitive.microsoft.com/`|
    |**“键”**|API 密钥|复制两个密钥中的 1 个。 它是一个由 32 个字母数字组成的字符串（不包含空格或短划线），即 `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`。|



