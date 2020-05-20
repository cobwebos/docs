---
title: 教程：创建单页 Web 应用 - 必应图像搜索 API
titleSuffix: Azure cognitive services
description: 通过必应图像搜索 API 可以搜索 Web 以寻找高质量的相关图像。 使用本教程构建可向 API 发送搜索查询并在网页内显示结果的单页 Web 应用程序。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: tutorial
ms.date: 03/05/2020
ms.author: aahi
ms.openlocfilehash: 9227417d28eb09a322dd4757033ee62fee97d91c
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "78943892"
---
# <a name="tutorial-create-a-single-page-app-using-the-bing-image-search-api"></a>教程：使用必应图像搜索 API 创建单页应用

通过必应图像搜索 API 可以搜索 Web 以寻找高质量的相关图像。 使用本教程构建可向 API 发送搜索查询并在网页内显示结果的单页 Web 应用程序。 本教程与针对必应 Web 搜索的[相应教程](../Bing-Web-Search/tutorial-bing-web-search-single-page-app.md)类似。

教程应用演示了如何：

> [!div class="checklist"]
> * 在 JavaScript 中执行必应图像搜索 API 调用
> * 使用搜索选项改进搜索结果
> * 显示搜索结果并对其分页
> * 请求并处理 API 订阅密钥和必应客户端 ID。

本教程的完整源代码可在 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Image-Search) 上获得。

## <a name="prerequisites"></a>必备条件

* 最新版本的 [Node.js](https://nodejs.org/)。
* 适用于 Node.js 的 [Express.js](https://expressjs.com/) 框架。 可在 GitHub 示例自述文件中获取源代码的安装说明。

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="manage-and-store-user-subscription-keys"></a>管理和存储用户订阅密钥

此应用程序使用 Web 浏览器的一致性存储来存储 API 订阅密钥。 如果尚未存储任何密钥，网页将提示用户输入其密钥进行存储，以备后用。 如果密钥在之后被 API 拒绝，应用会将其从存储中删除。 此示例使用全局终结点。 也可以使用资源的 Azure 门户中显示的[自定义子域](../../cognitive-services/cognitive-services-custom-subdomains.md)终结点。


定义 `storeValue` 和 `retrieveValue` 函数以使用 `localStorage` 对象（如果浏览器支持它）或 Cookie。

```javascript
// Cookie names for data being stored
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";
// The Bing Image Search API endpoint
BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/images/search";

try { //Try to use localStorage first
    localStorage.getItem;   

    window.retrieveValue = function (name) {
        return localStorage.getItem(name) || "";
    }
    window.storeValue = function(name, value) {
        localStorage.setItem(name, value);
    }
} catch (e) {
    //If the browser doesn't support localStorage, try a cookie
    window.retrieveValue = function (name) {
        var cookies = document.cookie.split(";");
        for (var i = 0; i < cookies.length; i++) {
            var keyvalue = cookies[i].split("=");
            if (keyvalue[0].trim() === name) return keyvalue[1];
        }
        return "";
    }
    window.storeValue = function (name, value) {
        var expiry = new Date();
        expiry.setFullYear(expiry.getFullYear() + 1);
        document.cookie = name + "=" + value.trim() + "; expires=" + expiry.toUTCString();
    }
}
```

`getSubscriptionKey()` 函数使用 `retrieveValue` 来尝试检索之前存储的密钥。 如果未找到密钥，它将提示用户输入其密钥，并使用 `storeValue` 存储该密钥。

```javascript

// Get the stored API subscription key, or prompt if it's not found
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

## <a name="send-search-requests"></a>发送搜索请求

此应用程序通过 `onsubmit` 属性调用 `newBingImageSearch()`，来使用 HTML `<form>` 首先发送用户搜索请求。

```html
<form name="bing" onsubmit="return newBingImageSearch(this)">
```

默认情况下，`onsubmit` 处理程序会返回 `false`，阻止表单提交。

## <a name="select-search-options"></a>选择搜索选项

![[必应图像搜索表单]](media/cognitive-services-bing-images-api/image-search-spa-form.png)

必应图像搜索 API 提供若干个[筛选器查询参数](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#filter-query-parameters)来缩小范围和筛选搜索结果。 此应用程序中的 HTML 表单使用并显示以下参数选项：

|              |                                                                                                                                                                                    |
|--------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `where`      | 用于选择市场（位置和语言）进行搜索的下拉菜单。                                                                                             |
| `query`      | 用于输入搜索词的文本字段。                                                                                                                                 |
| `aspect`     | 用于选择所发现图像比例的单选按钮：大致方形、宽或高。                                                                                     |
| `color`      |                                                                                                                                                                                    |
| `when`       | 用于视需要将搜索限制为最近一天、最近一周或最近一个月的下拉菜单。                                                                                          |
| `safe`       | 指示是否使用必应的安全搜索功能筛选出“成人”结果的复选框。                                                                                      |
| `count`      | 隐藏的字段。 将在每个请求后返回的搜索结果数。 更改为每页显示更少或更多结果。                                                            |
| `offset`     | 隐藏的字段。 请求中第一个搜索结果的偏移；用于分页。 在新请求中将重置为 `0`。                                                           |
| `nextoffset` | 隐藏的字段。 收到搜索结果以后，此字段会设置为响应中 `nextOffset` 的值。 使用此字段可避免连续页上的结果重叠。 |
| `stack`      | 隐藏的字段。 一个 JSON 编码的列表，其中包含搜索结果的前述页的偏移，方便导航回前述页。                                                      |

`bingSearchOptions()` 函数将这些选项格式化为部分查询字符串，该字符串可在应用的 API 请求中使用。  

```javascript
// Build query options from the HTML form
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

## <a name="performing-the-request"></a>执行请求

借助搜索查询、选项字符串和 API 密钥，`BingImageSearch()` 函数使用 XMLHttpRequest 对象向必应图像搜索终结点发出请求。


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

HTTP 请求成功完成后，JavaScript 会调用“load”事件处理程序 `handleBingResponse()` 来处理成功的 HTTP GET 请求。

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
> 成功的 HTTP 请求可能包含失败的搜索信息。 如果搜索操作期间出现错误，必应图像搜索 API 将在 JSON 响应中返回非 200 HTTP 状态代码和错误信息。 此外，如果请求速率受限制，该 API 还将返回空响应。

## <a name="display-the-search-results"></a>显示搜索结果

搜索结果由 `renderSearchResults()` 函数显示，该函数获得由必应图像搜索服务返回的 JSON，并对任何返回的图像和相关搜索调用适当的呈现器函数。

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

图像搜索结果包含在 JSON 响应内的顶级 `value` 对象中。 这些结果会传递到 `renderImageResults()`，其将循环访问结果并将每个项转换为 HTML。

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

必应图像搜索 API 可以返回四种类型的搜索建议以帮助引导用户的搜索体验，每个建议都在其自己的顶级对象中。

| 建议         | 说明                                                                                                                                                                                                         |
|--------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `pivotSuggestions` | 将原始搜索中的透视字替换为其他字的查询。 例如，如果搜索“red flowers”，透视字可能为“red”，并且透视建议可能为“yellow flowers”。 |
| `queryExpansions`  | 通过添加更多词缩小原始搜索范围的查询。 例如，如果搜索“Microsoft Surface”，查询扩展可能为“Microsoft Surface Pro”。                                   |
| `relatedSearches`  | 由输入了原始搜索的用户输入的查询。 例如，如果搜索“Mount Rainier”，相关搜索可能为“Mt. Saint Helens”。                       |
| `similarTerms`     | 与原始搜索含义类似的查询。 例如，如果搜索“kittens”，类似术语可能为“cute”。                                                                   |

此应用程序仅呈现 `relatedItems` 建议并将生成的链接放在页面的侧栏中。

## <a name="rendering-search-results"></a>呈现搜索结果

在此应用程序中，`searchItemRenderers` 对象包含为每种搜索结果生成 HTML 的呈现器函数。

```javascript
searchItemRenderers = {
    images: function(item, index, count) { ... },
    relatedSearches: function(item) { ... }
}
```

这些呈现器函数接受以下参数：

| 参数         | 说明                                                                                              |
|---------|----------------------------------------------------------------------------------------------|
| `item`  | 包含项目属性（如其 URL 及其说明）的 JavaScript 对象。 |
| `index` | 结果项集合中的结果项的索引。                                          |
| `count` | 搜索结果项集合中的项数。                                  |

`index` 和 `count` 参数用于为结果编号，为集合生成 HTML，以及整理内容。 具体而言，它：

* 计算图像缩略图大小（宽度可变，至少为 120 像素，高度则固定为 90 像素）。
* 可生成 HTML `<img>` 标记以显示图像缩略图。
* 生成链接到图像及所在页面的 HTML `<a>` 标记。
* 可生成显示有关图像及所在站点的信息的说明。

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

`<img>` 标记以及缩略图 URL 的 `h` 和 `w` 字段中均使用了缩略图图像的 `height` 和 `width`。 这使得必应可以返回大小恰好的[缩略图](../bing-web-search/resize-and-crop-thumbnails.md)。

## <a name="persisting-client-id"></a>保留客户端 ID

来自必应搜索 API 的响应可能包含应通过后续请求发送回 API 的 `X-MSEdge-ClientID` 标头。 如果正在使用多个必应搜索 API，应将相同客户端 ID 用于所有这些必应搜索 API（如有可能）。

提供 `X-MSEdge-ClientID` 标头可以让必应 API 关联用户的所有搜索，这非常有用。

首先，它允许必应搜索引擎将过去的上下文应用于搜索来查找更好地满足用户的结果。 例如，如果用户以前搜索过与航海相关的词汇，则稍后搜索“节”时，系统可能会优先返回在航海中使用的节的信息。

其次，在新功能广泛应用之前，必应可能会随机选择用户体验该功能。 为每个请求提供相同客户端 ID 可确保被允许看到某个功能的用户可以始终看到该功能。 如果没有客户端 ID，用户可能会看到功能在其搜索结果中随机出现和消失。

浏览器安全策略 (CORS) 可能会阻止将 `X-MSEdge-ClientID` 标头提供给 JavaScript。 当搜索响应的域不同于请求搜索的页面时，会出现此限制。 在生产环境中，应该托管一个服务器端脚本，以便在网页所在的域进行 API 调用，这样就可以解决此策略的问题。 由于脚本具有与网页相同的来源，因此会将 `X-MSEdge-ClientID` 标头提供给 JavaScript。

> [!NOTE]
> 在生产型 Web 应用程序中，无论如何都应在服务器端执行请求。 否则，你的必应搜索 API 密钥必须包含在网页中，该网页可供查看来源的任何人使用。 收费取决于 API 订阅密钥下的所有使用量（即使请求是由未经授权的用户发出的，也是如此），因此请确保不要公开你的密钥。

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
> [使用必应图像搜索 API 提取图像详细信息](tutorial-image-post.md)

## <a name="see-also"></a>另请参阅

* [必应图像搜索 API 参考](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
