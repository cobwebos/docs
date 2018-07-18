---
title: 必应图像搜索单页 Web 应用 | Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: 介绍如何在单页 Web 应用程序中使用必应图像搜索 API。
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 10/04/2017
ms.author: v-brapel
ms.openlocfilehash: 303d7745167d2ea25fda083ed99881ac4e0a7ec7
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366313"
---
# <a name="tutorial-visual-search-single-page-web-app"></a>教程：视觉搜索单页 Web 应用

必应视觉搜索 API 提供的体验类似于 Bing.com/images 上所示的图像详细信息。 借助视觉搜索，可以指定图像并取回有关图像的见解，如视觉上相似的图像、购物源、包含图像的网页等。 

本教程从必应图像搜索教程扩展单页 Web 应用（请参阅[单页 Web 应用](../Bing-Image-Search/tutorial-bing-image-search-single-page-app.md)）。 有关开始本教程的完整源代码，请参阅[单页 Web 应用（源代码）](../Bing-Image-Search/tutorial-bing-image-search-single-page-app-source.md)。 有关本教程的最终源代码，请参阅 [视觉搜索单页 Web 应用](tutorial-bing-visual-search-single-page-app-source.md)。

涵盖的任务包括：

> [!div class="checklist"]
> * 使用图像 insights 令牌调用必应视觉搜索 API
> * 显示相似的图像

## <a name="call-bing-visual-search"></a>调用必应视觉搜索
编辑必应图像搜索教程，并将以下代码添加到第 409 行的脚本元素的末尾。 此代码调用必应视觉搜索 API 并显示结果。

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
将以下代码添加到第 151 行的 `searchItemsRenderer` 对象中。 此代码添加“查找类似”链接，该链接在单击时调用 `bingVisualSearch` 函数。 该函数将 imageInsightsToken 接收为参数。

``` javascript
html.push("<a href='javascript:bingVisualSearch(\"" + item.imageInsightsToken + "\");'>find similar</a><br>");
```

## <a name="display-similar-images"></a>显示相似的图像
将以下 HTML 代码添加到第 601 行。 此标记代码添加了一个用于显示必应视觉搜索 API 调用结果的元素。

``` html
<div id="insights">
    <h3><a href="#" onclick="return toggleDisplay('_insights')">Similar</a></h3>
    <div id="_insights" style="display: none"></div>
</div>
```

所有新的 JavaScript 代码和 HTML 元素就绪后，搜索结果将通过“查找类似”链接显示。 单击此链接以使用与你选择的图像类似的图像填充“相似”部分。 你可能需要展开“相似”部分以显示图像。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [视觉搜索单页 Web 应用源](tutorial-bing-visual-search-single-page-app-source.md)
> [必应视觉搜索 API 参考](https://aka.ms/bingvisualsearchreferencedoc)