---
title: " 构建单页 Web 应用 - 必应视觉搜索"
titleSuffix: Azure Cognitive Services
description: 了解如何将必应视觉搜索 API 集成到单页 Web 应用程序中。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: article
ms.date: 04/05/2019
ms.author: aahi
ms.openlocfilehash: 084aad5540a2bd56d98e343639a45c16f786e599
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/10/2019
ms.locfileid: "59469091"
---
# <a name="create-a-visual-search-single-page-web-app"></a>创建视觉搜索单页 Web 应用

必应视觉搜索 API 返回图像见解。 你可以上传图像，也可以提供图像的 URL。 见解是视觉上类似的图像、购物源、包含图像的网页，等等。 必应视觉搜索 API 返回的见解类似于 Bing.com/images 上显示的见解。

本教程介绍如何扩展必应图像搜索 API 的单页 web 应用。 若要查看该教程或获取此处使用的源代码，请参阅[教程：创建必应图像搜索 API 的单页应用](../Bing-Image-Search/tutorial-bing-image-search-single-page-app.md)。

[GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchApp.html) 上提供了此应用程序的完整源代码（在扩展它以使用必应视觉搜索 API 后）。

## <a name="prerequisites"></a>必备组件

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="call-the-bing-visual-search-api-and-handle-the-response"></a>调用必应视觉搜索 API 并处理响应

编辑必应图像搜索教程，并将以下代码添加到 `<script>` 元素的末尾（在结尾 `</script>` 标记之前）。 下面的代码处理来自 API 的视觉搜索响应、 循环访问结果，并显示它们：

``` javascript
function handleVisualSearchResponse(){
    if(this.status !== 200){
        console.log(this.responseText);
        return;
    }
    let json = this.responseText;
    let response = JSON.parse(json);
    for (let i =0; i < response.tags.length; i++) {
        let tag = response.tags[i];
        if (tag.displayName === '') {
            for (let j = 0; j < tag.actions.length; j++) {
                let action = tag.actions[j];
                if (action.actionType === 'VisualSearch') {
                    let html = '';
                    for (let k = 0; k < action.data.value.length; k++) {
                        let item = action.data.value[k];
                        let height = 120;
                        let width = Math.max(Math.round(height * item.thumbnail.width / item.thumbnail.height), 120);
                        html += "<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width + "' height=" + height + " width=" + width + "'>";
                    }
                    showDiv("insights", html);
                    window.scrollTo({top: document.getElementById("insights").getBoundingClientRect().top, behavior: "smooth"});
                }
            }
        }
    }
}
```

以下代码将搜索请求发送到 API，使用事件侦听器调用`handleVisualSearchResponse()`:

```javascript
function bingVisualSearch(insightsToken){
    let visualSearchBaseURL = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch',
        boundary = 'boundary_ABC123DEF456',
        startBoundary = '--' + boundary,
        endBoundary = '--' + boundary + '--',
        newLine = "\r\n",
        bodyHeader = 'Content-Disposition: form-data; name="knowledgeRequest"' + newLine + newLine;

    let postBody = {
        imageInfo: {
            imageInsightsToken: insightsToken
        }
    }
    let requestBody = startBoundary + newLine;
    requestBody += bodyHeader;
    requestBody += JSON.stringify(postBody) + newLine + newLine;
    requestBody += endBoundary + newLine;

    let request = new XMLHttpRequest();

    try {
        request.open("POST", visualSearchBaseURL);
    } 
    catch (e) {
        renderErrorMessage("Error performing visual search: " + e.message);
    }
    request.setRequestHeader("Ocp-Apim-Subscription-Key", getSubscriptionKey());
    request.setRequestHeader("Content-Type", "multipart/form-data; boundary=" + boundary);
    request.addEventListener("load", handleVisualSearchResponse);
    request.send(requestBody);
}
```

## <a name="capture-insights-token"></a>捕获 insights 令牌

将以下代码添加到`searchItemsRenderer`对象。 此代码添加“查找类似”链接，该链接在单击时调用 `bingVisualSearch` 函数。 在函数收到`imageInsightsToken`作为自变量。

``` javascript
html.push("<a href='javascript:bingVisualSearch(\"" + item.imageInsightsToken + "\");'>find similar</a><br>");
```

## <a name="display-similar-images"></a>显示相似的图像

将以下 HTML 代码添加到第 601 行。 此标记代码将添加一个元素以显示必应视觉搜索 API 调用的结果：

``` html
<div id="insights">
    <h3><a href="#" onclick="return toggleDisplay('_insights')">Similar</a></h3>
    <div id="_insights" style="display: none"></div>
</div>
```

所有新的 JavaScript 代码和 HTML 元素就绪后，搜索结果将通过“查找类似”链接显示。 单击此链接以使用与你选择的图像类似的图像填充“相似”部分。 你可能需要展开“相似”部分以显示图像。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：使用适用于 C# 的必应视觉搜索 SDK 裁剪图像](tutorial-visual-search-crop-area-results.md)
