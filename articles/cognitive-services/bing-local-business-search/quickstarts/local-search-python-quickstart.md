---
title: 快速入门 - 使用 Python 将查询发送到必应当地企业搜索 API
titleSuffix: Azure Cognitive Services
description: 使用本文开始在 Python 中使用必应当地企业搜索 API。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: 100d731614e2193c5f858249315f748836323b4c
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2019
ms.locfileid: "69906256"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>快速入门：使用 Python 将查询发送到必应当地企业搜索 API

使用此快速入门开始向必应当地企业搜索 API 发送请求，该 API 是一项 Azure 认知服务。 虽然这个简单的应用程序是用 Python 编写的，但 API 是一种 RESTful Web 服务，可以与任何能够发出 HTTP 请求并解析 JSON 的编程语言兼容。

此示例应用程序从 API 获取搜索查询 `hotel in Bellevue` 的本地响应数据。

## <a name="prerequisites"></a>先决条件

* [Python](https://www.python.org/) 2.x 或 3.x
 
必须拥有包含必应 API 的[认知服务 API 帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。 [免费试用版](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)足以满足本快速入门的要求。 使用免费试用版提供的访问密钥。  另请参阅[认知服务定价 - 必应搜索 API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)。

## <a name="run-the-complete-application"></a>运行完整应用程序

以下代码获取本地化结果。 该代码通过以下步骤实现：
1. 声明变量，以按主机和路径指定终结点。
2. 指定查询参数。 
3. 定义搜索函数，用以创建请求并添加 Ocp-Apim-Subscription-Key 标头。
4. 设置 Ocp-Apim-Subscription-Key 标头。 
5. 建立连接，并发送请求。
6. 输出 JSON 结果。

本演示的完整代码如下：

```
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'

host = 'api.cognitive.microsoft.com/bing'
path = '/v7.0/localbusinesses/search'

query = 'restaurant in Bellevue'

params = '?q=' + urllib.parse.quote (query) + '&mkt=en-us'

def get_local():
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection (host)
    conn.request ("GET", path + params, None, headers)
    response = conn.getresponse ()
    return response.read ()

result = get_local()
print (json.dumps(json.loads(result), indent=4))

```

## <a name="next-steps"></a>后续步骤
- [当地企业搜索 Java 快速入门](local-search-java-quickstart.md)
- [当地企业搜索 C# 快速入门](local-quickstart.md)
- [当地企业搜索 Node 快速入门](local-search-node-quickstart.md)
