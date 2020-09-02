---
title: 快速入门：使用 REST API 更改模型 - LUIS
titleSuffix: Azure Cognitive Services
description: 此 REST API 快速入门使用可用的公共 LUIS 应用从对话文本中确定用户的意向。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18, devx-track-python, devx-track-javascript, devx-track-csharp
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 06/03/2020
ms.author: diberry
zone_pivot_groups: programming-languages-set-one
ms.openlocfilehash: a62b12ccbc6f7d96dc1a2a9b49ab5eec0175f49d
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078633"
---
# <a name="quickstart-change-model-with-rest-apis"></a>快速入门：使用 REST API 更改模型

在此快速入门中，你要将示例言语添加到披萨应用并训练该应用。 示例话语是映射到意向的对话式用户文本。 通过提供意向的示例话语，可以教 LUIS 识别用户提供的文本类型属于哪种意向。

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get intent with C# and REST](./includes/get-started-get-model-rest-csharp.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Get intent with Java and REST](./includes/get-started-get-model-rest-java.md)]
::: zone-end

::: zone pivot="programming-language-go"
[!INCLUDE [Get intent with Go and REST](./includes/get-started-get-model-rest-go.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Get intent with Node.js and REST](./includes/get-started-get-model-rest-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Get intent with Python and REST](./includes/get-started-get-model-rest-python.md)]
::: zone-end
