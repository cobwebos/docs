---
title: 预生成域参考 - LUIS
titleSuffix: Azure Cognitive Services
description: 预构建的域参考，这些参考是语言理解智能服务 (LUIS) 中意向和实体的预构建集合。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 05ad340b3856291832ba0521c7da70ad55260384
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270616"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>LUIS 应用的预构建的域参考
此参考提供有关[预构建的域](luis-how-to-use-prebuilt-domains.md)的信息，这些参考是 LUIS 提供的意向和实体的预构建集合。

相比之下，[自定义域](luis-how-to-start-new-app.md)从无意向和模型开始。 可将任何预构建的域意向和实体添加到自定义模型中。

## <a name="custom-domains-per-language"></a>每种语言的自定义域数

下表汇总了当前支持的域。 英语区域支持的域通常比其他语言区域更多。

| 实体类型       | EN-US      | ZH-CN   | DE    | FR     | ES    | IT      | PT-BR |  JP  |      KO |        NL |    TR |
|:-----------------:|:-------:|:-------:|:-----:|:------:|:-----:|:-------:| :-------:| :-------:| :-------:| :-------:|  :-------:|
| Calendar  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
|通信  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| 电子邮件     | ✓    | ✓       | ✓   | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| 家庭自动化          | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| 说明     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| 场所   | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| 餐位预订  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| 待办事项     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| 实用程序      | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| 天气        | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Web    | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |

预生成域在以下语言区域**不受支持**：

* 加拿大法语
* Hindi
* 墨西哥西班牙语

## <a name="next-steps"></a>后续步骤

了解[简单实体](reference-entity-simple.md)。