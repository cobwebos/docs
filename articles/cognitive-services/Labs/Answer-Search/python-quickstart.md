---
title: Microsoft 认知服务项目应答搜索的 Python 快速入门 | Microsoft Docs
description: Python 示例开始使用 Azure 上的 Microsoft 认知服务项目应答搜索。
services: cognitive-services
author: mikedodaro
ms.service: cognitive-services
ms.technology: project-answer-search
ms.topic: article
ms.date: 04/13/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 9cb5406c616ed8e96d73c00c788a0d20f66dcabd
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366244"
---
# <a name="project-answer-search-python-quickstart"></a>项目应答搜索 Python 快速入门

下面的 Python 示例创建并发送“直布罗陀巨岩”相关信息的请求。

## <a name="prerequisites"></a>先决条件

获取[认知服务实验室](https://aka.ms/answersearchsubscription)免费试用版的访问密钥

本示例使用 Python 3.6.4

## <a name="code-scenario"></a>代码方案 

下面的代码创建一个 URL 预览。
它是通过以下步骤实现的：
1. 声明变量来通过主机和路径指定终结点。
2. 指定要预览的查询 URL，然后添加查询参数。  
3. 设置查询参数。
4. 定义搜索函数，用以创建请求并添加 Ocp-Apim-Subscription-Key 标头。
5. 设置 Ocp-Apim-Subscription-Key 标头。 
6. 建立连接，并发送请求。
7. 输出 JSON 结果。

本演示的完整代码如下：

````
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

````
## <a name="next-steps"></a>后续步骤
- [C# 快速入门](c-sharp-quickstart.md)
- [Java 快速入门](java-quickstart.md)
- [节点快速入门](node-quickstart.md)