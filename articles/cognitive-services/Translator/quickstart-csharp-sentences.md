---
title: 文本翻译使用 C# 获取句子长度 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 本快速入门将在认知服务中使用文本翻译 API 和 C# 找到文本中句子的长度。
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/15/2018
ms.author: nolachar
ms.openlocfilehash: b3e7f1099b1a7584435646fe3fae237cd458967f
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "43768641"
---
# <a name="quickstart-get-sentence-lengths-with-c35"></a>快速入门：使用 C&#35; 获取句子长度

本快速入门使用文本翻译 API 找到文本中句子的长度。

## <a name="prerequisites"></a>先决条件

需要使用 [Visual Studio 2017](https://www.visualstudio.com/downloads/) 才能在 Windows 上运行此代码。 （免费的社区版也可以。）

若要使用文本翻译 API，还需要订阅密钥；请参阅[如何注册文本翻译 API](translator-text-how-to-signup.md)。

## <a name="breaksentence-request"></a>BreakSentence 请求

以下代码使用 [BreakSentence](./reference/v3-0-break-sentence.md) 方法将源文本分解为句子。

1. 在喜欢使用的 IDE 中新建一个 C# 项目。
2. 添加下方提供的代码。
3. 使用对订阅有效的访问密钥替换 `key` 值。
4. 运行该程序。

```csharp
using System;
using System.Net.Http;
using System.Text;
// NOTE: Install the Newtonsoft.Json NuGet package.
using Newtonsoft.Json;

namespace TranslatorTextQuickStart
{
    class Program
    {
        static string host = "https://api.cognitive.microsofttranslator.com";
        static string path = "/breaksentence?api-version=3.0";

        static string uri = host + path;

        // NOTE: Replace this example key with a valid subscription key.
        static string key = "ENTER KEY HERE";

        static string text = "How are you? I am fine. What did you do today?";

        async static void Break()
        {
            System.Object[] body = new System.Object[] { new { Text = text } };
            var requestBody = JsonConvert.SerializeObject(body);

            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                var responseBody = await response.Content.ReadAsStringAsync();
                var result = JsonConvert.SerializeObject(JsonConvert.DeserializeObject(responseBody), Formatting.Indented);

                Console.OutputEncoding = UnicodeEncoding.UTF8;
                Console.WriteLine(result);
            }
        }

        static void Main(string[] args)
        {
            Break();
            Console.ReadLine();
        }
    }
}
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
> [浏览 GitHub 上的 C# 示例](https://aka.ms/TranslatorGitHub?type=&language=c%23)
