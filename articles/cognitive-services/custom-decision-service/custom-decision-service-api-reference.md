---
title: API - Azure 认知服务 | Microsoft Docs
description: 一个完整的用户友好型 API 指南，适用于 Azure 自定义 AI 决策服务，该服务是一种基于云的日益成熟的 API，用于进行上下文决策。
services: cognitive-services
author: slivkins
manager: slivkins
ms.service: cognitive-services
ms.topic: article
ms.date: 05/11/2018
ms.author: slivkins
ms.reviewer: marcozo, alekh
ms.openlocfilehash: 403b17e33394016a07a7b33ba1bcbfe6afdcc05b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366584"
---
# <a name="api"></a>API

Azure 自定义 AI 决策服务提供两种可以根据每种决策进行调用的 API：[排名 API](#ranking-api)，用于输入操作的排名；[奖励 API](#reward-api)，用于输出奖励。 另外，请提供一个[操作集 API](#action-set-api-customer-provided)，用于指定针对 Azure 自定义 AI 决策服务的操作。 本文介绍以下三个 API。 下面使用了一个典型的方案来演示自定义 AI 决策服务何时优化文章的排名。

## <a name="ranking-api"></a>排名 API

排名 API 使用标准的 [JSONP](https://en.wikipedia.org/wiki/JSONP) 样式通信模式来优化延迟并绕过[同源策略](https://en.wikipedia.org/wiki/Same-origin_policy)。 后者禁止 JavaScript 从页面所在的源外部提取数据。

请将以下代码片段插入主页（在其中显示个性化的文章列表）的 HTML 头中：

```html
// define the "callback function" to render UI
<script> function callback(data) { … } </script>
// "data" provides the ranking of URLs, see example below.

// call to Ranking API
<script src="https://ds.microsoft.com/api/v2/<appId>/rank/<actionSetId>?<parameters>" async></script>
// action set id is the name of the corresponding RSS/Atom feed.

// same call with multiple action sets:
// <script src="https://ds.microsoft.com/api/v2/<appId>/rank/<A1>/<A2>/.../<An>?<parameters>" async></script>
```

> [!IMPORTANT]
> 在调用排名 API 之前，必须先定义回调函数。

> [!TIP]
> 为了改善延迟情况，请通过 HTTP 而非 HTTPS 来公开排名 API，如 `http://ds.microsoft.com/api/v2/<appId>/rank/*` 中所示。
> 不过，如果主页是通过 HTTPS 提供的，则必须使用 HTTPS 终结点。

不使用参数时，来自排名 API 的 HTTP 响应是 JSONP 格式的字符串：

```json
callback({
   "ranking":[{"id":"url1"}, {"id":"url2"}, {"id":"url3"}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "actionSets":[{"id":"<A1>","lastRefresh":"2017-04-29T22:34:25.3401438Z"},
                 {"id":"<A2>","lastRefresh":"2017-04-30T22:34:25.3401438Z"}]});
```

浏览器随后会通过调用 `callback()` 函数的方式执行此字符串。

前一示例中回调函数的参数具有以下架构：

- `ranking` 提供要显示的 URL 的排名。
- `eventId` 供自定义 AI 决策服务在内部使用，这样此排名就会与相应的点击数相匹配。
- 回调函数可以通过 `appId` 在同一网页的多个正在运行的自定义 AI 决策服务应用程序之间进行区分。
- `actionSets` 会列出在排名 API 调用中使用的每个操作集，以及上次成功刷新的 UTC 时间戳。 自定义 AI 决策服务定期刷新操作集源。 例如，如果部分操作集不是最新的，则回调函数可能需要回退到其默认的排名。

> [!IMPORTANT]
> 指定的操作集会进行处理，并且可能会进行修剪，形成文章的默认排名。 然后，默认排名会重新排序并返回到 HTTP 响应中。 默认排名定义如下：
>
> - 在每个操作集中，系统会对文章进行修剪，只保留 15 篇最新的文章（如果返回的文章超出 15 篇）。
> - 指定多个操作集时，会将其合并，合并顺序与在 API 调用中一样。 文章的原始顺序会在每个操作集中保留。 删除重复项时，会保留较早的副本。
> - 头 `n` 篇文章不在文章的合并列表中，其中 `n=20`（默认）。

### <a name="ranking-api-with-parameters"></a>带参数的排名 API

排名 API 允许以下参数：

- `details=1` 和 `details=2` 插入 `ranking` 中列出的每篇文章的其他详细信息。
- `limit=<n>` 指定默认排名中文章的最大数目。 `n` 必须为 `2` 到 `30`（否则会将其分别截成 `2` 或 `30`）。
- `dnt=1` 禁用用户 Cookie。

可以使用标准的查询字符串语法来组合参数，例如 `details=2&dnt=1`。

> [!IMPORTANT]
> 欧洲的默认设置应该为 `dnt=1`，除非客户同意 Cookie 横幅的设置。 它也应该是以未成年人为目标的网站的默认设置。 有关详细信息，请参阅[使用条款](https://www.microsoft.com/cognitive-services/en-us/legal/CognitiveServicesTerms20160804)。

`details=1` 元素插入每篇文章的 `guid`，前提是该文章通过操作集 API 提供。 HTTP 响应：

```json
callback({
   "ranking":[{"id":"url1","details":[{"guid":"123"}]},
              {"id":"url2","details":[{"guid":"456"}]},
              {"id":"url3","details":[{"guid":"789"}]}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "actionSets":[{"id":"<A1>","lastRefresh":"timeStamp1"},
                 {"id":"<A2>","lastRefresh":"timeStamp2"}]});
```

`details=2` 元素添加更多详细信息，自定义 AI 决策服务可以从文章的 SEO 元标记[特征化代码](https://github.com/Microsoft/mwt-ds/tree/master/Crawl)中提取这些详细信息：

- `title` 来自 `<meta property="og:title" content="..." />`、`<meta property="twitter:title" content="..." />` 或 `<title>...</title>`
- `description` 来自 `<meta property="og:description" ... />`、`<meta property="twitter:description" content="..." />` 或 `<meta property="description" content="..." />`
- `image` 来自 `<meta property="og:image" content="..." />`
- `ds_id` 来自 `<meta name=”microsoft:ds_id” content="..." />`

HTTP 响应：

```json
callback({
   "ranking":[{"id":"url1","details":<details>},
              {"id":"url2","details":<details>},
              {"id":"url3","details":<details>}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "actionSets":[{"id":"<A1>","lastRefresh":"timeStamp1"},
                 {"id":"<A2>","lastRefresh":"timeStamp2"}]});
```

`<details>` 元素：

```json
[{"guid":"123"}, {"description":"some text", "ds_id":"234", "image":"ImageUrl1", "title":"some text"}]
```

## <a name="reward-api"></a>奖励 API

自定义 AI 决策服务仅在榜首使用点击数。 可以将每个点击视为奖励 1。 没有点击视为奖励 0。 点击数通过[排名 API](#ranking-api) 调用所生成的事件 ID 与相应的排名匹配。 事件 ID 可以根据需要通过会话 Cookie 传递。

若要处理榜首的点击，请将以下代码置于主页中：

```javascript
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/' + data.eventId,
    contentType: "application/json" })
```

在这里，`data` 是 `callback()` 函数的参数，如前所述。 在点击处理代码中使用 `data` 需谨慎。 此[教程](custom-decision-service-tutorial-news.md#use-the-apis)中演示了一个示例。

奖励 API 可以通过 [cURL](https://en.wikipedia.org/wiki/CURL) 调用（仅限测试）：

```sh
curl -v https://ds.microsoft.com/api/v2/<appId>/reward/<eventId> -X POST -d 1 -H "Content-Type: application/json"
```

期望的效果是 HTTP 响应“200 (正常)”。 可以在日志中看到此事件的奖励 1（前提是在门户中提供了 Azure 存储帐户密钥）。

## <a name="action-set-api-customer-provided"></a>操作集 API（客户提供）

操作集 API 在高级别返回文章（操作）的列表。 每篇文章按其 URL 和/或文章标题及发布日期来指定。 可以在门户中指定多个操作集。 应该为每个操作集使用不同的操作集 API（用作不同的 URL）。

每个操作集 API 都可以通过两种方式来实现：充当 RSS 源或 Atom 源。 每种方式都应遵循标准并返回正确的 XML。 下面是一个 RSS 的示例：

```xml
<rss version="2.0">
<channel>
   <item>
      <title><![CDATA[title (possibly with url) ]]></title>
      <link>url</link>
      <guid>guid (could be a your internal database id)</guid>
      <pubDate>date</pubDate>
    </item>
   <item>
       ....
   </item>
</channel>
</rss>
```

每个顶级 `<item>` 元素均描述一项操作：

- `<link>` 是必需的，用作操作 ID。
- `<date>` 在少于或等于 15 个项的情况下会被忽略，否则就是必需的。
  - 如果超出 15 个项，则使用 15 个最新的项。
  - 必须分别采用 RSS 或 Atom 的标准格式：
    - [RFC 822](https://tools.ietf.org/html/rfc822) 适用于 RSS：例如 `"Fri, 28 Apr 2017 18:02:06 GMT"`
    - [RFC 3339](https://tools.ietf.org/html/rfc3339) 适用于 Atom：例如 `"2016-12-19T16:39:57-08:00"`
- `<title>` 为可选，用于生成描述文章的特征。
- `<guid>` 为可选，可以通过系统传递至回调函数（前提是在排名 API 调用中指定了 `?details` 参数）。

`<item>` 中的其他元素会被忽略。

Atom 源版本使用相同的 XML 语法和约定。

> [!TIP]
> 如果系统使用其自己的文章 ID，则可通过 `<guid>` 将其传递到回调函数中。