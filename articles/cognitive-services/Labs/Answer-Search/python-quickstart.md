---
title: 快速入门：项目答案搜索、Python
titlesuffix: Azure Cognitive Services
description: 开始使用项目答案搜索的 Python 示例。
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: quickstart
ms.date: 04/13/2018
ms.author: rosh
ms.openlocfilehash: c0da596e84ac827b55affd5545c516e7623980f5
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698066"
---
# <a name="quickstart-project-answer-search-with-python"></a>快速入门：通过 Python 使用项目答案搜索

下面的 Python 示例创建并发送“直布罗陀巨岩”相关信息的请求。

## <a name="prerequisites"></a>先决条件

获取[认知服务实验室](https://labs.cognitive.microsoft.com/en-us/project-answer-search)免费试用版的访问密钥

本示例使用 Python 3.6.4

## <a name="code-scenario"></a>代码方案 

下面的代码创建一个 URL 预览。
它是通过以下步骤实现的：
1. 声明变量，以按主机和路径指定终结点。
2. 指定要预览的查询 URL，然后添加查询参数。  
3. 设置查询参数。
4. 定义搜索函数，用以创建请求并添加 Ocp-Apim-Subscription-Key 标头  。
5. 设置 Ocp-Apim-Subscription-Key 标头  。 
6. 建立连接，并发送请求。
7. 输出 JSON 结果。

本演示的完整代码如下：

```
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'

host = 'https://api.labs.cognitive.microsoft.com'
path = '/answerSearch/v7.0/search '

query = 'Rock of Gibraltar'

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
- [C# 快速入门](c-sharp-quickstart.md)
- [Java 快速入门](java-quickstart.md)
- [节点快速入门](node-quickstart.md)
