---
title: 必应 Web 搜索单页 Web 应用 | Microsoft Docs
description: 介绍如何在单页 Web 应用程序中使用必应 Web 搜索 API。
services: cognitive-services
author: v-jerkin
manager: ehansen
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 10/04/2017
ms.author: v-jerkin
ms.openlocfilehash: f22e38a1d6ee4042684b9822b58669bed6fe29a0
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366468"
---
# <a name="tutorial-single-page-web-app"></a>教程：单页 Web 应用

可以通过必应 Web 搜索 API 搜索 Web 并获取与搜索查询相关的各类结果。 本教程将生成一个单页 Web 应用程序，该应用程序使用必应 Web 搜索 API 直接在页面中显示搜索结果。 该应用程序包含 HTML、CSS 和 JavaScript 组件。

<!-- Remove until this can be replaced with a sanitized version.
![[Single-page Bing Web Search app]](media/cognitive-services-bing-web-api/web-search-spa-demo.png)
-->

> [!NOTE]
> 页面底部出现的 JSON 和 HTTP 标题揭示单击时的 JSON 响应和 HTTP 请求信息。 这些详细信息在浏览该服务时很有用。

教程应用演示了如何：

> [!div class="checklist"]
> * 在 JavaScript 中执行必应 Web 搜索 API 调用
> * 将搜索选项传递到必应 Web 搜索 API
> * 显示 Web、新闻、图像和视频搜索结果
> * 对搜索结果进行分页
> * 处理必应客户端 ID 和 API 订阅密钥
> * 处理可能出现的错误

教程页是完全独立的；它不使用任何外部框架、样式表，甚至也不使用外部图像文件， 而只使用获得广泛支持的 JavaScript 语言功能，适用于所有主要 Web 浏览器的最新版本。

在本教程中，我们只讨论源代码的选定部分。 完整的源代码[在单独页上](tutorial-bing-web-search-single-page-app-source.md)提供。 请将此代码复制并粘贴到文本编辑器中，并将其另存为 `bing.html`。

## <a name="app-components"></a>应用组件

与任何单页 Web 应用一样，本教程应用程序包含以下三个部分：

> [!div class="checklist"]
> * HTML - 定义页面的结构和内容
> * CSS - 定义页面的外观
> * JavaScript - 定义页面的行为

本教程不详细介绍大多数 HTML 或 CSS，因为它们很简单。

HTML 包含搜索表单，用户可以在其中输入查询并选择搜索选项。 该表单连接到实际上会通过 `<form>` 标记的 `onsubmit` 属性执行搜索的 JavaScript：

```html
<form name="bing" onsubmit="return newBingWebSearch(this)">
```

`onsubmit` 处理程序返回 `false`，这会阻止表单提交到服务器。 JavaScript 代码实际上执行从表单中收集所需的信息并执行搜索的工作。

HTML 还包含划分（HTML `<div>` 标记），可以在其中显示搜索结果。

## <a name="managing-subscription-key"></a>管理订阅密钥

为了避免将必应搜索 API 订阅密钥包含在代码中，我们使用浏览器的持久性存储来存储密钥。 如果尚未存储任何密钥，我们会提示你输入用户密钥进行存储，以备后用。 如果该密钥稍后被 API 拒绝，我们将使已存储的密钥失效，因此系统会再次提示用户输入。

我们定义使用 `localStorage` 对象（如果浏览器支持它）或 Cookie 的 `storeValue` 和 `retrieveValue` 函数。 `getSubscriptionKey()` 函数使用这些函数来存储和检索用户的密钥。

```javascript
// cookie names for data we store
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";

BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/search";

// ... omitted definitions of storeValue() and retrieveValue()

// get stored API subscription key, or prompt if it's not found
function getSubscriptionKey() {
    var key = retrieveValue(API_KEY_COOKIE);
    while (key.length !== 32) {
        key = prompt("Enter Bing Search API subscription key:", "").trim();
    }
    // always set the cookie in order to update the expiration date
    storeValue(API_KEY_COOKIE, key);
    return key;
}
```

HTML `form` 标记 `onsubmit` 可调用 `bingWebSearch` 函数以返回搜索结果。 `bingWebSearch` 使用 `getSubscriptionKey` 对每个查询进行身份验证。 如先前定义中所示，如果尚未输入密钥，`getSubscriptionKey` 会提示用户输入密钥。 然后会存储该密钥以供应用程序继续使用。

```html
<form name="bing" onsubmit="this.offset.value = 0; return bingWebSearch(this.query.value, 
    bingSearchOptions(this), getSubscriptionKey())">
```

## <a name="selecting-search-options"></a>选择搜索选项

![[必应 Web 搜索表单]](media/cognitive-services-bing-web-api/web-search-spa-form.png)

HTML 表单包括具有以下名称的元素：

| | |
|-|-|
| `where` | 用于选择市场（位置和语言）进行搜索的下拉菜单。 |
| `query` | 用于输入搜索词的文本字段。 |
| `what` | 用于提升特定结果类型的复选框。 提升图像，例如，提升图像的排名。 |
| `when` | 用于视需要将搜索限制为最近一天、最近一周或最近一个月的下拉菜单。 |
| `safe` | 指示是否使用必应的安全搜索功能筛选出“成人”结果的复选框。 |
| `count` | 隐藏的字段。 将在每个请求后返回的搜索结果数。 更改为每页显示更少或更多结果。 |
| `offset` | 隐藏的字段。 请求中第一个搜索结果的偏移；用于分页。 在新请求中将重置为 `0`。 |

> [!NOTE]
> 必应 Web 搜索还提供许多其他的查询参数。 我们在这里仅使用其中一些参数。

JavaScript 函数 `bingSearchOptions()` 会将这些字段转换为必应搜索 API 要求的格式。

```javascript
// build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    if (form.when.value.length) options.push("freshness=" + form.when.value);
    var what = [];
    for (var i = 0; i < form.what.length; i++) 
        if (form.what[i].checked) what.push(form.what[i].value);
    if (what.length) {
        options.push("promote=" + what.join(","));
        options.push("answerCount=9");
    }
    options.push("count=" + form.count.value);
    options.push("offset=" + form.offset.value);
    options.push("textDecorations=true");
    options.push("textFormat=HTML");
    return options.join("&");
}
```

例如，实际 API 调用中的 `SafeSearch` 参数可以为 `strict`、`moderate` 或 `off`，并将 `moderate` 作为默认值。 但是，我们的表单会使用仅具有两种状态的复选框。 JavaScript 代码会将此设置转换为 `strict` 或 `off`（不会使用 `moderate`）。

如果选中了任何“提升”复选框，我们还会向查询添加 `answerCount` 参数。 使用 `promote` 参数时，`answerCount` 是必需的。 我们直接将其设置为 `9`（必应 Web 搜索 API 支持的结果类型数），这样可确保尽可能获得最大的结果类型数。

> [!NOTE]
> 提升某个结果类型并不能确保搜索结果包含该类型的结果。 不过，提升可以提高此类结果的排名（相对于其通常的排名而言）。 若要将搜索限制为特定类型的结果，请使用 `responseFilter` 查询参数，或者调用更具体的终结点，例如必应图像搜索或必应新闻搜索。

我们还发送 `textDecoration` 和 `textFormat` 查询参数，使搜索词在搜索结果中显示为粗体。 这些值在脚本中进行硬编码。

## <a name="performing-the-request"></a>执行请求

有了查询、选项字符串和 API 密钥以后，`BingWebSearch` 函数就会使用 `XMLHttpRequest` 对象向必应 Web 搜索终结点发出请求。

```javascript
// perform a search given query, options string, and API key
function bingWebSearch(query, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("pole", "mainline", "sidebar", "_json", "_http", "paging1", "paging2", "error");

    var request = new XMLHttpRequest();
    var queryurl = BING_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;

    // open the request
    try {
        request.open("GET", queryurl);
    } 
    catch (e) {
        renderErrorMessage("Bad request (invalid URL)\n" + queryurl);
        return false;
    }

    // add request headers
    request.setRequestHeader("Ocp-Apim-Subscription-Key", key);
    request.setRequestHeader("Accept", "application/json");
    var clientid = retrieveValue(CLIENT_ID_COOKIE);
    if (clientid) request.setRequestHeader("X-MSEdge-ClientID", clientid);
    
    // event handler for successful response
    request.addEventListener("load", handleBingResponse);
    
    // event handler for erorrs
    request.addEventListener("error", function() {
        renderErrorMessage("Error completing request");
    });

    // event handler for aborted request
    request.addEventListener("abort", function() {
        renderErrorMessage("Request aborted");
    });

    // send the request
    request.send();
    return false;
}
```

HTTP 请求成功完成后，JavaScript 会调用 `load` 事件处理程序（即 `handleBingResponse()` 函数）来处理对 API 的成功 HTTP GET 请求。 

```javascript
// handle Bing search request results
function handleBingResponse() {
    hideDivs("noresults");

    var json = this.responseText.trim();
    var jsobj = {};

    // try to parse JSON results
    try {
        if (json.length) jsobj = JSON.parse(json);
    } catch(e) {
        renderErrorMessage("Invalid JSON response");
        return;
    }

    // show raw JSON and HTTP request
    showDiv("json", preFormat(JSON.stringify(jsobj, null, 2)));
    showDiv("http", preFormat("GET " + this.responseURL + "\n\nStatus: " + this.status + " " + 
        this.statusText + "\n" + this.getAllResponseHeaders()));

    // if HTTP response is 200 OK, try to render search results
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

    // Any other HTTP response is an error
    else {
        // 401 is unauthorized; force re-prompt for API key for next request
        if (this.status === 401) invalidateSubscriptionKey();

        // some error responses don't have a top-level errors object, so gin one up
        var errors = jsobj.errors || [jsobj];
        var errmsg = [];

        // display HTTP status code
        errmsg.push("HTTP Status " + this.status + " " + this.statusText + "\n");

        // add all fields from all error responses
        for (var i = 0; i < errors.length; i++) {
            if (i) errmsg.push("\n");
            for (var k in errors[i]) errmsg.push(k + ": " + errors[i][k]);
        }

        // also display Bing Trace ID if it isn't blocked by CORS
        var traceid = this.getResponseHeader("BingAPIs-TraceId");
        if (traceid) errmsg.push("\nTrace ID " + traceid);

        // and display the error message
        renderErrorMessage(errmsg.join("\n"));
    }
}
```

> [!IMPORTANT]
> HTTP 请求成功不一定意味着搜索本身成功。 如果搜索操作中出现错误，必应 Web 搜索 API 将返回非 200 HTTP 状态代码并将错误信息包含在 JSON 响应中。 此外，如果请求速率受限制，该 API 还会返回空响应。

上面两个函数中的很多代码专用于错误处理。 以下阶段可能会出现错误：

|阶段|可能的错误|处理方式|
|-|-|-|
|生成 JavaScript 请求对象|无效的 URL|`try`/`catch` 块|
|发出请求|网络错误，已中止连接|`error` 和 `abort` 事件处理程序|
|执行搜索|无效的请求、无效的 JSON、速率限制|`load` 事件处理程序中的测试|

处理错误时，会调用 `renderErrorMessage()`，获取有关该错误的任何已知详细信息。 如果响应通过全部错误测试，则会调用 `renderSearchResults()`，在页面中显示搜索结果。

## <a name="displaying-search-results"></a>显示搜索结果

必应 Web 搜索 API [要求按指定顺序显示结果](useanddisplayrequirements.md)。 由于 API 可能返回不同类型的响应，在 JSON 响应中循环访问顶级 `WebPages` 集合并显示这些结果会显得不够。 （如果只需一类结果，请使用 `responseFilter` 查询参数或其他必应搜索终结点。）

请在搜索结果中改用 `rankingResponse`，对要显示的结果排序。 此对象是指 JSON 响应中 `WebPages`、`News`、`Images` 和/或 `Videos` 集合中的项，或其他顶级答案集合中的项。

`rankingResponse` 最多可能包含三个搜索结果集合，分别称为 `pole`、`mainline` 和 `sidebar`。 

`pole`，如果存在，是最相关的搜索结果，应该突出显示。 `mainline` 是指主要搜索结果。 mainline 结果应该紧跟在 `pole` 后面显示（或者显示在最前面，如果 `pole` 不存在的话）。 

最后， `sidebar` 是指辅助搜索结果。 通常情况下，这些结果是相关的搜索或图像。 可能情况下，这些结果会显示在实际的侧栏中。 如果屏幕限制导致在侧栏中显示不现实（例如，在使用移动设备的情况下），则应将其显示在 `mainline` 结果之后。

`rankingResponse` 集合中的每个项采用两种不同但却等效的方式来映射实际的搜索结果项。

| | |
|-|-|
|`id`|`id` 看起来像 URL，但不应将其用于链接。 排名结果的 `id` 类型与答案集合中某个搜索结果项的 `id` 匹配，或者与整个答案集合（例如 `Images`）匹配。
|`answerType`、`resultIndex`|`answerType` 是指包含结果的顶级答案集合（例如 `WebPages`）。 `resultIndex` 是指结果在该集合中的索引。 如果省略 `resultIndex`，则排名结果指整个集合。

> [!NOTE]
> 有关此部分搜索响应的详细信息，请参阅[排名结果](rank-results.md)。

可以根据应用程序的情况，使用最方便的方法来定位引用的搜索结果项。 在教程代码中，我们使用 `answerType` 和 `resultIndex` 来定位每个搜索结果。

最后，让我们看看函数 `renderSearchResults()`。 此函数循环访问三个 `rankingResponse` 集合，这些集合代表搜索结果的三个部分。 对于每个部分，我们都调用 `renderResultsItems()` 来呈现该部分的结果。

```javascript
// render the search results given the parsed JSON response
function renderSearchResults(results) {

    // if spelling was corrected, update search field
    if (results.queryContext.alteredQuery) 
        document.forms.bing.query.value = results.queryContext.alteredQuery;

    // add Prev / Next links with result count
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);
    
    // for each possible section, render the resuts from that section
    for (section in {pole: 0, mainline: 0, sidebar: 0}) {
        if (results.rankingResponse[section])
            showDiv(section, renderResultsItems(section, results));
    }
}
```

`renderResultsItems()` 则会循环访问每个 `rankingResponse` 部分中的项，使用 `answerType` 和 `resultIndex` 字段将每个排名结果映射到一个搜索结果，并调用相应的呈现函数来生成结果的 HTML。 

如果未为给定的排名项指定 `resultIndex`，则 `renderResultsItems()` 会循环访问该类型的所有结果，并针对每个项来调用呈现函数。 

不管什么方式，生成的 HTML 都会插入到页面的相应 `<div>` 元素中。

```javascript
// render search results from rankingResponse object in specified order
function renderResultsItems(section, results) {

    var items = results.rankingResponse[section].items;
    var html = [];
    for (var i = 0; i < items.length; i++) {
        var item = items[i];
        // collection name has lowercase first letter while answerType has uppercase
        // e.g. `WebPages` rankingResult type is in the `webPages` top-level collection
        var type = item.answerType[0].toLowerCase() + item.answerType.slice(1);
        // must have results of the given type AND a renderer for it
        if (type in results && type in searchItemRenderers) {
            var render = searchItemRenderers[type];
            // this ranking item refers to ONE result of the specified type
            if ("resultIndex" in item) {
                html.push(render(results[type].value[item.resultIndex], section));
            // this ranking item refers to ALL results of the specified type
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

## <a name="rendering-result-items"></a>呈现结果项

在 JavaScript 代码中有一个对象 `searchItemRenderers`，其中包含呈现器函数，为每种搜索结果生成 HTML。

```javascript
// render functions for various types of search results
searchItemRenderers = { 
    webPages: function(item) { ... },
    news: function(item) { ... },
    images: function(item, section, index, count) { ... },
    videos: function(item, section, index, count) { ... },
    relatedSearches: function(item, section, index, count) { ... }
}
```

> [!NOTE]
> 教程应用有用于网页、新闻项、图像、视频和相关搜索的呈现器。 你自己的应用程序需要呈现器，该呈现器应该适合你可能收到的任何类型的结果，其中可能包括计算、拼写建议、实体、时区以及定义。

某些呈现函数只接受 `item` 参数，该参数是一个 JavaScript 对象，代表单个搜索结果。 其他呈现函数接受其他参数，这些参数可以用来在不同的上下文中以不同方式呈现项目。 （不使用此信息的呈现器不需接受这些参数。）

上下文参数为：

| | |
|-|-|
|`section`|结果部分（`pole`、`mainline` 或 `sidebar`），在其中会显示项。
|`index`<br>`count`|在 `rankingResponse` 项指定显示给定集合中的所有项时可用；否则为 `undefined`。 这些参数接收项集合中的项索引，以及该集合中的项总数。 可以利用此信息为结果编号、为第一个或最后一个结果生成不同的 HTML，等等。|

在教程应用中，`images` 和 `relatedSearches` 呈现器都使用上下文参数来自定义所生成的 HTML。 让我们更仔细地看看 `images` 呈现器：

```javascript
searchItemRenderers = { 
    // render image result using thumbnail
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
    }, // other renderers omitted
}
```

图像呈现器函数具有以下功能：

> [!div class="checklist"]
> * 计算图像缩略图大小（宽度可变，高度则固定为 60 像素）。
> * 根据上下文插入位于图像结果之前的 HTML。
> * 生成链接到图像所在页面的 HTML `<a>` 标记。
> * 生成 HTML `<img>` 标记以显示图像缩略图。 

图像呈现器使用 `section` 和 `index` 变量以不同方式显示结果，具体取决于它们的出现位置。 换行符（`<br>` 标记）插入到侧栏中的图像结果之间，因此侧栏会显示一个包含图像的列。 在其他部分，第一个图像结果 `(index === 0)` 之前有 `<p>` 标记。 否则，缩略图会彼此对接好，并根据需要在浏览器窗口中换行。

`<img>` 标记以及缩略图 URL 的 `h` 和 `w` 字段中均使用了缩略图大小。 然后，[必应缩略图服务](resize-and-crop-thumbnails.md)会提供正好为该大小的缩略图。 `title` 和 `alt` 属性（对图像进行文字说明）是根据 URL 中图像的名称和主机名构建的。

图像显示在主线搜索结果中，如下所示。

![[必应图像结果]](media/cognitive-services-bing-web-api/web-search-spa-images.png)

## <a name="persisting-client-id"></a>保留客户端 ID

来自必应搜索 API 的响应可能包含应通过后续请求发送回 API 的 `X-MSEdge-ClientID` 标头。 如果正在使用多个必应搜索 API，应将相同客户端 ID 用于所有这些必应搜索 API（如有可能）。

提供 `X-MSEdge-ClientID` 标头可以让必应 API 关联用户的所有搜索，这有两个主要好处。

首先，它允许必应搜索引擎将过去的上下文应用于搜索来查找更好地满足用户的结果。 例如，如果用户以前搜索过与航海相关的词汇，则稍后搜索“节”时，系统可能会优先返回在航海中使用的节的信息。

其次，在新功能广泛应用之前，必应可能会随机选择用户体验该功能。 为每个请求提供相同客户端 ID 可确保被允许看到某个功能的用户可以始终看到该功能。 如果没有客户端 ID，用户可能会看到功能在其搜索结果中随机出现和消失。

浏览器安全策略 (CORS) 可能会阻止将 `X-MSEdge-ClientID` 标头提供给 JavaScript。 当搜索响应的域不同于请求搜索的页面时，会出现此限制。 在生产环境中，应该托管一个服务器端脚本，以便在网页所在的域进行 API 调用，这样就可以解决此策略的问题。 由于脚本与网页的域相同，因此 `X-MSEdge-ClientID` 标头可供 JavaScript 使用。

> [!NOTE]
> 在生产型 Web 应用程序中，无论如何都应在服务器端执行请求。 否则就必须将必应搜索 API 密钥包含在网页中，这样查看源代码的任何人都可以获得它。 收费取决于 API 订阅密钥下的所有使用量（即使请求是由未经授权的用户发出的，也是如此），因此请确保不要公开你的密钥。

进行开发时，可以通过 CORS 代理发出必应 Web 搜索 API 请求。 来自此类代理的响应有一个 `Access-Control-Expose-Headers` 标头，此标头将响应头列入允许列表，并将它们提供给 JavaScript。

安装 CORS 代理很容易，教程应用可以用它来访问客户端 ID 标头。 首先，请[安装 Node.js](https://nodejs.org/en/download/)（如果尚未安装）。 然后，在命令窗口中发出以下命令：

    npm install -g cors-proxy-server

接下来，在 HTML 文件中将必应 Web 搜索终结点更改为：

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

最后，使用以下命令启动 CORS 代理：

    cors-proxy-server

在使用教程应用期间，请让命令窗口保持打开状态；关闭此窗口会阻止代理。 在搜索结果下的可展开 HTTP 标头部分中，现在可以看到 `X-MSEdge-ClientID` 标头（以及其他标头），并可验证每个请求的该标头是否都相同。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [视觉搜索移动应用教程](computer-vision-web-search-tutorial.md)

> [!div class="nextstepaction"]
> [必应 Web 搜索 API 参考](//docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)
