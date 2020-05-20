---
title: 教程：创建单页 Web 应用 - 必应 Web 搜索 API
titleSuffix: Azure Cognitive Services
description: 此单页应用展示了如何使用必应 Web 搜索 API 检索、分析和显示单页应用中的相关搜索结果。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: tutorial
ms.date: 03/05/2020
ms.author: aahi
ms.openlocfilehash: f692367ad431dc8f1623e1b3d5109c313e351934
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "78943878"
---
# <a name="tutorial-create-a-single-page-app-using-the-bing-web-search-api"></a>教程：使用必应 Web 搜索 API 创建单页应用

此单页应用展示了如何从必应 Web 搜索 API 中检索、分析和显示搜索结果。 此教程使用 HTML 和 CSS 样板文件，并重点介绍 JavaScript 代码。 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search) 上提供有 HTML、CSS 和 JS 文件，并随附快速入门说明。

该示例应用可执行以下操作：

> [!div class="checklist"]
> * 使用搜索选项调用必应 Web 搜索 API
> * 显示 Web、图像、新闻和视频结果
> * 为结果标记页码
> * 管理订阅密钥
> * 处理错误

要使用此应用，需具备带必应搜索 API 的 [Azure 认知服务帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。 如果没有帐户，可以使用[免费试用版](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)获取订阅密钥。

## <a name="prerequisites"></a>必备条件

需具备以下几项才可运行应用：

* Node.js 8 或更高版本
* 必应搜索 API 的订阅密钥。 如果没有该密钥，请[创建必应搜索 v7 资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7)。 也可以使用[试用密钥](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)。
## <a name="get-the-source-code-and-install-dependencies"></a>获取源代码并安装依赖项

首先是使用示例应用的源代码克隆存储库。

```console
git clone https://github.com/Azure-Samples/cognitive-services-REST-api-samples.git
```

然后运行 `npm install`。 在本教程中，只使用 Express.js 依赖项。

```console
cd <path-to-repo>/cognitive-services-REST-api-samples/Tutorials/Bing-Web-Search
npm install
```

## <a name="app-components"></a>应用组件

要构建的示例应用由 4 部分构成：

* `bing-web-search.js` - 我们的 Express.js 应用。 它处理请求/响应逻辑和路由。
* `public/index.html` - 应用的框架，它定义了向用户显示数据的方式。
* `public/css/styles.css` - 定义页面样式，例如字体、颜色和文本大小。
* `public/js/scripts.js` - 包含用于发出必应 Web 搜索 API 请求、管理订阅密钥、处理和分析响应，以及显示结果的逻辑。

本教程重点介绍用于调用必应 Web 搜索 API 和处理响应的 `scripts.js` 和逻辑。

## <a name="html-form"></a>HTML 表单

`index.html` 包含一个让用户能够搜索和选择搜素选项的表单。 提交表单时会触发 `onsubmit` 属性，从而调用在 `scripts.js` 中定义的 `bingWebSearch()` 方法。 该脚本使用了三个参数：

* 搜索查询
* 所选选项
* 订阅密钥

```html
<form name="bing" onsubmit="return bingWebSearch(this.query.value,
    bingSearchOptions(this), getSubscriptionKey())">
```

## <a name="query-options"></a>查询选项

HTML 表单包含用于映射到[必应 Web 搜索 API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query-parameters) 中的查询参数的选项。 下表分类介绍了用户可如何使用示例应用筛选搜索结果：

| 参数 | 说明 |
|-----------|-------------|
| `query` | 用于输入查询字符串的文本字段。 |
| `where` | 用于选择市场（位置和语言）的下拉菜单。 |
| `what` | 用于提升特定结果类型的复选框。 例如，提升图像后，这些图像在搜索结果中的排名将上升。 |
| `when` | 允许用户将搜索结果限制为今天、本周或本月份的下拉菜单。 |
| `safe` | 用于启用必应安全搜索的复选框，该功能可筛选出成人内容。 |
| `count` | 隐藏的字段。 将在每个请求后返回的搜索结果数。 更改此值，以增加或减少每页上显示的结果数。 |
| `offset` | 隐藏的字段。 请求中第一个搜索结果的偏移量，它用于进行分页。 通过每个新的请求将其重置为 `0`。 |

> [!NOTE]
> 必应 Web 搜索 API 提供了额外的查询参数，可帮助优化搜索结果。 本示例仅使用其中一些参数。 有关可用参数的完整列表，请参阅[必应 Web 搜索 API v7 参考](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query-parameters)。

`bingSearchOptions()` 会转换这些选项，使其与必应搜索 API 所要求的格式相一致。

```javascript
// Build query options from selections in the HTML form.
function bingSearchOptions(form) {

    var options = [];
    // Where option.
    options.push("mkt=" + form.where.value);
    // SafeSearch option.
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "moderate"));
    // Freshness option.
    if (form.when.value.length) options.push("freshness=" + form.when.value);
    var what = [];
    for (var i = 0; i < form.what.length; i++)
        if (form.what[i].checked) what.push(form.what[i].value);
    // Promote option.
    if (what.length) {
        options.push("promote=" + what.join(","));
        options.push("answerCount=9");
    }
    // Count option.
    options.push("count=" + form.count.value);
    // Offset option.
    options.push("offset=" + form.offset.value);
    // Hardcoded text decoration option.
    options.push("textDecorations=true");
    // Hardcoded text format option.
    options.push("textFormat=HTML");
    return options.join("&");
}
```

可将 `SafeSearch` 设置为 `strict`、`moderate` 或 `off`，其中 `moderate` 是必应 Web 搜索的默认值。 此表单使用一个复选框，它有两种状态：`strict` 或 `moderate`。

如果选择任一“提升”复选框，则向查询添加 `answerCount` 参数。 使用 `promote` 参数时，`answerCount` 是必需的。 在此片段中，将值设置为 `9`，以返回所有可用的结果类型。
> [!NOTE]
> 提升结果类型后，该类型不一定会包含在搜索结果中  。 不过，提升可以提高此类结果的排名（相对于其通常的排名而言）。 若要将搜索限制为特定类型的结果，请使用 `responseFilter` 查询参数，或者调用更具体的终结点，例如必应图像搜索或必应新闻搜索。

将 `textDecoration` 和 `textFormat` 查询参数硬编码到脚本中，这两个参数可使搜索词在搜索结果中显示为粗体。 这些参数并非必需。

## <a name="manage-subscription-keys"></a>管理订阅密钥

为避免硬编码必应搜索 API 订阅密钥，此示例应用使用浏览器的持久性存储来存储订阅密钥。 如果未存储订阅密钥，则系统将提示用户输入此密钥。 如果 API 拒绝此订阅密钥，则系统将提示用户重新输入一个订阅密钥。

`getSubscriptionKey()` 函数使用 `storeValue` 和 `retrieveValue` 函数来存储和检索用户的订阅密钥。 这两个函数使用 `localStorage` 对象（若受支持）或 Cookie。

```javascript
// Cookie names for stored data.
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";

BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/search";

// See source code for storeValue and retrieveValue definitions.

// Get stored subscription key, or prompt if it isn't found.
function getSubscriptionKey() {
    var key = retrieveValue(API_KEY_COOKIE);
    while (key.length !== 32) {
        key = prompt("Enter Bing Search API subscription key:", "").trim();
    }
    // Always set the cookie in order to update the expiration date.
    storeValue(API_KEY_COOKIE, key);
    return key;
}
```

正如之前所见，在提交表单时，会触发 `onsubmit`，从而调用 `bingWebSearch`。 此函数对请求进行初始化并发送此请求。 每次提交来验证请求时，都会调用 `getSubscriptionKey`。

## <a name="call-bing-web-search"></a>调用必应 Web 搜索

有了查询、选项字符串和订阅密钥，`BingWebSearch` 函数就会创建一个 `XMLHttpRequest` 对象来调用必应 Web 搜索终结点。

```javascript
// Perform a search constructed from the query, options, and subscription key.
function bingWebSearch(query, options, key) {
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;

    showDiv("noresults", "Working. Please wait.");
    hideDivs("pole", "mainline", "sidebar", "_json", "_http", "paging1", "paging2", "error");

    var request = new XMLHttpRequest();
    var queryurl = BING_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;

    // Initialize the request.
    try {
        request.open("GET", queryurl);
    }
    catch (e) {
        renderErrorMessage("Bad request (invalid URL)\n" + queryurl);
        return false;
    }

    // Add request headers.
    request.setRequestHeader("Ocp-Apim-Subscription-Key", key);
    request.setRequestHeader("Accept", "application/json");
    var clientid = retrieveValue(CLIENT_ID_COOKIE);
    if (clientid) request.setRequestHeader("X-MSEdge-ClientID", clientid);

    // Event handler for successful response.
    request.addEventListener("load", handleBingResponse);

    // Event handler for errors.
    request.addEventListener("error", function() {
        renderErrorMessage("Error completing request");
    });

    // Event handler for an aborted request.
    request.addEventListener("abort", function() {
        renderErrorMessage("Request aborted");
    });

    // Send the request.
    request.send();
    return false;
}
```

在请求成功后，`load` 事件处理程序会启动并调用 `handleBingResponse` 函数。 `handleBingResponse` 会分析结果对象、显示结果，并包含失败请求的错误逻辑。

```javascript
function handleBingResponse() {
    hideDivs("noresults");

    var json = this.responseText.trim();
    var jsobj = {};

    // Try to parse results object.
    try {
        if (json.length) jsobj = JSON.parse(json);
    } catch(e) {
        renderErrorMessage("Invalid JSON response");
        return;
    }

    // Show raw JSON and the HTTP request.
    showDiv("json", preFormat(JSON.stringify(jsobj, null, 2)));
    showDiv("http", preFormat("GET " + this.responseURL + "\n\nStatus: " + this.status + " " +
        this.statusText + "\n" + this.getAllResponseHeaders()));

    // If the HTTP response is 200 OK, try to render the results.
    if (this.status === 200) {
        var clientid = this.getResponseHeader("X-MSEdge-ClientID");
        if (clientid) retrieveValue(CLIENT_ID_COOKIE, clientid);
        if (json.length) {
            if (jsobj._type === "SearchResponse" && "rankingResponse" in jsobj) {
                renderSearchResults(jsobj);
            } else {
                renderErrorMessage("No search results in JSON response");
            }
        } else {
            renderErrorMessage("Empty response (are you sending too many requests too quickly?)");
        }
    }

    // Any other HTTP response is considered an error.
    else {
        // 401 is unauthorized; force a re-prompt for the user's subscription
        // key on the next request.
        if (this.status === 401) invalidateSubscriptionKey();

        // Some error responses don't have a top-level errors object, if absent
        // create one.
        var errors = jsobj.errors || [jsobj];
        var errmsg = [];

        // Display the HTTP status code.
        errmsg.push("HTTP Status " + this.status + " " + this.statusText + "\n");

        // Add all fields from all error responses.
        for (var i = 0; i < errors.length; i++) {
            if (i) errmsg.push("\n");
            for (var k in errors[i]) errmsg.push(k + ": " + errors[i][k]);
        }

        // Display Bing Trace ID if it isn't blocked by CORS.
        var traceid = this.getResponseHeader("BingAPIs-TraceId");
        if (traceid) errmsg.push("\nTrace ID " + traceid);

        // Display the error message.
        renderErrorMessage(errmsg.join("\n"));
    }
}
```

> [!IMPORTANT]
> HTTP 请求成功并不表示搜索本身会成功  。 如果搜索操作中出现错误，必应 Web 搜索 API 将返回非 200 HTTP 状态代码并将错误信息包含在 JSON 响应中。 如果请求速率受到限制，该 API 会返回空响应。

上面两个函数中的很多代码专用于错误处理。 以下阶段可能会出现错误：

| 阶段 | 可能的错误 | 处理方式 |
|-------|--------------------|------------|
| 生成请求对象 | 无效的 URL | `try` / `catch` 块 |
| 发出请求 | 网络错误，已中止连接 | `error` 和 `abort` 事件处理程序 |
| 执行搜索 | 无效的请求、无效的 JSON、速率限制 | `load` 事件处理程序中的测试 |

通过调用 `renderErrorMessage()` 处理错误。 如果响应中传递了所有错误测试，则调用 `renderSearchResults()` 以显示搜索结果。

## <a name="display-search-results"></a>显示搜索结果

对于必应 Web 搜索 API 返回的结果，存在[使用和显示要求](useanddisplayrequirements.md)。 由于响应可能包含各种结果类型，因此除了通过顶级 `WebPages` 结合集合进行循环访问，还需其他操作。 相反，该示例应用使用 `RankingResponse` 按规范对结果进行排序。

> [!NOTE]
> 如果只希望具有一个结果类型，请使用 `responseFilter` 查询参数，或考虑使用某个其他必应搜索终结点，例如必应图像搜索。

每个响应都具有一个 `RankingResponse` 对象，它可能包含多达 3 个集合：`pole`、`mainline` 和 `sidebar`。 `pole`如果存在）是最相关的搜索结果，必须突出显示。 `mainline` 包含了大部分搜索结果，它紧接在 `pole` 后面显示。 `sidebar` 包含辅助搜索结果。 如果可能，应在侧栏中显示这些结果。 如果屏幕限制导致无法在侧栏中显示，则应在 `mainline` 结果后面显示这些结果。

每个 `RankingResponse` 都包含一个 `RankingItem` 数组，它指示了结果的排序方式。 我们的示例应用使用 `answerType` 和 `resultIndex` 参数来标识结果。

> [!NOTE]
> 还可采用其他方法来标识结果并对其进行排名。 有关详细信息，请参阅[通过排名显示结果](rank-results.md)。

让我们看一下代码：

```javascript
// Render the search results from the JSON response.
function renderSearchResults(results) {

    // If spelling was corrected, update the search field.
    if (results.queryContext.alteredQuery)
        document.forms.bing.query.value = results.queryContext.alteredQuery;

    // Add Prev / Next links with result count.
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);

    // Render the results for each section.
    for (section in {pole: 0, mainline: 0, sidebar: 0}) {
        if (results.rankingResponse[section])
            showDiv(section, renderResultsItems(section, results));
    }
}
```

`renderResultsItems()` 函数会循环访问每个 `RankingResponse` 集合中的项目，使用 `answerType` 和 `resultIndex` 字值将每个排名结果映射到一个搜索结果，并调用相应的呈现函数来生成 HTML。 如果未向项目指定 `resultIndex`，则 `renderResultsItems()` 会循环访问该类型的所有结果，并为每个项目调用呈现函数。 生成的 HTML 将插入到 `index.html` 中相应的 `<div>` 元素中。

```javascript
// Render search results from the RankingResponse object per rank response and
// use and display requirements.
function renderResultsItems(section, results) {

    var items = results.rankingResponse[section].items;
    var html = [];
    for (var i = 0; i < items.length; i++) {
        var item = items[i];
        // Collection name has lowercase first letter while answerType has uppercase
        // e.g. `WebPages` RankingResult type is in the `webPages` top-level collection.
        var type = item.answerType[0].toLowerCase() + item.answerType.slice(1);
        if (type in results && type in searchItemRenderers) {
            var render = searchItemRenderers[type];
            // This ranking item refers to ONE result of the specified type.
            if ("resultIndex" in item) {
                html.push(render(results[type].value[item.resultIndex], section));
            // This ranking item refers to ALL results of the specified type.
            } else {
                var len = results[type].value.length;
                for (var j = 0; j < len; j++) {
                    html.push(render(results[type].value[j], section, j, len));
                }
            }
        }
    }
    return html.join("\n\n");
}
```

## <a name="review-renderer-functions"></a>查看呈现器函数

在示例应用中，`searchItemRenderers` 对象包含可为每个类型的搜索结果生成 HTML 的函数。

```javascript
// Render functions for each result type.
searchItemRenderers = {
    webPages: function(item) { ... },
    news: function(item) { ... },
    images: function(item, section, index, count) { ... },
    videos: function(item, section, index, count) { ... },
    relatedSearches: function(item, section, index, count) { ... }
}
```

> [!IMPORTANT]
> 该示例应用具有用于网页、新闻、图像、视频和相关搜索项的呈现器。 在你的应用程序中，对于该应用可能接收的任意类型的结果（例如计算、拼写建议、实体、时区和定义），都需要使用呈现器。

一些呈现函数仅接受 `item` 参数。 而其他一些接受其他参数，这些参数可用于根据上下文按不同的方式呈现项目。 如果呈现器不使用此信息，则该呈现器无需接受这些参数。

上下文参数为：

| 参数  | 说明 |
|------------|-------------|
| `section` | 结果部分（`pole`、`mainline` 或 `sidebar`），在其中会显示项。 |
| `index`<br>`count` | 在 `RankingResponse` 项指定显示给定集合中的所有项时可用；否则为 `undefined`。 其集合中项目的索引，以及该集合中的项目总数。 此信息可用于设置结果编号，还可用于为第一个或最后一个结果生成不同的 HTML 等等。 |

在示例应用中，`images` 和 `relatedSearches` 呈现器都使用上下文参数来自定义所生成的 HTML。 让我们更仔细地看看 `images` 呈现器：

```javascript
searchItemRenderers = {
    // Render image result with thumbnail.
    images: function(item, section, index, count) {
        var height = 60;
        var width = Math.round(height * item.thumbnail.width / item.thumbnail.height);
        var html = [];
        if (section === "sidebar") {
            if (index) html.push("<br>");
        } else {
            if (!index) html.push("<p class='images'>");
        }
        html.push("<a href='" + item.hostPageUrl + "'>");
        var title = escape(item.name) + "\n" + getHost(item.hostPageDisplayUrl);
        html.push("<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width +
            "' height=" + height + " width=" + width + " title='" + title + "' alt='" + title + "'>");
        html.push("</a>");
        return html.join("");
    },
    // Other renderers are omitted from this sample...
}
```

图像呈现器：

* 计算图像缩略图大小（宽度可变，高度则固定为 60 像素）。
* 根据上下文插入图像结果前面的 HTML。
* 生成链接到图像所在页面的 HTML `<a>` 标记。
* 可生成 HTML `<img>` 标记以显示图像缩略图。

图像呈现器使用 `section` 和 `index` 变量以不同方式显示结果，具体取决于它们的出现位置。 换行符（`<br>` 标记）插入到侧栏中的图像结果之间，因此侧栏会显示一个包含图像的列。 在其他部分，第一个图像结果 `(index === 0)` 之前有 `<p>` 标记。

`<img>` 标记以及缩略图 URL 的 `h` 和 `w` 字段中均使用了缩略图大小。 `title` 和 `alt` 属性（对图像进行文字说明）是根据 URL 中图像的名称和主机名构建的。

下面的示例展示了如何在示例应用中显示图像：

![[必应图像结果]](media/cognitive-services-bing-web-api/web-search-spa-images.png)

## <a name="persist-the-client-id"></a>保留客户端 ID

来自必应搜索 API 的响应可能包含 `X-MSEdge-ClientID` 标头，它应随附每个后续请求一并发送回 API。 如果你的应用使用了多个必应搜索 API，请确保在不同的服务中随附每个请求发送的客户端 ID 一致。

如果提供 `X-MSEdge-ClientID` 标头，则必应 API 可关联用户的搜索项。 首先，它允许必应搜索引擎将过去的上下文应用于搜索，从而查找能更好地满足请求的结果。 例如，如果用户以前搜索过与航海相关的词汇，则稍后搜索“节”时，系统可能会优先返回在航海中使用的节的信息。 其次，在新功能广泛应用之前，必应可能会随机选择用户体验该功能。 为每个请求提供相同的客户端 ID 可确保获准查看某项功能的用户将始终看到此功能。 如果没有客户端 ID，用户可能会看到功能在其搜索结果中随机出现和消失。

浏览器安全策略（例如跨源资源共享 (CORS)）可能阻止示例应用访问 `X-MSEdge-ClientID` 标头。 当搜索响应的域不同于请求搜索的页面时，会出现此限制。 在生产环境中，应该托管一个服务器端脚本，以便在网页所在的域进行 API 调用，这样就可以解决此策略的问题。 由于脚本具有与网页相同的来源，因此会将 `X-MSEdge-ClientID` 标头提供给 JavaScript。

> [!NOTE]
> 在生产型 Web 应用程序中，无论如何都应在服务器端执行请求。 否则，必应搜索 API 订阅密钥必须包含在网页中，该网页可供查看来源的任何人使用。 收费取决于 API 订阅密钥下的所有使用量（即使请求是由未经授权的用户发出的，也是如此），因此请确保不要公开你的密钥。

进行开发时，可通过 CORS 代理发出请求。 此类代理的响应具有 `Access-Control-Expose-Headers` 标头，它将响应头列入允许列表，让响应头可供 JavaScript 访问。

轻松操作即可安装 CORS 代理，它使示例应用能够访问客户端 ID 标头。 运行以下命令：

```console
npm install -g cors-proxy-server
```

接下来，在 `script.js` 中将必应 Web 搜索终结点更改为：

```javascript
http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search
```

使用以下命令启动 CORS 代理：

```console
cors-proxy-server
```

使用示例应用时，不要关闭命令窗口；关闭窗口会导致代理停止运行。 搜索结果下可展开的 HTTP 标头部分中应显示有 `X-MSEdge-ClientID` 标头。 验证确保每个请求的此标头相同。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [必应 Web 搜索 API v7 参考](//docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)
