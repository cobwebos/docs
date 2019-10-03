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
ms.openlocfilehash: cbf11c13bfb5c90739ea67fab92df08796a88e50
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717279"
---
## <a name="create-an-computer-vision-resource"></a>创建计算机视觉资源

1. 登录到 [Azure 门户](https://portal.azure.com)
1. 单击[创建**计算机视觉**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)资源
1. 输入所有所需的设置：

    |设置|值|
    |--|--|
    |名称|所需的名称 （2-64 个字符）|
    |订阅|选择相应的订阅|
    |Location|选择任何附近和可用位置|
    |定价层|`F0` -最小的定价层|
    |资源组|选择可用的资源组|

1. 单击**创建**并等待要创建的资源。 创建后，导航到资源页
1. 收集配置`endpoint`和 API 密钥：

    |在门户中的资源选项卡|设置|ReplTest1|
    |--|--|--|
    |**概述**|终结点|复制的终结点。 它看起来类似于 `https://computer-vision.cognitiveservices.azure.com/`|
    |密钥 |API 密钥|将复制两个密钥的 1。 它是不包含空格或短划线的 32 字母数字字符字符串`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`。|
