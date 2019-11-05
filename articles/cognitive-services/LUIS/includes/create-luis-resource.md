---
title: 创建 LUIS 资源
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/23/2019
ms.author: dapine
ms.openlocfilehash: a765ac27936da9da5a2f41464c17491e3561f44b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73465942"
---
## <a name="create-a-luis-resource"></a>创建 LUIS 资源

1. 登录到 [Azure 门户](https://portal.azure.com)
1. 单击“[创建**语言理解**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)”
1. 输入所有必需的设置：

    |设置|值|
    |--|--|
    |名称|所需名称（2-64 个字符）|
    |订阅|选择相应的订阅|
    |位置|选择附近任何可用的位置|
    |定价层|`F0` - 最小定价层|
    |资源组|选择可用的资源组|

1. 单击“创建”并等待创建资源。 创建后，导航到 "资源" 页。
1. 收集配置的 `endpoint` 和 API 密钥，请参阅[收集所需的参数](#gathering-required-parameters)。

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]
