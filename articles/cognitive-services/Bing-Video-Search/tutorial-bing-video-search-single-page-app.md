---
title: 教程：生成单页必应视频搜索应用
titleSuffix: Azure Cognitive Services
description: 本教程介绍如何在单页 Web 应用程序中使用必应视频搜索 API。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: tutorial
ms.date: 02/03/2020
ms.author: aahi
ms.openlocfilehash: fb989825ed27cc83c14c36e6394e37ae2db2c12a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988254"
---
# <a name="tutorial-single-page-video-search-app"></a>教程：单页视频搜索应用
使用必应视频搜索 API 可以搜索 Web，并获取与搜索查询相关的视频结果。 本教程将生成一个单页 Web 应用程序，该应用程序使用必应搜索 API 在页面中显示搜索结果。 该应用程序包含 HTML、CSS 和 JavaScript 组件。

<!-- Remove until it can be replaced with a sanitized version.
![Single-page Bing Video Search app](./media/video-search-singlepage.png)
-->

> [!NOTE]
> 单击时页面底部出现的 JSON 和 HTTP 标题显示 JSON 响应和 HTTP 请求信息。 这些详细信息在浏览该服务时非常有用。

![JSON、HTTP 原始结果](./media/json-http-raw-results.png)

本教程应用演示如何执行以下操作：
> [!div class="checklist"]
> * 在 JavaScript 中执行必应视频搜索 API 调用
> * 将搜索选项传递到必应搜索 API
> * 显示视频搜索结果或（可选）包含网页、新闻或图像
> * 基于 24 小时、过去一周、过去一月或所有可用时间的时间范围进行搜索
> * 翻页浏览搜索结果
> * 处理必应客户端 ID 和 API 订阅密钥
> * 处理可能出现的错误

教程页是完全独立的；它不使用任何外部框架、样式表或图像文件， 而仅使用广泛支持的 JavaScript 语言功能，并且适用于所有主要 Web 浏览器的当前版本。

本教程将讨论源代码的选定部分。 提供了完整的[源代码](tutorial-bing-video-search-single-page-app-source.md)。 若要运行该示例，请将源代码复制并粘贴到文本编辑器中，并将其另存为 `bing.html`。

## <a name="app-components"></a>应用组件
与任何单页 Web 应用一样，本教程应用程序包含以下三个部分：

> [!div class="checklist"]
> * HTML - 定义页面的结构和内容
> * CSS - 定义页面的外观
> * JavaScript - 定义页面的行为

大部分 HTML 和 CSS 是常规的，因此本教程不做讨论。 HTML 包含搜索表单，用户可以在其中输入查询并选择搜索选项。 该表单会连接到使用 `<form>` 标记的 `onsubmit` 属性执行搜索的 JavaScript：

```html
<form name="bing" onsubmit="return bingWebSearch(this)">
```
`onsubmit` 处理程序返回 `false`，这会阻止表单提交到服务器。 JavaScript 代码将执行从表单中收集所需的信息并执行搜索的工作。

HTML 还包含部门（HTML `<div>` 标记），其中显示搜索结果。

## <a name="managing-subscription-key"></a>管理订阅密钥

为了避免将必应搜索 API 订阅密钥包含在代码中，我们使用浏览器的持久性存储来存储密钥。 在存储密钥之前，系统会提示我们输入用户的密钥。 如果该密钥稍后被 API 拒绝，我们将使已存储的密钥失效，因此系统会再次提示用户输入。

我们定义使用 `localStorage` 对象（并非所有浏览器都支持它）或 cookie 的 `storeValue` 和 `retrieveValue` 函数。 `getSubscriptionKey()` 函数使用这些函数来存储和检索用户的密钥。

``` javascript
// Cookie names for data we store
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";

// ... omitted definitions of store value and retrieve value
// Browsers differ in their support for persistent storage by 
// local HTML files. See the source code for browser-specific
// options.

// Get stored API subscription key, or prompt if it's not found.
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
HTML `<form>` 标记 `onsubmit` 可调用 `bingWebSearch` 函数以返回搜索结果。 `bingWebSearch` 使用 `getSubscriptionKey()` 对每个查询进行身份验证。 如先前定义中所示，如果尚未输入密钥，`getSubscriptionKey` 会提示用户输入密钥。 然后会存储该密钥以供应用程序继续使用。

```html
<form name="bing" onsubmit="this.offset.value = 0; return bingWebSearch(this.query.value, 
    bingSearchOptions(this), getSubscriptionKey())">
```
## <a name="selecting-search-options"></a>选择搜索选项
下图显示了查询文本框以及用于定义搜索的选项。

![必应新闻搜索选项](media/video-search-options.png)

HTML 表单包括具有以下名称的元素：

|元素|说明|
|-|-|
| `where` | 用于选择市场（位置和语言）进行搜索的下拉菜单。 |
| `query` | 用于输入搜索条件的文本字段。 |
| `modules` | 用于提升特定结果模块、所有结果或相关视频的复选框。 |
| `when` | 用于视需要将搜索限制为最近一天、最近一周或最近一个月的下拉菜单。 |
| `safe` | 指示是否使用必应安全搜索功能筛选出“成人”结果的复选框。 |
| `count` | 隐藏的字段。 将在每个请求后返回的搜索结果数。 更改为每页显示更少或更多结果。 |
| `offset`|  隐藏的字段。 请求中第一个搜索结果的偏移；用于分页。 在新请求中将重置为 `0`。 |

> [!NOTE]
> 必应 Web 搜索提供了其他查询参数。 我们仅使用其中一些参数。

``` javascript
// build query options from the HTML form
// build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "moderate"));

    if (form.when.value.length) options.push("freshness=" + form.when.value);
    var what = [];
    for (var i = 0; i < form.what.length; i++) 
        if (form.what[i].checked) what.push(form.what[i].value);
    if (what.length) {
        options.push("modules=" + what.join(","));
        options.push("answerCount=9");
    }
    options.push("count=" + form.count.value);
    options.push("offset=" + form.offset.value);
    options.push("textDecorations=true");
    options.push("textFormat=HTML");
    return options.join("&");
}
```

例如，实际 API 调用中的 `SafeSearch` 参数可以是 `strict` 或 `moderate`，其中 `moderate` 是默认值。

## <a name="performing-the-request"></a>执行请求
鉴于查询、选项字符串和 API 密钥，`BingWebSearch` 函数会使用 `XMLHttpRequest` 对象向必应搜索终结点发出请求。 可以使用下面的全局终结点，也可以使用资源的 Azure 门户中显示的[自定义子域](../../cognitive-services/cognitive-services-custom-subdomains.md)终结点。

```javascript
// Search on the query, using search options, authenticated by the key.
function bingWebSearch(query, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("pole", "mainline", "sidebar", "_json", "_headers", "paging1", "paging2", "error");

    var endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/videos/search";
    var request = new XMLHttpRequest();
    var queryurl = endpoint + "?q=" + encodeURIComponent(query) + "&" + options;

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
    request.addEventListener("load", handleOnLoad);

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
HTTP 请求成功完成后，JavaScript 会调用 `load` 事件处理程序和 `handleOnLoad()` 来处理对 API 的成功 HTTP GET 请求。 

```javascript
// handle Bing search request results
function handleOnLoad() {
    hideDivs("noresults");

    var json = this.responseText.trim();
    var jsobj = {};

    // try to parse JSON results
    try {
        if (json.length) jsobj = JSON.parse(json);
    } catch(e) {
        renderErrorMessage("Invalid JSON response");
    }

    // show raw JSON and headers
    showDiv("json", preFormat(JSON.stringify(jsobj, null, 2)));
    showDiv("http", preFormat("GET " + this.responseURL + "\n\nStatus: " + this.status + " " + 
        this.statusText + "\n" + this.getAllResponseHeaders()));

    // if HTTP response is 200 OK, try to render search results
    if (this.status === 200) {
        var clientid = this.getResponseHeader("X-MSEdge-ClientID");
        if (clientid) retrieveValue(CLIENT_ID_COOKIE, clientid);
        if (json.length) {
            if (jsobj._type === "Videos") {//"SearchResponse" && "rankingResponse" in jsobj) {
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
> 如果搜索操作中出现错误，必应新闻搜索 API 将返回非 200 HTTP 状态代码并将错误信息包含在 JSON 响应中。 此外，如果请求速率受限制，该 API 还会返回空响应。
成功的 HTTP 请求不  一定意味着搜索本身成功。 

上面两个函数中的很多代码专用于错误处理。 以下阶段可能会出现错误：

|阶段|可能的错误|处理方式|
|-|-|-|
|生成 JavaScript 请求对象|无效的 URL|`try`/`catch` 块|
|发出请求|网络错误，已中止连接|`error` 和 `abort` 事件处理程序|
|执行搜索|无效的请求、无效的 JSON、速率限制|`load` 事件处理程序中的测试|

处理错误时，会调用 `renderErrorMessage()`，获取有关该错误的任何已知详细信息。 如果响应通过全部错误测试，则会调用 `renderSearchResults()`，在页面中显示搜索结果。

## <a name="displaying-search-results"></a>显示搜索结果
用于显示搜索结果的主函数是 `renderSearchResults()`。 此函数将获取必应新闻搜索服务返回的 JSON 并显示新闻结果和相关搜索（如果有）。

```javascript
// render the search results given the parsed JSON response
function renderSearchResults(results) {

    // add Prev / Next links with result count
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);

    // Render the results to the mainline section
    for (section in { mainline: 0 }) {
         showDiv(section, renderResultsItems(section, results));
    }
}
```
搜索结果在 JSON 响应中返回为顶级 `value` 对象。 我们将这些结果传递给函数 `renderResultsItems()`，该函数会对其进行遍历并调用一个函数以将每一项呈现到 HTML 中。 生成的 HTML 将返回到 `renderSearchResults()`，该 HTML 在其中插入到页面中的 `results` 划分。

```javascript
// render search results
    function renderResultsItems(section, results) {   

        var items = results.value;
        var html = [];
        for (var i = 0; i < items.length; i++) { 
            var item = items[i];
            // collection name has lowercase first letter
            var type = "videos";
            var render = searchItemRenderers[type];
            html.push(render(item, section));  
        }
    return html.join("\n\n");
}
```

必应新闻搜索 API 最多返回四种不同类型的相关结果，每个都有其自己的顶级对象。 它们分别是：

|关系|说明|
|-|-|
|`pivotSuggestions`|将原始搜索中的透视字替换为其他字的查询。 例如，如果搜索“red flowers”，透视字可能为“red”，并且透视建议可能为“yellow flowers”。|
|`queryExpansions`|通过添加更多词缩小原始搜索范围的查询。 例如，如果搜索“Microsoft Surface”，查询扩展可能为“Microsoft Surface Pro”。|
|`relatedSearches`|由输入了原始搜索的用户输入的查询。 例如，如果搜索“Mount Rainier”，相关搜索可能为“Mt. Saint Helens”。|
|`similarTerms`|与原始搜索含义类似的查询。 例如，如果搜索“schools”，类似字词可能为“education”。|

如之前在 `renderSearchResults()` 中所示，我们仅呈现 `relatedItems` 建议并将生成的链接放在页面的侧栏中。

## <a name="rendering-result-items"></a>呈现结果项

在 JavaScript 代码中，对象 `searchItemRenderers` 可能包含呈现器：为每种搜索结果生成 HTML 的函数  。 视频搜索页仅使用 `videos`。 请参阅其他教程了解各种类型的呈现器。

```javascript
searchItemRenderers = {
    news: function(item) { ... },
    webPages: function (item) { ... }, 
    images: function(item, index, count) { ... },
    videos: function (item, section, index, count) { ... },
    relatedSearches: function(item) { ... }
}
```
呈现器函数可以接受以下参数：

|参数|说明|
|-|-|
|`item`| 包含项目属性（如其 URL 及其说明）的 JavaScript 对象。|
|`index`| 结果项集合中的结果项的索引。|
|`count`| 搜索结果项集合中的项数。|

`index` 和 `count` 参数可用于计算结果数、为集合的开头或末尾生成特殊的 HTML、在一定数量的项后插入换行符，等等。 呈现器在不需要此功能的情况下，不需接受这两个参数。

`video` 呈现器显示在以下 javascript 摘录中： 使用视频终结点时，所有结果的类型都是 `Videos`。 以下代码段中显示了 `searchItemRenderers`。

```javascript
// render functions for various types of search results
    searchItemRenderers = {

    videos: function (item, section, index, count) {
        var height = 60;
        var width = Math.round(height * item.thumbnail.width / item.thumbnail.height);
        var html = [];

        html.push("<p class='images'>");
        html.push("<a href='" + item.hostPageUrl + "'>");
        var title = escapeQuotes(item.name) + "\n" + getHost(item.hostPageDisplayUrl);
        html.push("<img src='" + item.thumbnailUrl + "&h=" + height + "&w=" + width +
            "' height=" + height + " width=" + width + " title='" + title + "' alt='" + title + "'>");
        html.push("</a>");
        html.push("<br>");
        html.push("<nobr><a href='" + item.contentUrl + "'>Video page source</a> - ");
        html.push(title.replace("\n", " (").replace(/([a-z0-9])\.([a-z0-9])/g, "$1.<wbr>$2") + ")</p>");
        return html.join("");
    }
}
```

呈现器函数：
> [!div class="checklist"]
> * 可创建段落标记，将其分配给 `images` 类，并将其推到 html 数组。
> * 可计算图像缩略图大小（宽度固定为 60 像素，高度按比例计算）。
> * 可生成 HTML `<img>` 标记以显示图像缩略图。 
> * 生成链接到图像及所在页面的 HTML `<a>` 标记。
> * 可生成显示有关图像及所在站点的信息的说明。

`<img>` 标记以及缩略图 URL 的 `h` 和 `w` 字段中均使用了缩略图大小。 必应将返回恰好该大小的[缩略图](../bing-web-search/resize-and-crop-thumbnails.md)。

## <a name="persisting-client-id"></a>保留客户端 ID
来自必应搜索 API 的响应可能包含应通过后续请求发送回 API 的 `X-MSEdge-ClientID` 标头。 如果正在使用多个必应搜索 API，应将相同客户端 ID 用于所有这些必应搜索 API（如有可能）。

提供 `X-MSEdge-ClientID` 标头可以让必应 API 关联用户的所有搜索，这有两个主要好处。

首先，它允许必应搜索引擎将过去的上下文应用于搜索来查找更好地满足用户的结果。 例如，如果用户以前搜索过与航海相关的词汇，则稍后搜索“节”时，系统可能会优先返回在航海中使用的节的信息。

其次，在新功能广泛应用之前，必应可能会随机选择用户体验该功能。 为每个请求提供相同客户端 ID 可确保看到该功能的用户可以始终看到。 如果没有客户端 ID，用户可能会看到功能在其搜索结果中随机出现和消失。

浏览器安全策略 (CORS) 可能会阻止将 `X-MSEdge-ClientID` 标头提供给 JavaScript。 当搜索响应的域不同于请求搜索的页面时，会出现此限制。 在生产环境中，应该托管一个服务器端脚本，以便在网页所在的域进行 API 调用，这样就可以解决此策略的问题。 由于脚本具有与网页相同的来源，因此会将 `X-MSEdge-ClientID` 标头提供给 JavaScript。

> [!NOTE]
> 在生产 Web 应用程序中，应执行请求服务器端。 否则，你的必应搜索 API 密钥必须包含在网页中，该网页可供查看来源的任何人使用。 收费取决于 API 订阅密钥下的所有使用量（即使请求是由未经授权的用户发出的，也是如此），因此请确保不要公开你的密钥。

进行开发时，可以通过 CORS 代理发出必应 Web 搜索 API 请求。 此类代理的响应包含 `Access-Control-Expose-Headers` 标头，该标头允许响应标头并使其可供 JavaScript 访问。

安装 CORS 代理很容易，教程应用可以用它来访问客户端 ID 标头。 首先，如果尚未安装 Node.js，请先[安装](https://nodejs.org/en/download/)。 然后，在命令窗口中发出以下命令：

    npm install -g cors-proxy-server

接下来，在 HTML 文件中将必应 Web 搜索终结点更改为：

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

最后，运行下面的命令，启动 CORS 代理：

    cors-proxy-server

使用教程应用期间，不要关闭命令窗口；关闭窗口会导致代理停止运行。 在搜索结果下的可展开 HTTP 响应头部分中，现在可以看到 `X-MSEdge-ClientID` 响应头（以及其他响应头），并验证此响应头是否对所有请求都相同。

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [必应视频搜索 API 参考](//docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference)
