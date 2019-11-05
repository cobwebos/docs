---
title: 容器支持
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: b40f1833f08074cb0a8d45fe3afc6bac7cbac7f0
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73522348"
---
## <a name="create-an-anomaly-detector-resource"></a>创建异常检测器资源

1. 登录到 [Azure 门户](https://portal.azure.com)
1. 单击 "[创建**异常检测**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector)资源"
1. 输入所有必需的设置：

    |设置|值|
    |--|--|
    |名称|所需名称（2-64 个字符）|
    |订阅|选择相应的订阅|
    |位置|选择附近任何可用的位置|
    |定价层|`F0` - 最小定价层|
    |资源组|选择可用的资源组|
    |预览确认复选框（必需）|是否已阅读**预览**通知|

1. 单击“创建”并等待创建资源。 创建资源后，导航到“资源”页
1. 收集配置的 `endpoint` 和 API 密钥：

    |门户中的“资源”选项卡|设置|值|
    |--|--|--|
    |**概述**|终结点|复制终结点。 它看起来类似于 `https://westus2.api.cognitive.microsoft.com/`|
    |密钥|API 密钥|复制两个密钥中的 1 个。 它是一个由 32 个字母数字组成的字符串（不包含空格或短划线），即 `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`。|



