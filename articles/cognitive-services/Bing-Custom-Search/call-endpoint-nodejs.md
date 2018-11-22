---
title: 快速入门：使用 Node.js 调用终结点 - 必应自定义搜索
titlesuffix: Azure Cognitive Services
description: 本快速入门演示如何通过使用 Node.js 调用必应自定义搜索终结点来从自定义搜索实例中请求搜索结果。
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: quickstart
ms.date: 05/07/2018
ms.author: aahi
ms.openlocfilehash: 64fc631e3d8473b53e4b360001dcc4e87dd27be6
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2018
ms.locfileid: "52162613"
---
# <a name="quickstart-call-bing-custom-search-endpoint-nodejs"></a>快速入门：调用必应自定义搜索终结点 (Node.js)

本快速入门介绍了如何使用 Node.js 调用必应自定义搜索终结点，以请求从自定义搜索实例中获取搜索结果。 

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，你需要：

- 现成的自定义搜索实例。 请参阅[创建第一个必应自定义搜索实例](quick-start.md)。
- 安装 [Node.js](https://www.nodejs.org/)。
- 订阅密钥。 可以在激活[免费试用版](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search)时获取订阅密钥，也可以使用 Azure 仪表板中的付费订阅密钥（请参阅[认知服务 API 帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)）。    

## <a name="run-the-code"></a>运行代码

若要运行该示例，请遵循以下步骤：

1. 为代码创建文件夹。  
  
2. 从命令提示符或终端导航至刚刚创建的文件夹。  
  
3. 安装 request 节点模块：
    <pre>
    npm install request
    </pre>  
    
4. 在创建的文件夹中创建 BingCustomSearch.js 文件，并将以下代码复制到其中。 将“YOUR-SUBSCRIPTION-KEY”和“YOUR-CUSTOM-CONFIG-ID”分别替换为订阅密钥和配置 ID。  
  
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
  
6. 使用以下命令运行代码：  
  
    ```    
    node BingCustomSearch.js
    ``` 

## <a name="next-steps"></a>后续步骤
- [配置托管 UI 体验](./hosted-ui.md)
- [使用修饰标记来突出显示文本](./hit-highlighting.md)
- [网页](./page-webpages.md)
