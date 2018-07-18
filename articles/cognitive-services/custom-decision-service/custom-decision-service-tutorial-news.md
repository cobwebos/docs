---
title: 文章个性化 - Azure 认知服务 | Microsoft Docs
description: 此教程介绍了 Azure 自定义 AI 决策服务（一种用于上下文决策的基于云的 API）的文章个性化。
services: cognitive-services
author: slivkins
manager: slivkins
ms.service: cognitive-services
ms.topic: article
ms.date: 05/08/2018
ms.author: slivkins;marcozo;alekh;marossi
ms.openlocfilehash: 35d0567f81a23d4726461059eb6fd31e04228697
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366463"
---
# <a name="article-personalization"></a>文章个性化

此教程重点介绍如何在网页首页中个性化文章选择。 例如，自定义 AI 决策服务会影响首页中的“多个”文章列表。 页面可能是仅涵盖政治和体育的新闻网站。 它将显示排名前三的文章列表：政治、体育和最新。

## <a name="applications-and-action-sets"></a>应用程序和操作集

下面介绍了如何使方案适合框架。 假设有三个应用程序，分别用于要优化的每个列表：app-politics、app-sports 和 app-recent。 要指定每个应用程序的候选文章，有两个操作集：一个用于政治，一个用于体育。 app-recent 的操作集作为其他两个集的整合自动生成。

> [!TIP]
> 可以在自定义 AI 决策服务中跨应用程序共享操作集。

## <a name="prepare-action-set-feeds"></a>准备操作集源

自定义 AI 决策服务通过客户提供的 RSS 或 Atom 源来使用操作集。 提供两个源：一个用于政治，一个用于体育。 假设由 `http://www.domain.com/feeds/<feed-name>` 提供。

每个源提供一个文章列表。 在 RSS 中，`<item>` 元素指定每个项，如下所示：

```xml
<rss version="2.0"><channel>
   <item>
      <title><![CDATA[article title]]></title>
      <link>"article url"</link>
      <pubDate>publication date</pubDate>
    </item>
</channel></rss>
```

顺序文章非常重要。 它指定默认排名，这是文章排序方式的最佳猜测。 然后，在[仪表板](#performance-dashboard)上使用默认排名进行性能比较。

有关源格式的详细信息，请参阅 [API 参考](custom-decision-service-api-reference.md#action-set-api-customer-provided)。

## <a name="register-a-new-app"></a>注册新应用

1. 注册 [Microsoft 帐户](https://account.microsoft.com/account)。 在功能区上，单击“我的门户”。

2. 要注册一个应用程序，请单击“新建应用”按钮。

    ![自定义 AI 决策服务门户](./media/custom-decision-service-tutorial/portal.png)

3. 在“应用程序 ID”文本框中输入应用程序的唯一名称。 如果其他客户已使用此名称，系统会要求你选择其他应用程序 ID。 选中“高级”复选框，然后针对 Azure 存储帐户输入[连接字符串](../../storage/common/storage-configure-connection-string.md)。 通常情况下，对所有应用程序使用相同的存储帐户。

    ![“新建应用”对话框](./media/custom-decision-service-tutorial/new-app-dialog.png)

    在上面方案中注册所有三个应用程序之后，会列出它们：

    ![应用列表](./media/custom-decision-service-tutorial/apps.png)

    通过单击“应用”按钮，可以返回此列表。

4. 在“新建应用”对话框中，指定操作源。 此外，通过单击“源”按钮，然后单击“新建源”按钮，也可以指定操作源。 输入新建源的“名称”、提供它的“URL”和“刷新时间”。 刷新时间指定自定义 AI 决策服务应刷新源的频率。

    ![“新建源”对话框](./media/custom-decision-service-tutorial/new-feed-dialog.png)

    任何应用均可以使用操作源（无论指定位置如何）。 在方案中指定两个操作源之后，会列出它们：

    ![源列表](./media/custom-decision-service-tutorial/feeds.png)

    通过单击“源”按钮，可以返回此列表。

## <a name="use-the-apis"></a>使用 API

自定义 AI 决策服务通过排名 API 对文章进行排名。 要使用此 API，请将下面代码插入首页的 HTML 标头：

```html
<!-- Define the "callback function" to render UI -->
<script> function callback(data) { … } </script>

<!--  call Ranking API after callback() is defined, separately for each app -->
<script src="https://ds.microsoft.com/api/v2/app-politics/rank/feed-politics" async></script>
<script src="https://ds.microsoft.com/api/v2/app-sports/rank/feed-sports" async></script>
<script src="https://ds.microsoft.com/api/v2/app-recent/rank/feed-politics/feed-sports" async></script>
<!-- NB: action feeds for 'app-recent' are listed one after another. -->
```

来自排名 API 的 HTTP 响应是 JSONP 格式的字符串。 例如，对于 app-politics，字符串如下所示：

```json
callback({
   "ranking":[{"id":"url1"}, {"id":"url2"}, {"id":"url3"}],
   "eventId":"<opaque event string>",
   "appId":"app-politics",
   "actionSets":[{"id":"feed-politics","lastRefresh":"date"}] });
```

然后，调用 `callback()` 函数时浏览器执行此字符串。 `callback()` 函数中的 `data` 参数包含要呈现的应用 ID 和 URL 排名。 具体而言，`callback()` 应使用 `data.appId` 来区分三个应用程序。 `eventId` 供自定义 AI 决策服务在内部使用以针对相应单击（如果有）来匹配提供的排名。

> [!TIP]
> `callback()` 可以使用 `lastRefresh` 字段来检查每个操作源的新鲜程度。 如果给定的源不足够新鲜，`callback()` 可能忽略提供的排名，直接调用此源，并使用源提供的默认排名。

有关规范和排名 API 提供的其他选项的详细信息，请参阅 [API 参考](custom-decision-service-api-reference.md)。

通过调用回报 API 返回用户中的热门文章选择。 收到热门文章选择时，应在首页上调用以下代码：

```javascript
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/<eventId>',
    contentType: "application/json" })
```

在 click-handling 代码中使用 `appId` 和 `eventId` 时需要非常谨慎。 例如，可以实现 `callback()` 函数，如下所示：

```javascript
function callback(data) {
    $.map(data.ranking, function (element) {
        //custom rendering function given content info
        render({appId:data.appId,
                article:element,
                onClick: element.id != data.rewardAction ? null :
                   function() {
                       $.ajax({
                       type: "POST",
                       url: '//ds.microsoft.com/api/v2/' + data.appId + '/reward/' + data.eventId,
                       contentType: "application/json" })}
                });
}}
```

在此示例中，实现 `render()` 函数来呈现给定应用程序的给定文章。 此函数输入应用 ID 和文章（采用排名 API 中的格式）。 `onClick` 参数是应从 `render()` 调用以处理单击的函数。 它会检查单击是否位于榜首。 然后，通过适当的应用 ID 和事件 ID 调用回报 API。

## <a name="next-steps"></a>后续步骤

* 请参阅 [API 参考](custom-decision-service-api-reference.md)详细了解提供的功能。