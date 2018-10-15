---
title: 快速入门：必应拼写检查 API、Python
titlesuffix: Azure Cognitive Services
description: 获取信息和代码示例，以帮助你快速开始使用必应拼写检查 API。
services: cognitive-services
author: v-jaswel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-spell-check
ms.topic: quickstart
ms.date: 09/14/2017
ms.author: v-jaswel
ms.openlocfilehash: 8614e4388f45c3bc9f71697bad44589e2165f6c8
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2018
ms.locfileid: "48803284"
---
# <a name="quickstart-for-bing-spell-check-api-with-python"></a>通过 Python 使用必应拼写检查 API 快速入门 

本文展示了如何通过 Python 使用[必应拼写检查 API](https://azure.microsoft.com/services/cognitive-services/spell-check/)。 拼写检查 API 返回它无法识别的单词和建议的替换的列表。 通常，你将向此 API 提交文本，然后在文本中进行建议的替换，或者向应用程序的用户显示这些替换，以便他们可以决定是否进行替换。 本文介绍如何发送包含文本“Hollo, wrld!”的请求 建议的替换为“Hello”和“world”。

## <a name="prerequisites"></a>先决条件

需要使用 [Python 3.x](https://www.python.org/downloads/) 来运行此代码。

必须创建一个使用必应拼写检查 API v7 的[认知服务 API 帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。 [免费试用版](https://azure.microsoft.com/try/cognitive-services/#lang)足以满足本快速入门的要求。 需要激活免费试用版时提供的访问密钥，或使用 Azure 仪表板中的付费订阅密钥。

## <a name="get-spell-check-results"></a>获取拼写检查结果

1. 在喜欢使用的 IDE 中创建新的 Python 项目。
2. 添加下方提供的代码。
3. 使用对订阅有效的访问密钥替换 `subscriptionKey` 值。
4. 运行该程序。

```python
import http.client, urllib.parse, json

text = 'Hollo, wrld!'

data = {'text': text}

# NOTE: Replace this example key with a valid subscription key.
key = 'ENTER KEY HERE'

host = 'api.cognitive.microsoft.com'
path = '/bing/v7.0/spellcheck?'
params = 'mkt=en-us&mode=proof'

headers = {'Ocp-Apim-Subscription-Key': key,
'Content-Type': 'application/x-www-form-urlencoded'}

# The headers in the following example 
# are optional but should be considered as required:
#
# X-MSEdge-ClientIP: 999.999.999.999  
# X-Search-Location: lat: +90.0000000000000;long: 00.0000000000000;re:100.000000000000
# X-MSEdge-ClientID: <Client ID from Previous Response Goes Here>

conn = http.client.HTTPSConnection(host)
body = urllib.parse.urlencode (data)
conn.request ("POST", path + params, body, headers)
response = conn.getresponse ()
output = json.dumps(json.loads(response.read()), indent=4)
print (output)
```

**响应**

在 JSON 中返回成功的响应，如以下示例所示： 

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [必应拼写检查教程](../tutorials/spellcheck.md)

## <a name="see-also"></a>另请参阅

- [必应拼写检查概述](../proof-text.md)
- [必应拼写检查 API v7 参考](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference)
