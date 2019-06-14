---
title: 从应用调用 API - 自定义 AI 决策服务
titlesuffix: Azure Cognitive Services
description: 如何从智能手机应用调用自定义 AI 决策服务 API。
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/10/2018
ms.author: slivkins
ms.openlocfilehash: 0e5c99aae61fb927ea7f101bab74d661a747f88b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60511550"
---
# <a name="call-api-from-an-app"></a>从应用调用 API

从智能手机应用调用 Azure 自定义 AI 决策服务 API。 本文介绍如何开始使用一些基本选项。

确保先[注册应用程序](custom-decision-service-get-started-register.md)。

可以通过智能手机应用程序向自定义 AI 决策服务进行两次 API 调用：调用排名 API 以获取内容的排名列表以及调用回报 API 以报告回报。 以下是 [cURL](https://en.wikipedia.org/wiki/CURL) 中的示例调用。

有关详细信息，请参阅 [API 参考](custom-decision-service-api-reference.md)。

首先调用排名 API。 创建具有操作集 ID 的文件 `<request.json>`。 此 ID 是你在门户上输入的相应 RSS 或 Atom 馈送的名称：

```json
{"decisions":
     [{ "actionSets":[{"id":{"id":"<actionSetId>"}}] }]}
```

许多操作集可以指定如下：

```json
{"decisions":
    [{ "actionSets":[{"id":{"id":"<actionSetId1>"}},
                     {"id":{"id":"<actionSetId2>"}}] }]}
```

然后此 JSON 文件将作为排名请求的一部分发送：

```shell
curl -d @<request.json> -X POST https://ds.microsoft.com/api/v2/<appId>/rank --header "Content-Type: application/json"
```

在这里，`<appId>` 是在门户上注册的应用程序的名称。 应该会收到一组有序的内容项，你可以在应用程序中呈现这些内容项。 示例返回如下所示：

```json
[{ "ranking":[{"id":"actionId3"}, {"id":"actionId1"}, {"id":"actionId2"}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "rewardAction":"actionId3",
   "actionSets":[{"id":"<actionSetId1>","lastRefresh":"2017-04-29T22:34:25.3401438Z"},
                 {"id":"<actionSetId2>","lastRefresh":"2017-04-30T22:34:25.3401438Z"}]}]
```

返回的第一部分包含由其操作 ID 指定的有序操作列表。 对于文章，操作 ID 是 URL。 总体请求还具有由系统创建的唯一 `<eventId>`。

稍后，你可以指定是否观察到此事件中第一个内容项（即 `<actionId3>`）上的单击。 然后，可以使用另一个请求，通过回报 API 向自定义 AI 决策服务报告对此 `<eventId>` 的回报，例如：

```shell
curl -v https://ds.microsoft.com/api/v2/<appId>/reward/<eventId> -X POST
```

最后，需要提供操作集 API，这将返回自定义 AI 决策服务考虑的文章（操作）列表。 将此 API 实现为 RSS 源，如下所示：

```xml
<rss version="2.0">
<channel>
   <item>
      <title><![CDATA[title (possibly with url) ]]></title>
      <link>url</link>
      <pubDate>Thu, 27 Apr 2017 16:30:52 GMT</pubDate>
    </item>
   <item>
       ....
   </item>
</channel>
</rss>
```

此处，每个顶级 `<item>` 元素各描述一篇文章。 `<link>` 是必需的，由自定义 AI 决策服务用作操作 ID。 如果你的文章超过 15 篇，请指定 `<date>`（采用标准 RSS 格式）。 将使用 15 个最新文章。 `<title>` 是可选的，用于为文章创建与文本相关的功能。

## <a name="next-steps"></a>后续步骤

* 通读[教程](custom-decision-service-tutorial-news.md)以获取更深入的示例。
* 请参阅 [API 参考](custom-decision-service-api-reference.md)，详细了解提供的功能。
