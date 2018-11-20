---
title: 快速入门：必应实体搜索 SDK、Node
titleSuffix: Azure Cognitive Services
description: 使用 Node 设置实体搜索 SDK 控制台应用程序。
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: quickstart
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: 69bcca7871d9bf4bebf64c0c0ae1b54cd8408927
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2018
ms.locfileid: "51684114"
---
# <a name="quickstart-bing-entity-search-sdk-with-node"></a>快速入门：通过 Node 使用必应实体搜索 SDK

必应实体搜索 SDK 包含 REST API 的功能，可用于实体查询和分析结果。 

Git Hub 上提供 [C# 必应实体搜索 SDK 示例的源代码](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/entitySearch.js)。
## <a name="application-dependencies"></a>应用程序依赖项

若要使用必应实体搜索 SDK 来设置控制台应用程序，请执行以下操作：
* 在开发环境中运行 `npm install ms-rest-azure`。
* 在开发环境中运行 `npm install azure-cognitiveservices-entitysearch`。

## <a name="entity-search-client"></a>实体搜索客户端
在“搜索”下获取[认知服务访问密钥](https://azure.microsoft.com/try/cognitive-services/)。 创建 `CognitiveServicesCredentials` 的实例：
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
然后对该客户端进行实例化，并搜索结果：
```
const EntitySearchAPIClient = require('azure-cognitiveservices-entitysearch');

let entitySearchApiClient = new EntitySearchAPIClient(credentials);

entitySearchApiClient.entitiesOperations.search('seahawks').then((result) => {
    console.log(result.queryContext);
    console.log(result.entities.value);
    console.log(result.entities.value[0].description);
}).catch((err) => {
    throw err;
});

```
代码会将 `result.value` 项打印至控制台，并且不会分析任何文本。  结果（如果每个类别都有结果）将包括：
- _type: 'Thing'
- _type: 'ImageObject'

<!-- Removing until we can replace with a sanitized version.
![Entity results](media/entity-search-sdk-node-quickstart-results.png)
-->

## <a name="next-steps"></a>后续步骤

[认知服务 Node.js SDK 示例](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)