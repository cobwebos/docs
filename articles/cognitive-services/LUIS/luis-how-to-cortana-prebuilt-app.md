---
title: 使用 LUIS 的 Cortana 预构建应用 | Microsoft Docs
description: 使用语言理解智能服务 (LUIS) 的预构建应用程序 - Cortana 个人助理。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 07/03/2018
ms.author: v-geberr
ms.openlocfilehash: f5cc97e49d3c127ffe7f53a315c42ab872782a49
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38968718"
---
# <a name="cortana-prebuilt-app"></a>Cortana 预构建应用

> [!IMPORTANT]
> 建议使用[预构建的域](./luis-how-to-use-prebuilt-domains.md)，而非 Cortana 预构建应用。 例如，使用“Calendar”预构建域中的 Calendar.Add，而非 builtin.intent.calendar.create_calendar_entry。
> 预构建域提供以下优势： 
> * 它们提供预构建和预训练的意向和实体包，旨在相互协作。 可以将预构建的域直接集成到应用中。 例如，如果正在构建健身追踪器，可以添加“Fitness”域，并拥有一整套用于跟踪健身活动的意向和实体，包括用于跟踪体重和膳食计划、剩余时间或距离以及保存健身活动笔记的意向。
> * 预构建的域意向是可自定义的。 例如，若要提供酒店评论，可以从“Places”域中训练并自定义“Places.GetReviews”意向，用于识别酒店评论的请求。
> * 预构建的域是可扩展的。 例如，若要在搜索餐馆的机器人中使用“Places”预构建域，并且需要用于获取烹饪类型的意向，可以构建并训练“Places.GetCuisine”意向。

除了使你构建自己的应用程序外，LUIS 还提供预构建应用 Microsoft Cortana 个人助理的意向和实体。 此公开发布的 LUIS 应用的行为无法更改。 无法编辑此应用程序中的意向和实体，或将其集成到其他 LUIS 应用中。 如果要使客户端应用程序能够访问此预构建的应用程序和你自己的 LUIS 应用程序，则客户端应用程序必须引用这两个 LUIS 应用。

预构建的个人助理应用可用于以下区域性（区域设置）：英语、法语、意大利语、西班牙语和中文。

## <a name="get-the-endpoint-for-the-cortana-prebuilt-app"></a>获取 Cortana 预构建应用的终结点

可以使用以下终结点访问 Cortana 预构建应用： 

| 语言 | 终结点|
|--------| ------------------|
| 英语| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/c413b2ef-382c-45bd-8ff0-f76d60e2a821|
|    中文| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/c27c4af7-d44a-436f-a081-841bb834fa29|
|    法语| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/0355ead1-2d08-4955-ab95-e263766e8392|
|    西班牙语| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/cb2675e5-fbea-4f8b-8951-f071e9fc7b38|
|    意大利语| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/30a0fddc-36f4-4488-b022-03de084c1633|


终结点 URL 还可从 [apps - Get personal assistant applications](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c32)（应用 - 获取个人助理应用程序）API 获取。

## <a name="try-out-the-personal-assistant-app"></a>试用个人助理应用
例如，如果要解释的陈述为“create an appointment for team meeting”，则可以将该陈述追加到终结点 URL。 

```
https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/c413b2ef-382c-45bd-8ff0-f76d60e2a821?subscription-key=YOUR-SUBSCRIPTION-KEY&q=create%20an%20appointment%20for%20team%20meeting
```

将该 URL 粘贴到 Web 浏览器中，并替换 `YOUR-SUBSCRIPTION-KEY` 字段的终结点密钥。

在浏览器可以看到对于陈述 `create an appointment for team meeting`，Cortana 预构建应用将 `builtin.intent.calendar.create_calendar_entry` 标识为意向，将 `builtin.calendar.title` 标识为实体类型。

```JSON
{
  "query": "create an appointment for team meeting",
  "topScoringIntent": {
    "intent": "builtin.intent.calendar.create_calendar_entry"
  },
  "entities": [
    {
      "entity": "team meeting",
      "type": "builtin.calendar.title"
    }
  ]
}
```

由于使用 Cortana 预构建应用，因此 LUIS 不返回评分。 

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [Cortana 预构建的应用参考](./luis-reference-cortana-prebuilt.md)

