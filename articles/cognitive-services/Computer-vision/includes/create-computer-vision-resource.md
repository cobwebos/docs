---
title: 容器支持
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 2647c604bebc2885f5c8516eb997792b65751e88
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "80876062"
---
## <a name="create-an-computer-vision-resource"></a>创建计算机视觉资源

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 单击[创建“计算机视觉”](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)资源****。
1. 输入所有必需的设置：

    |设置|Value|
    |--|--|
    |名称|所需名称（2-64 个字符）|
    |订阅|选择相应的订阅|
    |位置|选择附近任何可用的位置|
    |定价层|`F0` - 最小定价层|
    |资源组|选择可用的资源组|

1. 单击“创建”**** 并等待创建资源。 创建资源后，导航到资源页。
1. 收集配置的 `{ENDPOINT_URI}` 和 `{API_KEY}`，请参阅[收集所需参数](../computer-vision-how-to-install-containers.md#gathering-required-parameters)获取详细信息。
