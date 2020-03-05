---
title: 预生成的域引用-LUIS
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
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270616"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>LUIS 应用的预构建的域参考
此参考提供有关[预构建的域](luis-how-to-use-prebuilt-domains.md)的信息，这些参考是 LUIS 提供的意向和实体的预构建集合。

相比之下，[自定义域](luis-how-to-start-new-app.md)从无意向和模型开始。 可将任何预构建的域意向和实体添加到自定义模型中。

## <a name="custom-domains-per-language"></a>每语言自定义域

下表汇总了当前支持的域。 对英语的支持通常比其他人更完整。

| 实体类型       | ZH-CN      | ZH-CHS-CN   | DE    | FR     | ES    | IT      | PT-BR |  JP  |      KO |        NL |    TR |
|:-----------------:|:-------:|:-------:|:-----:|:------:|:-----:|:-------:| :-------:| :-------:| :-------:| :-------:|  :-------:|
| 日历  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
|通信  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| 电子邮件     | ✓    | ✓       | ✓   | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| 家庭自动化          | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| 说明     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| 场所   | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| 餐位预订  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| 做     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| 实用程序      | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| 天气        | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Web    | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |

预生成的域在中**不受支持**：

* 加拿大法语
* Hindi
* 西班牙语（墨西哥）

## <a name="next-steps"></a>后续步骤

了解[简单的实体](reference-entity-simple.md)。