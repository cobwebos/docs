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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "73465942"
---
## <a name="create-a-luis-resource"></a>创建 LUIS 资源

1. 登录到[Azure 门户](https://portal.azure.com)
1. 单击[创建语言理解****](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
1. 输入所有必需的设置：

    |设置|“值”|
    |--|--|
    |“属性”|所需名称（2-64 个字符）|
    |订阅|选择相应的订阅|
    |位置|选择任何附近的可用位置|
    |定价层|`F0` - 最低定价层|
    |资源组|选择可用的资源组|

1. 单击“创建”**** 并等待创建资源。 创建资源后，导航到资源页。
1. 收集配置`endpoint`的 API 密钥，请参阅[收集所需的参数](#gathering-required-parameters)。

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]
