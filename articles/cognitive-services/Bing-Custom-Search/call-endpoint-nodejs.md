---
title: 使用 Node.js 调用终结点 - 必应自定义搜索 - Microsoft 认知服务
description: 本快速入门演示如何通过使用 Node.js 调用必应自定义搜索终结点来从自定义搜索实例中请求搜索结果。
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: 5d9391cc486dc868a1a291ccc7095291cddd3e4c
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2018
ms.locfileid: "37858453"
---
# <a name="call-bing-custom-search-endpoint-nodejs"></a>调用必应自定义搜索终结点 (Node.js)

本快速入门演示如何通过使用 Node.js 调用必应自定义搜索终结点来从自定义搜索实例中请求搜索结果。 

## <a name="prerequisites"></a>先决条件
若要完成本快速入门，你需要：

- 自定义搜索实例。 请参阅[创建第一个必应自定义搜索实例](quick-start.md)。

- 安装 [Node.js](https://www.nodejs.org/)。

-  具有必应搜索 API 的[认知服务 API 帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。 [免费试用版](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search)足以满足此快速入门的要求。 你需要使用激活免费试用版时提供的访问密钥，也可以使用 Azure 仪表板中的付费订阅密钥。

## <a name="run-the-code"></a>运行代码

要调用必应自定义搜索终结点，请执行以下步骤：

1. 为代码创建文件夹。

2. 从命令提示符或终端导航至刚刚创建的文件夹。

3. 安装 request 节点模块：
    <pre>
    npm install request
    </pre>
    
4. 创建 BingCustomSearch.js 文件并向其中复制以下代码。

5. 用密钥和配置 ID（见步骤 1）替换“YOUR-SUBSCRIPTION-KEY”和“YOUR-CUSTOM-CONFIG-ID”。

    ``` javascript
    var request = require("request");
    
    var subscriptionKey = 'YOUR-SUBSCRIPTION-KEY';
    var customConfigId = 'YOUR-CUSTOM-CONFIG-ID';
    var searchTerm = 'microsoft';
    
    var options = {
        url: 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 
          'q=' + searchTerm + 
          '&customconfig=' + customConfigId,
        headers: {
            'Ocp-Apim-Subscription-Key' : subscriptionKey
        }
    }
    
    request(options, function(error, response, body){
        var searchResponse = JSON.parse(body);
        for(var i = 0; i < searchResponse.webPages.value.length; ++i){
            var webPage = searchResponse.webPages.value[i];
            console.log('name: ' + webPage.name);
            console.log('url: ' + webPage.url);
            console.log('displayUrl: ' + webPage.displayUrl);
            console.log('snippet: ' + webPage.snippet);
            console.log('dateLastCrawled: ' + webPage.dateLastCrawled);
            console.log();
        }
    })
    ```
6. 使用以下命令运行代码。
    ```    
    node BingCustomSearch.js
   ``` 

## <a name="next-steps"></a>后续步骤
- [配置托管 UI 体验](./hosted-ui.md)
- [使用修饰标记来突出显示文本](./hit-highlighting.md)
- [网页](./page-webpages.md)
