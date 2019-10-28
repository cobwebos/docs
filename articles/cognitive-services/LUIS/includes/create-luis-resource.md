---
title: 容器支持
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/23/2019
ms.author: dapine
ms.openlocfilehash: ecb172aec37fdab21bed8373ac7484dc52a54829
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72821953"
---
## <a name="create-a-luis-resource"></a>创建 LUIS 资源

1. 登录到 [Azure 门户](https://portal.azure.com)
1. 单击 "[创建"**语言理解**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
1. 输入所有必需的设置：

    |设置|Value|
    |--|--|
    |名称|所需名称（2-64 个字符）|
    |Subscription|选择适当的订阅|
    |Location|选择任何附近和可用位置|
    |定价层|`F0`-最低定价层|
    |资源组|选择可用的资源组|

1. 单击 "**创建**" 并等待创建资源。 创建后，导航到 "资源" 页
1. 收集配置的 `endpoint` 和 API 密钥：

    |门户中的 "资源" 选项卡|设置|Value|
    |--|--|--|
    |**概述**|终结点|复制终结点。 其外观类似于 `https://luis.cognitiveservices.azure.com/luis/v2.0`|
    |密钥|API 密钥|2个键的副本1。 它是一个32的字母数字字符串，其中不含空格或短划线，`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`。|
