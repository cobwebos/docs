---
title: 文本翻译使用 Python 获取句子长度 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 本快速入门将在认知服务中使用文本翻译 API 和 Python 找到文本中句子的长度。
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/21/2018
ms.author: nolachar
ms.openlocfilehash: 7b822dfa2db1b9d106d6d569fb26d9f6e8109c83
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2018
ms.locfileid: "43768688"
---
# <a name="quickstart-get-sentence-lengths-with-python"></a>快速入门：使用 Python 获取句子长度

本快速入门使用文本翻译 API 找到文本中句子的长度。

## <a name="prerequisites"></a>先决条件

需要使用 [Python 3.x](https://www.python.org/downloads/) 运行此代码。

若要使用文本翻译 API，还需要订阅密钥；请参阅[如何注册文本翻译 API](translator-text-how-to-signup.md)。

## <a name="breaksentence-request"></a>BreakSentence 请求

以下代码使用 [BreakSentence](./reference/v3-0-break-sentence.md) 方法将源文本分解为句子。

1. 在喜欢使用的代码编辑器中新建一个 Python 项目。
2. 添加以下提供的代码。
3. 使用对订阅有效的访问密钥替换 `subscriptionKey` 值。
4. 运行该程序。

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, uuid, json

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

host = 'api.cognitive.microsofttranslator.com'
path = '/breaksentence?api-version=3.0'

params = ''

text = 'How are you? I am fine. What did you do today?'

def breakSentences (content):

    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-type': 'application/json',
        'X-ClientTraceId': str(uuid.uuid4())
    }

    conn = http.client.HTTPSConnection(host)
    conn.request ("POST", path + params, content, headers)
    response = conn.getresponse ()
    return response.read ()

requestBody = [{
    'Text' : text,
}]
content = json.dumps(requestBody, ensure_ascii=False).encode('utf-8')
result = breakSentences (content)

# Note: We convert result, which is JSON, to and from an object so we can pretty-print it.
# We want to avoid escaping any Unicode characters that result contains. See:
# https://stackoverflow.com/questions/18337407/saving-utf-8-texts-in-json-dumps-as-utf8-not-as-u-escape-sequence
output = json.dumps(json.loads(result), indent=4, ensure_ascii=False)

print (output)
```

## <a name="breaksentence-response"></a>BreakSentence 响应

在 JSON 中返回成功的响应，如以下示例所示：

```json
[
  {
    "detectedLanguage": {
      "language": "en",
      "score": 1.0
    },
    "sentLen": [
      13,
      11,
      22
    ]
  }
]
```

## <a name="next-steps"></a>后续步骤

浏览此快速入门的示例代码和其他内容，包括翻译和音译，以及 GitHub 上的其他示例文本翻译项目。

> [!div class="nextstepaction"]
> [浏览 GitHub 上的 Python 示例](https://aka.ms/TranslatorGitHub?type=&language=python)
