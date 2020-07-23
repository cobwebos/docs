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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "80876062"
---
## <a name="create-an-computer-vision-resource"></a>创建计算机视觉资源

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 单击 "[创建**计算机视觉**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)资源"。
1. 输入所有必需的设置：

    |设置|值|
    |--|--|
    |名称|所需名称（2-64 个字符）|
    |订阅|选择相应的订阅|
    |位置|选择任何附近的可用位置|
    |定价层|`F0` - 最低定价层|
    |资源组|选择可用的资源组|

1. 单击“创建”  并等待创建资源。 创建资源后，导航到资源页。
1. 已配置`{ENDPOINT_URI}`收集`{API_KEY}`，有关详细信息，请参阅[收集必需的参数](../computer-vision-how-to-install-containers.md#gathering-required-parameters)。
