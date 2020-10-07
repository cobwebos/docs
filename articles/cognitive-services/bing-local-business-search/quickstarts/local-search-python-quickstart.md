---
title: 快速入门 - 使用 Python 将查询发送到 API - 必应当地企业搜索
titleSuffix: Azure Cognitive Services
description: 使用本快速入门开始在 Python 中使用必应当地企业搜索 API。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 05/12/2020
ms.author: aahi
ms.custom: devx-track-python
ms.openlocfilehash: 30e66260f0603139bf45f9a0f0b5f19539ae468e
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "87843549"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>快速入门：使用 Python 将查询发送到必应当地企业搜索 API

使用此快速入门了解如何向必应当地企业搜索 API 发送请求，该 API 是一项 Azure 认知服务。 尽管这个简单的应用程序是用 Python 编写的，但 API 是一种 RESTful Web 服务，可以与任何能够发出 HTTP 请求并解析 JSON 的编程语言兼容。

此示例应用程序从 API 获取搜索查询的本地响应数据。

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services/)
* [Python](https://www.python.org/) 2.x 或 3.x。
* 拥有 Azure 订阅后，在 Azure 门户中<a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title="创建必应搜索资源"  target="_blank">创建必应搜索资源<span class="docon docon-navigate-external x-hidden-focus"></span></a>来获取密钥和终结点。 部署后，单击“转到资源”。

## <a name="run-the-complete-application"></a>运行完整应用程序

下面的示例将获取本地化结果，实现步骤如下：
1. 声明变量，以按主机和路径指定终结点。
2. 指定查询参数。 
3. 定义搜索函数，用以创建请求并添加 `Ocp-Apim-Subscription-Key` 标头。
4. 设置 `Ocp-Apim-Subscription-Key` 标头。 
5. 建立连接，并发送请求。
6. 输出 JSON 结果。

本演示的完整代码如下所示：

```python
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'

host = 'api.cognitive.microsoft.com'
path = '/bing/v7.0/localbusinesses/search'

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
- [当地企业搜索 Node.js 快速入门](local-search-node-quickstart.md)
