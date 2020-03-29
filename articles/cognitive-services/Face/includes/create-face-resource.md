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
ms.openlocfilehash: 3886777b283af35e84683480a59097584b537fea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "73522394"
---
## <a name="create-an-face-resource"></a>创建面资源

1. 登录到[Azure 门户](https://portal.azure.com)
1. 单击["创建**面"**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)资源
1. 输入所有必需的设置：

    |设置|“值”|
    |--|--|
    |“属性”|所需名称（2-64 个字符）|
    |订阅|选择相应的订阅|
    |位置|选择任何附近的可用位置|
    |定价层|`F0` - 最低定价层|
    |资源组|选择可用的资源组|

1. 单击“创建”**** 并等待创建资源。 创建资源后，导航到“资源”页
1. 收集配置的 `endpoint` 和 API 密钥：

    |门户中的“资源”选项卡|设置|“值”|
    |--|--|--|
    |**概述**|终结点|复制终结点。 它看起来类似于 `https://face.cognitiveservices.azure.com/face/v1.0`|
    |**密钥**|API 密钥|复制两个密钥中的 1 个。 它是一个由 32 个字母数字组成的字符串（不包含空格或短划线），即 `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`。|
