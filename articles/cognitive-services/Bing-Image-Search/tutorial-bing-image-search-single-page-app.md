---
title: 必应图像搜索单页 Web 应用 | Microsoft Docs
description: 介绍如何在单页 Web 应用程序中使用必应图像搜索 API。
services: cognitive-services
author: v-jerkin
manager: ehansen
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 10/04/2017
ms.author: v-jerkin
ms.openlocfilehash: d0e1dc24513c8fc3a405cf1c18f531a0c58fad13
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366467"
---
# <a name="tutorial-single-page-web-app"></a>教程：单页 Web 应用

可以通过必应图像搜索 API 搜索 Web 并获取与搜索查询相关的图像结果。 本教程将生成一个单页 Web 应用程序，该应用程序使用必应图像搜索 API 直接在页面中显示搜索结果。 该应用程序包含 HTML、CSS 和 JavaScript 组件。

<!-- Remove until we can sanitize images
![[Single-page Bing Image Search app]](media/cognitive-services-bing-images-api/image-search-spa-demo.png)
-->

> [!NOTE]
> 页面底部出现的 JSON 和 HTTP 标题揭示单击时的 JSON 响应和 HTTP 请求信息。 这些详细信息在浏览该服务时很有用。

教程应用演示了如何：

> [!div class="checklist"]
> * 在 JavaScript 中执行必应图像搜索 API 调用
> * 将搜索选项传递到必应图像搜索 API
> * 显示搜索结果
> * 对搜索结果进行分页
> * 处理必应客户端 ID 和 API 订阅密钥
> * 处理可能出现的错误

教程页是完全独立的；它不使用任何外部框架、样式表，甚至也不使用外部图像文件， 而只使用获得广泛支持的 JavaScript 语言功能，适用于所有主要 Web 浏览器的最新版本。

在本教程中，我们只讨论源代码的选定部分。 完整的源代码[在单独页上](tutorial-bing-image-search-single-page-app-source.md)提供。 请将此代码复制并粘贴到文本编辑器中，并将其另存为 `bing.html`。

> [!NOTE]
> 本教程大致类似于[单页必应 Web 搜索应用教程](../Bing-Web-Search/tutorial-bing-web-search-single-page-app.md)，但只涉及图像搜索结果。

## <a name="app-components"></a>应用组件

与任何单页 Web 应用一样，本教程应用程序包含以下三个部分：

> [!div class="checklist"]
> * HTML - 定义页面的结构和内容
> * CSS - 定义页面的外观
> * JavaScript - 定义页面的行为

本教程不详细介绍大多数 HTML 或 CSS，因为它们很简单。

HTML 包含搜索表单，用户可以在其中输入查询并选择搜索选项。 该表单连接到实际上会通过 `<form>` 标记的 `onsubmit` 属性执行搜索的 JavaScript：

```html
<form name="bing" onsubmit="return newBingImageSearch(this)">
```

`onsubmit` 处理程序返回 `false`，这会阻止表单提交到服务器。 JavaScript 代码实际上执行从表单中收集所需的信息并执行搜索的工作。

HTML 还包含划分（HTML `<div>` 标记），可以在其中显示搜索结果。

## <a name="managing-subscription-key"></a>管理订阅密钥

为了避免将必应搜索 API 订阅密钥包含在代码中，我们使用浏览器的持久性存储来存储密钥。 如果尚未存储任何密钥，我们会提示你输入用户密钥进行存储，以备后用。 如果该密钥稍后被 API 拒绝，我们会使已存储的密钥失效，因此系统会再次提示用户输入。

我们定义使用 `localStorage` 对象（如果浏览器支持它）或 Cookie 的 `storeValue` 和 `retrieveValue` 函数。 `getSubscriptionKey()` 函数使用这些函数来存储和检索用户的密钥。

```javascript
// cookie names for data we store
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";

BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/images/search";

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

HTML `<form>` 标记 `onsubmit` 可调用 `bingWebSearch` 函数以返回搜索结果。 `bingWebSearch` 使用 `getSubscriptionKey` 对每个查询进行身份验证。 如先前定义中所示，如果尚未输入密钥，`getSubscriptionKey` 会提示用户输入密钥。 然后会存储该密钥以供应用程序继续使用。

```html
<form name="bing" onsubmit="this.offset.value = 0; return bingWebSearch(this.query.value, 
    bingSearchOptions(this), getSubscriptionKey())">
```

## <a name="selecting-search-options"></a>选择搜索选项

![[必应图像搜索表单]](media/cognitive-services-bing-images-api/image-search-spa-form.png)

HTML 表单包括以下控件：

| | |
|-|-|
|`where`|用于选择市场（位置和语言）进行搜索的下拉菜单。|
|`query`|用于输入搜索词的文本字段。|
|`aspect`|用于选择所发现图像比例的单选按钮：大致方形、宽或高。|
|`color`|选择彩色或黑白色，或者某个主色调。
|`when`|用于视需要将搜索限制为最近一天、最近一周或最近一个月的下拉菜单。|
|`safe`|指示是否使用必应的安全搜索功能筛选出“成人”结果的复选框。|
|`count`|隐藏的字段。 将在每个请求后返回的搜索结果数。 更改为每页显示更少或更多结果。|
|`offset`|隐藏的字段。 请求中第一个搜索结果的偏移；用于分页。 在新请求中将重置为 `0`。|
|`nextoffset`|隐藏的字段。 收到搜索结果以后，此字段会设置为响应中 `nextOffset` 的值。 使用此字段可避免连续页上的结果重叠。|
|`stack`|隐藏的字段。 一个 JSON 编码的列表，其中包含搜索结果的前述页的偏移，方便导航回前述页。|

> [!NOTE]
> 必应图像搜索还提供许多其他的查询参数。 我们在这里仅使用其中一些参数。

JavaScript 函数 `bingSearchOptions()` 会按必应搜索 API 要求的格式将这些字段转换为部分查询字符串。

```javascript
// build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    if (form.when.value.length) options.push("freshness=" + form.when.value);
    var aspect = "all";
    for (var i = 0; i < form.aspect.length; i++) {
        if (form.aspect[i].checked) {
            aspect = form.aspect[i].value;
            break;
        }
    }
    options.push("aspect=" + aspect);
    if (form.color.value) options.push("color=" + form.color.value);
    options.push("count=" + form.count.value);
    options.push("offset=" + form.offset.value);
    return options.join("&");
}
```

例如，安全搜索功能可以是 `strict`、`moderate` 或 `off`，`moderate` 为默认设置。 但是，我们的表单使用只有两种状态的复选框。 JavaScript 代码会将此设置转换为 `strict` 或 `off`（不使用 `moderate`）。

## <a name="performing-the-request"></a>执行请求

有了查询、选项字符串和 API 密钥以后，`BingImageSearch` 函数就会使用 `XMLHttpRequest` 对象向必应图像搜索终结点发出请求。

```javascript
// perform a search given query, options string, and API key
function bingImageSearch(query, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("results", "related", "_json", "_http", "paging1", "paging2", "error");

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
            if (jsobj._type === "Images") {
                if (jsobj.nextOffset) document.forms.bing.nextoffset.value = jsobj.nextOffset;
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
> HTTP 请求成功不一定意味着搜索本身成功。 如果搜索操作中出现错误，必应图像搜索 API 将返回非 200 HTTP 状态代码并将错误信息包含在 JSON 响应中。 此外，如果请求速率受限制，该 API 还会返回空响应。

上面两个函数中的很多代码专用于错误处理。 以下阶段可能会出现错误：

|阶段|可能的错误|处理方式|
|-|-|-|
|生成 JavaScript 请求对象|无效的 URL|`try`/`catch` 块|
|发出请求|网络错误，已中止连接|`error` 和 `abort` 事件处理程序|
|执行搜索|无效的请求、无效的 JSON、速率限制|`load` 事件处理程序中的测试|

处理错误时，会调用 `renderErrorMessage()`，获取有关该错误的任何已知详细信息。 如果响应通过全部错误测试，则会调用 `renderSearchResults()`，在页面中显示搜索结果。

## <a name="displaying-search-results"></a>显示搜索结果

用于显示搜索结果的主函数是 `renderSearchResults()`。 此函数将获取必应图像搜索服务返回的 JSON 并显示图像和相关搜索（如果有）。

```javascript
function renderSearchResults(results) {

    // add Prev / Next links with result count
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);
    
    showDiv("results", renderImageResults(results.value));
    if (results.relatedSearches)
        showDiv("sidebar", renderRelatedItems(results.relatedSearches));
}
```

主要的图像搜索结果在 JSON 响应中返回为顶级 `value` 对象。 我们将这些结果传递给函数 `renderImageResults()`，该函数会对其进行遍历并调用一个单独的函数以将每一项呈现到 HTML 中。 生成的 HTML 将返回到 `renderSearchResults()`，该 HTML 在其中插入到页面中的 `results` 划分。

```javascript
function renderImageResults(items) {
    var len = items.length;
    var html = [];
    if (!len) {
        showDiv("noresults", "No results.");
        hideDivs("paging1", "paging2");
        return "";
    }
    for (var i = 0; i < len; i++) {
        html.push(searchItemRenderers.images(items[i], i, len));
    }
    return html.join("\n\n");
}
```

必应图像搜索 API 最多返回四种不同类型的相关结果，每个都有其自己的顶级对象。 它们是：

|||
|-|-|
|`pivotSuggestions`|将原始搜索中的透视字替换为其他字的查询。 例如，如果搜索“red flowers”，透视字可能为“red”，并且透视建议可能为“yellow flowers”。|
|`queryExpansions`|通过添加更多词缩小原始搜索范围的查询。 例如，如果搜索“Microsoft Surface”，查询扩展可能为“Microsoft Surface Pro”。|
|`relatedSearches`|由输入了原始搜索的用户输入的查询。 例如，如果搜索“Mount Rainier”，相关搜索可能为“Mt. Saint Helens”。|
|`similarTerms`|与原始搜索含义类似的查询。 例如，如果搜索“kittens”，类似术语可能为“cute”。|

如之前在 `renderSearchResults()` 中所示，我们仅呈现 `relatedItems` 建议并将生成的链接放在页面的侧栏中。

## <a name="rendering-result-items"></a>呈现结果项

在 JavaScript 代码中有一个对象 `searchItemRenderers`，其中包含呈现器函数，为每种搜索结果生成 HTML。

```javascript
searchItemRenderers = { 
    images: function(item, index, count) { ... },
    relatedSearches: function(item) { ... }
}
```

呈现器函数可接受以下参数：

| | |
|-|-|
|`item`|包含项目属性（例如其 URL 和说明）的 JavaScript 对象。|
|`index`|结果项集合中的结果项的索引。|
|`count`|搜索结果项集合中的项数。|

`index` 和 `count` 参数可用于计算结果数、为集合的开头或末尾生成特殊的 HTML、在一定数量的项后插入换行符，等等。 呈现器在不需要此功能的情况下，不需接受这两个参数。

让我们更仔细地看看 `images` 呈现器：

```javascript
    images: function (item, index, count) {
        var height = 120;
        var width = Math.max(Math.round(height * item.thumbnail.width / item.thumbnail.height), 120);
        var html = [];
        if (index === 0) html.push("<p class='images'>");
        var title = escape(item.name) + "\n" + getHost(item.hostPageDisplayUrl);
        html.push("<p class='images' style='max-width: " + width + "px'>");
        html.push("<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width + 
            "' height=" + height + " width=" + width + "'>");
        html.push("<br>");
        html.push("<nobr><a href='" + item.contentUrl + "'>Image</a> - ");
        html.push("<a href='" + item.hostPageUrl + "'>Page</a></nobr><br>");
        html.push(title.replace("\n", " (").replace(/([a-z0-9])\.([a-z0-9])/g, "$1.<wbr>$2") + ")</p>");
        return html.join("");
    }, // relatedSearches renderer omitted
```

图像呈现器函数具有以下功能：

> [!div class="checklist"]
> * 计算图像缩略图大小（宽度可变，至少为 120 像素，高度则固定为 90 像素）。
> * 生成 HTML `<img>` 标记以显示图像缩略图。 
> * 生成链接到图像及所在页面的 HTML `<a>` 标记。
> * 生成可显示有关图像及其所在站点的信息的说明。

我们会测试 `index` 变量，以便在第一个图像结果前面插入 `<p>` 标记。 否则，缩略图会彼此对接好，并根据需要在浏览器窗口中换行。

`<img>` 标记以及缩略图 URL 的 `h` 和 `w` 字段中均使用了缩略图大小。 然后，[必应缩略图服务](resize-and-crop-thumbnails.md)会提供正好为该大小的缩略图。

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
> [必应图像搜索 API 参考](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)

