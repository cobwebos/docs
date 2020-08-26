---
title: 快速入门：使用 REST API 获取意向 - LUIS
description: 此 REST API 快速入门使用可用的公共 LUIS 应用从对话文本中确定用户的意向。
ms.topic: quickstart
ms.date: 05/18/2020
ms.custom: devx-track-python, devx-track-javascript
zone_pivot_groups: programming-languages-set-one
ms.openlocfilehash: 2fd52011ed0d139e98740c8de077987edfae2c32
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87873154"
---
# <a name="quickstart-get-intent-with-rest-apis"></a>快速入门：使用 REST API 获取意向

在本快速入门中，你将使用 LUIS 应用从会话文本中确定用户的意向。 将用户的意向作为文本发送到 Pizza 应用的 HTTP 预测终结点。 在终结点处，LUIS 应用 Pizza 应用的模型来分析自然语言文本的含义，确定总体意向并提取与应用的主题域相关的数据。

对于本文，需要一个免费的 [LUIS](https://www.luis.ai) 帐户。

<a name="create-luis-subscription-key"></a>

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get intent with C# and REST](./includes/get-started-get-intent-rest-csharp.md)]
::: zone-end

::: zone pivot="programming-language-go"
[!INCLUDE [Get intent with Go and REST](./includes/get-started-get-intent-rest-go.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Get intent with Java and REST](./includes/get-started-get-intent-rest-java.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Get intent with Node.js and REST](./includes/get-started-get-intent-rest-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Get intent with Python and REST](./includes/get-started-get-intent-rest-python.md)]
::: zone-end
