---
title: 命名实体识别支持的类别
titleSuffix: Azure Cognitive Services
description: 了解文本分析 API 中支持的实体类别。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: e36a69be19844a75562f87d3c195494e3ef148a9
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108477"
---
# <a name="supported-entity-categories-in-the-text-analytics-api-v3"></a>文本分析 API v3 中支持的实体类别

使用本文查找可通过[命名实体识别](how-tos/text-analytics-how-to-entity-linking.md)（NER）返回的实体类别。 还提供了 NER 3.1 的预览，其中包括检测个人（ `PII` ）和运行状况（）信息的能力 `PHI` 。 此外，单击 "**运行状况**" 选项卡以查看文本分析 for Health 中支持的类别列表。

## <a name="entity-categories"></a>实体类别

#### <a name="general"></a>[常规](#tab/general)

[!INCLUDE [supported entity types - general](./includes/entity-types/general-entities.md)]

#### <a name="personal"></a>[个人](#tab/personal)

[!INCLUDE [supported entity types - personally identifying information](./includes/entity-types/personal-information-entities.md)]

#### <a name="health"></a>[健康产业](#tab/health)

[!INCLUDE [biomedical entity types](./includes/entity-types/health-entities.md)]

***

## <a name="next-steps"></a>后续步骤

* [如何在文本分析中使用命名实体识别](how-tos/text-analytics-how-to-entity-linking.md)
